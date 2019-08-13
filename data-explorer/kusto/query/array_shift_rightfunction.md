---
title: array_shift_right() - Azure Data Explorer | Microsoft Docs
description: This article describes array_shift_right() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2019
---
# array_shift_right()

Shifts values inside of an array to the right.

**Syntax**

`array_shift_right(`*arr*, *shift_count* [, *fill_value* ]`)`

**Arguments**

* *arr*: Input array to split, must be dynamic array.
* *shift_count*: Integer specifying the number of positions that array elements will be shifted to the right. If the value is negative, the elements will be shifted to the left.
* *fill_value*: scalar value that is used for inserting elements instead of the ones that were shifted and removed. Default: null value or empty string (depending on the *arr* type).

**Returns**

Dynamic array containing the same amount of the elements as in original array, where each element was shifted according to *shift_count*. New elements that are added instead of the elements that are removed will have value of *fill_value*.

**See also**
* For shifting array left, see [array_shift_left()](array_shift_leftfunction.md).
* For rotating array right, see [array_rotate_right()](array_rotate_rightfunction.md).
* For rotating array left, see [array_rotate_left()](array_rotate_leftfunction.md).

**Examples**

1. Shifting to the right by two positions:

```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_shift=array_shift_right(arr, 2)
```

|arr|arr_shift|
|---|---|
|[1,2,3,4,5]|[null,null,1,2,3]|

2. Shifting to the right by two positions and adding default value:

```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_shift=array_shift_right(arr, 2, -1)
```

|arr|arr_shift|
|---|---|
|[1,2,3,4,5]|[-1,-1,1,2,3]|


3. Shifting to the left by two positions by using negative shift_count value:

```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_shift=array_shift_right(arr, -2, -1)
```

|arr|arr_shift|
|---|---|
|[1,2,3,4,5]|[3,4,5,-1,-1]|