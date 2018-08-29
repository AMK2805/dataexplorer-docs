---
title: table() (scope function) (Azure Kusto)
description: This article describes table() (scope function) in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# table() (scope function)

References specific table using an query-time evaluated string-expression. 

    table('StormEvent')

**Syntax**

`table(`*stringConstant* [`,` *query_data_scope* ]`)`

**Arguments**

* *stringConstant*: Name of the table that is referenced. Argument has to be _constant_ prior of query execution, i.e. cannot come from sub-query evaluation.

* *query_data_scope*: An optional parameter that controls the tables's datascope -- whether the query applies to all data or just part of it. Possible values:
    - `"hotcache"`: Table scope is data that is covered by [cache policy](https://kusdoc2.azurewebsites.net/docs/concepts/cachepolicy.html)
    - `"all"`: Table scope is all data, hot or cold.
    - `"default"`: Table scope is default (cluster default policy)

## Examples

### Use table() to access table of the current database. 

```kusto
table('StormEvent') | count
```

|Count|
|---|
|59066|

### Use table() inside let statements 

The same query as above can be rewritten to use inline function (let statement) that 
receives a parameter `tableName` - which is passed into the table() function.

```kusto
let foo = (tableName:string)
{
    table(tableName) | count
};
foo('help')
```

|Count|
|---|
|59066|

### Use table() inside Functions 

The same query as above can be rewritten to be used in a function that 
receives a parameter `tableName` - which is passed into the table() function.

```kusto
.create function foo(tableName:string)
{
    table(tableName) | count
};
```

**Note:** such functions can be used only locally and not in the cross-cluster query.

### Use table() with non-constant parameter

A parameter, which is not scalar constant string can't be passed as parameter to `table()` function.

Below, given an example of workaround for such case.

```kusto
let T1 = range x from 1 to 1 step 1;
let T2 = range x from 2 to 2 step 1;
let _choose = (_selector:string)
{
    union 
    (T1 | where _selector == 'T1'),
    (T2 | where _selector == 'T2')
};
_choose('T2')

```

|x|
|---|
|2|