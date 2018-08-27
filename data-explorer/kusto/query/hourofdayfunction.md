---
title: hourofday() (Azure Kusto)
description: This article describes hourofday() in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# hourofday()

Returns the integer number representing the hour number of the given date

    hourofday(datetime(2015-12-14 18:54)) == 18

**Syntax**

`hourofday(`*a-date*`)`

**Arguments**

* `a-date`: A `datetime`.

**Returns**

`hour number` of the day (0-23).