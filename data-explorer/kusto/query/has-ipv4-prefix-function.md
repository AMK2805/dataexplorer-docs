---
title: has_ipv4_prefix() - Azure Data Explorer
description: This article describes has_ipv4_prefix() in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/21/2021
---
# has_ipv4_prefix()

Returns a value indicating whether a specified IPv4 address prefix appears in a text.

A valid IP address prefix is either a complete IPv4 address (`192.168.1.11`) or its prefix ending with a dot (`192.`, `192.168.` or `192.168.1.`).

IP address entrances in a text must be properly delimited with non-alphanumeric characters. For example, properly delimited IP addresses are:

 * "These requests came from: 192.168.1.1, 10.1.1.115 and 10.1.1.201"
 * "05:04:54 127.0.0.1 GET /favicon.ico 404"

The function works significantly faster if the searched text column is indexed using special tokenizer. To update the column tokenizer type to be used in future data ingestions, use the command:

```kusto
.alter column MyTable.TextColumn policy encoding type='NetworkLogIpv4'
```


## Syntax

`has_ipv4_prefix(`*text* `,` *ip_address_prefix* `)`

## Arguments

* *text*: The value containing the text to search in.
* *ip_address_prefix*: String value containing the IP address prefix to look for.

## Returns

`true` if the *ip_address_prefix* is a valid IPv4 address prefix, and it was found in *text*. Otherwise, the function returns `false`.

## Examples

```kusto
has_ipv4_prefix('05:04:54 127.0.0.1 GET /favicon.ico 404', '127.0.')          // true
has_ipv4_prefix('05:04:54 127.0.0.1 GET /favicon.ico 404', '127.0')           // false, invalid IPv4 prefix
has_ipv4_prefix('05:04:54 127.0.0.256 GET /favicon.ico 404', '127.0.')        // false, invalid IPv4 address
has_ipv4_prefix('05:04:54127.0.0.1 GET /favicon.ico 404', '127.0.')           // false, improperly delimited IP address
```
