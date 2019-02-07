---
title: Commands and queries - Azure Data Explorer | Microsoft Docs
description: This article describes Commands and queries in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
---
# Commands and queries

## .show commands-and-queries 

`.show` `commands-and-queries` returns a table with admin commands and queries completed in the last month.

The information presented in the output of the command is similar to that presented by [.show commands](commands.md) 
and [.show queries](queries.md), however it essentially allows to union both result sets in a simple manner.

**Syntax**

`.show` `commands-and-queries`
 
**Output**
 
The output schema is as follows:

| ColumnName               | ColumnType |
|--------------------------|------------|
| ClientActivityId         | string     |
| CommandType              | string     |
| Text                     | string     |
| Database                 | string     |
| StartedOn                | datetime   |
| LastUpdatedOn            | datetime   |
| Duration                 | timespan   |
| State                    | string     |
| FailureReason            | string     |
| RootActivityId           | guid       |
| User                     | string     |
| Application              | string     |
| Principal                | string     |
| ClientRequestProperties  | dynamic    |
| TotalCpu                 | timespan   |
| MemoryPeak               | long       |
| CacheStatistics          | dynamic    |
| ScannedExtentsStatistics | dynamic    |
| ResultSetStatistics      | dynamic    |

Note that for queries, the value of `CommandType` is `Query`.