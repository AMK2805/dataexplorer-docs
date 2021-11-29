---
title: UI deep links - Azure Data Explorer | Microsoft Docs
description: This article describes UI deep links in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
---
# UI deep links

The REST API provides a deep link functionality that allows HTTP `GET` requests
to redirect the caller to a UI tool. For example, you can craft a URI that opens
the Kusto.Explorer tool, auto-configures it for a specific cluster and database, runs a specific query and displays its results to the user.

The UI deep links REST API:

* Cluster (mandatory) is commonly defined implicitly, as the service that
  implements the REST API, but can be overridden by specifying the URI query
  parameter `uri`.

* The database (optional) is specified as the first and only fragment of the URI
  path. The database is mandatory for queries and optional for control commands.

* The query or control command (optional) is specified by using the
  URI query parameter `query`, or the URI query parameter `querysrc` (which
  points at a web resource that holds the query).
  `query` can be used in the text of the query or control command itself (encoded
  using the HTTP query parameter encoding). Alternatively, it can be used in the base64 encoding of the gzip of the query or control command text (making it possible to compress
  long queries so that they fit the default browser URI length limits).

* The name of the cluster connection (optional) is specified by using the
  URI query parameter `name`.

* The UI tool is specified by using the `web` optional URI query parameter.
  `web=0` indicates the desktop application Kusto.Explorer. `web=1` indicates
  the Kusto.WebExplorer web application.
`web=2` is the old version of Kusto.WebExplorer
  (based in Application Insights Analytics). `web=3` is the Kusto.WebExplorer
  with an empty profile (no previously-open tabs or clusters will be
  available). Last, the `web` query parameter can be replaced by `saw=1` in
  order to indicate the SAW version of Kusto.Explorer.

Here are a few examples for links:

* `https://help.kusto.windows.net/`: When a user agent (such as a browser) issues
  a `GET /` request it'll be redirected to the default UI tool configured
  to query the `help` cluster.
* `https://help.kusto.windows.net/Samples`: When a user agent (such as a browser) issues
  a `GET /Samples` request it'll be redirected to the default UI tool configured
  to query the `help` cluster `Samples` database.
* `http://help.kusto.windows.net/Samples?query=StormEvents`: When a user (such as a browser) issues
  a `GET /Samples?query=StormEvents` request it'll be redirected to the default UI tool configured
  to query the `help` cluster `Samples` database, and issue the `StormEvents` query.

> [!NOTE]
> The deep link URIs don't require authentication since authentication
> is performed by the UI tool used for redirection.
> Any `Authorization` HTTP header, if provided, is ignored.

> [!IMPORTANT]
> For security reasons, UI tools do not automatically execute control commands,
> even if `query` or `querysrc` are specified in the deep link.

## Deep linking to Kusto.Explorer

This REST API performs redirection that installs and runs the
Kusto.Explorer desktop client tool with specially-crafted startup
parameters that open a connection to a specific Kusto engine cluster
and execute a query against that cluster.

* Path: `/` [*DatabaseName*`]
* Verb: `GET`
* Query string: `web=0`

> [!NOTE]
> See [Deep-linking with Kusto.Explorer](../../tools/kusto-explorer-using.md#deep-linking-queries) 
> for a description of the redirect URI syntax for starting up Kusto.Explorer.

## Deep linking to Kusto.WebExplorer

This REST API performs redirection to Kusto.WebExplorer, a web application.

* Path: `/` clusters/<ClusterName>/databases/<DatabaseName>
* Verb: `GET`
* Query string parameters: 
  `web=1`
  `tenant=`<TenantGuid>
  `login_hint`=<Login>
  
## Specifying the query or control command in the URI

When the URI query string parameter `query` is specified, it must be encoded
according to the URI query string encoding HTML rules. Alternatively, the text of
the query or control command can be compressed by gzip, and then encoded
via base64 encoding. This allows you to send longer queries or control
commands (since the latter encoding method results in shorter URIs).

## Specifying the query or control command by indirection

If the query or control command is very long, even encoding it using gzip/base64 will exceed the maximum URI length of the user agent. Alternatively, the URI query string parameter
`querysrc` is provided, and its value is a short URI pointing at a web resource
that holds the query or control command text.

For example, this can be the URI for a file hosted by Azure Blob Storage.

> [!NOTE]
> If the deep link is to the web application UI tool, the web service providing
> the query or control command (that is, the service providing the `querysrc` URI)
> must be configured to support CORS for `dataexplorer.azure.com`.
>
> Additionally, if authentication/authorization information is required by that
> service, it must be provided as part of the URI itself.
>
> For example, if `querysrc` points at a blob in Azure Blob Storage, one must
> configure the storage account to support CORS, and either make the blob itself
> public (so it can be downloaded without security claims) or add an appropriate
> Azure Storage SAS to the URI. CORS configuration can be done from the
> [Azure portal](https://portal.azure.com/) or from
> [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
> See [CORS support in Azure Storage](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).
