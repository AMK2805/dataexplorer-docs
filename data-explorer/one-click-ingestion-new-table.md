---
title: Use one-click ingestion to ingest data into a new existing Azure Data Explorer table
description: Ingesting (loading) data into a new Azure Data Explorer table simply, using one-click ingestion.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/22/2020
---

# Use one-click ingestion to ingest data to a new table in Azure Data Explorer

One-click ingestion enables you to quickly ingest a new table in JSON, CSV, and other formats. Using the Azure Data Explorer Web UI, you can ingest data from storage, from a local file, or from a container. This document describes using the intuitive one-click wizard to ingest your data into a new table. You can then edit the table and run queries with the Azure Data Explorer Web UI.

One-click ingestion is particularly useful when ingesting data for the first time, or when your data's schema is unfamiliar to you. 

For an overview of one-click ingestion and a list of prerequisites, see [One-click ingestion](ingest-data-one-click.md).
For information about ingesting data into an existing table in Azure Data Explorer, see [One-click ingestion to an existing table](one-click-ingestion-existing-table.md)

## Ingest new data

1. In the left menu of the Web UI, right-click a *database* and select **Ingest new data (Preview)**.

    ![Select one-click ingestion in the Web UI](media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png)   
 
1. In the **Ingest new data (Preview)** window, the **Source** tab is automatically selected. Complete the **Project Details**:

    1. Select **Create new table** and enter a name for the new table. You can use alphanumeric, hyphens and underscores. Special characters aren't supported.
    ![Create a new table](media/one-click-ingestion-new-table/create-new-table.png) 
    
    1. For **Ingestion type**, select one of the following options:
       * **from storage** - in the **Link to storage** field, add the URL of the storage account. Use [Blob SAS URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) for private storage accounts. 
       ![One-click ingestion from storage](media/one-click-ingestion-new-table/from-storage-blob.png)
      * **from file** - select **Browse** to locate the file, or drag the file into the field.
      ![One-click ingestion from file](media/one-click-ingestion-new-table/from-file.png)
      * **from container** - in the **Link to storage** field, add the URL of the container, and enter the sample size
      ![One-click ingestion from container](media/one-click-ingestion-new-table/from-container.png)
      A sample of the data appears. If you want to, you can filter it to show only files that begin end with specific characters. When you adjust the filters, the preview automatically updates.
      For example, you can filter for all files that begin with the word *data* and end with a *.csv.gz* extension.
      ![One-click ingestion filter](media/one-click-ingestion-new-table/from-container-with-filter.png)
1. Select **Edit schema** to view and edit your table column configuration.

## Edit the schema

1. In the **Schema** tab:
    1. Select **Compression type**, and then select either **Uncompressed** or **GZip**.
    1. Select a **Data format**:
        * JSON
        * CSV
        * TSV
        * SCSV
        * SOHSV
        * TSVE
        * PSV

        * If you select  **JSON**, you must also select **JSON levels**, from 1 to 10. The levels affect the table column data depiction.
        ![Select JSON levels](media/one-click-ingestion-new-table/json-levels.png)
        * If you select a format other than JSON, you can select the check box **Include column names** to ignore the heading row of the file.
        ![Select Include column names](media/one-click-ingestion-new-table/non-json-format.png)
    1. In the **Mapping name** field, enter a mapping name. You can use alphanumeric characters and underscores. Spaces, special characters, and hyphens aren't supported.
    ![Table mapping name](media/one-click-ingestion-new-table/table-mapping.png)

## Copy and paste queries

1. Above the **Editor** pane, select the **v** button to open the editor. In the editor, you can view and copy the automatic queries generated from your inputs. 
1. In the table: 
    * Select new column headers to **Change data type**, **Rename column**, **New column**, **Delete column**, **Sort ascending**, or **Sort descending**. On existing columns, only data sorting is available. 
    * Double-click the new column name to edit.
[![](media/one-click-ingestion-new-table/edit-view.png "One-click ingestion edit view")](media/one-click-ingestion-new-table/edit-view.png#lightbox) 

## Start ingestion

Select **Start ingestion** to create a table and mapping and to begin data ingestion.
![Start ingestion](media/one-click-ingestion-new-table/start-ingestion.png)

## Query data

1. In the **Data ingestion completed** window, all three steps will be marked with green check marks if data ingestion finishes successfully.
 
    ![One-click data ingestion complete](media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png)

1. Select the **v** button to open the query. Copy to the Web UI to edit the query.

1. In the right-hand menu, select **Quick queries** and **Tools**: 

    * **Quick queries** includes links to the Web UI with example queries.
    * **Tools** includes a link to **Drop commands** on the Web UI, which enable you to troubleshoot issues by running the relevant `.drop` commands.

    > [!TIP]
    > You might lose data when you use `.drop` commands. Use them carefully.

## Next steps

* [Query data in Azure Data Explorer Web UI](/azure/data-explorer/web-query-data)
* [Write queries for Azure Data Explorer using Kusto Query Language](/azure/data-explorer/write-queries)