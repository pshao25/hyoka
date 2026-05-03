---
id: resource-manager-mp-dotnet-lro-patterns
properties:
  service: resource-manager
  plane: management-plane
  language: dotnet
  category: polling
  difficulty: advanced
  description: >
    Can a developer correctly use long-running operation (LRO) patterns including
    WaitUntil.Started vs WaitUntil.Completed, operation rehydration, and template export?
  sdk_package: Azure.ResourceManager
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager-readme
  created: '2026-05-02'
  author: pashao
tags:
- lro
- polling
- rehydration
- async
---

# Long-Running Operation Patterns: Azure Resource Manager (.NET)

## Prompt

Write a C# program that demonstrates long-running operation (LRO) patterns with the
Azure.ResourceManager SDK:
1. Authenticate using DefaultAzureCredential
2. Create a resource group using WaitUntil.Completed (blocks until done)
3. Create another resource group using WaitUntil.Started (returns immediately)
4. Poll for completion manually after a WaitUntil.Started operation using WaitForCompletionAsync
5. Demonstrate LRO rehydration: save the rehydration token from an in-progress operation,
   then use ArmOperation.Rehydrate to resume tracking the operation from a new client
6. Export a resource group template using ExportTemplateAsync (another LRO)
7. Delete a resource group using WaitUntil.Completed

Show the difference between WaitUntil.Started (fire-and-forget with manual polling)
and WaitUntil.Completed (blocking wait). Demonstrate how rehydration tokens allow
operation recovery across process restarts.

Show required NuGet packages and proper error handling.
Use the Azure.ResourceManager SDK (not the older Microsoft.Azure.Management packages).

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager` NuGet package
- `ArmClient` creation with `DefaultAzureCredential`
- `CreateOrUpdateAsync(WaitUntil.Completed, ...)` for blocking creation
- `CreateOrUpdateAsync(WaitUntil.Started, ...)` for non-blocking creation
- `operation.WaitForCompletionAsync()` or `WaitForCompletionResponseAsync()` for manual polling
- `operation.GetRehydrationToken()` to save operation state
- `ArmOperation.Rehydrate<ResourceGroupResource>(client, token)` to resume an operation
- `resourceGroup.ExportTemplateAsync(WaitUntil.Completed, ...)` for template export
- `resourceGroup.DeleteAsync(WaitUntil.Completed)` for deletion
- Proper understanding of WaitUntil.Started vs WaitUntil.Completed semantics

## Context

Long-running operations are fundamental to Azure management plane. Many operations
(creating resources, deleting resource groups, exporting templates) are asynchronous
and can take minutes. The Azure.ResourceManager SDK provides WaitUntil semantics and
LRO rehydration for operation recovery. This tests advanced async patterns that are
critical for production-quality Azure management code.
