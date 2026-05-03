---
id: cosmos-db-mp-dotnet-sql-server-side-logic
properties:
  service: cosmos-db
  plane: management-plane
  language: dotnet
  category: crud
  difficulty: intermediate
  description: >
    Can a developer create and manage SQL stored procedures, triggers, and
    user-defined functions using the Azure.ResourceManager.CosmosDB SDK?
  sdk_package: Azure.ResourceManager.CosmosDB
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager.cosmosdb-readme
  created: '2026-05-03'
  author: pashao
tags:
- sql-api
- stored-procedure
- trigger
- udf
- server-side
---

# SQL Server-Side Logic: Azure Cosmos DB (.NET)

## Prompt

Write a C# console application that manages server-side programmability resources
(stored procedures, triggers, and user-defined functions) in an Azure Cosmos DB
SQL container using the Azure.ResourceManager.CosmosDB SDK:
1. Authenticate using DefaultAzureCredential and create an ArmClient
2. Get an existing Cosmos DB account with a SQL database and container
3. Create a stored procedure with a JavaScript body that reads and returns a greeting
4. List all stored procedures in the container
5. Update the stored procedure body
6. Create a pre-trigger with TriggerOperation set to All and a JavaScript body
   that validates documents before insertion
7. List all triggers in the container
8. Create a user-defined function with a JavaScript body that performs
   a simple transformation (e.g., converts a string to uppercase)
9. List all UDFs in the container
10. Delete the stored procedure, trigger, and UDF

Show required NuGet packages and demonstrate proper JavaScript body strings for each resource type.

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager.CosmosDB` and `Azure.Identity` NuGet packages
- `CosmosDBSqlStoredProcedureCreateOrUpdateContent` with `CosmosDBSqlStoredProcedureResourceInfo` including a `Body` property with JavaScript code
- `container.GetCosmosDBSqlStoredProcedures().CreateOrUpdateAsync(WaitUntil.Completed, name, content)` for stored procedure creation
- `CosmosDBSqlTriggerCreateOrUpdateContent` with `CosmosDBSqlTriggerResourceInfo` including:
  - `TriggerOperation = CosmosDBSqlTriggerOperation.All`
  - `TriggerType = CosmosDBSqlTriggerType.Pre`
  - `Body` with JavaScript code
- `container.GetCosmosDBSqlTriggers().CreateOrUpdateAsync(WaitUntil.Completed, name, content)` for trigger creation
- `CosmosDBSqlUserDefinedFunctionCreateOrUpdateContent` with `CosmosDBSqlUserDefinedFunctionResourceInfo` including `Body`
- `container.GetCosmosDBSqlUserDefinedFunctions().CreateOrUpdateAsync(WaitUntil.Completed, name, content)` for UDF creation
- `await foreach` for listing each resource type via `GetAllAsync()`
- `DeleteAsync(WaitUntil.Completed)` for cleanup of each resource

## Context

Server-side programmability in Cosmos DB enables executing JavaScript logic
directly in the database engine. Stored procedures provide transactional execution,
pre-triggers validate documents before writes, and UDFs extend the query language.
This tests whether the generated code correctly sets up all three resource types
with appropriate JavaScript bodies and trigger configuration.
