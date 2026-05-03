---
id: cosmos-db-mp-dotnet-sql-database-container
properties:
  service: cosmos-db
  plane: management-plane
  language: dotnet
  category: crud
  difficulty: intermediate
  description: >
    Can a developer create SQL API databases and containers with partition keys,
    indexing policies, and throughput settings using the Azure.ResourceManager.CosmosDB SDK?
  sdk_package: Azure.ResourceManager.CosmosDB
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager.cosmosdb-readme
  created: '2026-05-03'
  author: pashao
tags:
- sql-api
- container
- indexing-policy
- partition-key
---

# SQL Database and Container: Azure Cosmos DB (.NET)

## Prompt

Write a C# console application that creates and manages SQL API databases and
containers in Azure Cosmos DB using the Azure.ResourceManager.CosmosDB SDK:
1. Authenticate using DefaultAzureCredential and create an ArmClient
2. Get an existing Cosmos DB account (or create one)
3. Create a SQL database with manual throughput of 400 RU/s
4. Create a SQL container within the database with:
   - A hash partition key on `/address/zipCode`
   - An indexing policy with consistent indexing mode
   - Included path `/*` and excluded path `/pathToNotIndex/*`
   - A composite index with ascending and descending sort orders
   - Manual throughput of 400 RU/s
5. List all SQL databases in the account
6. List all containers in the database
7. Get a specific container by name
8. Update the container throughput
9. Delete the container and then the database

Show required NuGet packages and demonstrate proper construction of indexing policies.

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager.CosmosDB` and `Azure.Identity` NuGet packages
- `CosmosDBSqlDatabaseCreateOrUpdateContent` with `CosmosDBSqlDatabaseResourceInfo(name)` and `CosmosDBCreateUpdateConfig` for throughput
- `account.GetCosmosDBSqlDatabases().CreateOrUpdateAsync(WaitUntil.Completed, name, content)` for database creation
- `CosmosDBSqlContainerCreateOrUpdateContent` with `CosmosDBSqlContainerResourceInfo` including:
  - `CosmosDBContainerPartitionKey` with path list and `CosmosDBPartitionKind("Hash")`
  - `CosmosDBIndexingPolicy` with `CosmosDBIndexingMode.Consistent`
  - `CosmosDBIncludedPath` and `CosmosDBExcludedPath` configuration
  - `CosmosDBCompositePath` items with `CompositePathSortOrder.Ascending` / `Descending`
- `database.GetCosmosDBSqlContainers().CreateOrUpdateAsync(WaitUntil.Completed, name, content)` for container creation
- `await foreach` for listing databases and containers via `GetAllAsync()`
- `container.DeleteAsync(WaitUntil.Completed)` for cleanup
- Proper `WaitUntil.Completed` on all long-running operations

## Context

SQL API databases and containers are the primary resource types in Cosmos DB.
Containers require careful configuration of partition keys and indexing policies
for optimal performance. This tests whether the generated code correctly constructs
complex nested objects like `CosmosDBIndexingPolicy` with composite indexes and
understands the relationship between databases, containers, and throughput settings.
