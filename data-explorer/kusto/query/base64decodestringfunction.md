---
title: base64_decodestring() (Azure Kusto)
description: This article describes base64_decodestring() in Azure Kusto.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: kusto
ms.topic: reference
ms.date: 09/24/2018
---
# base64_decodestring()

Decodes a base64 string to a UTF-8 string

**Syntax**

`base64_decodestring(`*String*`)`

**Arguments**

* *String*: Input string to be decoded from base64 to UTF8-8 string.

**Returns**

Returns UTF-8 string decoded from base64 string.

**Example**

```kusto
range x from 1 to 1 step 1
| project base64_decodestring("S3VzdG8=")
```

|Column1|
|---|
|Kusto|

Trying to decode a base64 string which was generated from invalid UTF-8 encoding will return null:

```kusto
range x from 1 to 1 step 1
| project base64_decodestring("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Column1|
|---|
||