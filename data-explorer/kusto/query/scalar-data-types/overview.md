﻿---
title: Scalar data types - Azure Kusto | Microsoft Docs
description: This article describes Scalar data types in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# Scalar data types

In Kusto, evry column, expression, and parameter has a related data type.
A data type is an attribute that specifies the type of the data that the
object can hold: integer data, text (string) data, date and time data, and so on.

Kusto supplies a set of system data types that define all the types of data
that can be used with Kusto.

> User-defined data types are not supported in Kusto.

The following table lists the data types supported by in Kusto, alongside
additional aliases one can use to refer to them and a roughly equivalent
.NET Framework type.

| Type       | Additional name(s)   | Equivalent .NET type              | gettype()   |Storage Type (internal name)|
| ---------- | -------------------- | --------------------------------- | ----------- |----------------------------|
| `bool`     | `boolean`            | `System.Boolean`                  | `int8`      |`I8`                        |
| `datetime` | `date`               | `System.DateTime`                 | `datetime`  |`DateTime`                  |
| `dynamic`  |                      | `System.Object`                   | `array` or `dictionary` or any of the other values |`Dynamic`|
| `guid`     | `uuid`, `uniqueid`   | `System.Guid`                     | `guid`      |`UniqueId`                  |
| `int`      |                      | `System.Int32`                    | `int`       |`I32`                       |
| `long`     |                      | `System.Int64`                    | `long`      |`I64`                       |
| `real`     | `double`             | `System.Double`                   | `real`      |`R64`                       |
| `string`   |                      | `System.String`                   | `string`    |`StringBuffer`              |
| `timespan` | `time`               | `System.TimeSpan`                 | `timespan`  |`TimeSpan`                  |
| `decimal`  |                      | `System.Data.SqlTypes.SqlDecimal` | `decimal`   | `Decimal`                  |

All data types include a special "null" value, which represents the lack of data
or a mismatch of data. (For example, attempting to ingest the string `"abc"`
into an `int` column results in this value.)
It is not possible to materialize this value explicitly, but one can detect
whether an expression evaluates to this value by using the `isnull()` function.

> [!WARNING]
> As of this writing, support for the `guid` type is
> incomplete. We strongly recommend that teams use values of type `string`
> instead.