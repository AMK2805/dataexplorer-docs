﻿---
title: tostring() - Azure Kusto | Microsoft Docs
description: This article describes tostring() in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# tostring()

Converts input to a string representation.

    tostring(123) == "123"

**Syntax**

`tostring(`*Expr*`)`

**Arguments**

* *Expr*: Expression that will be converted to string. 

**Returns**

If *Expr* value is non-null result will be a string representation of *Expr*.
If *Expr* value is null, result will be empty string.
 