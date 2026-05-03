---
id: cosmos-db-mp-dotnet-throughput-management
properties:
  service: cosmos-db
  plane: management-plane
  language: dotnet
  category: provisioning
  difficulty: intermediate
  description: >
    Can a developer read, update, and migrate throughput settings between
    manual and autoscale modes on Cosmos DB SQL databases and containers
    using the Azure.ResourceManager.CosmosDB SDK?
  sdk_package: Azure.ResourceManager.CosmosDB
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager.cosmosdb-readme
  created: '2026-05-03'
  author: pashao
tags:
- throughput
- autoscale
- manual
- ru
---

# Throughput Management: Azure Cosmos DB (.NET)

## Prompt

Write a C# console application that manages throughput settings on Azure Cosmos DB
SQL databases and containers using the Azure.ResourceManager.CosmosDB SDK:
1. Authenticate using DefaultAzureCredential and create an ArmClient
2. Get an existing Cosmos DB account (or create one)
3. Create a SQL database with manual throughput of 400 RU/s
4. Get the current throughput setting on the database
5. Update the database throughput to 800 RU/s
6. Migrate the database throughput from manual to autoscale
7. Migrate the database throughput back from autoscale to manual
8. Create a SQL container with manual throughput of 400 RU/s
9. Get the container throughput setting
10. Update the container throughput to 600 RU/s
11. Migrate the container to autoscale and back to manual
12. Clean up resources

Show required NuGet packages and demonstrate all throughput operations
including the migration between manual and autoscale modes.

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager.CosmosDB` and `Azure.Identity` NuGet packages
- `CosmosDBSqlDatabaseCreateOrUpdateContent` with `CosmosDBCreateUpdateConfig { Throughput = 400 }` for initial manual throughput
- `database.GetCosmosDBSqlDatabaseThroughputSetting().GetAsync()` for reading throughput
- `ThroughputSettingsUpdateData` with `ThroughputSettingsResourceInfo` containing new throughput value for updates
- `database.GetCosmosDBSqlDatabaseThroughputSetting().CreateOrUpdateAsync(WaitUntil.Completed, updateData)` for throughput update
- `database.MigrateSqlDatabaseToAutoscaleAsync(WaitUntil.Completed)` for manual-to-autoscale migration
- `database.MigrateSqlDatabaseToManualThroughputAsync(WaitUntil.Completed)` for autoscale-to-manual migration
- Equivalent container throughput operations:
  - `container.GetCosmosDBSqlContainerThroughputSetting().GetAsync()`
  - `container.GetCosmosDBSqlContainerThroughputSetting().CreateOrUpdateAsync()`
  - `container.MigrateSqlContainerToAutoscaleAsync(WaitUntil.Completed)`
  - `container.MigrateSqlContainerToManualThroughputAsync(WaitUntil.Completed)`
- Proper `WaitUntil.Completed` on all long-running operations

## Context

Throughput management is critical for cost optimization in Cosmos DB. Developers
need to understand how to provision, read, update, and migrate throughput between
manual and autoscale modes at both the database and container level. This tests
whether the generated code correctly uses the throughput setting sub-resources
and the dedicated migration methods.
