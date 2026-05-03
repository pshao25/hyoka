---
id: app-configuration-mp-dotnet-store-crud
properties:
  service: app-configuration
  plane: management-plane
  language: dotnet
  category: crud
  difficulty: basic
  description: >
    Can a developer create, list, get, update, and delete Azure App Configuration stores
    using the Azure.ResourceManager.AppConfiguration SDK?
  sdk_package: Azure.ResourceManager.AppConfiguration
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager.appconfiguration-readme
  created: '2026-05-03'
  author: pashao
tags:
- configuration-store
- management-plane
- getting-started
---

# Configuration Store CRUD: Azure App Configuration (.NET)

## Prompt

Write a C# console application that manages Azure App Configuration stores
using the Azure.ResourceManager.AppConfiguration SDK:
1. Authenticate using DefaultAzureCredential and create an ArmClient
2. Create a resource group for the configuration stores
3. Create a new App Configuration store with Standard SKU and public network access disabled
4. List all configuration stores in the resource group
5. Get a specific configuration store by name
6. Update the store to enable public network access
7. Add tags to the configuration store
8. Delete the configuration store

Show required NuGet packages and proper error handling.
Use the Azure.ResourceManager.AppConfiguration SDK.

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager.AppConfiguration` and `Azure.Identity` NuGet packages
- `ArmClient` creation with `DefaultAzureCredential`
- `AppConfigurationStoreData` construction with location, `AppConfigurationSku("Standard")`, and `PublicNetworkAccess`
- `resourceGroup.GetAppConfigurationStores().CreateOrUpdateAsync(WaitUntil.Completed, name, data)` for creation
- `resourceGroup.GetAppConfigurationStores().GetAllAsync()` for listing with `await foreach`
- `resourceGroup.GetAppConfigurationStores().GetAsync(name)` for getting by name
- Update via modifying `Data` and calling `CreateOrUpdateAsync` again
- `configStore.AddTagAsync(key, value)` or `SetTagsAsync(tags)` for tagging
- `configStore.DeleteAsync(WaitUntil.Completed)` for deletion
- Proper `WaitUntil` usage for long-running operations

## Context

App Configuration store management is the foundation for using Azure App Configuration.
This tests whether the generated code correctly creates and manages configuration stores
using the management plane SDK, including SKU selection, network access configuration,
and the full CRUD lifecycle.
