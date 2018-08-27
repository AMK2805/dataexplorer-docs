---
title: todynamic(), toobject() (Azure Kusto)
description: This article describes todynamic(), toobject() in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# todynamic(), toobject()

Interprets a `string` as a [JSON value](http://json.org/) and returns the value as [`dynamic`](./scalar-data-types/dynamic.md). 

It is superior to using [extractjson() function](./extractjsonfunction.md)
when you need to extract more than one element of a JSON compound object.

Aliases to [parsejson()](./parsejsonfunction.md) function.

**Syntax**

`todynamic(`*json*`)`
`toobject(`*json*`)`

**Arguments**

* *json*: A JSON document.

**Returns**

An object of type `dynamic` specified by *json*.

*Note*: Prefer using [dynamic()](./scalar-data-types/dynamic.md) when possible.