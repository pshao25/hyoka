---
id: resource-manager-mp-dotnet-management-lock
properties:
  service: resource-manager
  plane: management-plane
  language: dotnet
  category: crud
  difficulty: intermediate
  description: >
    Can a developer create, list, get, and delete management locks at multiple scopes
    (subscription, resource group, and individual resource) using the Azure.ResourceManager SDK?
  sdk_package: Azure.ResourceManager
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager-readme
  created: '2026-05-02'
  author: pashao
tags:
- management-lock
- governance
- multi-scope
---

# Management Lock Operations: Azure Resource Manager (.NET)

## Prompt

Write a C# program that manages Azure management locks at multiple scopes
using the Azure.ResourceManager SDK:
1. Authenticate using DefaultAzureCredential
2. Create a CanNotDelete lock on a resource group
3. Create a ReadOnly lock on a subscription
4. List all management locks for a resource group
5. Get a specific lock by name
6. Delete a management lock

Show how locks can be applied at different scopes (subscription and resource group).
Include lock level (CanNotDelete, ReadOnly) and notes in the lock data.

Show required NuGet packages and proper error handling.
Use the Azure.ResourceManager SDK (not the older Microsoft.Azure.Management packages).

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager` NuGet package
- `ArmClient` creation with `DefaultAzureCredential`
- `rg.GetManagementLocks()` and `subscription.GetManagementLocks()` for multi-scope access
- `ManagementLockData` construction with `Level` and `Notes`
- `.CreateOrUpdateAsync(WaitUntil.Completed, name, data)` for creation
- `.GetAllAsync()` for listing locks with `await foreach`
- `.GetAsync(name)` for getting by name
- `.DeleteAsync(WaitUntil.Completed)` for deletion
- Use of `ManagementLockLevel.CanNotDelete` and `ManagementLockLevel.ReadOnly`

## Context

Management locks prevent accidental deletion or modification of Azure resources.
They can be applied at subscription, resource group, or individual resource scopes.
This tests whether the generated code correctly uses the multi-scope lock collection
pattern and properly configures lock levels and notes.
