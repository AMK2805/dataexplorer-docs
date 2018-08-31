---
title: bin() - Azure Kusto | Microsoft Docs
description: This article describes bin() in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# bin()

Rounds values down to an integer multiple of a given bin size. 

Used a lot in the [`summarize by ...`](./summarizeoperator.md) query. 
If you have a scattered set of values, they will be grouped into a smaller set of specific values.

Alias to `floor()` function.

**Syntax**

`bin(`*value*`,`*roundTo*`)`

**Arguments**

* *value*: A number, date, or timespan. 
* *roundTo*: The "bin size". A number, date or timespan that divides *value*. 

**Returns**

The nearest multiple of *roundTo* below *value*.  
 
 `(toint((value/roundTo))) * roundTo`

**Examples**

Expression | Result
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1970-05-11 13:45:07), 1d)`|  `datetime(1970-05-11)`


The following expression calculates a histogram of durations,
with a bucket size of 1 second:

```kusto
T | summarize Hits=count() by bin(Duration, 1s)
```