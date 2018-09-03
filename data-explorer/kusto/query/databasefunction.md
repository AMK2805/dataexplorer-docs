﻿---
title: database() (scope function) - Azure Kusto | Microsoft Docs
description: This article describes database() (scope function) in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# database() (scope function)

Changes the reference of the query to a specific database within the cluster scope. 

    database('Sample').StormEvents
    cluster('help').database('Sample').StormEvents

**Syntax**

`database(`*stringConstant*`)`

**Arguments**

* *stringConstant*: Name of the database that is referenced. Database identified can be either `DatabaseName` or `PrettyName`. Argument has to be _constant_ prior of query execution, i.e. cannot come from sub-query evaluation.

**Notes**

* For accessing remote cluster and remote database, see [cluster()](clusterfunction.md) scope function.
* More information about cross-cluster and cross-database queries available [here](syntax.md)

## Examples

### Use database() to access table of other database. 

```kusto
database('Samples').StormEvents | count
```

|Count|
|---|
|59066|

### Use database() inside let statements 

The same query as above can be rewritten to use inline function (let statement) that 
receives a parameter `dbName` - which is passed into the database() function.

```kusto
let foo = (dbName:string)
{
    database(dbName).StormEvents | count
};
foo('help')
```

|Count|
|---|
|59066|

### Use database() inside Functions 

The same query as above can be rewritten to be used in a function that 
receives a parameter `dbName` - which is passed into the database() function.

```kusto
.create function foo(dbName:string)
{
    database(dbName).StormEvents | count
};
```

**Note:** such functions can be used only locally and not in the cross-cluster query.