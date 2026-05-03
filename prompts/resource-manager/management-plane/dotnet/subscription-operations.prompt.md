---
id: resource-manager-mp-dotnet-subscription-ops
properties:
  service: resource-manager
  plane: management-plane
  language: dotnet
  category: crud
  difficulty: basic
  description: >
    Can a developer list subscriptions, get subscription details, check subscription existence,
    and enumerate available locations using the Azure.ResourceManager SDK?
  sdk_package: Azure.ResourceManager
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager-readme
  created: '2026-05-02'
  author: pashao
tags:
- subscription
- locations
- getting-started
---

# Subscription Operations: Azure Resource Manager (.NET)

## Prompt

Write a C# console application that demonstrates subscription-level operations
using the Azure.ResourceManager SDK:
1. Authenticate using DefaultAzureCredential and create an ArmClient
2. List all subscriptions the authenticated user has access to
3. Get the default subscription details (display name, subscription ID, state)
4. Get a specific subscription by its subscription ID
5. Check if a subscription exists using ExistsAsync
6. List all available locations for the default subscription

Show required NuGet packages and use async/await throughout.
Use the Azure.ResourceManager SDK (not the older Microsoft.Azure.Management packages).

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager` and `Azure.Identity` NuGet packages
- `ArmClient` creation with `DefaultAzureCredential`
- `Client.GetSubscriptions().GetAllAsync()` for listing subscriptions
- `Client.GetDefaultSubscriptionAsync()` for default subscription
- `Client.GetSubscriptions().GetAsync(subscriptionId)` for get by ID
- `Client.GetSubscriptions().ExistsAsync(subscriptionId)` for existence check
- `subscription.GetLocationsAsync()` for listing locations
- Proper async enumeration with `await foreach`

## Context

Subscription operations are fundamental to Azure management plane development.
This tests whether the generated code correctly uses the ArmClient to navigate
the subscription hierarchy and enumerate locations, which are prerequisites
for all resource provisioning tasks.
