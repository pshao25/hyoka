---
id: resource-manager-mp-dotnet-policy-assignment
properties:
  service: resource-manager
  plane: management-plane
  language: dotnet
  category: crud
  difficulty: intermediate
  description: >
    Can a developer create, list, get, filter, and delete Azure Policy assignments
    at different scopes (subscription and resource group) using the Azure.ResourceManager SDK?
  sdk_package: Azure.ResourceManager
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager-readme
  created: '2026-05-02'
  author: pashao
tags:
- policy
- authorization
- governance
- multi-scope
---

# Policy Assignment Management: Azure Resource Manager (.NET)

## Prompt

Write a C# program that manages Azure Policy assignments at multiple scopes
using the Azure.ResourceManager SDK:
1. Authenticate using DefaultAzureCredential
2. Create a policy assignment at the subscription level using a built-in policy definition
3. Create a policy assignment at a resource group level
4. List all policy assignments for a resource group
5. Filter policy assignments by policy definition ID
6. Get a specific policy assignment by name
7. Delete a policy assignment

Use a built-in policy definition ID such as "/providers/Microsoft.Authorization/policyDefinitions/0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56"
(Audit VMs that do not use managed disks) for demonstration.

Show required NuGet packages and proper error handling.
Use the Azure.ResourceManager SDK (not the older Microsoft.Azure.Management packages).

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager` NuGet package
- `ArmClient` creation with `DefaultAzureCredential`
- `subscription.GetPolicyAssignments()` and `rg.GetPolicyAssignments()` for multi-scope access
- `PolicyAssignmentData` construction with `PolicyDefinitionId`
- `.CreateOrUpdateAsync(WaitUntil.Completed, name, data)` for creation
- `.GetAllAsync(filter)` for filtering by policy definition ID
- `.GetAsync(name)` for getting by name
- `.DeleteAsync(WaitUntil.Completed)` for deletion
- Proper `WaitUntil` usage for long-running operations

## Context

Policy assignments are a key governance feature in Azure. They can be applied
at multiple scopes (management group, subscription, resource group, individual resource).
This tests whether the generated code correctly uses the multi-scope collection pattern
and handles the policy assignment lifecycle with proper filtering support.
