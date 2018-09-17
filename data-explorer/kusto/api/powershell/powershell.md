---
title: Using the .NET Client Libraries from PowerShell - Azure Data Explorer | Microsoft Docs
description: This article describes Using the .NET Client Libraries from PowerShell in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: reference
ms.date: 09/24/2018
---
# Using the .NET Client Libraries from PowerShell

The Kusto .NET client libraries can be used by PowerShell scripts through
PowerShell's built-in integration with arbitrary (non-PowerShell) .NET libraries.

## Getting the .NET Client Libraries for scripting with PowerShell

To start working with the Kusto .NET client libraries using PowerShell:

1. Download the `Microsoft.Azure.Kusto.Tools` NuGet package from [here](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/).
2. Extract the contents of the 'tools' directory in the package (using 7-zip, for example).
3. Use the PowerShell `Add-Type` command to load the desired library.
   The `-Path` parameter to the command should by used to indicate the location
   of the extracted files.
4. Once all dependent .NET assemblies are loaded, create a Kusto connection string,
   instantiate a *query provider* or an *admin provider*, and run the queries or commands (as shown in the [example](powershell.md#example) below).

For detailed information about using the Kusto Client Libraries, see the topic
with the same name in the documentation.

## Example

```powershell
#  Initialization - 1/3
#  Packages location - This is an example to the location where you extract the Microsoft.Azure.Kusto.Tools package.
#  Please make sure you load the types from a local directory and not from a remote share.
$packagesRoot = "C:\Microsoft.Azure.Kusto.Tools\Tools"

#  Initialization - 2/3
#  Loading the Kusto.Client library and its dependencies
dir $packagesRoot\* | Unblock-File
[System.Reflection.Assembly]::LoadFrom("$packagesRoot\Kusto.Data.dll")

#  Initialization - 3/3
#  Defining the connection to your cluster / database
$clusterUrl = "https://help.kusto.windows.net;Fed=True"
$databaseName = "Samples"

#   Option A: using AAD User Authentication
$kcsb = New-Object Kusto.Data.KustoConnectionStringBuilder ($clusterUrl, $databaseName)
 
#   Option B: using AAD application Authentication
#     $applicationId = "application ID goes here"
#     $applicationKey = "application key goes here"
#     $kcsb = $kcsb.WithAadApplicationKeyAuthentication($applicationId, $applicationKey)

# Running queries and commands
#   EXAMPLE 1: Running an admin command - e.g. ".show diagnostics"
$adminProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslAdminProvider($kcsb)
$command = [Kusto.Data.Common.CslCommandGenerator]::GenerateDiagnosticsShowCommand()
Write-Host "Executing command: '$command' with connection string: '$($kcsb.ToString())'"
$reader = $adminProvider.ExecuteControlCommand($command)
$reader.Read() # this reads a single row/record. If you have multiple ones returned, you can read in a loop 
$isHealthy = $Reader.GetBoolean(0)
Write-Host "IsHealthy = $isHealthy"
 
#   EXAMPLE 2: Running a query - e.g. against the "StormEvents" table in the "Samples" database
$queryProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslQueryProvider($kcsb)
$query = "StormEvents | limit 10"
Write-Host "Executing query: '$query' with connection string: '$($kcsb.ToString())'"
#   Optional: set a client request ID and set a client request property (e.g. Server Timeout)
$crp = New-Object Kusto.Data.Common.ClientRequestProperties
$crp.ClientRequestId = "MyPowershellScript.ExecuteQuery." + [Guid]::NewGuid().ToString()
$crp.SetOption([Kusto.Data.Common.ClientRequestProperties]::OptionServerTimeout, [TimeSpan]::FromSeconds(30))

#   Execute the query
$reader = $queryProvider.ExecuteQuery($query, $crp)

# Do something with the result datatable, for example: print it formatted as a table, sorted by the 
# "Timestamp" column, in descending order
$dataTable = [Kusto.Cloud.Platform.Data.ExtendedDataReader]::ToDataSet($reader).Tables[0]
$dataView = New-Object System.Data.DataView($dataTable)
$dataView | Sort Timestamp -Descending | Format-Table -AutoSize
```