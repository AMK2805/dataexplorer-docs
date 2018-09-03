﻿---
title: tolong() - Azure Kusto | Microsoft Docs
description: This article describes tolong() in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# tolong()

Converts input to long (signed 64-bit) number representation.

    tolong("123") == 123

**Syntax**

`tolong(`*Expr*`)`

**Arguments**

* *Expr*: Expression that will be converted to long. 

**Returns**

If conversion is successful, result will be a long number.
If conversion is not successful, result will be `null`.
 
*Note*: Prefer using [long()](./scalar-data-types/long.md) when possible.