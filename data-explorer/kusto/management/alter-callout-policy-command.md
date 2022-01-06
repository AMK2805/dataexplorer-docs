---
title: ".alter callout policy command - Azure Data Explorer"
description: "This article describes the .alter callout policy command in Azure Data Explorer."
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yonil
ms.service: data-explorer
ms.topic: reference
ms.date: 11/29/2021
---
# .alter callout policy

Change the cluster [callout policy](calloutpolicy.md). Azure Data Explorer clusters can communicate with external services in many different scenarios. Cluster admins can manage the authorized domains for external calls, by updating the cluster's callout policy.

## Syntax

`.alter` `cluster` `policy` `callout` *SerializedArrayOfPolicyObjects* 

## Arguments

*SerializedArrayOfPolicyObjects* - A serialized array of JSON policy objects defined. See [callout policy](calloutpolicy.md) for policy properties. 

## Returns

Returns a JSON representation of the policy.

## Example

Define permitted callouts for the cluster callout policy.

```kusto
.alter cluster policy callout @'[{"CalloutType": "sql","CalloutUriRegex": "sqlname\\.database\\.azure\\.com/?$","CanCall": true}]'
```

|PolicyName|EntityName|Policy|ChildEntities|EntityType|
|---|---|---|---|---|
|CalloutPolicy||[{<br>"CalloutType": "sql",<br>"CalloutUriRegex": "sqlname\\\\.database\\\\.azure\\\\.com/?$",<br>"CanCall": true<br>}]|||
