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

An **external table** is a Kusto schema entity that references data stored outside the Azure Data Explorer database.

Similar to [tables](tables.md), an external table has a well-defined schema (an ordered list of column name and data type pairs). Unlike tables where data is ingested into ADX cluster, external tables operate on data stored and managed outside ADX cluster. 

Supported external data stores are:

* Files stored in Azure Blob Storage or in Azure Data Lake. Most commonly the data is stored in some standard format such as CSV, JSON, Parquet, AVRO, etc. For the list of supported formats, please refer to [supported formats](../../../ingestion-supported-formats.md).
* SQL Server table.

See the following ways of creating external tables:

* [Create and alter Azure Storage external tables](../../management/external-tables-azurestorage-azuredatalake.md)
* [Create and alter SQL Server external tables](../../management/external-sql-tables.md)
* [Create external table using Web UI Wizard](../../../external-table.md)

An **external table** can be referenced by its name using the [external_table()](../../query/externaltablefunction.md) function.

Use the following commands to manage external tables:
* [`.drop external table`](../../management/drop-external-table.md) 
* [`.show external tables`](../../management/show-external-tables.md) 
* [`.show external table schema`](../../management/show-external-table-schema.md) 

**Notes**

* External table names:
   * Case-sensitive.
   * Can’t overlap with Kusto table names.
   * Follow the rules for [entity names](./entity-names.md).
* Maximum limit of external tables per database is 1,000.
* Kusto supports [export](../../management/data-export/export-data-to-an-external-table.md) and [continuous export](../../management/data-export/continuous-data-export.md) to an external table, and [querying external tables](../../../data-lake-query-data.md).
* [Data purge](../../concepts/data-purge.md) isn't applied on external tables. Records are never deleted from external tables.