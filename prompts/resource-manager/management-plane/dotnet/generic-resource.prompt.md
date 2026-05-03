---
id: resource-manager-mp-dotnet-generic-resource
properties:
  service: resource-manager
  plane: management-plane
  language: dotnet
  category: crud
  difficulty: advanced
  description: >
    Can a developer use the generic resource abstraction to discover, list, and manage
    Azure resources across different types without type-specific SDK packages?
  sdk_package: Azure.ResourceManager
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager-readme
  created: '2026-05-02'
  author: pashao
tags:
- generic-resource
- resource-discovery
- cross-type
---

# Generic Resource Operations: Azure Resource Manager (.NET)

## Prompt

Write a C# program that demonstrates generic resource operations using the
Azure.ResourceManager SDK. Generic resources allow you to work with any Azure resource
type without importing service-specific SDK packages:
1. Authenticate using DefaultAzureCredential
2. List all resources in the subscription using GetGenericResourcesAsync
3. List resources with expanded properties (e.g., expand "createdTime" to see creation timestamps)
4. List all resources within a specific resource group using GetGenericResourcesAsync on the resource group
5. Get a specific generic resource by its ResourceIdentifier
6. Add a tag to a generic resource using AddTagAsync
7. Delete a generic resource

Show how to use the expand parameter to retrieve additional properties like
createdTime, changedTime, and provisioningState.

Show required NuGet packages and proper error handling (including handling 404 for non-existent resources).
Use the Azure.ResourceManager SDK (not the older Microsoft.Azure.Management packages).

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager` NuGet package
- `ArmClient` creation with `DefaultAzureCredential`
- `subscription.GetGenericResourcesAsync()` for listing all resources in a subscription
- `subscription.GetGenericResourcesAsync(expand: "createdTime")` for expanded listing
- `rg.GetGenericResourcesAsync()` for listing resources in a specific resource group
- `Client.GetGenericResources().GetAsync(resourceId)` for getting by ResourceIdentifier
- `genericResource.AddTagAsync(key, value)` for tagging
- `genericResource.DeleteAsync(WaitUntil.Completed)` for deletion
- Access to `genericResource.Data.CreatedOn`, `ChangedOn`, `ProvisioningState` from expanded data
- Proper `await foreach` for async enumeration
- Error handling for `RequestFailedException` with 404 status

## Context

The generic resource abstraction is a powerful feature of Azure.ResourceManager that enables
type-agnostic resource discovery and management. It allows developers to list and manipulate
resources across different providers without importing each service-specific SDK. This is
particularly useful for resource inventory, cross-cutting governance, and tooling scenarios.
