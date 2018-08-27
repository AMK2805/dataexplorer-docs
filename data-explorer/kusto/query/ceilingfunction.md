---
title: ceiling() (Azure Kusto)
description: This article describes ceiling() in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# ceiling()

Calculates the smallest integer greater than, or equal to, the specified numeric expression.

**Syntax**

`ceiling(`*x*`)`

**Arguments**

* *x*: A real number.

**Returns**

* The smallest integer greater than, or equal to, the specified numeric expression. 

**Examples**

```kusto
print c1 = ceiling(-1.1), c2 = ceiling(0), c3 = ceiling(0.9)

```

|c1|c2|c3|
|---|---|---|
|-1|0|1|


