---
id: resource-manager-mp-dotnet-feature-registration
properties:
  service: resource-manager
  plane: management-plane
  language: dotnet
  category: provisioning
  difficulty: intermediate
  description: >
    Can a developer discover resource provider features, check their registration state,
    and register/unregister features using the Azure.ResourceManager SDK?
  sdk_package: Azure.ResourceManager
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager-readme
  created: '2026-05-02'
  author: pashao
tags:
- feature
- provider
- registration
---

# Feature Registration: Azure Resource Manager (.NET)

## Prompt

Write a C# program that manages resource provider features using the Azure.ResourceManager SDK:
1. Authenticate using DefaultAzureCredential
2. Get a resource provider by namespace (e.g., "Microsoft.Compute")
3. List all features for that provider
4. Get a specific feature by name (e.g., "AHUB")
5. Check if a feature exists using ExistsAsync
6. Register a feature and observe its state change to "Pending"
7. Unregister a feature and observe its state change to "Unregistering"

Display the feature name, registration state, and resource type for each feature.

Show required NuGet packages and proper error handling (including 404 for non-existent features).
Use the Azure.ResourceManager SDK (not the older Microsoft.Azure.Management packages).

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager` NuGet package
- `ArmClient` creation with `DefaultAzureCredential`
- `subscription.GetResourceProviders().GetAsync("Microsoft.Compute")` to get a provider
- `provider.GetFeatures().GetAllAsync()` for listing features
- `provider.GetFeatures().GetAsync(featureName)` for getting a feature
- `provider.GetFeatures().ExistsAsync(featureName)` for existence check
- `feature.RegisterAsync()` for registration
- `feature.UnregisterAsync()` for unregistration
- Access to `feature.Data.Properties.State` for registration state
- Proper async enumeration with `await foreach`

## Context

Resource provider features allow Azure users to opt in to preview or gated functionality.
Discovering and managing feature registration state is an important management plane operation.
This tests whether the generated code correctly navigates the provider → features hierarchy
and handles the feature registration lifecycle with proper state observation.
