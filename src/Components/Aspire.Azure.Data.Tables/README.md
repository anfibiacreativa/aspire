# Aspire.Azure.Data.Tables library

Registers [TableServiceClient](https://learn.microsoft.com/dotnet/api/azure.data.tables.tableserviceclient) as a singleton in the DI container for connecting to Azure Table storage. Enables corresponding health check, logging and telemetry.

## Getting started

### Prerequisites

- Azure subscription - [create one for free](https://azure.microsoft.com/free/)
- An Azure storage account or Azure Cosmos DB database with Azure Table API specified. - [create a storage account](https://learn.microsoft.com/azure/storage/common/storage-account-create)

### Install the package

Install the Aspire Azure Table storage library with [NuGet][nuget]:

```dotnetcli
dotnet add package Aspire.Azure.Data.Tables
```

## Usage Example

In the `Program.cs` file of your project, call the `AddAzureTableService` extension to register a `TableServiceClient` for use via the dependency injection container.

```cs
builder.AddAzureTableService();
```

You can then retrieve the `TableServiceClient` instance using dependency injection. For example, to retrieve the cache from a Web API controller:

```cs
private readonly TableServiceClient _client;

public ProductsController(TableServiceClient client)
{
    _client = client;
}
```

See the [Azure.Data.Tables documentation](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/tables/Azure.Data.Tables/README.md) for examples on using the `TableServiceClient`.

## Configuration

The Aspire Azure Table storage library provides multiple options to configure the Azure Table connection based on the requirements and conventions of your project. Note that `ServiceUri` is a required option that must be supplied.

### Use configuration providers

The Azure Table storage library supports [Microsoft.Extensions.Configuration](https://learn.microsoft.com/dotnet/api/microsoft.extensions.configuration). It loads the `AzureDataTablesSettings` and `TableClientOptions` from configuration by using the `Aspire:Azure:Data:Tables` key. Example `appsettings.json` that configures some of the options:

```json
{
  "Aspire": {
    "Azure": {
      "Data": {
        "Tables": {
          "ServiceUri": "https://{account_name}.table.core.windows.net/",
          "HealthChecks": false,
          "Tracing": true,
          "ClientOptions": {
            "Diagnostics": {
              "ApplicationId": "myapp"
            }
          }
        }
      }
    }
  }
}
```

### Use inline delegates

You can also pass the `Action<AzureDataTablesSettings>` delegate to set up some or all the options inline, for example to set the `ServiceUri`:

```cs
    builder.AddAzureTableService(settings => settings.ServiceUri = new Uri("https://{account_name}.table.core.windows.net/"));
```

You can also setup the [TableClientOptions](https://learn.microsoft.com/dotnet/api/azure.data.tables.tableclientoptions) using `Action<IAzureClientBuilder<TableServiceClient, TableClientOptions>>` delegate, the second parameter of the `AddAzureTableService` method. For example, to set the first part of "User-Agent" headers for all requests issues by this client:

```cs
    builder.AddAzureTableService(configureClientBuilder: clientBuilder => clientBuilder.ConfigureOptions(options => options.Diagnostics.ApplicationId = "myapp"));
```

## Additional documentation

* https://github.com/dotnet/astra/tree/main/src/Components/README.md
* https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/tables/Azure.Data.Tables/README.md

## Feedback & Contributing

https://github.com/dotnet/astra