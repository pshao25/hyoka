---
id: cosmos-db-mp-dotnet-mongodb-resources
properties:
  service: cosmos-db
  plane: management-plane
  language: dotnet
  category: crud
  difficulty: intermediate
  description: >
    Can a developer create and manage MongoDB API databases and collections
    with throughput settings using the Azure.ResourceManager.CosmosDB SDK?
  sdk_package: Azure.ResourceManager.CosmosDB
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager.cosmosdb-readme
  created: '2026-05-03'
  author: pashao
tags:
- mongodb-api
- database
- collection
- throughput
---

# MongoDB Database and Collection: Azure Cosmos DB (.NET)

## Prompt

Write a C# console application that manages MongoDB API databases and collections
in Azure Cosmos DB using the Azure.ResourceManager.CosmosDB SDK:
1. Authenticate using DefaultAzureCredential and create an ArmClient
2. Create a Cosmos DB account with MongoDB API (Kind = MongoDB)
3. Create a MongoDB database with manual throughput of 400 RU/s
4. List all MongoDB databases in the account
5. Get the database throughput setting
6. Create a MongoDB collection within the database with throughput of 400 RU/s
7. List all collections in the database
8. Get a specific collection by name
9. Update the collection throughput
10. Delete the collection and then the database

Show required NuGet packages and demonstrate how to configure a MongoDB-kind account.

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager.CosmosDB` and `Azure.Identity` NuGet packages
- `CosmosDBAccountCreateOrUpdateContent` with `Kind = CosmosDBAccountKind.MongoDB`
- `MongoDBDatabaseCreateOrUpdateContent` with `MongoDBDatabaseResourceInfo(name)` and `CosmosDBCreateUpdateConfig { Throughput = 400 }`
- `account.GetMongoDBDatabases().CreateOrUpdateAsync(WaitUntil.Completed, name, content)` for database creation
- `MongoDBCollectionCreateOrUpdateContent` with `MongoDBCollectionResourceInfo(name)` and throughput config
- `database.GetMongoDBCollections().CreateOrUpdateAsync(WaitUntil.Completed, name, content)` for collection creation
- `database.GetMongoDBDatabaseThroughputSetting().GetAsync()` for throughput retrieval
- `await foreach` for listing databases and collections via `GetAllAsync()`
- `DeleteAsync(WaitUntil.Completed)` for cleanup
- Proper `WaitUntil.Completed` on all long-running operations

## Context

The MongoDB API in Cosmos DB allows applications to use the MongoDB wire protocol
with Cosmos DB as the backend. This tests whether the generated code correctly
creates a MongoDB-kind account and manages databases and collections with the
MongoDB-specific resource types, which differ from the SQL API types.
