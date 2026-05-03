---
id: resource-manager-mp-dotnet-tag-operations
properties:
  service: resource-manager
  plane: management-plane
  language: dotnet
  category: crud
  difficulty: basic
  description: >
    Can a developer perform comprehensive tag operations on Azure resources — add individual tags,
    replace all tags, and remove specific tags — using the Azure.ResourceManager SDK?
  sdk_package: Azure.ResourceManager
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager-readme
  created: '2026-05-02'
  author: pashao
tags:
- tags
- resource-groups
- metadata
---

# Tag Operations: Azure Resource Manager (.NET)

## Prompt

Write a C# program that demonstrates comprehensive tag management on Azure resources
using the Azure.ResourceManager SDK:
1. Authenticate using DefaultAzureCredential
2. Create a resource group with initial tags {"env": "dev", "team": "backend"}
3. Add a single tag to the resource group using AddTagAsync (should not remove existing tags)
4. Replace all tags on the resource group using SetTagsAsync with a new set of tags
5. Remove a specific tag by key using RemoveTagAsync
6. Display the current tags after each operation to show the effect

Show the difference between AddTagAsync (adds/updates one tag), SetTagsAsync (replaces all tags),
and RemoveTagAsync (removes one tag by key).

Show required NuGet packages and proper error handling.
Use the Azure.ResourceManager SDK (not the older Microsoft.Azure.Management packages).

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager` NuGet package
- `ArmClient` creation with `DefaultAzureCredential`
- Resource group creation with initial tags via `ResourceGroupData`
- `resourceGroup.AddTagAsync(key, value)` to add/update a single tag
- `resourceGroup.SetTagsAsync(dictionary)` to replace all tags
- `resourceGroup.RemoveTagAsync(key)` to remove a tag by key
- Display of `resourceGroup.Data.Tags` after each operation
- Proper understanding that AddTag is additive while SetTags is a full replacement

## Context

Tag management is essential for Azure resource organization, cost tracking, and governance.
The Azure.ResourceManager SDK provides three distinct tag operations with different semantics
(add, replace all, remove). This tests whether the generated code correctly distinguishes
between these operations and demonstrates their effects on the resource's tag collection.
