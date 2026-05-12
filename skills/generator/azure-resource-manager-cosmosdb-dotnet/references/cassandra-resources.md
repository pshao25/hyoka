# Cassandra API Resources

Patterns for managing Cassandra API keyspaces and tables via Azure Resource Manager.

> **Note:** The account must have the `EnableCassandra` capability to use Cassandra resources.

## Account Creation with Cassandra Capability

```csharp
using Azure.ResourceManager.CosmosDB;
using Azure.ResourceManager.CosmosDB.Models;

var accountData = new CosmosDBAccountCreateOrUpdateContent(
    location: AzureLocation.WestUS,
    locations: new[]
    {
        new CosmosDBAccountLocation
        {
            LocationName = AzureLocation.WestUS,
            FailoverPriority = 0
        }
    })
{
    Kind = CosmosDBAccountKind.GlobalDocumentDB,
    Capabilities = { new CosmosDBAccountCapability("EnableCassandra") },
    ConsistencyPolicy = new ConsistencyPolicy(DefaultConsistencyLevel.Session)
};

var operation = await resourceGroup.GetCosmosDBAccounts().CreateOrUpdateAsync(
    WaitUntil.Completed, "my-cassandra-account", accountData);
```

## Cassandra Keyspace Operations

### Create Keyspace

```csharp
var keyspaceData = new CassandraKeyspaceCreateOrUpdateContent(
    AzureLocation.WestUS,
    new CassandraKeyspaceResourceInfo("my-keyspace"))
{
    Options = new CosmosDBCreateUpdateConfig { Throughput = 400 }
};

var keyspaceCollection = account.GetCassandraKeyspaces();
var operation = await keyspaceCollection.CreateOrUpdateAsync(
    WaitUntil.Completed,
    "my-keyspace",
    keyspaceData);

CassandraKeyspaceResource keyspace = operation.Value;
```

### List, Get, Delete

```csharp
// List
await foreach (var ks in account.GetCassandraKeyspaces())
{
    Console.WriteLine($"Keyspace: {ks.Data.Name}");
}

// Get
CassandraKeyspaceResource keyspace = await account.GetCassandraKeyspaceAsync("my-keyspace");

// Delete
await keyspace.DeleteAsync(WaitUntil.Completed);
```

### Throughput Operations

```csharp
// Get
var throughput = await keyspace.GetCassandraKeyspaceThroughputSetting().GetAsync();

// Update
var updateData = new ThroughputSettingsUpdateData(
    new ThroughputSettingsResourceInfo { Throughput = 800 });
await keyspace.GetCassandraKeyspaceThroughputSetting()
    .CreateOrUpdateAsync(WaitUntil.Completed, updateData);

// Migrate
await keyspace.MigrateCassandraKeyspaceToAutoscaleAsync(WaitUntil.Completed);
await keyspace.MigrateCassandraKeyspaceToManualThroughputAsync(WaitUntil.Completed);
```

## Cassandra Table Operations

### Create Table with Schema

```csharp
var tableData = new CassandraTableCreateOrUpdateContent(
    AzureLocation.WestUS,
    new CassandraTableResourceInfo("my-table", default,
        new CassandraSchema
        {
            Columns =
            {
                new CassandraColumn { Name = "id", CassandraColumnType = "uuid" },
                new CassandraColumn { Name = "name", CassandraColumnType = "text" },
                new CassandraColumn { Name = "age", CassandraColumnType = "int" },
                new CassandraColumn { Name = "created_at", CassandraColumnType = "timestamp" }
            },
            PartitionKeys =
            {
                new CassandraPartitionKey { Name = "id" }
            },
            ClusterKeys =
            {
                new CassandraClusterKey { Name = "created_at", OrderBy = "Desc" }
            }
        },
        default, null))
{
    Options = new CosmosDBCreateUpdateConfig { Throughput = 400 }
};

var tableCollection = keyspace.GetCassandraTables();
var operation = await tableCollection.CreateOrUpdateAsync(
    WaitUntil.Completed,
    "my-table",
    tableData);

CassandraTableResource table = operation.Value;
```

### Common Cassandra Column Types

| CQL Type | `CassandraColumnType` String |
|----------|------------------------------|
| int | `"int"` |
| bigint | `"bigint"` |
| text | `"text"` |
| ascii | `"ascii"` |
| uuid | `"uuid"` |
| boolean | `"boolean"` |
| float | `"float"` |
| double | `"double"` |
| timestamp | `"timestamp"` |
| blob | `"blob"` |

### Cluster Key Order

| Order | Value |
|-------|-------|
| Ascending | `"Asc"` |
| Descending | `"Desc"` |

### List, Get, Delete Tables

```csharp
// List
await foreach (var table in keyspace.GetCassandraTables())
{
    Console.WriteLine($"Table: {table.Data.Name}");
}

// Get
CassandraTableResource table = await keyspace.GetCassandraTableAsync("my-table");

// Delete
await table.DeleteAsync(WaitUntil.Completed);
```

### Table Throughput

```csharp
// Get
var throughput = await table.GetCassandraTableThroughputSetting().GetAsync();

// Migrate
await table.MigrateCassandraTableToAutoscaleAsync(WaitUntil.Completed);
await table.MigrateCassandraTableToManualThroughputAsync(WaitUntil.Completed);
```

## Key Types

| Type | Purpose |
|------|---------|
| `CassandraKeyspaceResource` | Represents a Cassandra keyspace |
| `CassandraKeyspaceCreateOrUpdateContent` | Keyspace creation payload |
| `CassandraKeyspaceResourceInfo` | Keyspace resource metadata |
| `CassandraTableResource` | Represents a Cassandra table |
| `CassandraTableCreateOrUpdateContent` | Table creation payload |
| `CassandraTableResourceInfo` | Table resource metadata (includes schema) |
| `CassandraSchema` | Schema definition (columns, partition keys, cluster keys) |
| `CassandraColumn` | Column definition (Name + CassandraColumnType) |
| `CassandraPartitionKey` | Partition key reference (Name) |
| `CassandraClusterKey` | Cluster key (Name + OrderBy) |
