---
title: Streaming ingestion policy - Azure Data Explorer | Microsoft Docs
description: This article describes Streaming ingestion policy in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2020
---
# Streaming ingestion policy

## Streaming ingestion target scenario

Streaming ingestion is targeted for scenarios that require low latency with an ingestion time of less than 10 seconds for varied volume data. It's used to optimize operational processing of many tables, in one or more databases, where the stream of data into each table is relatively small (few records per second) but overall data ingestion volume is high (thousands of records per second).

Use the classic (bulk) ingestion instead of streaming ingestion when the amount of data grows to more than 1 MB per second per table. 

* To learn how to implement this feature, see [streaming ingestion](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming).
* For information about streaming ingestion control commands, see [Control commands are used to manage the streaming ingestion policy](../management/streamingingestion-policy.md)

## Streaming ingestion policy definition

Streaming ingestion policy can be defined on a table or a database. Defining this policy at the database level applies the same settings to all existing and future tables in the database. If streaming ingestion policy is set at both table and database levels, the table level setting takes precedence.

> [!NOTE]
> If a table doesn't get streaming ingestion directly, but only via an update policy, no streaming ingestion policy has to be defined on this table. 

The streaming ingestion policy sets the maximum number of row stores on which the table's streaming data will be distributed. The distribution is needed for both availability and data rate support.

## Setting the number of row stores

The number of row stores set in the streaming ingestion policy needs to be defined. This number should be based on the streaming data rate per-table (rough estimation is sufficient).
The minimum recommended number of row stores for any table is four. The maximum supported number of row stores is 64.
The higher the streaming data rate for the table, the higher the necessary number of row stores needed in the associated streaming ingestion policy.
Use the following table for the recommended settings (if in doubt use higher number):

|Estimated Peak Hourly Streaming Data Rate (per table)|Number of Row Stores|
|----------|------|
|< 1 Gb/hr |4|
|1 - 2 GB/hr |4-8|
|2 - 3 GB/hr |8-12|
|3 - 4 GB/hr |12-16|
| > 4 GB/hr |

 For further advice about this, open a [support ticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

For optimal query latency, the number of row stores defined per table shouldn't significantly exceed the recommendation above.

> [!NOTE]
> When setting streaming ingestion policy for the database, assign the number of row stores that is needed for the table with the highest data rate. 