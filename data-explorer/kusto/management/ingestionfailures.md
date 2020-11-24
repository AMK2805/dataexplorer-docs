---
title: Ingestion failures - Azure Data Explorer
description: This article describes Ingestion failures in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
---
# Ingestion failures

## .show ingestion failures


This command returns a result set that includes any ingestion failures that occur when the
[data ingestion control commands](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands) run.


> [!NOTE]
> Ingestion failures that occur during other parts of the ingestion flow will not appear in the result set of this command. Such a failure may occur, for example, before data ingestion control commands are sent to the Kusto Data Engine service.

> For more information on monitoring failures that occur in flows that involve [queued ingestion](../api/netfx/about-kusto-ingest.md#queued-ingestion), see [this guide](../api/netfx/kusto-ingest-client-status.md).

**Syntax**

|Syntax option|Description|
|---|---| 
|`.show` `ingestion` `failures`                                       |Returns all recorded ingestion failures  
|`.show` `ingestion` `failures` <code>&#124;</code> `where` ...       |Returns a filtered set of ingestion failures
|`.show` `ingestion` `failures` `with(OperationId="`*OperationId*`")` |Returns ingestion failures for a specific Operation ID

**Results**
 
|Output parameter           |Type     |Description                                                                              |
|---------------------------|---------|-----------------------------------------------------------------------------------------|
|OperationId                |String   |Operation identifier that can be used to view additional operation details via the <br> [.show operations](operations.md) command </br> 
|Database                   |String   |Database on which the failure occurred
|Table                      |String   |Table on which the failure occurred
|FailedOn                   |DateTime |Date/time (in UTC) when the failure was registered 
|IngestionSourcePath        |String   |Identifies the ingestion source (usually, an Azure Blob URI) 
|Details                    |String   |Failure details. Provides insight into the actual ingestion failure root cause
|FailureKind                |String   |Type of the failure (Permanent/Transient)
|RootActivityId             |String   |Root Activity ID.
|OperationKind              |String   |The ingestion operation type (phase) during which the failure was registered
|OriginatesFromUpdatePolicy |Boolean | Indicates whether the failure was registered while executing an [Update Policy](update-policy.md)
 
**Example**
 
|OperationId |Database |Table |FailedOn |IngestionSourcePath |Details |FailureKind |RootActivityId |OperationKind |OriginatesFromUpdatePolicy
|--|--|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |DB1 |Table1 |2017-02-14 22:25:03.1147331 |...url... |Stream with ID '*****.csv' has a malformed CSV format* |Permanent |3c883942-e446-4999-9b00-d4c664f06ef6 |DataIngestPull | 0
|841fafa4-076a-4cba-9300-4836da0d9c75 |DB1 |Table1 |2017-02-14 22:34:11.2565943 |...url... |Stream with ID '*****.csv' has a malformed CSV format* |Permanent |48571bdb-b714-4f32-8ddc-4001838a956c |DataIngestPull | 0
|e198c519-5263-4629-a158-8d68f7a1022f |DB1 |Table1 |2017-02-14 22:34:44.5824741 |...url... |Stream with ID '*****.csv' has a malformed CSV format* |Permanent |5e31ab3c-e2c7-489a-827e-e89d2d691ec4 |DataIngestPull | 0
|a9f287a1-f3e6-4154-ad18-b86438da0929 |DB1 |Table1 |2017-02-14 22:36:26.5525250 |...url... |Unknown error occurred: Exception of type 'System.Exception' was thrown |Transient |9b7bb017-471e-48f6-9c96-d16fcf938d2a |DataIngestPull | 0
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DB1 |Table1 |2017-02-14 23:52:31.5460071 |...url... |Failed to download blob: The client couldn't finish the operation within specified timeout |Permanent |21fa0dd6-cd7d-4493-b6f7-78916ce0d617 |DataIngestPull | 0
