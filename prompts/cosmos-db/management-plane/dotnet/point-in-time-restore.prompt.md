---
id: cosmos-db-mp-dotnet-point-in-time-restore
properties:
  service: cosmos-db
  plane: management-plane
  language: dotnet
  category: provisioning
  difficulty: advanced
  description: >
    Can a developer configure continuous backup and perform point-in-time
    restore of a Cosmos DB account using the Azure.ResourceManager.CosmosDB SDK?
  sdk_package: Azure.ResourceManager.CosmosDB
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager.cosmosdb-readme
  created: '2026-05-03'
  author: pashao
tags:
- backup
- restore
- continuous
- point-in-time
---

# Point-in-Time Restore: Azure Cosmos DB (.NET)

## Prompt

Write a C# console application that demonstrates continuous backup and
point-in-time restore for an Azure Cosmos DB account using the
Azure.ResourceManager.CosmosDB SDK:
1. Authenticate using DefaultAzureCredential and create an ArmClient
2. Create a Cosmos DB account with continuous backup enabled (7-day tier)
3. Create a SQL database and container in the account
4. List restorable database accounts in the subscription
5. Find the restorable account corresponding to our account
6. Restore the account to a new account using point-in-time restore with:
   - CreateMode set to Restore
   - RestoreMode set to PointInTime
   - RestoreTimestampInUtc set to a recent timestamp
   - RestoreSource set to the restorable account's ID
7. Verify the restored account exists

Show required NuGet packages and explain the relationship between
the source account, restorable account, and restored account.

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager.CosmosDB` and `Azure.Identity` NuGet packages
- Source account creation with `CosmosDBAccountCreateOrUpdateContent` including:
  - `BackupPolicy` set to `ContinuousModeBackupPolicy` (optionally with `ContinuousTier.Continuous7Days`)
- `subscription.GetRestorableCosmosDBAccounts().GetAllAsync()` or location-scoped listing for finding restorable accounts
- `CosmosDBAccountRestoreParameters` with:
  - `RestoreMode = "PointInTime"`
  - `RestoreTimestampInUtc` set to a `DateTime` within the backup window
  - `RestoreSource` set to the restorable account's resource ID string
- Restored account creation with `CosmosDBAccountCreateOrUpdateContent` including:
  - `CreateMode = CosmosDBAccountCreateMode.Restore`
  - `RestoreParameters` with the restore parameters above
- Proper `WaitUntil.Completed` for all long-running operations
- Verification of the restored account via `GetAsync()` or `ExistsAsync()`

## Context

Continuous backup with point-in-time restore is a key business continuity feature
in Cosmos DB. It allows restoring an account to any point within the retention
window (7 or 30 days). This tests whether the generated code correctly configures
continuous backup, navigates the restorable accounts API, and constructs the complex
restore parameters needed to create a restored copy of the account.
