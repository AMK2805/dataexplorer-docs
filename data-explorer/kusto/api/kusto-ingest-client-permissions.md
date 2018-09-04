---
title: Kusto.Ingest Reference - Ingestion Permissions - Azure Kusto | Microsoft Docs
description: This article describes Kusto.Ingest Reference - Ingestion Permissions in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# Kusto.Ingest Reference - Ingestion Permissions
This article explains what permissions need to be set up on your service in order for `Native` ingestion to work.

>Note: if you are using Geneva or Aria ingestion pipelines, no extra setup is required.

## Prerequisites
* This article instructs how to use [Kusto control commands](../controlCommands/roles.md) to view and modify authorization settings on Kusto services and databases
* The following AAD Applications are used as sample principals in examples below:
    * [DataGrid SQLizer](http://datagrid/egress) AAD App (2a904276-4e97-45ed-b9e6-66fc53add60b)
    * Kusto Internal Ingestion AAD App (76263cdb-2183-4596-8949-545644e9c404)

## Ingestion Permission Model for Queued Ingestion
Implemented by [KustoQueuedIngestClient](kusto-ingest-client-reference.md#class-kustoqueuedingestclient), this mode limits the client code dependency on the Kusto service. Ingestion is performed by posting a Kusto ingestion message to an Azure queue, which, in turn is acquired from Kusto Data Management (a.k.a. Ingestion) service. Any intermediate storage artifacts will be created by the ingest client using the resources allocated by Kusto Data Management service.<BR>

The following diagram outlines the Queued ingestion client interaction with Kusto:<BR>

![alt text](images/queued-ingest.jpg "queued-ingest")

### Permissions on the Engine Service
In order to qualify for data ingestion into table `T1` on database `DB1` the principal performing the ingest operation must be authorized for that.
Minimal required permission levels are `Database Ingestor` that can also create tables and `Table Ingestor` that can only ingest data into an existing table.


|Role |PrincipalType	|PrincipalDisplayName
|--------|------------|------------
|Database *** Ingestor |AAD Application |DataGrid SQLizer (app id: 2a904276-4e97-45ed-b9e6-66fc53add60b)
|Table *** Ingestor |AAD Application |DataGrid SQLizer (app id: 2a904276-4e97-45ed-b9e6-66fc53add60b)

>`Kusto Internal Ingestion AAD App (76263cdb-2183-4596-8949-545644e9c404)` principal (Kusto internal Ingestion App) is immutably mapped to the `Cluster Admin` role and thus authorized to ingest data to any table (that is what's happening on Kusto-managed ingestion pipelines).

Granting required permissions on database `DB1` or table `T1` to AAD App `DataGrid SQLizer (76263cdb-2183-4596-8949-545644e9c404)` would look as follows (on the Engine cluster):
```kusto
.add database DB1 ingestors ('aadapp=76263cdb-2183-4596-8949-545644e9c404') 'DataGrid SQLizer AAD App'
.add table T1 ingestors ('aadapp=76263cdb-2183-4596-8949-545644e9c404') 'DataGrid SQLizer AAD App'
```

### Permissions on the Data Management Service
In order for the `KustoQueuedIngestClient` to interact with the Data Management service, and be able to successfully ingest data, the principal running the client only needs to be properly authorized on the Engine service (no additional setup is required).

**Example**:

|Cluster |Database |Table |
|--------|---------|------
|C1      |DB1, DB2 |T1, T2|
|C2      |DB1, DB2 |T1, T2|

|Role on DM      |Role on the Engine |Target table     |Ingestion Authorized? |
|----------------|-------------------|-----------------|----------------
|Cluster Admin   |`DON'T CARE`       |`ANY`            |True (This is a Kusto internal flow)
|N/A             |C1.DB1.T1 Ingestor |C1.DB1.T1        |True
|N/A             |C1.DB1 User        |C1.DB1.T1        |False
|N/A             |C1.DB1 Ingestor    |Any table in DB1 |True
|N/A             |C1.DB1 Ingestor    |Any table in DB2 |False