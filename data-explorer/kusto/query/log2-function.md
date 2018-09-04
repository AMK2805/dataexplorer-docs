---
title: log2() - Azure Kusto | Microsoft Docs
description: This article describes log2() in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# log2()

Returns the base-2 logarithm function.  

**Syntax**

`log2(`*x*`)`

**Arguments**

* *x*: A real number > 0.

**Returns**

* The logarithm is the base-2 logarithm: the inverse of the exponential function (exp) with base 2.
* For natural (base-e) logarithms, see [log()](log-function.md).
* For common (base-10) logarithms, see [log10()](log10-function.md)
* `null` if the argument is negative or null or cannot be converted to a `real` value. 