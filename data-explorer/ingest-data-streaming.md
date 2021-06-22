---
title: Configure streaming ingestion on your Azure Data Explorer cluster using the Azure portal
description: Learn how to configure your Azure Data Explorer cluster and start loading data with streaming ingestion  using Azure portal.
author: orspod
ms.author: orspodek
ms.reviewer: alexefro
ms.service: data-explorer
ms.topic: how-to
ms.date: 07/13/2020
---

# Configure streaming ingestion on your Azure Data Explorer cluster using the Azure portal

> [!div class="op_single_selector"]
> * [Portal](ingest-data-streaming.md)
> * [C#](ingest-data-streaming-csharp.md)

[!INCLUDE [ingest-data-streaming-intro](includes/ingest-data-streaming-intro.md)]

## Prerequisites

* If you don't have an Azure subscription, create a [free Azure account](https://azure.microsoft.com/free/) before you begin.
* Create [an Azure Data Explorer cluster and database](create-cluster-database-portal.md)

## Enable streaming ingestion on your cluster

### Enable streaming ingestion while creating a new cluster in the Azure portal

You can enable streaming ingestion while creating a new Azure Data Explorer cluster.

In the **Configurations** tab, select **Streaming ingestion** > **On**.

:::image type="content" source="media/ingest-data-streaming/cluster-creation-enable-streaming.png" alt-text="Enable streaming ingestion while creating a cluster in Azure Data Explorer":::

### Enable streaming ingestion on an existing cluster in the Azure portal

1. In the Azure portal, go to your Azure Data Explorer cluster.
1. In **Settings**, select **Configurations**.
1. In the **Configurations** pane, select **On** to enable **Streaming ingestion**.
1. Select **Save**.

    :::image type="content" source="media/ingest-data-streaming/streaming-ingestion-on.png" alt-text="Turn on streaming ingestion in Azure Data Explorer":::

> [!WARNING]
> Review the [limitations](#limitations) prior to enabling steaming ingestion.

## Create a target table and define the policy in the Azure portal

1. In the Azure portal, navigate to your cluster.
1. Select **Query**.

    :::image type="content" source="media/ingest-data-streaming/cluster-select-query-tab.png" alt-text="Select query in the Azure Data Explorer portal to enable streaming ingestion":::

1. To create the table that will receive the data via streaming ingestion, copy the following command into the **Query pane** and select **Run**.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    :::image type="content" source="media/ingest-data-streaming/create-table.png" alt-text="Create a table for streaming ingestion into Azure Data Explorer":::

1. Define the [streaming ingestion policy](kusto/management/streamingingestionpolicy.md) on the table you've created or on the database that contains this table.

    > [!TIP]
    > A policy that is defined at the database level applies to all existing and future tables in the database.

1. Copy one of the following commands into the **Query pane** and select **Run**.

    ```kusto
    .alter table TestTable policy streamingingestion enable
    ```

    or

    ```kusto
    .alter database StreamingTestDb policy streamingingestion enable
    ```

    :::image type="content" source="media/ingest-data-streaming/define-streaming-ingestion-policy.png" alt-text="Define the streaming ingestion policy in Azure Data Explorer":::

[!INCLUDE [ingest-data-streaming-use](includes/ingest-data-streaming-types.md)]

### [Go](#tab/go)

```go
// Creates a Kusto Authorizer using your client identity, secret and tenant identity.
// You may also uses other forms of authorization, see GoDoc > Authorization type.
// auth package is: "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer := kusto.Authorization{
  Config: auth.NewClientCredentialsConfig(clientID, clientSecret, tenantID),
}

database, table :=
    "StreamingTestDb", // Your database
    "TestTable" // Your table

// Create a client
client, err := kusto.New("https://<CLUSTER_NAME>.kusto.windows.net", authorizer)
if err != nil {
    panic("add error handling")
}

// Setup is quite simple
// Pass a *kusto.Client, the name of the database and table you wish to ingest into.
in, err := ingest.New(client, database, table)
if err != nil {
  panic("add error handling")
}

// Ingestion from a stream commits blocks of fully formed data encodes (JSON, AVRO, ...) into Kusto:
if err := in.Stream(client, jsonEncodedData, ingest.JSON, "mappingName"); err != nil {
  panic("add error handling")
}
```

### [Java](#tab/java)

```java
// Build connection string and initialize
ConnectionStringBuilder csb =
    ConnectionStringBuilder.createWithAadApplicationCredentials(System.getProperty("clusterPath"),
            System.getProperty("appId"),
            System.getProperty("appKey"),
            System.getProperty("appTenant"));

// Initialize client and it's properties
IngestClient client = IngestClientFactory.createClient(csb);
IngestionProperties ingestionProperties =
    new IngestionProperties(
        System.getProperty("dbName"),
        System.getProperty("tableName")
    );

// Create Source info
InputStream inputStream = new ByteArrayInputStream(Charset.forName("UTF-8").encode(data).array());
StreamSourceInfo streamSourceInfo = new StreamSourceInfo(inputStream);

// If the data is compressed
streamSourceInfo.setIsCompressed(true);

// Ingest from stream
OperationStatus status = streamingIngestClient.ingestFromStream(streamSourceInfo, ingestionProperties).getIngestionStatusCollection().get(0).status;
```

### [Node.js](#tab/nodejs)

```nodejs
// Streaming ingest client 
const props = new IngestionProps({
database: "StreamingTestDb", // Your database
table: "TestTable", // Your table
format: DataFormat.JSON,
ingestionMappingReference: "Pre-defined mapping name" // For json format mapping is required
});
// Init with engine endpoint
const streamingIngestClient = new StreamingIngestClient(
KustoConnectionStringBuilder.withAadApplicationKeyAuthentication(
`https://<CLUSTER_NAME>.kusto.windows.net`, <APP_ID>, <APP_KEY>, <AUTHORITY_ID>
),
props
);
await streamingIngestClient.ingestFromFile("file.json", props);
```

### [Python](#tab/python)

```python
import io
from azure.kusto.data import KustoConnectionStringBuilder
from azure.kusto.ingest import (
    IngestionProperties,
    DataFormat,
    KustoStreamingIngestClient,
)

