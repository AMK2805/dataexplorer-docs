---
title: .drop function - Azure Data Explorer | Microsoft Docs
description: This article describes .drop function in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
---
# .drop function

Drops a function from the database.
For dropping multiple functions from the database, see [.drop functions](#drop-functions).

**Syntax**

`.drop` `function` *FunctionName* [`ifexists`]

* `ifexists`: If specified, modifies the behavior of the command to
  not fail for a non-existent function.

> [!NOTE]
> * Requires [database admin permission](../management/access-control/role-based-authorization.md).
> * The [database user](../management/access-control/role-based-authorization.md) who originally created the function is allowed to modify the function.  
    
|Output parameter |Type |Description
|---|---|--- 
|Name  |String |The name of the function that was removed
 
**Example** 

```
.drop function MyFunction1
```

## .drop functions

Drops multiple functions from the database.

**Syntax**

`.drop` `functions` (*FunctionName1*, *FunctionName2*,..) [ifexists]

**Returns**

This command returns a list of the remaining functions in the database.

|Output parameter |Type |Description
|---|---|--- 
|Name  |String |The name of the function. 
|Parameters  |String |The parameters required by the function.
|Body  |String |(Zero or more) `let` statements followed by a valid CSL expression that is evaluated upon function invocation.
|Folder|String|A folder used for UI functions categorization. This parameter doesn't change the way the function is invoked.
|DocString|String|A description of the function for UI purposes.

Requires [function admin permission](../management/access-control/role-based-authorization.md).

**Example** 
 
```
.drop functions (Function1, Function2, Function3) ifexists
```