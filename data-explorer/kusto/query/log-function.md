---
title: log() (Azure Kusto)
description: This article describes log() in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# log()

Returns the natural logarithm function.  

**Syntax**

`log(`*x*`)`

**Arguments**

* *x*: A real number > 0.

**Returns**

* The natural logarithm is the base-e logarithm: the inverse of the natural exponential function (exp).
* For common (base-10) logarithms, see [log10()](log10-function.md).
* For base-2 logarithms, see [log2()](log2-function.md)
* `null` if the argument is negative or null or cannot be converted to a `real` value. 