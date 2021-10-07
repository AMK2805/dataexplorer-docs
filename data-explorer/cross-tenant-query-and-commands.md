---
title: Allow cross-tenant queries and commands in Azure Data Explorer
description: Learn how to allow queries or commands from multiple tenants on Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: reference
ms.date: 01/06/2021
---
# Allow cross-tenant queries and commands 

Multiple tenants can run queries and commands in a single Azure Data Explorer cluster. In this article, you will learn how to give cluster access to principals from another tenant.

Cluster owners can protect their cluster from queries and commands from other tenants. This is done by managing the `TrustedExternalTenants` cluster property. The `trustedExternalTenants` property explicitly defines which tenants are allowed to run queries and commands on the cluster and exists at the cluster's resource level. The value for this property can be set using [ARM Templates](/azure/templates/microsoft.kusto/clusters?tabs=json#trustedexternaltenant-object), [AZ CLI](/cli/azure/kusto/cluster?view=azure-cli-latest#az_kusto_cluster_update-optional-parameters), [PowerShell](/powershell/module/az.kusto/new-azkustocluster?view=azps-6.3.0), or the [Azure Resource Explorer](https://resources.azure.com/). For more information, see [Azure Data Explorer cluster request body](/rest/api/azurerekusto/clusters/createorupdate#request-body).

> [!NOTE]
> The principal that will be running queries or commands must also have a relevant database role. For more information, see [role-based authorization](./kusto/management/access-control/role-based-authorization.md). Validation of correct roles takes place after validation of trusted external tenants.

## Syntax

**Define specific tenants**

`trustedExternalTenants: [ {"`*value*`": "`*tenantId1*`" }, { "`*value*`": "`*tenantId2*`" }, ... ]`

**Allow all tenants**

The trustedExternalTenants array supports also all-tenants star ('*') notation, which allows queries and commands from all tenants. 

`trustedExternalTenants: [ { "`*value*`": "`*`" }]`

> [!NOTE]
> The default value for `trustedExternalTenants` is all tenants: `[ { "value": "*" }]`. If the external tenants array was not defined on cluster creation, it can be overridden with a cluster update operation. An empty array isn't accepted.

## Example

Update the cluster using the following operation:

```http
PATCH https://management.azure.com/subscriptions/12345678-1234-1234-1234-123456789098/resourceGroups/kustorgtest/providers/Microsoft.Kusto/clusters/kustoclustertest?api-version=2020-09-18
```

### Request body specific tenants

```json
{
    "properties": { 
        "trustedExternalTenants": [
            { "value": "tenantId1" }, 
            { "value": "tenantId2" }, 
            ...
        ]
    }
}
```

### Request body all tenants

```json
{
    "properties": { 
        "trustedExternalTenants": [  { "value": "*" }  ]
    }
}
```

## Add Principals  

After updating the `trustedExternalTenants` property, you will need to give cluster access to principals from the approved tenant(s) using the `.add` KQL command. For more information, see [Identities - AAD Tenants](./kusto/management/access-control/principals-and-identity-providers.md#aad-tenants).
