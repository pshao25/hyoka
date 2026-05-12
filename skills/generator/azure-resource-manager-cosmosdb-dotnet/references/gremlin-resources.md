# Gremlin API Resources

Patterns for managing Gremlin (graph) API databases and graphs via Azure Resource Manager.

> **Note:** The account must have the `EnableGremlin` capability to use Gremlin resources.

## Account Creation with Gremlin Capability

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
    Capabilities = { new CosmosDBAccountCapability("EnableGremlin") },
    ConsistencyPolicy = new ConsistencyPolicy(DefaultConsistencyLevel.Session)
};

var operation = await resourceGroup.GetCosmosDBAccounts().CreateOrUpdateAsync(
    WaitUntil.Completed, "my-gremlin-account", accountData);
```

## Gremlin Database Operations

### Create Database

```csharp
var databaseData = new GremlinDatabaseCreateOrUpdateContent(
    AzureLocation.WestUS,
    new GremlinDatabaseResourceInfo("my-graph-db"))
{
    Options = new CosmosDBCreateUpdateConfig { Throughput = 400 }
};

var databaseCollection = account.GetGremlinDatabases();
var operation = await databaseCollection.CreateOrUpdateAsync(
    WaitUntil.Completed,
    "my-graph-db",
    databaseData);

GremlinDatabaseResource database = operation.Value;
```

### List, Get, Delete

```csharp
// List
await foreach (var db in account.GetGremlinDatabases())
{
    Console.WriteLine($"Database: {db.Data.Name}");
}

// Get
GremlinDatabaseResource database = await account.GetGremlinDatabaseAsync("my-graph-db");

// Delete
await database.DeleteAsync(WaitUntil.Completed);
```

### Throughput Operations

```csharp
// Get
var throughput = await database.GetGremlinDatabaseThroughputSetting().GetAsync();

// Migrate
await database.MigrateGremlinDatabaseToAutoscaleAsync(WaitUntil.Completed);
await database.MigrateGremlinDatabaseToManualThroughputAsync(WaitUntil.Completed);
```

## Gremlin Graph Operations

### Create Graph with Full Configuration

```csharp
// Indexing policy
var indexingPolicy = new CosmosDBIndexingPolicy()
{
    IsAutomatic = true,
    IndexingMode = CosmosDBIndexingMode.Consistent,
    IncludedPaths = { new CosmosDBIncludedPath { Path = "/*" } },
    ExcludedPaths = { new CosmosDBExcludedPath { Path = "/myPathToNotIndex/*" } },
    CompositeIndexes =
    {
        new List<CosmosDBCompositePath>
        {
            new CosmosDBCompositePath { Path = "/orderByPath1", Order = CompositePathSortOrder.Ascending },
            new CosmosDBCompositePath { Path = "/orderByPath2", Order = CompositePathSortOrder.Descending }
        }
    },
    SpatialIndexes =
    {
        new SpatialSpec("/*", new List<CosmosDBSpatialType> { new CosmosDBSpatialType("Point") }, null)
    }
};

// Partition key
var partitionKey = new CosmosDBContainerPartitionKey(
    new List<string> { "/pk" },
    CosmosDBPartitionKind.Hash,
    null, null, null);

// Unique key policy
var uniqueKeyPolicy = new CosmosDBUniqueKeyPolicy()
{
    UniqueKeys = { new CosmosDBUniqueKey(new List<string>() { "/myUniqueKey" }, null) }
};

// Conflict resolution
var conflictResolution = new ConflictResolutionPolicy(
    ConflictResolutionMode.LastWriterWins,
    "/_ts",       // conflict resolution path
    "",           // conflict resolution procedure (empty for LWW)
    null);

// Create graph
var graphData = new GremlinGraphCreateOrUpdateContent(
    AzureLocation.WestUS,
    new GremlinGraphResourceInfo(
        "my-graph",
        indexingPolicy,
        partitionKey,
        -1,                    // defaultTtl: -1 = off but TTL-enabled
        uniqueKeyPolicy,
        conflictResolution,
        null,                  // analyticalStorageTtl
        restoreParameters: null,
        createMode: null,
        null))
{
    Options = new CosmosDBCreateUpdateConfig { Throughput = 400 }
};

var graphCollection = database.GetGremlinGraphs();
var operation = await graphCollection.CreateOrUpdateAsync(
    WaitUntil.Completed,
    "my-graph",
    graphData);

GremlinGraphResource graph = operation.Value;
```

### Simple Graph (Minimum Configuration)

```csharp
var graphData = new GremlinGraphCreateOrUpdateContent(
    AzureLocation.WestUS,
    new GremlinGraphResourceInfo("my-graph")
    {
        PartitionKey = new CosmosDBContainerPartitionKey
        {
            Paths = { "/pk" },
            Kind = CosmosDBPartitionKind.Hash
        }
    })
{
    Options = new CosmosDBCreateUpdateConfig { Throughput = 400 }
};
```

### List, Get, Delete Graphs

```csharp
// List
await foreach (var graph in database.GetGremlinGraphs())
{
    Console.WriteLine($"Graph: {graph.Data.Name}");
}

// Get
GremlinGraphResource graph = await database.GetGremlinGraphAsync("my-graph");

// Delete
await graph.DeleteAsync(WaitUntil.Completed);
```

### Graph Throughput

```csharp
// Get
var throughput = await graph.GetGremlinGraphThroughputSetting().GetAsync();

// Migrate
await graph.MigrateGremlinGraphToAutoscaleAsync(WaitUntil.Completed);
await graph.MigrateGremlinGraphToManualThroughputAsync(WaitUntil.Completed);
```

## Key Types

| Type | Purpose |
|------|---------|
| `GremlinDatabaseResource` | Represents a Gremlin database |
| `GremlinDatabaseCreateOrUpdateContent` | Database creation payload |
| `GremlinDatabaseResourceInfo` | Database resource metadata |
| `GremlinGraphResource` | Represents a Gremlin graph |
| `GremlinGraphCreateOrUpdateContent` | Graph creation payload |
| `GremlinGraphResourceInfo` | Graph resource metadata (includes indexing, partition, unique keys, conflict resolution) |
| `ConflictResolutionPolicy` | Conflict resolution configuration |
| `ConflictResolutionMode` | LastWriterWins or Custom |
| `CosmosDBUniqueKeyPolicy` | Unique key constraints |
