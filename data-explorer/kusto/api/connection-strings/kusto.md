---
title: Kusto connection strings - Azure Data Explorer | Microsoft Docs
description: This article describes Kusto connection strings in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: reference
ms.date: 06/26/2019
---
# Kusto connection strings

Kusto connection strings can provide the information necessary
for a Kusto client application to establish a connection to a Kusto service
endpoint. Kusto connection strings are modeled after the ADO.NET connection
strings, namely, the connection string is a semicolon-delimited list of name/value
parameter pairs, optionally prefixed by a single URI. For example:

```text
https://help.kusto.windows.net/Samples; Fed=true; Accept=true
```

The URI here provides the service endpoint to communicate with:
* (`https://help.kusto.windows.net`) - value of the `Data Source` property.
* `Samples`(default database) - value of the`Initial Catalog` property.

Two additional properties are provided using the name/value syntax: 
* `Fed` property (also called `AAD Federated Security`) set to `true`.
* `Accept` property set to `true`.

> [!NOTE]
> * Property names are not case sensitive, and spaces between name/value pairs are ignored.
> * Property values **are** case sensitive. A property value that contains
a semicolon (`;`), a single quotation mark (`'`), or a double quotation mark (`"`)
must be enclosed between double quotation marks.

Several Kusto client tools support an extension over the URI prefix of the connection
string, in that they allow the shorthand format `@` *ClusterName* `/` *InitialCatalog* to be used.
For example, the connection string `@help/Samples` is translated by these tools
to `https://help.kusto.windows.net/Samples; Fed=true`, which indicates three
properties (`Data Source`, `Initial Catalog`, and `AAD Federated Security`).

Programmatically, Kusto connection strings can be parsed and manipulated
by the C# `Kusto.Data.KustoConnectionStringBuilder` class. This class validates
all connection strings and will generate a runtime exception if validation fails.
This functionality is present in all flavors of Kusto SDK.

## Connection string properties

The following table lists all the properties you can specify in a Kusto connection string.
It lists programmatic names (which is the name of the property in the
`Kusto.Data.KustoConnectionStringBuilder` object) as well as additional property names that are aliases.

### General properties

|Property name                      |Alternative names|Programmatic name  |Description                                                   |
|-----------------------------------|-----------------|-------------------|--------------------------------------------------------------|
|Client Version for Tracing         |                                      |TraceClientVersion|When tracing the client version, use this value                                          |
|Data Source                        |Addr, Address, Network Address, Server|DataSource        |The URI specifying the Kusto service endpoint. For example, https://mycluster.kusto.windows.net or net.tcp://localhost
|Initial Catalog                    |Database                              |InitialCatalog    |The name of the database to be used by default. For example, MyDatabase|
|Query Consistency                  |QueryConsistency                      |QueryConsistency  |Set to either `strongconsistency` or `weakconsistency` to determine if the query should synchronize with the metadata before running|


### User Authentication properties


|Property name                      |Alternative names|Programmatic name  |Description                                                   |
|-----------------------------------|-----------------|-------------------|--------------------------------------------------------------|
|AAD Federated Security             |Federated Security, Federated, Fed, AADFed|FederatedSecurity    |A Boolean value that instructs the client to perform AAD federated authentication|
|Enforce MFA                        |MFA,EnforceMFA                            |EnforceMfa           |A Boolean value that instructs the client to force acquiring a multifactor-authentication token regardless of the service endpoint connecting to|
|User ID                            |UID, User                                 |UserID               |Username hint for AAD Federated AuthN|
|User Name for Tracing              |                                          |TraceUserName        |When tracing the user name, use this value                                               |
|User Token                         |UsrToken, UserToken                       |UserToken            |AAD-issued user token to use for authentication when AAD Federated authentication is used. Overrides ApplicationClientId, ApplicationKey and ApplicationToken. (Used rarely, if the caller has already authenticated against AAD and wants to use the token to communicate with Kusto)|



### Application Authentication properties


|Property name                      |Alternative names|Programmatic name  |Description                                                   |
|-----------------------------------|-----------------|-------------------|--------------------------------------------------------------|
|AAD Federated Security                            |Federated Security, Federated, Fed, AADFed|AadFederatedSecurity                          |A Boolean value that instructs the client to perform AAD federated authentication|
|Application Certificate Thumbprint                |AppCert                                   |ApplicationCertificateThumbprint              |The thumbprint of the certificate to use for AAD application authentication when federated authentication is used|
|Application Client Id                             |AppClientId                               |ApplicationClientId                           |The application client id to use for authentication when federated authentication is used|
|Application Key                                   |AppKey                                    |ApplicationKey                                |The application key to use for authentication when federated authentication is used      |
|Application Name for Tracing                      |                                          |TraceAppName                                  |When tracing the application name, use this value                                        |
|Application Token                                 |AppToken                                  |ApplicationToken                              |The application token to use for authentication when federated authentication is used. Overrides ApplicationClientId and ApplicationKey. (Used rarely, if the caller has already authenticated against AAD and wants to use the token to communicate with Kusto)|
|Authority Id                                      |TenantId                                  |Authority                                     |The name or ID of the AAD tenant where the application is configured|



### Client communication properties

