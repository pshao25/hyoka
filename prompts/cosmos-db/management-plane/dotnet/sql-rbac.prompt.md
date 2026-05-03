---
id: cosmos-db-mp-dotnet-sql-rbac
properties:
  service: cosmos-db
  plane: management-plane
  language: dotnet
  category: auth
  difficulty: advanced
  description: >
    Can a developer create and manage SQL RBAC role definitions and role
    assignments for fine-grained data plane access control using the
    Azure.ResourceManager.CosmosDB SDK?
  sdk_package: Azure.ResourceManager.CosmosDB
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager.cosmosdb-readme
  created: '2026-05-03'
  author: pashao
tags:
- rbac
- role-definition
- role-assignment
- security
---

# SQL RBAC Role Definitions and Assignments: Azure Cosmos DB (.NET)

## Prompt

Write a C# console application that sets up SQL role-based access control (RBAC)
for an Azure Cosmos DB account using the Azure.ResourceManager.CosmosDB SDK:
1. Authenticate using DefaultAzureCredential and create an ArmClient
2. Get an existing Cosmos DB account with a SQL database
3. Create a custom SQL role definition with:
   - A descriptive role name
   - Type set to CustomRole
   - An assignable scope at the database level (e.g., `{accountId}/dbs/{dbName}`)
   - Permissions granting data actions for creating and reading container items
     (e.g., `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create`,
     `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read`)
4. List all SQL role definitions on the account
5. Update the role definition to add a delete data action
6. Create a SQL role assignment that:
   - References the created role definition
   - Assigns to a specific principal (service principal or user) by object ID
   - Scopes to the database level
7. List all SQL role assignments on the account
8. Delete the role assignment and then the role definition

Show required NuGet packages and demonstrate proper RBAC data action strings.

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager.CosmosDB` and `Azure.Identity` NuGet packages
- `CosmosDBSqlRoleDefinitionCreateOrUpdateContent` with:
  - `RoleName` set to a descriptive name
  - `RoleDefinitionType = CosmosDBSqlRoleDefinitionType.CustomRole`
  - `AssignableScopes` containing database-level or account-level scope strings
  - `Permissions` with `CosmosDBSqlRolePermission` containing `DataActions` list
- `account.GetCosmosDBSqlRoleDefinitions().CreateOrUpdateAsync(WaitUntil.Completed, roleDefinitionId, content)` where `roleDefinitionId` is a GUID string
- `CosmosDBSqlRoleAssignmentCreateOrUpdateContent` with:
  - `RoleDefinitionId` referencing the definition's resource ID
  - `Scope` at database or account level
  - `PrincipalId` set to a Guid
- `account.GetCosmosDBSqlRoleAssignments().CreateOrUpdateAsync(WaitUntil.Completed, roleAssignmentId, content)` where `roleAssignmentId` is a GUID string
- `await foreach` for listing role definitions and assignments via `GetAllAsync()`
- `DeleteAsync(WaitUntil.Completed)` for cleanup in correct order (assignment before definition)

## Context

SQL RBAC in Cosmos DB enables fine-grained access control at the data plane level,
allowing developers to define custom roles with specific data actions and assign
them to Azure AD principals. This is critical for zero-trust security architectures.
This tests whether the generated code correctly constructs role definitions with
proper data action strings, creates scoped assignments, and handles the dependency
order during cleanup.
