﻿---
title: tdigest_merge() (aggregation function) - Azure Kusto | Microsoft Docs
description: This article describes tdigest_merge() (aggregation function) in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# tdigest_merge() (aggregation function)

Merges tdigest results across the group. 

* Can be used only in context of aggregation inside [summarize](summarizeoperator.md).

Read more about the underlying algorithm (T-Digest) and the estimated error [here](percentiles-aggfunction.md#estimation-error-in-percentiles).

**Syntax**

summarize `tdigest_merge(`*Expr*`)`.

**Arguments**

* *Expr*: Expression that will be used for aggregation calculation. 

**Returns**

The merged tdigest values of *Expr* across the group.
 

**Tips**

1) You may use the function [`percentile_tdigest()`] (percentile-tdigestfunction.md).

2) All tdigests that are included in the same group must be of the same type.