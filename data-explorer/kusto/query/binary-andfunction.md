﻿---
title: binary_and() - Azure Kusto | Microsoft Docs
description: This article describes binary_and() in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# binary_and()

Returns a result of the bitwise `and` operation between two values

    binary_and(x,y)	

**Syntax**

`binary_and(`*num1*`,` *num2* `)`

**Arguments**

* *num1*, *num2*: long numbers.

**Returns**

Returns logical AND operation on a pair of numbers: num1 & num2.