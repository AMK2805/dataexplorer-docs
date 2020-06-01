---
title: External tables - Azure Data Explorer | Microsoft Docs
description: This article describes External tables in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
---
# External tables

An **external table** is a Kusto schema entity that references data stored outside Kusto database.

Similar to [tables](tables.md), an external table has a well-defined schema (an ordered list of column name and data type pairs). Unlike tables, data is stored and managed outside Kusto Cluster. Most commonly the data is stored in some standard format such as CSV, Parquet, Avro, and is not ingested by Kusto.

An **external table** is created once (see [External table general control commands](../../management/externaltables.md), [Create and alter external SQL tables](../../management/external-sql-tables.md), and [Create and alter tables in Azure Storage or Azure Data Lake](../../management/external-tables-azurestorage-azuredatalake.md))
and can be referenced by its name using [external_table()](../../query/externaltablefunction.md) function. 

**Notes**

* External table names are case-sensitive.
* External table names can’t overlap with Kusto table names.
* External table names follow the rules for [entity names](./entity-names.md).
* Maximum limit of external tables per database is 1,000.
* Kusto supports [export](../../management/data-export/export-data-to-an-external-table.md) and [continuous export](../../management/data-export/continuous-data-export.md)  to an external table, as well as [querying external tables](../../../data-lake-query-data.md).
    * [Data purge](../../concepts/data-purge.md) is not applied on external tables. Records are never deleted from external tables.
