---
title: Kusto RestrictedViewAccess policy management - Azure Data Explorer
description: This article describes RestrictedViewAccess policy in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
---
# RestrictedViewAccess policy

The *RestrictedViewAccess* policy is documented [here](../management/restrictedviewaccesspolicy.md).

Control commands for enabling or disabling the policy on table(s) in the database are the following:

To enable/disable the policy:
```kusto
.alter table TableName policy restricted_view_access true|false
```

To enable/disable the policy of multiple tables:
```kusto
.alter tables (TableName1, ..., TableNameN) policy restricted_view_access true|false
```

To view the policy:
```kusto
.show table TableName policy restricted_view_access  

.show table * policy restricted_view_access  
```

To delete the policy (equivalent to disabling):
```kusto
.delete table TableName policy restricted_view_access  
```