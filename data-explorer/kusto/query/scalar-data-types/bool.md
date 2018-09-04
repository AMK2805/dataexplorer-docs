---
title: The bool data type - Azure Kusto | Microsoft Docs
description: This article describes The bool data type in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# The bool data type

The `bool` (`boolean`) data type can have one of two states: `true` or `false`
(internally encoded as `1` and `0`, respectively), as well as the null value.

## bool literals

The `bool` data type has the following literals:
* `true` and `bool(true)`: Representing trueness
* `false` and `bool(false)`:  Representing falsehood
* `bool(null)`: See [null values](null-values.md)

## bool operators

The `bool` data type supports the following operators:
equality (`==`), inequality (`!=`), logical-and (`and`), and logical-or (`or`).