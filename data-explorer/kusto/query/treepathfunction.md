---
title: treepath() (Azure Kusto)
description: This article describes treepath() in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# treepath()

Enumerates all the path expressions that identify leaves in a dynamic object.

`treepath(`*dynamic object*`)`

**Returns**

An array of path expressions.

**Examples**

|Expression|Evaluates to|
|---|---|
|`treepath(parsejson('{"a":"b", "c":123}'))` | `["['a']","['c']"]`|
|`treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))`|`["['prop1']","['prop1'][0]","['prop2']"]`|
|`treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))`|`["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]`|