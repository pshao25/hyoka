---
id: cosmos-db-mp-dotnet-cassandra-resources
properties:
  service: cosmos-db
  plane: management-plane
  language: dotnet
  category: crud
  difficulty: intermediate
  description: >
    Can a developer create and manage Cassandra API keyspaces and tables
    with column schemas, partition keys, and cluster keys using the
    Azure.ResourceManager.CosmosDB SDK?
  sdk_package: Azure.ResourceManager.CosmosDB
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager.cosmosdb-readme
  created: '2026-05-03'
  author: pashao
tags:
- cassandra-api
- keyspace
- table
- schema
---

# Cassandra Keyspace and Table: Azure Cosmos DB (.NET)

## Prompt

Write a C# console application that manages Cassandra API keyspaces and tables
in Azure Cosmos DB using the Azure.ResourceManager.CosmosDB SDK:
1. Authenticate using DefaultAzureCredential and create an ArmClient
2. Create a Cosmos DB account with the Cassandra capability enabled
3. Create a Cassandra keyspace with manual throughput of 400 RU/s
4. Create a Cassandra table in the keyspace with:
   - A schema defining columns: `columnA` (int) and `columnB` (ascii)
   - A partition key on `columnA`
   - A cluster key on `columnB` with ascending order
   - Manual throughput of 400 RU/s
5. List all keyspaces in the account
6. List all tables in the keyspace
7. Get a specific table by name
8. Get and update the table throughput
9. Delete the table and then the keyspace

Show required NuGet packages and demonstrate proper Cassandra schema construction
with columns, partition keys, and cluster keys.

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager.CosmosDB` and `Azure.Identity` NuGet packages
- `CosmosDBAccountCreateOrUpdateContent` with `Capabilities` including `new CosmosDBAccountCapability { Name = "EnableCassandra" }`
- `CassandraKeyspaceCreateOrUpdateContent` with `CassandraKeyspaceResourceInfo(name)` and `CosmosDBCreateUpdateConfig { Throughput = 400 }`
- `account.GetCassandraKeyspaces().CreateOrUpdateAsync(WaitUntil.Completed, name, content)` for keyspace creation
- `CassandraTableCreateOrUpdateContent` with `CassandraTableResourceInfo` including a `CassandraSchema` with:
  - `CassandraColumn` entries with `Name` and `CassandraColumnType` (e.g., "int", "ascii")
  - `CassandraPartitionKey` with `Name` matching a column
  - `CassandraClusterKey` with `Name` and `OrderBy` ("Asc" or "Desc")
- `keyspace.GetCassandraTables().CreateOrUpdateAsync(WaitUntil.Completed, name, content)` for table creation
- `table.GetCassandraTableThroughputSetting().GetAsync()` for throughput retrieval
- `await foreach` for listing keyspaces and tables via `GetAllAsync()`
- `DeleteAsync(WaitUntil.Completed)` for cleanup

## Context

The Cassandra API in Cosmos DB allows wide-column workloads using the CQL wire protocol.
Tables require explicit schema definitions with typed columns, partition keys,
and optional cluster keys for sort order. This tests whether the generated code
correctly enables the Cassandra capability, constructs the schema objects, and
manages the full lifecycle of keyspaces and tables.
