---
title: isnotempty() - Azure Data Explorer | Microsoft Docs
description: This article describes isnotempty() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
---
# isnotempty()

Returns `true` if the argument is not an empty string nor it is a null.

```kusto
isnotempty("") == false
```

**Syntax**

`isnotempty(`[*value*]`)`

`notempty(`[*value*]`)` -- alias of `isnotempty`