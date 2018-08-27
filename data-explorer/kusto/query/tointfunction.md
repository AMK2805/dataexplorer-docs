---
title: toint() (Azure Kusto)
description: This article describes toint() in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# toint()

Converts input to integener (signed 32-bit) number representation.

    toint("123") == 123

**Syntax**

`toint(`*Expr*`)`

**Arguments**

* *Expr*: Expression that will be converted to integer. 

**Returns**

If conversion is successful, result will be a integer number.
If conversion is not successful, result will be `null`.
 
*Note*: Prefer using [int()](./scalar-data-types/int.md) when possible.