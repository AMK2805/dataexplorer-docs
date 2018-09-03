﻿---
title: startofweek() - Azure Kusto | Microsoft Docs
description: This article describes startofweek() in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# startofweek()

Returns the start of the week containing the date, shifted by an offset, if provided.

Start of the week is considered to be a Sunday.

**Syntax**

`startofweek(`*date* [`,`*offset*]`)`

**Arguments**

* `date`: The input date.
* `offset`: An optional number of offset weeks from the input date (integer, default - 0).

**Returns**

A datetime representing the start of the week for the given *date* value, with the offset, if specified.

**Example**

```kusto
  range offset from -1 to 1 step 1
 | project weekStart = startofweek(datetime(2017-01-01 10:10:17), offset) 
```

|weekStart|
|---|
|2016-12-25 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-08 00:00:00.0000000|