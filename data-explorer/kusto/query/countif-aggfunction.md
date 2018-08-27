---
title: countif() (aggregation function) (Azure Kusto)
description: This article describes countif() (aggregation function) in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# countif() (aggregation function)

Returns a count of rows for which *Predicate* evaluates to `true`.

* Can be used only in context of aggregation inside [summarize](summarizeoperator.md)

See also - [count()](count-aggfunction.md) function, which counts rows without predicate expression.

**Syntax**

summarize `countif(`*Predicate*`)`

**Arguments**

* *Predicate*: Expression that will be used for aggregation calculation. 

**Returns**

Returns a count of rows for which *Predicate* evaluates to `true`.

**Tip**

Use `summarize countif(filter)` instead of `where filter | summarize count()`