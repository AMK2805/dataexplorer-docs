---
title: argmin() (aggregation function) (Azure Kusto)
description: This article describes argmin() (aggregation function) in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# argmin() (aggregation function)

Finds a row in the group that minimizes *ExprToMinimize*, and returns the value of *ExprToReturn* (or `*` to return the entire row).

* Can be used only in context of aggregation inside [summarize](summarizeoperator.md)

**Syntax**

`summarize` [`(`*NameExprToMinimize* `,` *NameExprToReturn* [`,` ...] `)=`] `argmin` `(`*ExprToMinimize*, `*` | *ExprToReturn*  [`,` ...]`)`

**Arguments**

* *ExprToMinimize*: Expression that will be used for aggregation calculation. 
* *ExprToReturn*: Expression that will be used for returning the value when *ExprToMinimize* is
  minimum. Expression to return may be a wildcard (*) to return all columns of the input table.
* *NameExprToMinimize*: An optional name for the result column representing *ExprToMinimize*.
* *NameExprToReturn*: Additional optional names for the result columns representing *ExprToReturn*.

**Returns**

Finds a row in the group that minimizes *ExprToMinimize*, and returns the value of *ExprToReturn* (or `*` to return the entire row).

**Examples**

Show cheapest supplier of each product:

    Supplies | summarize argmin(Price, Supplier) by Product

Show all the details, not just the supplier name:

    Supplies | summarize argmin(Price, *) by Product

Find the southernmost city in each continent, with its country:

    PageViewLog 
    | summarize (latitude, City, country)=argmin(latitude, City, country) 
      by continent

![](./images/aggregations/argmin.png)
 
 **Notes**

When using a wildcard (`*`) as *ExprToReturn*, it is **strongly recommended** that
the input to the `summarize` operator will be restricted to include only the columns
that are used following that operator, as the optimization rule to automatically 
project-away such columns is currently not implemented. In other words, make sure
to introduce a projection similar to the marked line below:

```kusto
datatable(a:string, b:string, c:string, d:string) [...]
| project a, b, c // <-- Add this projection to remove d
| summarize argmin(a, *)
| project B=max-a-b, C=max-a-c
```