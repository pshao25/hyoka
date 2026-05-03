---
id: cosmos-db-mp-dotnet-gremlin-graph
properties:
  service: cosmos-db
  plane: management-plane
  language: dotnet
  category: crud
  difficulty: intermediate
  description: >
    Can a developer create and manage Gremlin API databases and graphs with
    indexing policies, unique keys, and conflict resolution using the
    Azure.ResourceManager.CosmosDB SDK?
  sdk_package: Azure.ResourceManager.CosmosDB
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager.cosmosdb-readme
  created: '2026-05-03'
  author: pashao
tags:
- gremlin-api
- graph
- unique-keys
- conflict-resolution
---

# Gremlin Database and Graph: Azure Cosmos DB (.NET)

## Prompt

Write a C# console application that manages Gremlin API databases and graphs
in Azure Cosmos DB using the Azure.ResourceManager.CosmosDB SDK:
1. Authenticate using DefaultAzureCredential and create an ArmClient
2. Create a Cosmos DB account with the Gremlin capability enabled
3. Create a Gremlin database with manual throughput of 400 RU/s
4. Create a Gremlin graph in the database with:
   - A partition key on `/pk`
   - An indexing policy with consistent mode, included path `/*`, and excluded path `/myPathToNotIndex/*`
   - A composite index with two paths (one ascending, one descending)
   - A unique key policy with a unique key on `/myUniqueKey`
   - A conflict resolution policy using LastWriterWins with path `/_ts`
   - Default TTL of -1 (off, but TTL-enabled)
5. List all Gremlin databases and graphs
6. Get a specific graph by name
7. Update the graph throughput
8. Delete the graph and database

Show required NuGet packages and demonstrate proper configuration of
capabilities, indexing policies, unique keys, and conflict resolution.

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager.CosmosDB` and `Azure.Identity` NuGet packages
- `CosmosDBAccountCreateOrUpdateContent` with `Capabilities` including `new CosmosDBAccountCapability { Name = "EnableGremlin" }`
- `GremlinDatabaseCreateOrUpdateContent` with `GremlinDatabaseResourceInfo(name)` and throughput config
- `account.GetGremlinDatabases().CreateOrUpdateAsync(WaitUntil.Completed, name, content)` for database creation
- `GremlinGraphCreateOrUpdateContent` with `GremlinGraphResourceInfo` including:
  - `CosmosDBContainerPartitionKey` with path `/pk` and `CosmosDBPartitionKind("Hash")`
  - `CosmosDBIndexingPolicy` with `CosmosDBIndexingMode.Consistent`, included/excluded paths
  - `CosmosDBCompositePath` entries with ascending and descending orders
  - `CosmosDBUniqueKeyPolicy` with `CosmosDBUniqueKey` containing unique key paths
  - `ConflictResolutionPolicy` with `ConflictResolutionMode.LastWriterWins` and `ConflictResolutionPath`
  - Default TTL set to -1
- `database.GetGremlinGraphs().CreateOrUpdateAsync(WaitUntil.Completed, name, content)` for graph creation
- `await foreach` for listing resources via `GetAllAsync()`
- `DeleteAsync(WaitUntil.Completed)` for cleanup

## Context

The Gremlin API enables graph database scenarios in Cosmos DB. Graphs require
careful configuration of indexing policies, unique key constraints, and
conflict resolution strategies. This tests whether the generated code correctly
enables the Gremlin capability, constructs complex nested graph resource objects,
and manages the full lifecycle of Gremlin databases and graphs.