# In case you want to authenticate with AAD device code.
# Please note that if you choose this option, you'll need to authenticate for every new instance that is initialized.
# It is highly recommended to create one instance and use it for all of your queries.

cluster = "https://{cluster_name}.kusto.windows.net"
kcsb = KustoConnectionStringBuilder.with_aad_device_authentication(cluster)
client = KustoStreamingIngestClient(kcsb)

ingestion_properties = IngestionProperties(database="{database_name}", table="{table_name}", data_format=DataFormat.CSV)

# ingest from stream
byte_sequence = b"56,56,56"
bytes_stream = io.BytesIO(byte_sequence)
client.ingest_from_stream(bytes_stream, ingestion_properties=ingestion_properties)
```

[!INCLUDE [ingest-data-streaming-disable](includes/ingest-data-streaming-disable.md)]

## Drop the streaming ingestion policy in the Azure portal

1. In the Azure portal, go to your Azure Data Explorer cluster and select **Query**.
1. To drop the streaming ingestion policy from the table, copy the following command into **Query pane** and select **Run**.

    ```Kusto
    .delete table TestTable policy streamingingestion
    ```

    :::image type="content" source="media/ingest-data-streaming/delete-streaming-ingestion-policy.png" alt-text="Delete streaming ingestion policy in Azure Data Explorer":::

1. In **Settings**, select **Configurations**.
1. In the **Configurations** pane, select **Off** to disable **Streaming ingestion**.
1. Select **Save**.

    :::image type="content" source="media/ingest-data-streaming/streaming-ingestion-off.png" alt-text="Turn off streaming ingestion in Azure Data Explorer":::

[!INCLUDE [ingest-data-streaming-limitations](includes/ingest-data-streaming-limitations.md)]

## Next steps

* [Query data in Azure Data Explorer](web-query-data.md)
