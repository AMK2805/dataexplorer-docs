---
title: 'Tutorial: Ingest diagnostic and activity log data in Azure Data Explorer without one line of code'
description: 'In this tutorial, you learn how to ingest data to Azure Data Explorer without one line of code and query that data.'
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 2/5/2019

#Customer intent: I want to ingest data to Azure Data Explorer without one line of code, so that I can explore and analyze my data using queries.
---

# Tutorial: Ingest data in Azure Data Explorer without one line of code

This tutorial will teach you how to ingest diagnostic and activity log data to an Azure Data Explorer cluster without one line of code, enabling you to query Azure Data Explorer for data analysis.

// TODO: edit.
In this tutorial you will:
* Collect data from [Azure Monitor diagnostic logs](/azure/azure-monitor/platform/diagnostic-logs-overview) and [Azure Monitor activity logs](/azure/azure-monitor/platform/activity-logs-overview).
* Stream data to an [Event Hub](/azure/event-hubs/event-hubs-about).
* Connect the Event Hub to Azure Data Explorer.
* In an Azure Data Explorer database, create tables and an ingestion mapping. Format the ingested data using an update policy.
* Query the ingested data using Azure Data Explorer.

## Prerequisites

* If you don't have an Azure subscription, create a [free Azure account](https://azure.microsoft.com/free/) before you begin.
* [An Azure Data Explorer cluster and database](create-cluster-database-portal.md). In this tutorial, the database name is *AzureMonitoring*.

## Azure Monitoring data provider - diagnostic and activity logs

View and understand the data provided by the Azure Monitoring diagnostic and activity logs. We will create an ingestion pipeline based on these data schemas.

### Diagnostic logs example

Azure diagnostic logs are metrics emitted by an Azure service that provide data about the operation of that service. Data is aggregated with time grain of 1 minute. Each diagnostic logs event contains one record. This is an example of an Azure Data Explorer metric event schema, on query duration:

```json
{
	"count": 14,
	"total": 0,
	"minimum": 0,
	"maximum": 0,
	"average": 0,
	"resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
	"time": "2018-12-20T17:00:00.0000000Z",
	"metricName": "QueryDuration",
	"timeGrain": "PT1M"
}
```

### Activity logs example

Azure activity logs are subscription level logs containing a collection of records. The logs provide insight into the operations performed on resources in your subscription. Unlike diagnostic logs, an activity logs event has an array of records. We will need to split this array of records later in the tutorial. This is an example of an activity log event for checking access:

```json
{
	"records": [
	{
		"time": "2018-12-26T16:23:06.1090193Z",
		"resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
		"operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
		"category": "Action",
		"resultType": "Start",
		"resultSignature": "Started.",
		"durationMs": 0,
		"callerIpAddress": "13.66.225.188",
		"correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
		"identity": {
			"authorization": {
                ...
            },
            "claims": {
                ...
            }
		},
		"level": "Information",
		"location": "global",
		"properties": {
			...
		}
	},
	{
		"time": "2018-12-26T16:23:06.3040244Z",
		"resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
		"operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
		"category": "Action",
		"resultType": "Success",
		"resultSignature": "Succeeded.OK",
		"durationMs": 194,
		"callerIpAddress": "13.66.225.188",
		"correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
		"identity": {
			"authorization": {
                ...
            },
            "claims": {
                ...
            }
		},
		"level": "Information",
		"location": "global",
		"properties": {
			"statusCode": "OK",
			"serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
		}
	}]
}
```

## Azure Data Explorer ingestion pipeline setup

