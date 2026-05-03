---
id: app-configuration-mp-dotnet-private-endpoint
properties:
  service: app-configuration
  plane: management-plane
  language: dotnet
  category: provisioning
  difficulty: advanced
  description: >
    Can a developer configure private endpoint connections for an Azure App Configuration
    store using the Azure.ResourceManager.AppConfiguration SDK?
  sdk_package: Azure.ResourceManager.AppConfiguration
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager.appconfiguration-readme
  created: '2026-05-03'
  author: pashao
tags:
- private-endpoint
- networking
- vnet
---

# Private Endpoint Connection: Azure App Configuration (.NET)

## Prompt

Write a C# console application that sets up a private endpoint connection for an
Azure App Configuration store using the Azure.ResourceManager.AppConfiguration
and Azure.ResourceManager.Network SDKs:
1. Authenticate using DefaultAzureCredential and create an ArmClient
2. Create a resource group
3. Create a virtual network with a subnet (disable private endpoint network policies on the subnet)
4. Create an App Configuration store with Standard SKU
5. Create a private endpoint targeting the configuration store
6. List private endpoint connections on the configuration store
7. Update the private endpoint connection status (e.g., set description)

Show required NuGet packages for both App Configuration and Network resource management.

## Evaluation Criteria

The generated code should include:
- `Azure.ResourceManager.AppConfiguration`, `Azure.ResourceManager.Network`, and `Azure.Identity` NuGet packages
- `ArmClient` with `DefaultAzureCredential`
- Virtual network creation with `VirtualNetworkData` including a subnet
- Subnet configured with `PrivateEndpointNetworkPolicies` disabled
- `PrivateEndpointData` with a `PrivateLinkServiceConnection` targeting the App Configuration store resource ID
- `subnet.GetPrivateEndpoints().CreateOrUpdateAsync(WaitUntil.Completed, name, data)` for endpoint creation
- `configStore.GetAppConfigurationPrivateEndpointConnections()` to list connections
- Update connection state via `CreateOrUpdateAsync` with modified `AppConfigurationPrivateEndpointConnectionData`
- Proper `WaitUntil.Completed` for all long-running operations
- Resource cleanup or note about cleanup order (private endpoint before VNet)

## Context

Private endpoints allow Azure App Configuration stores to be accessed securely over
a private link within a virtual network, removing exposure to the public internet.
This is a common enterprise scenario that combines networking and App Configuration
resource management. It tests the developer's ability to orchestrate multiple resource
types across SDKs in a single coherent workflow.
