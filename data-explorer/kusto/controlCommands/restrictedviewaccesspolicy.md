---
title: RestrictedViewAccess policy - Azure Kusto | Microsoft Docs
description: This article describes RestrictedViewAccess policy in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# RestrictedViewAccess policy

The *RestrictedViewAccess* policy is documented [here](https://kusdoc2.azurewebsites.net/docs/concepts/restrictedviewaccesspolicy.html).

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