The Azure Data Explorer pipeline setup contains various steps that include [table creation and data ingestion](/azure/data-explorer/ingest-sample-data#ingest-data) as well as manipulating, mapping, and updating the data.

### Connect to Azure Data Explorer Web UI

1. In your Azure Data Explorer *AzureMonitoring* database, select **Query**, which will open the Azure Data Explorer web UI.

![Query](media/ingest-data-no-code/query-database.png)

### Create target tables

Use the Azure Data Explorer web UI to create the target tables in Azure Data Explorer database.

#### Diagnostic logs table

1. Create a table *DiagnosticLogsRecords* in the *AzureMonitoring* database that will receive the diagnostic log records using the `.create table` control command:

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Select **Run** to create the table.

![Run Query](media/ingest-data-no-code/run-query.png)

#### Activity logs tables

Since the activity logs structure is not tabular, you will need to manipulate the data and expand each event to one or more records. The raw data will be ingested to an intermediate table *ActivityLogsRawRecords* where the data will be manipulated and expanded. The data will then be re-ingested into the *ActivityLogsRecords* table using an update policy. Therefore, you will need to create two separate tables for activity logs ingestion.

1. Create a table *ActivityLogsRecords* in the *AzureMonitoring* database that will receive activity log records. Run the following Azure Data Explorer query to create the table:

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Create the intermediate data table *ActivityLogsRawRecords* in the *AzureMonitoring* database for data manipulation:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```

[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.

### Create table mappings

 The data format is `json`, therefore, data mapping is required. The `json` mapping maps each json path to a table column name.

#### Diagnostic logs table mapping

    ```kusto
    .create table DiagnosticLogsRecords ingestion json mapping 'DiagnosticLogsRecordsMapping' '[
    {"column":"Timestamp","path":"$.time"},
    {"column":"ResourceId","path":"$.resourceId"},
    {"column":"MetricName","path":"$.metricName"},
    {"column":"Count","path":"$.count"},
    {"column":"Total","path":"$.total"},
    {"column":"Minimum","path":"$.minimum"},
    {"column":"Maximum","path":"$.maximum"},
    {"column":"Average","path":"$.average"},
    {"column":"TimeGrain","path":"$.timeGrain"}]'
    ```

#### Activity logs table mapping

    ```kusto
    .create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[
    {"column":"Records","path":"$.records"}]'
    ```

### Create update policy

1. Create a [function](/azure/kusto/management/functions) that expands the collection of records so that each value in the collection receives a separate row. Use the [`mvexpand`](/azure/kusto/query/mvexpandoperator) operator:

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords 
        | mvexpand events = Records 
        | project 
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events["identity.authorization"],
            IdentityClaims = events["identity.claims"],
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. Add an [update policy](/azure/kusto/concepts/updatepolicy) to the target table. It will automatically run the query on any newly-ingested data in the *ActivityLogsRawRecords* intermediate data table and ingest its results into *ActivityLogsRecords* table:

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()"}]'
    ```

## Create an Event Hub Namespace

Azure diagnostic logs enable exporting metrics to a Storage account or an Event Hub. In this tutorial, we route metrics via an Event Hub. You will create an Event Hubs Namespace and Event Hub for diagnostic logs in the following steps. Azure Monitoring will create the Event Hub *insights-operational-logs* for activity logs.

