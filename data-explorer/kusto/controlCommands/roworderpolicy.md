---
title: Row Order policy control commands - Azure Kusto | Microsoft Docs
description: This article describes Row Order policy control commands in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# Row Order policy control commands

```kusto
.show table <table_name> policy roworder

.show table * policy roworder

.alter table <table_name> policy roworder (<row_order_policy>)

.alter tables (<table_name> [, ...]) policy roworder (<row_order_policy>)

.alter-merge table <table_name> policy roworder (<row_order_policy>)

.delete table <table_name> policy roworder

```

TODO: Add syntax, examples, etc.

**Examples**

The following example sets the row order policy to be on the `TenantId` column (ascending) as
a primary key, and on the `Timestamp` column (ascending) as the secondary key; it then queries the policy:

```kusto
.alter table events policy roworder (TenantId asc, Timestamp desc)

.alter tables (events1, events2, events3) policy roworder (TenantId asc, Timestamp desc)

.show table events policy roworder 
```

|TableName|RowOrderPolicy| 
|---|---|
|events|(TenantId asc, Timestamp desc)| 