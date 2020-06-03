---
title: Extents (data shards) - Azure Data Explorer | Microsoft Docs
description: This article describes Extents (data shards) in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/13/2020
---
# Extents (data shards)

## Overview

Kusto is built to support tables with a huge number of records (rows)
and large amounts of data. To handle such large tables, each table's data 
is divided into smaller "tablets" called **data shards**
or **extents** (the two terms are synonyms). The union of
all the table's extents holds the table's data. Individual extents
are kept smaller than a single node's capacity, and the extents
are spread over the cluster's nodes, achieving scale-out.

An extent is a like a type of mini-table. It contains data and metadata
and information such as its creation time and optional tags
that are associated with its data. Additionally, the extent
usually holds information that lets Kusto query the data efficiently.
For example, an index for each column of data in the extent, and an encoding
dictionary, if column data is encoded. As a result, the table's data is the union
of all the data in the table's extents.

Extents are immutable and can never be modified. It may only be queried, 
reassigned to a different node, or dropped out of the table. 
Data modification happens by creating one or more new extents 
and transactionally swapping old extents with new ones.

Extents hold a collection of records that are physically arranged in columns.
This technique is called **columnar store**. It enables efficient encoding and compression of the data, because different values from the same
column often "resemble" each other. It also makes querying large
spans of data more efficient, because only the columns used by the query
need to be loaded. Internally, each column of data in the extent is 
subdivided into segments, and the segments into blocks. This division is not observable to queries, and lets Kusto optimize column compression and indexing.

To maintain query efficiency, smaller extents are merged into larger extents.
The merge is done automatically, as a background process, according
to the configured [merge policy](mergepolicy.md) and 
[sharding policy](shardingpolicy.md).
Merging extents reduces the management overhead of having a large number of extents to track. More importantly, it allows Kusto to optimize its indexes and improve compression.

Extent merging stops once an extent reaches certain limits, such as size,
since beyond a certain point, merging reduces rather than increases efficiency.

When a [Data partitioning policy](partitioningpolicy.md) is defined
on a table, extents go through another background process after they are
created (post-ingestion). This process reingests the data from the source extents
and creates *homogeneous* extents, in which the values of the column that is the
table's *partition key* all belong to the same partition. If the policy includes a
*hash partition key*, all homogeneous extents that belong to
the same partition will be assigned to the same data node in the cluster.

> [!NOTE]
> Extent-level operations, such as merging or altering extent tags, do not modify existing extents.
> Instead, new extents are created in these operations, based on existing source extents. The new extents replace their forefathers in a single transaction.

The common extent lifecycle is:

1. The extent is created by an **ingestion** operation.
1. The extent is merged with other extents. When the extents being merged
   are small, Kusto actually carries out an ingestion process on them, called **rebuild**. Once extents reach a certain size, merging is done only for indexes. The extents' data artifacts in storage aren't modified.
1. The merged extent (possibly one that tracks its lineage to other
   merged extents, and so on) is eventually dropped becasue of a retention policy. 
   When extents are dropped based on time (older x hours / days), then the creation date of the newest extent inside the merged one is used in the calculation.

## Extent Creation time

One of the more important pieces of information for each extent is its
creation time. This time is used for:

1. **Retention** - Extents that were created earlier will be dropped earlier.
1. **Caching* - Extents that were created recently will be kept in [hot cache](cachepolicy.md))
1. **Sampling** - Recent extents are favored, when using query operations such as `take`

In fact, Kusto tracks two `datetime` values per extent: `MinCreatedOn` and `MaxCreatedOn`.
Initially, the two values are the same, but when the extent is merged with other
extents, the new values are according to the minimum and maximum values of the merged extents.

Normally, an extent's creation time is set according to the time in which the data in the extent is ingested. Clients can optionally overwrite the extent's creation time, by providing an alternative creation time in the [ingestion properties](../../ingestion-properties.md).
Overwriting is useful, for example for retention purposes, if the client wants to reingest data and doesn't want it to appear as if it had arrived late.

## Extent Tagging

Kusto supports attaching multiple optional *extent tags* to the extent, as part of its metadata. An extent tag (or simply *tag*), is a string that is associated with the extent. You can use the [.show extents](extents-commands.md#show-extents)
commands to see the tags associated with an extent, and the
[extent-tags()](../query/extenttagsfunction.md) 
function to see the tags associated with records in an extent.
Extent tags can be used to efficiently describe properties that are common to 
all of the data in the extent.
For example, you could add an extent tag during ingestion, that indicates
the source of the data being ingested, and use that tag later. Since the extents
describe data, when two or more merge, their associated tags also merge. The resulting extent's tags will be the union of all the tags of those merged extents.

Kusto assigns a special meaning to all extent tags whose value has the
format *prefix* *suffix*, where *prefix* is one of:

* `drop-by:`
* `ingest-by:`

### 'drop-by:' extent tags

Tags that start with a **`drop-by:`** prefix can be used to control which other
extents to merge with. Extents that have a given `drop-by:` tag can be merged
together, but they won't be merged with other extents. 
You can then issue a command to drop extents according to their `drop-by:` tag.

For example:

```kusto
.ingest ... with @'{"tags":"[\"drop-by:2016-02-17\"]"}'

.drop extents <| .show table MyTable extents where tags has "drop-by:2016-02-17" 
```

#### Performance notes

* Do not overuse `drop-by` tags. Dropping data in the manner mentioned above is meant for rarely occurring events. It isn't for replacing record-level data, and it relies on the fact that the data tagged in this manner is bulky. Attempting to give a different tag for each record, or small number of records, might result in a severe impact on performance.
* If such tags aren't required a period of time after data is ingested,
we recommend that you [drop the tags](extents-commands.md#drop-extent-tags).

### 'ingest-by:' extent tags

Tags that start with an **`ingest-by:`** prefix can be used to ensure that data
is only ingested once. You can issue an **`ingestIfNotExists`** property command that prevents the data from being ingested if there already exists an extent with this specific `ingest-by:` tag.
The values for both `tags` and `ingestIfNotExists` are arrays of strings,
serialized as JSON.

The following example ingests data only once. The 2nd and 3rd commands do nothing:

```kusto
.ingest ... with (tags = '["ingest-by:2016-02-17"]')

.ingest ... with (ingestIfNotExists = '["2016-02-17"]')

.ingest ... with (ingestIfNotExists = '["2016-02-17"]', tags = '["ingest-by:2016-02-17"]')
```

> [!NOTE]
> Generally, an ingest command is likely to include
> both an `ingest-by:` tag and an `ingestIfNotExists` property,
> set to the same value, as shown in the 3rd command above.

#### Performance notes

* Overusing `ingest-by` tags is not recommended.
If the pipeline feeding Kusto is known to have data duplications, we recommended
that you solve these duplications as much as possible, before ingesting the data into Kusto. Also, use `ingest-by` tags in Kusto only when the part that ingests to Kusto
might introduce duplicates by itself. There's a retry mechanism that can overlap 
with already-in-progress ingestion calls. Attempting to set a unique `ingest-by` tag
for each ingestion call might result in a severe impact on performance.
* If such tags aren't required for some period of time after the data is ingested,
we recommend that you [drop the tags](extents-commands.md#drop-extent-tags).
