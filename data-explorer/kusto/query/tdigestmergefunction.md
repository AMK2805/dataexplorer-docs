---
title: merge_tdigests() - Azure Kusto | Microsoft Docs
description: This article describes merge_tdigests() in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# merge_tdigests()

Merges tdigest results (scalar version of the aggregate version [`merge_tdigests()`](merge-tdigests-aggfunction.md)).

Read more about the underlying algorithm (T-Digest) and the estimated error [here](percentiles-aggfunction.md#estimation-error-in-percentiles).

**Syntax**

`merge_tdigests(` *Expr1*`,` *Expr2*`, ...)`

`tdigest_merge(` *Expr1*`,` *Expr2*`, ...)` - An alias.

**Arguments**

* Columns which has the tdigests to be merged.

**Returns**

The result for merging the columns `*Exrp1*`, `*Expr2*`, ... `*ExprN*` to one tdigest.

**Examples**

```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize tdigestX = tdigest(x), tdigestY = tdigest(y)
| project merged = merge_tdigests(tdigestX, tdigestY)
| project percentile_tdigest(merged, 100, typeof(long))
```

|percentile_tdigest_merged|
|---|
|20|