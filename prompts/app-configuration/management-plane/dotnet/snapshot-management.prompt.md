---
id: app-configuration-mp-dotnet-snapshot-management
properties:
  service: app-configuration
  plane: management-plane
  language: dotnet
  category: crud
  difficulty: intermediate
  description: >
    Can a developer create, list, and get snapshots of Azure App Configuration stores
    with key-value filters using the Azure.ResourceManager.AppConfiguration SDK?
  sdk_package: Azure.ResourceManager.AppConfiguration
  doc_url: https://learn.microsoft.com/en-us/dotnet/api/overview/azure/resourcemanager.appconfiguration-readme
  created: '2026-05-03'
  author: pashao
tags:
- snapshot
- key-value-filter
- retention
---

# Snapshot Management: Azure App Configuration (.NET)

## Prompt

Write a C# console application that manages snapshots of Azure App Configuration
stores using the Azure.ResourceManager.AppConfiguration SDK:
1. Authenticate using DefaultAzureCredential and create an ArmClient
2. Get or create a configuration store
3. Create key-values in the store using `AppConfigurationKeyValueResource`
4. Create a snapshot with key-value filters (e.g., filter by key prefix "app/*")
   and a retention period
5. List all snapshots in the store
6. Get a specific snapshot by name and verify it exists
7. Check the snapshot's provisioning state and item count

Show required NuGet packages and demonstrate how to set up
`SnapshotKeyValueFilter` objects for targeted snapshots.

## Evaluation Criteria

The generated code should include:
- `ArmClient` with `DefaultAzureCredential`
- `configStore.GetAppConfigurationKeyValues().CreateOrUpdateAsync()` to create key-values before snapshotting
- `AppConfigurationSnapshotData` construction with `SnapshotKeyValueFilter` items (e.g., `new SnapshotKeyValueFilter("app/*")`)
- Optional `RetentionPeriod` set on the snapshot data
- `configStore.GetAppConfigurationSnapshots().CreateOrUpdateAsync(WaitUntil.Completed, snapshotName, data)` for creation
- `configStore.GetAppConfigurationSnapshots().GetAsync(snapshotName)` for retrieval
- `configStore.GetAppConfigurationSnapshots().ExistsAsync(snapshotName)` for existence check
- Proper `WaitUntil.Completed` usage for the long-running snapshot creation
- `await foreach` for listing snapshots

## Context

Snapshots in Azure App Configuration capture a point-in-time view of key-value pairs
matching specified filters. They are useful for release pinning and audit trails.
This tests whether the generated code correctly constructs snapshot filters,
handles the asynchronous creation lifecycle, and queries snapshot metadata.
