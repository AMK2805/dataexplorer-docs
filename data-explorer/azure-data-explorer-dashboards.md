---
title: Visualize data with the Azure Data Explorer dashboard
description: Learn how to visualize data with the Azure Data Explorer dashboard
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/31/2021
ms.localizationpriority: high 
---

# Visualize data with Azure Data Explorer dashboards(Preview)

Azure Data Explorer is a fast and highly scalable data exploration service for log and telemetry data. Azure Data Explorer provides a web application that enables you to run queries and build dashboards. Dashboards are available in the stand-alone web application, the [Web UI](web-query-data.md). Azure Data Explorer is also integrated with other dashboard services like [Power BI](power-bi-connector.md) and [Grafana](grafana.md).

Azure Data Explorer dashboards provide three main advantages:

* Natively export queries from the Web UI to Azure Data Explorer dashboards. 
* Explore the data in the Web UI.
* Optimized dashboard rendering performance.

The following image depicts an Azure Data Explorer dashboard.

:::image type="content" source="media/adx-dashboards/dash.png" alt-text="Final dashboard.":::

> [!IMPORTANT]
> Your data is secure. Dashboards and dashboard-related metadata about users is encrypted at rest.

## Prerequisites

* If you don't have an Azure subscription, create a [free Azure account](https://azure.microsoft.com/free/) before you begin.
* Create [an Azure Data Explorer cluster and database](create-cluster-database-portal.md).
* Sign in to the [Azure Data Explorer Web UI](https://dataexplorer.azure.com/) and [add a connection to your cluster](web-query-data.md#add-clusters).

## Create a dashboard

1. In the navigation bar, select **Dashboards (Preview)** and then select **New dashboard**.

    :::image type="content" source="media/adx-dashboards/new-dashboard.png" alt-text="New dashboard.":::

1. Enter a dashboard name and then select **Create**.

    :::image type="content" source="media/adx-dashboards/new-dashboard-popup.png" alt-text="Create a dashboard.":::

## Add data source

Add a data source for the dashboard.

1. Select **Data sources**.
1. In the **Data sources** pane, select **New data source**.

    :::image type="content" source="media/adx-dashboards/data-source.png" alt-text="Data source.":::

1. In the **Create new data source** pane:
    1. Enter a **Data source name**. 
    1. Enter the **Cluster URI** region and then select **Connect**.
    1. Select the **Database** from the drop-down list.
    1. Select **Apply**.

    :::image type="content" source="media/adx-dashboards/data-source-pane.png" alt-text="Data source pane.":::

## Use Parameters

Parameters significantly improve dashboard rendering performance, and enable you to use filter values as early as possible in the query. Filtering is enabled when the parameter is included in the query associated with your tile(s).  For more information about how to set up and use different kinds of parameters, see [Use parameters in Azure Data Explorer dashboards](dashboard-parameters.md).

1. Select **Parameters** on the top bar.
1. Select the **+ New parameter** button in the **Parameters** pane.

    :::image type="content" source="media/adx-dashboards/parameters.png" alt-text="Select new parameter.":::

1. Enter values for all the mandatory fields and select **Done**. In this example, we're using a query-based parameter that allows you to select one or more states and see events associated with this selection.

    :::image type="content" source="media/adx-dashboards/parameter-pane.png" alt-text="Parameter pane.":::

|Field  |Description |
|---------|---------|
|**Parameter type**    |One of the following:<ul><li>**Single Selection**: Only one value can be selected in the filter as input for the parameter.</li><li>**Multiple Selection**: One or more values can be selected in the filter as input(s) for the parameter.</li><li>**Time Range**: Allows creating additional parameters to filter the queries and dashboards based on time. Every dashboard has a time range picker by default.</li></ul>  The parameter type you select will affect the way you write any query that's based on this parameter.  |
|**Variable name**     |   The name of the parameter to be used in the query.      |
|**Data type**    |    The data type of the parameter values.     |
|**Pin as dashboard filter**   |   The option to pin the parameter-based filter to the dashboard .       |
|**Source**     |    The source of the parameter values: <ul><li>**Fixed values**: Manually introduced static filter values. </li><li>**Query**: Dynamically introduced values using a KQL query.  </li></ul>    |
| **Value column** | Results column to be used as parameter values. Only applicable for query-based parameters.
| **Label column** | Results column to be used for parameter labels. Only applicable for query-based parameters.
|**Add empty "Select all" value**    |   Applicable only to single selection and multiple selection parameter types. Used to retrieve data for all the parameter values.      |
|**Display name**    |   The name of the parameter shown on the dashboard or the edit card.      |
| **Default value** | The default parameter value. |

### Parameter query

The following is an example of a query using the parameter defined in [Use parameters](azure-data-explorer-dashboards.md#use-parameters).

:::image type="content" source="media/adx-dashboards/parameter-query.png" alt-text="Screenshot of query used to generate parameters.":::

1. Select the source data from the drop-down bar.
1. Enter your query and then select **Run**.

1. Select **Apply changes**.

> [!NOTE]
> The parameter query is used to generate dynamically introduced values as parameters using a KQL query. It's not the query used for generating the dashboard visual.

For more information about generating parameter queries, see [Create a parameter](dashboard-parameters.md#create-a-parameter).

## Add tile

**Add tile** uses Kusto Query Language snippets to retrieve data and render visuals. Each tile/query can support a single visual.

1. Select **Add tile** from the dashboard canvas or the top menu bar.

    :::image type="content" source="media/adx-dashboards/empty-dashboard-new-query.png" alt-text="New query.":::

1. In the **Query** pane, 
    1. Select the data source from the drop-down menu.
    1. Type the query, and the select **Run**. For more information about generating queries that use parameters, see [Use parameters in your query](dashboard-parameters.md#use-parameters-in-your-query).

    1. Select **+ Add visual**.

    :::image type="content" source="media/adx-dashboards/initial-query.png" alt-text="Execute query.":::

1. In the **Visual formatting** pane, select **Visual type** to choose the type of visual.
1. Select **Apply changes** to pin the visual to the dashboard.

    :::image type="content" source="media/adx-dashboards/add-visual.png" alt-text="Add visual to query.":::

1. You can resize the visual and then **Save changes** to save the dashboard.

    :::image type="content" source="media/adx-dashboards/save-dashboard.png" alt-text="save dashboard.":::

## Share dashboards

Use the share menu to [grant permissions](#grant-permissions) for an Azure Active Directory (AAD) user or AAD group to access the dashboard, [change a user's permission level](#change-a-user-permission-level), and [share the dashboard link](#share-the-dashboard-link).

> [!IMPORTANT]
> To access the dashboard, a dashboard viewer needs the following:
> * Dashboard link for access
> * Dashboard permissions
> * Access to the underlying database in the Azure Data Explorer cluster  

1. Select the **Share** menu item in the top bar of the dashboard.
1. Select **Manage permissions** from the drop-down. 

    :::image type="content" source="media/adx-dashboards/share-dashboard.png" alt-text="Share dashboard drop-down.":::

### Grant permissions

To grant permissions to a user in the **Dashboard permissions** pane:
1. Write the user's name or email in **Add new members** box.
1. In the **Permission** level, select one of the following values: **Can view** or **Can edit**.
1. Select **Add**.

:::image type="content" source="media/adx-dashboards/dashboard-permissions.png" alt-text="Manage dashboard permissions.":::

### Change a user permission level

To change a user permission level in the **Dashboard permissions** pane:
1. Either use the search box or scroll the user list to find the user.
1. Change the **Permission** level as needed.

### Share the dashboard link

To share the dashboard link:
* Select **Share** and then select **Copy link**
Or
* In the **Dashboard permissions** window, select **Copy link**. 

## Enable auto refresh 

1. Select **Edit** in dashboard menu to switch to edit mode.
1. Select **Auto refresh**. 
 
    :::image type="content" source="media/adx-dashboards/auto-refresh.png" alt-text="Select auto refresh.":::

1. Toggle the option so auto refresh is **Enabled**. 
1. Select values for **Minimum time interval** and **Default refresh rate**. 

    :::image type="content" source="media/adx-dashboards/auto-refresh-toggle.png" alt-text="Enable auto refresh.":::

1. Select **Apply** and then **Save** the dashboard.

> [!NOTE]
> * Select the smallest minimum time interval to reduce unnecessary load on the cluster. 
> * A dashboard viewer: 
>    * Can change the minimum time intervals for personal use only. 
>    * Can't select a value which is smaller than the **Minimum time interval** specified by the editor.

## Next Steps

* [Use parameters in Azure Data Explorer dashboards](dashboard-parameters.md)
* [Customize dashboard visuals](dashboard-customize-visuals.md)
* [Query data in Azure Data Explorer](web-query-data.md)
