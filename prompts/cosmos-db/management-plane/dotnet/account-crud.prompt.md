---
id: cosmos-db-mp-dotnet-account-crud
properties:
  service: cosmos-db
  plane: management-plane
  language: dotnet
  category: crud
  difficulty: basic
  description: >
    Can a developer create, configure, list, and delete Azure Cosmos DB accounts
    using the Azure.ResourceManager.CosmosDB SDK, including consistency policies,
    failover configuration, and key management?
  sdk_package: Azure.ResourceManager.CosmosDB
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager.cosmosdb-readme
  created: '2026-05-03'
  author: pashao
tags:
- database-account
- management-plane
- getting-started
---

# Database Account CRUD: Azure Cosmos DB (.NET)

## Prompt

Write a C# console application that manages Azure Cosmos DB database accounts
using the Azure.ResourceManager.CosmosDB SDK:
1. Authenticate using DefaultAzureCredential and create an ArmClient
2. Create a resource group
3. Create a Cosmos DB account with the SQL API in West US with:
   - BoundedStaleness consistency policy
   - IP firewall rule allowing a specific IP address
   - Automatic failover disabled
4. Get the account by name and verify it exists
5. List all Cosmos DB accounts in the resource group
6. Retrieve the account keys (read-write and read-only)
7. Retrieve connection strings for the account
8. Regenerate the primary key
9. Update the account using a patch to change tags
10. Delete the account

Show required NuGet packages and proper async/await patterns.

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager.CosmosDB` and `Azure.Identity` NuGet packages
- `ArmClient` creation with `DefaultAzureCredential`
- `CosmosDBAccountCreateOrUpdateContent` with `AzureLocation.WestUS` and `CosmosDBAccountLocation` list
- `ConsistencyPolicy` set to `DefaultConsistencyLevel.BoundedStaleness` with `MaxStalenessPrefix` and `MaxIntervalInSeconds`
- `IPRules` configured with `CosmosDBIPAddressOrRange`
- `EnableAutomaticFailover = false`
- `resourceGroup.GetCosmosDBAccounts().CreateOrUpdateAsync(WaitUntil.Completed, name, content)` for creation
- `resourceGroup.GetCosmosDBAccounts().GetAsync(name)` or `ExistsAsync(name)` for retrieval
- `account.GetKeysAsync()` and `GetReadOnlyKeysAsync()` for key retrieval
- `account.GetConnectionStringsAsync()` for connection strings
- `account.RegenerateKeyAsync(WaitUntil.Completed, CosmosDBAccountRegenerateKeyContent)` for key regeneration
- `account.UpdateAsync(WaitUntil.Completed, CosmosDBAccountPatch)` for updating
- `account.DeleteAsync(WaitUntil.Completed)` for deletion
- Proper `WaitUntil.Completed` usage for all long-running operations

## Context

Cosmos DB account management is the foundation of using Azure Cosmos DB. This tests
whether the generated code correctly creates accounts with the appropriate consistency
model, IP security rules, and failover configuration, and performs the full lifecycle
of key management and CRUD operations.