|Property name                      |Alternative names|Programmatic name  |Description                                                   |
|-----------------------------------|-----------------|-------------------|--------------------------------------------------------------|
|Accept      ||Accept      |A Boolean value that requests detailed error objects to be returned on failures.|
|Streaming   ||Streaming   |A Boolean value that requests the client will not accumulate data before providing it to the caller.|
|Uncompressed||Uncompressed|A Boolean value that requests the client will not ask for transport-level compression.|

## Authentication properties (details)

One of the important tasks of the connection string is to tell the client how to authenticate to the service.
The following algorithm is generally used by clients for authentication against HTTP/HTTPS endpoints:

1. If AadFederatedSecurity is true:
    1. If UserToken is specified, use AAD federated authentication with the specified token
    2. Otherwise, If ApplicationToken is specified, perform federated authentication with the specified token
    3. Otherwise, if ApplicationClientId and ApplicationKey are specified, perform federated authentication with the specified application client id and key
    4. Otherwise, if ApplicationClientId and ApplicationCertificateThumbprint are specified, perform federated authentication with the specified application client id and certificate
    5. Otherwise, perform federated authentication with the current logged-on user's identity (user will be prompted if this is the first authentication in the session)

2. Otherwise do not authenticate





### AAD federated application authentication with application certificate:

1. Authentication based on application's certificate if supported only for web applications (and not for native client applications).
2. The web application should be configured to accept the given certificate. [How to authentication based-on AAD application's certificate](https://azure.microsoft.com/en-in/documentation/samples/active-directory-dotnet-daemon-certificate-credential/)
3. The web application should be configured as an authorized principal in the relevant Kusto's cluster.
4. The certificate with the given thumbprint should be installed (in Local Machine store or in Current User store).
5. The certificate's public key should contain at least 2048 bits.

## AAD-based authentication examples

**AAD Federated authentication based-on the current logged-on user's identity (user will be prompted if required)**

```csharp
// Option 1
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder($"https://{serviceNameAndRegion}.kusto.windows.net")
  .WithAadUserPromptAuthentication(authority);

// Option 2
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder($"https://{serviceNameAndRegion}.kusto.windows.net")
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source=https://{serviceNameAndRegion}.kusto.windows.net:443;Database=NetDefaultDB;Fed=True;authority={authority}"
```

**AAD Federated application authentication based-on a given ApplicationClientId and ApplicationKey**

```csharp
// Option 1
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var applicationClientId = APP_GUID;
var applicationKey = secret;
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder($"https://{serviceNameAndRegion}.kusto.windows.net")
    .WithAadApplicationKeyAuthentication(applicationClientId, applicationKey, authority);

// Option 2
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var applicationClientId = <ApplicationClientId>;
var applicationKey = <ApplicationKey>;
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(@"https://{serviceNameAndRegion}.kusto.windows.net")
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    ApplicationClientId = applicationClientId,
    ApplicationKey = applicationKey,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source=https://{serviceNameAndRegion}.kusto.windows.net:443;Database=NetDefaultDB;Fed=True;AppClientId={applicationClientId};AppKey={applicationKey};authority={authority}"
```

**AAD Federated authentication based-on a given user's / application's token**

```csharp
// AAD User - Option 1
var serviceName = "help";
var userToken = "<UserToken>";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(@"https://{serviceNameAndRegion}.kusto.windows.net")
    .WithAadUserTokenAuthentication(userToken);

// AAD User - Option 2
var serviceName = "help";
var userToken = "<UserToken>";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(@"https://{serviceNameAndRegion}.kusto.windows.net")
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    UserToken = userToken,
    Authority = authority,
};

// Equivalent Kusto connection string: "Data Source=https://{serviceNameAndRegion}.kusto.windows.net:443;Database=NetDefaultDB;Fed=True;UserToken={user_token};;authority={authority}"

// AAD Application - Option 1
var serviceName = "help";
var applicationToken = "<ApplicationToken>";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(@"https://{serviceNameAndRegion}.kusto.windows.net")
    .WithAadApplicationTokenAuthentication();

// AAD Application - Option 2
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var applicationToken = "<UserToken>";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(@"https://{serviceNameAndRegion}.kusto.windows.net")
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    ApplicationToken = applicationToken,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source=https://{serviceNameAndRegion}.kusto.windows.net:443;Database=NetDefaultDB;Fed=True;AppToken={applicationToken};authority={authority}"
```

**Using certificate thumbprint (client will attempt to load the certificate from local store)**

```csharp
string applicationClientId = "<applicationClientId>";
string applicationCertificateThumbprint = "<ApplicationCertificateThumbprint>";
 
// Option 1
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(@"https://{serviceNameAndRegion}.kusto.windows.net")
    .WithAadApplicationThumbprintAuthentication(applicationClientId, applicationCertificateThumbprint, authority);

// Option 2
var serviceName = "help";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(@"https://{serviceNameAndRegion}.kusto.windows.net")
{
    FederatedSecurity = true,
    ApplicationClientId = applicationClientId,
    ApplicationCertificateThumbprint = applicationCertificateThumbprint,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source=https://{serviceNameAndRegion}.kusto.windows.net:443;Database=NetDefaultDB;Fed=True;AppClientId={applicationClientId};AppCert={applicationCertificateThumbprint};authority={authority}"
```

