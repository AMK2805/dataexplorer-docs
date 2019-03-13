---
title: partition operator - Azure Data Explorer | Microsoft Docs
description: This article describes partition operator in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: reference
ms.date: 03/05/2019
---
# partition operator

The partition operator partitions its input table into multiple sub-tables
according to the values of the specified column, executes a sub-query over each
sub-table, and produces a single output table that is the union of the results
of all sub-queries. 

```kusto
T | partition by Col1 ( top 10 by MaxValue )
```

**Syntax**

*T* `|` `partition` [*PartitionParameters*] `by` *Column* `(` *Subquery* `)`

**Arguments**

* *T*: The tabular source whose data is to be processed by the operator.

* *Column*: The name of a column in *T* whose values determine how the input table
  is to be partitioned. See **Notes** below.

* *Subquery*: A tabular expression that gets evaluated for each partition of the
  input, and whose output is returned by the operator.

* *PartitionParameters*: Zero or more (space-separated) parameters in the form of:
  *Name* `=` *Value* that control the behavior
  of the operator. The following parameters are supported:

  |Name               |Values         |Description|
  |-------------------|---------------|-----------|
  |`hint.materialized`|`true`,`false` |If set to `true` will materialize the source of the `partition` operator (default: `false`)|

**Returns**

The operator returns a union of the results of applying the subquery to each
partition of the input data.

**Notes**

* The partition operator is currently limited by the number of partitions.
  Up to 64 distinct partitions may be created.
  The operator will yield an error if the partition column (*Column*) has more
  than 64 distinct values.

* The subquery references the input partition implicitly (there's no "name" for
  the partition in the subquery). To reference the input partition multiple times
  within the subquery, use the [as operator](asoperator.md), as in
  **Example: partition-reference** below.

**Example: top-nested case**

At some cases - it is more performant and easier to write query using `partition` operator rather using [`top-nested` operator](topnestedoperator.md)
The next example runs a sub-query calculating `summarize` and `top` for-each of States starting with `W`: (WYOMING, WASHINGTON, WEST VIRGINIA, WISCONSIN)

```kusto
StormEvents
| where State startswith 'W'
| partition by State 
(
    summarize Events=count(), Injuries=sum(InjuriesDirect) by EventType, State
    | top 3 by Events 
) 

```
|EventType|State|Events|Injuries|
|---|---|---|---|
|Hail|WYOMING|108|0|
|High Wind|WYOMING|81|5|
|Winter Storm|WYOMING|72|0|
|Heavy Snow|WASHINGTON|82|0|
|High Wind|WASHINGTON|58|13|
|Wildfire|WASHINGTON|29|0|
|Thunderstorm Wind|WEST VIRGINIA|180|1|
|Hail|WEST VIRGINIA|103|0|
|Winter Weather|WEST VIRGINIA|88|0|
|Thunderstorm Wind|WISCONSIN|416|1|
|Winter Storm|WISCONSIN|310|0|
|Hail|WISCONSIN|303|1|

**Example: query non-overlapping data partitions**

Sometimes it is useful (perf-wise) to run a complex subquery over non-overlapping
data partitions in a map/reduce style. The example below shows how to create a
manual distribution of aggregation over 10 partitions.

```kusto
StormEvents
| extend p = hash(EventId, 10)
| partition by p
(
    summarize Count=count() by Source 
)
| summarize Count=sum(Count) by Source
| top 5 by Count
```

|Source|Count|
|---|---|
|Trained Spotter|12770|
|Law Enforcement|8570|
|Public|6157|
|Emergency Manager|4900|
|COOP Observer|3039|

**Example: partition-reference**

The following example shows how one can use the [as operator](asoperator.md) to
give a "name" to each data partition and then reuse that name within the subquery:

```kusto
T
| partition by Dim
(
    as Partition
    | extend MetricPct = Metric * 100.0 / toscalar(Partition | summarize sum(Metric))
)
```

**Example: complex subquery hidden by a function call**

The same technique can be applied with much more complex subqueries. To simplify
the syntax, one can wrap the subquery in a function call:

```kusto
let partition_function = (T:(Source:string)) 
{
    T
    | summarize Count=count() by Source
};
StormEvents
| extend p = hash(EventId, 10)
| partition by p
(
    invoke partition_function()
)
| summarize Count=sum(Count) by Source
| top 5 by Count
```

|Source|Count|
|---|---|
|Trained Spotter|12770|
|Law Enforcement|8570|
|Public|6157|
|Emergency Manager|4900|
|COOP Observer|3039|