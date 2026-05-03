---
id: app-configuration-mp-dotnet-deleted-store-recovery
properties:
  service: app-configuration
  plane: management-plane
  language: dotnet
  category: crud
  difficulty: intermediate
  description: >
    Can a developer list, inspect, and purge soft-deleted Azure App Configuration stores
    using the Azure.ResourceManager.AppConfiguration SDK?
  sdk_package: Azure.ResourceManager.AppConfiguration
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager.appconfiguration-readme
  created: '2026-05-03'
  author: pashao
tags:
- soft-delete
- purge
- deleted-store
---

# Deleted Store Recovery: Azure App Configuration (.NET)

## Prompt

Write a C# console application that demonstrates how to work with soft-deleted
Azure App Configuration stores using the Azure.ResourceManager.AppConfiguration SDK:
1. Authenticate using DefaultAzureCredential and create an ArmClient
2. Get a subscription resource
3. List all soft-deleted App Configuration stores in the subscription
4. Get details of a specific deleted configuration store by location and name
5. Purge a soft-deleted configuration store permanently

Show required NuGet packages, proper error handling, and explain
when purging is appropriate versus waiting for automatic cleanup.

## Evaluation Criteria

The generated code should include:
- `ArmClient` with `DefaultAzureCredential`
- `subscription.GetDeletedAppConfigurationStores().GetAllAsync()` for listing deleted stores with `await foreach`
- `subscription.GetDeletedAppConfigurationStores().GetAsync(location, storeName)` for getting a specific deleted store
- `deletedStore.PurgeDeletedAsync(WaitUntil.Completed)` for permanent purge
- Proper `WaitUntil.Completed` usage on the purge operation
- Error handling for stores that do not exist (e.g., 404 / `RequestFailedException`)
- Comments or explanation distinguishing soft-delete recovery from permanent purge

## Context

Azure App Configuration stores support soft-delete by default. After deletion,
stores can be listed, inspected, and either recovered or permanently purged.
This prompt tests the developer's ability to navigate the `DeletedAppConfigurationStore`
resource type and perform purge operations, which is important for subscription cleanup
and compliance scenarios.
