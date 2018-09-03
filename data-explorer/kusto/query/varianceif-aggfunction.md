﻿---
title: varianceif() (aggregation function) - Azure Kusto | Microsoft Docs
description: This article describes varianceif() (aggregation function) in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# varianceif() (aggregation function)

Calculates the [variance](variance-aggfunction.md) of *Expr* across the group for which *Predicate* evaluates to `true`.

* Can be used only in context of aggregation inside [summarize](summarizeoperator.md)

**Syntax**

summarize `varianceif(`*Expr*`, `*Predicate*`)`

**Arguments**

* *Expr*: Expression that will be used for aggregation calculation. 
* *Predicate*:  predicate that if true, the *Expr* calculated value will be added to the variance.

**Returns**

The variance value of *Expr* across the group where *Predicate* evaluates to `true`.
 
**Examples**

```kusto
range x from 1 to 100 step 1
| summarize varianceif(x, x%2 == 0)

```

|varianceif_x|
|---|
|850|