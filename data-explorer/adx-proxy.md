---
title: 'Azure Data Explorer proxy for cross-product queries'
description: 'In this topic, create an Azure Data Explorer proxy for cross product queries with Application Insights and Log Analytics in Azure Monitor'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/05/2019

#Customer intent: I want to use create an Azure Data Explorer proxy for cross product queries with Log Analytics and Application Insights 
---

# Azure Data Explorer proxy for cross product queries (Preview)

The Azure Data Explorer proxy enables you to perform cross product queries between Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview), and [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) in the [Azure Monitor](/azure/azure-monitor/) service. The Azure Data Explorer proxy allows you to define an Azure Monitor logs and Application Insights cluster as a virtual cluster. You can then query the cluster using Azure Data Explorer tools and connect to it as a virtual cluster in a cross cluster query. The article depicts how to connect to the proxy, add a proxy cluster to Azure Data Explorer Web UI Explorer, and run queries again your AI and LA clusters from the Azure Data Explorer proxy. 

The Azure Data Explorer proxy flow is depicted below: 

![ADX proxy flow](media/adx-proxy/adx-proxy-flow.png)

## Prerequisites

> [!NOTE]
> The ADX Proxy is now in **Private Preview**. To enable this feature, contact the [ADXProxy](mailto:adxproxy@microsoft.com) team.

## Connect to the proxy

1. Verify your Azure Data Explorer native cluster *help* appears on the left menu before you connect to your Log Analytics or Application Insights cluster.

    ![ADX native cluster](media/adx-proxy/web-ui-help-cluster.png)

1. In the Azure Data Explorer UI (https://dataexplorer.azure.com/clusters), select **Add Cluster**.

1. In the **Add Cluster** window:

    * Add the URL to the LA or AI cluster. For example: `https://ade.loganalytics.io/subscriptions/<Subscription ID>/workspaces/<Workspace name>`
    * Select **Add**.

    ![Add cluster](media/adx-proxy/add-cluster.png)

    If you add a connection to more than one proxy cluster, give each a different name. Otherwise they'll all have the same name in the left pane.

1. After the connection is established, your LA or AI cluster will appear in the left pane with your native ADX cluster. 

    ![Log Analytics and Azure Data Explorer clusters](media/adx-proxy/la-adx-clusters.png)

## Run queries

You can use Kusto Explorer, ADX web Explorer, Jupyter Kqlmagic, or REST API to query the proxy clusters. 

> [!TIP]
> * Database names in Azure Data Explorer are case-sensitive. 
> * In cross cluster queries, make sure that the [naming of apps and workspaces](#application-insights-app-and-log-analytics-workspace-names) is correct.

### Query against the native Azure Data Explorer cluster 

Run queries against your Azure Data Explorer cluster (such as *StormEvents* table). When running the query, verify that your native Azure Data Explorer cluster is selected in the left pane.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![Query StormEvents table](media/adx-proxy/query-adx.png)

### Query against your LA or AI cluster

When you run queries on your LA or AL cluster, verify that your LA or AI cluster is selected in the left pane. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Query LA workspace](media/adx-proxy/query-la.png)

### Query against your LA or AI cluster from the ADX proxy  

When you run queries against your LA or AI cluster from the proxy (such as on *Perf* table), verify your ADX native cluster is selected in the left pane

```kusto
cluster(`https://ade.loganalytics.io/subscriptions/<subscription ID>/workspaces/<workspace name>`) .database(`<workspace name).Perf
| take 10 // Demonstrate query of the LA workspace through the native DX cluster
```

![Query from Azure Data Explorer proxy](media/adx-proxy/query-adx-proxy.png)

### Cross query of LA or AI cluster and the ADX cluster from the ADX proxy 

> [!IMPORTANT]
> When you run cross cluster queries from the proxy, verify your ADX native cluster is selected in the left pane.

```kusto
unionStormEvents, cluster(`https://ade.loganalytics.io/subscriptions/<subscription ID>/workspaces/<workspace name>`).database(<workspace name>).Perf
| take 10 // union tables from both the ADX cluster and the LA workspace
```

![Cross query from the Azure Data Explorer proxy](media/adx-proxy/cross-query-adx-proxy.png)

Using the [`join` operator](/azure/kusto/query/joinoperator) may require a hint to run it on an Azure Data Explorer native cluster (and not on the proxy). 

## Additional syntax examples

The following syntax options are available when calling the Application Insights (AI) or Log Analytics (LA) clusters:

|Syntax Description  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Database within a cluster that contains all apps in this subscription    |   (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`).database(`<database-name>`)      |    (`https://ade.loganalytics.io/subscriptions/<subscription-id>`).database(`<database-name>`)     |
|Cluster that contains all apps/workspaces in this subscription    |     (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster that contains all apps/workspaces in the subscription and are members of this resource group    |   (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)     |
|Cluster that contains only this app/workspace (recommended)    |     (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/apps/<ai-app-name>`) or  (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/providers/microsoft.insights/components/<ai-app-name>`)  | (`https://ade.loganalytics.io/subscriptions/<subscription-id>/workspaces/<ai-app-name>`)  or   (`https://ade.loganalytics.io/subscriptions/<subscription-id>/providers/microsoft.operationalinsights/workspaces/<ai-app-name>`)   |
|Cluster that contains only this resource group      |    (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/apps/<ai-app-name>`) or  (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/workspaces/<ai-app-name>`) or  (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<ai-app-name>`)    |

### Application Insights app and Log Analytics workspace names

* If names contain special characters, they are replaced by URL encoding in the proxy cluster name. 
* If names include characters that don't meet [KQL identifier naming rules](/azure/kusto/query/schema-entities/entity-names), they'll be replaced by the dash character **-**.

## Next steps

* [Write queries](write-queries.md)