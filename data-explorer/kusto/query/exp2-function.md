---
title: exp2() - Azure Kusto | Microsoft Docs
description: This article describes exp2() in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# exp2()

The base-2 exponential function of x, which is 2 raised to the power x: 2^x.  

**Syntax**

`exp2(`*x*`)`

**Arguments**

* *x*: A real number, value of the exponent.

**Returns**

* Exponential value of x.
* For natural (base-2) logarithms, see [log2()](log2-function.md).
* For exponential functions of base-e and base-10 logarithms, see [exp()](exp-function.md), [exp10()](exp10-function.md)