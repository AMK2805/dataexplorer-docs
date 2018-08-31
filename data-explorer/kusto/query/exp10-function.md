---
title: exp10() - Azure Kusto | Microsoft Docs
description: This article describes exp10() in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# exp10()

The base-10 exponential function of x, which is 10 raised to the power x: 10^x.  

**Syntax**

`exp10(`*x*`)`

**Arguments**

* *x*: A real number, value of the exponent.

**Returns**

* Exponential value of x.
* For natural (base-10) logarithms, see [log10()](log10-function.md).
* For exponential functions of base-e and base-2 logarithms, see [exp()](exp-function.md), [exp2()](exp2-function.md)