1. Create an event hub using an Azure Resource Manager template in the Azure portal. Use the following button to start the deployment. Right-click and select **Open in new window**, so you can follow the rest of the steps in this article. The **Deploy to Azure** button takes you to the Azure portal.

    [![Deploy to Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Create an Event Hub Namespace and an Event Hub for the diagnostic logs.

    ![Event Hub creation]((media/ingest-data-no-code/event-hub.png)

    Fill out the form with the following information. Use defaults for any settings not listed in the following table.

    **Setting** | **Suggested value** | **Field description**
    |---|---|---|
    | Subscription | Your subscription | Select the Azure subscription that you want to use for your event hub.|
    | Resource group | *test-resource-group* | Create a new resource group. |
    | Location | Select the region that best meets your needs. | Create the event hub namespace in the same Location as the Kusto cluster for best performance (most important for event hub namespaces with high throughput).
    | Namespace name | *AzureMonitoringData* | Choose a unique name that identifies your namespace.
    | Event hub name | *DiagnosticLogsData* | The event hub sits under the namespace, which provides a unique scoping container. The event hub name must be unique within the namespace. |
    | Consumer group name | *adxpipeline* | Create a consumer group name. This enables multiple consuming applications to each have a separate view of the event stream. |
    | | |

## Connect Azure Monitoring logs to Event Hub

### Diagnostic logs connection to Event Hub

Select a resource from which to export metrics. There are several resource types that enables exporting diagnostic logs (Event Hub Namespace, KeyVault, IoT Hub, Azure Data Explorer cluster etc.). In this tutorial we use the Azure Data Explorer cluster as our resource.

1. Select your Kusto cluster in the Azure portal.

    ![Diagnostic settings]((media/ingest-data-no-code/diagnostic-settings.png)

1. Select **Diagnostic settings** from left menu.
1. Click **Turn on diagnostics** link. The **Diagnostics settings** window opens.

    ![Diagnostic settings window]((media/ingest-data-no-code/diagnostic-settings-window.png)

1. In the **Diagnostics settings** pane:
    1. Name your diagnostics log data: *ADXExportedData*
    1. Select **AllMetrics** checkbox (optional).
    1. Select **Stream to an event hub** checkbox.
    1. Click **Configure**

1. In the **Select event hub** pane configure exporting to the event hub you just created:
    1. **Select event hub namespace** dropdown: *AzureMonitoringData*.
    1. **Select event hub name (optional)** dropdown: *diagnosticlogsdata*.
    1. **Select event hub policy name** from dropdown.
    1. Click **OK**.

1. Click **Save** to finalize the diagnostic settings. Event hub namespace, name, and policy name will appear in the window.

![Save diagnostic settings]((media/ingest-data-no-code/save-diagnostic-settings.png)

### Activity logs connection to Event Hub

1. In left menu of Azure portal, select **Activity log**
1. **Activity log** window opens. **Click Export to Event Hub**

    ![Activity logs]((media/ingest-data-no-code/activity-log.png)

1. In the **Export activity log** window:
 
    ![Export activity log]((media/ingest-data-no-code/export-activity-log.png)

    1. Select your subscription.
    1. In **Regions** dropdown, choose **Select all**
    1. Select **Export to an event hub** checkbox.
    1. Click on **Select a service bus namespace** to open the **Select event hub** pane.
    1. In the **Select event hub** pane, select from the dropdown menus: your subscription, your event namespace *AzureMonitoringData*, and the default event hub policy name.
    1. Click **OK**.
    1. Click **Save** on top right side of the window. An event hub with the name *insights-operational-logs* will be created.

### See data flowing to your Event Hubs

1. Wait a few minutes until connection is defined and activity log export to event hub is completed. Go to your Event Hub namespace to see the event hubs you created.

![Event Hubs created]((media/ingest-data-no-code/event-hubs-created.png)

1. See data flowing to your Event Hub:

![Event Hubs data]((media/ingest-data-no-code/event-hubs-data.png)

## Connect Event Hub to Azure Data Explorer

### Diagnostic logs data connection

1. In your Azure Data Explorer cluster *kustodocs*, select **Databases** in the left menu.
1. In the **Databases** window, select your database name *AzureMonitoring*
1. In the left menu, select **Data ingestion**
1. In the **Data ingestion** window, click **+ Add Data Connection**
1. In the **Data connection** window, enter the following:

    ![Save diagnostic settings]((media/ingest-data-no-code/event-hub-data-connection.png)

    Data Source:

    **Setting** | **Suggested value** | **Field description**
    |---|---|---|
    | Data connection name | *DiagnosticsLogsConnection* | The name of the connection you want to create in Azure Data Explorer.|
    | Event hub namespace | *AzureMonitoringData* | The name you chose earlier that identifies your namespace. |
    | Event hub | *diagnosticlogsdata* | The event hub you created. |
    | Consumer group | *adxpipeline* | The consumer group defined in the event hub you created. |
    | | |

    Target table:

    There are two options for routing: *static* and *dynamic*. For this tutorial, you use static routing (the default), where you specify the table name, file format, and mapping. Therefore, leave **My data includes routing info** unselected.

     **Setting** | **Suggested value** | **Field description**
    |---|---|---|
    | Table | *DiagnosticLogsRecords* | The table you created in *AzureMonitoring* database. |
    | Data format | *JSON* | Format in table. |
    | Column mapping | *DiagnosticLogsRecordsMapping* | The mapping you created in *AzureMonitoring* database, which maps incoming JSON data to the column names and data types of *DiagnosticLogsRecords*.|
    | | |

1. Click **Create**  

### Activity logs data connection

Repeat the steps in the [Diagnostic logs data connection](#diagnostic-logs-data-connection) section to create the activity logs data connection.

1. Insert the following settings in the **Data Connection** window:

    Data Source:

    **Setting** | **Suggested value** | **Field description**
    |---|---|---|
    | Data connection name | *ActivityLogsConnection* | The name of the connection you want to create in Azure Data Explorer.|
    | Event hub namespace | *AzureMonitoringData* | The name you chose earlier that identifies your namespace. |
    | Event hub | *insights-operational-logs* | The event hub you created. |
    | Consumer group | *$Default* | The default consumer group. If needed, you can create a different consumer group. |
    | | |

    Target table:

    There are two options for routing: *static* and *dynamic*. For this tutorial, you use static routing (the default), where you specify the table name, file format, and mapping. Therefore, leave **My data includes routing info** unselected.

     **Setting** | **Suggested value** | **Field description**
    |---|---|---|
    | Table | *ActivityLogsRawRecords* | The table you created in *AzureMonitoring* database. |
    | Data format | *JSON* | Format in table. |
    | Column mapping | *ActivityLogsRawRecordsMapping* | The mapping you created in *AzureMonitoring* database, which maps incoming JSON data to the column names and data types of *ActivityLogsRawRecords*.|
    | | |

1. Click **Create**  

## Connect Azure Monitoring to Event Hub

1. Collect data from [Azure Monitoring diagnostic logs](/azure/azure-monitor/platform/diagnostic-logs-overview#how-to-enable-collection-of-diagnostic-logs).

## Query the new tables

you have a pipeline with data flowing. The kusto cluster reports metrics every 1 minute. Ingestion via the cluster take a few minutes, by default, so allow the data flow for a few minutes prior to query.

### Diagnostic logs table query

    ```kusto
    DiagnosticLogsRecords
    | count
    ```

    ```kusto
    DiagnosticLogsRecords
    | where Timestamp > ago(15m) and MetricName == 'QueryDuration'
    | summarize avg(Average) by MetricName, ResourceId
    ```

### Activity logs table query

    ```kusto
    ActivityLogsRawRecords
    | take 1
    ```

    ```kusto
    ActivityLogsRecords
    | take 10
    ```

## Next steps

> [!div class="nextstepaction"]
> [Write Queries for Azure Data Explorer](write-queries.md)