# MongoDB API Resources

Patterns for managing MongoDB API databases and collections via Azure Resource Manager.

> **Note:** The account must be created with `Kind = CosmosDBAccountKind.MongoDB` to use MongoDB resources.

## MongoDB Database Operations

### Create Database

```csharp
using Azure.ResourceManager.CosmosDB;
using Azure.ResourceManager.CosmosDB.Models;

var databaseData = new MongoDBDatabaseCreateOrUpdateContent(
    AzureLocation.WestUS,
    new MongoDBDatabaseResourceInfo("my-mongo-db"))
{
    Options = new CosmosDBCreateUpdateConfig { Throughput = 400 }
};

var databaseCollection = account.GetMongoDBDatabases();
var operation = await databaseCollection.CreateOrUpdateAsync(
    WaitUntil.Completed,
    "my-mongo-db",
    databaseData);

MongoDBDatabaseResource database = operation.Value;
```

### List Databases

```csharp
await foreach (var db in account.GetMongoDBDatabases())
{
    Console.WriteLine($"Database: {db.Data.Name}");
}
```

### Get Database

```csharp
MongoDBDatabaseResource database = await account.GetMongoDBDatabaseAsync("my-mongo-db");
```

### Check Existence

```csharp
bool exists = await account.GetMongoDBDatabases().ExistsAsync("my-mongo-db");
```

### Delete Database

```csharp
var database = await account.GetMongoDBDatabaseAsync("my-mongo-db");
await database.Value.DeleteAsync(WaitUntil.Completed);
```

### Throughput Operations

```csharp
// Get throughput
var throughput = await database.GetMongoDBDatabaseThroughputSetting().GetAsync();
Console.WriteLine($"Throughput: {throughput.Value.Data.Resource.Throughput}");

// Update throughput
var updateData = new ThroughputSettingsUpdateData(
    new ThroughputSettingsResourceInfo { Throughput = 800 });
await database.GetMongoDBDatabaseThroughputSetting()
    .CreateOrUpdateAsync(WaitUntil.Completed, updateData);

// Migrate to autoscale
await database.MigrateMongoDBDatabaseToAutoscaleAsync(WaitUntil.Completed);

// Migrate to manual
await database.MigrateMongoDBDatabaseToManualThroughputAsync(WaitUntil.Completed);
```

## MongoDB Collection Operations

### Create Collection

```csharp
var collectionData = new MongoDBCollectionCreateOrUpdateContent(
    AzureLocation.WestUS,
    new MongoDBCollectionResourceInfo("my-collection"))
{
    Options = new CosmosDBCreateUpdateConfig { Throughput = 400 }
};

var collectionCollection = database.GetMongoDBCollections();
var operation = await collectionCollection.CreateOrUpdateAsync(
    WaitUntil.Completed,
    "my-collection",
    collectionData);

MongoDBCollectionResource collection = operation.Value;
```

### List Collections

```csharp
await foreach (var coll in database.GetMongoDBCollections())
{
    Console.WriteLine($"Collection: {coll.Data.Name}");
}
```

### Get Collection

```csharp
MongoDBCollectionResource collection = await database.GetMongoDBCollectionAsync("my-collection");
```

### Delete Collection

```csharp
var collection = await database.GetMongoDBCollectionAsync("my-collection");
await collection.Value.DeleteAsync(WaitUntil.Completed);
```

### Collection Throughput

```csharp
// Get
var throughput = await collection.GetMongoDBCollectionThroughputSetting().GetAsync();

// Update
var updateData = new ThroughputSettingsUpdateData(
    new ThroughputSettingsResourceInfo { Throughput = 800 });
await collection.GetMongoDBCollectionThroughputSetting()
    .CreateOrUpdateAsync(WaitUntil.Completed, updateData);

// Migrate to autoscale / manual
await collection.MigrateMongoDBCollectionToAutoscaleAsync(WaitUntil.Completed);
await collection.MigrateMongoDBCollectionToManualThroughputAsync(WaitUntil.Completed);
```

## Account Creation for MongoDB

```csharp
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
    Kind = CosmosDBAccountKind.MongoDB, // Required for MongoDB API
    ConsistencyPolicy = new ConsistencyPolicy(DefaultConsistencyLevel.BoundedStaleness)
    {
        MaxStalenessPrefix = 100000,
        MaxIntervalInSeconds = 300
    }
};

var operation = await resourceGroup.GetCosmosDBAccounts().CreateOrUpdateAsync(
    WaitUntil.Completed, "my-mongo-account", accountData);
```

## Key Types

| Type | Purpose |
|------|---------|
| `MongoDBDatabaseResource` | Represents a MongoDB database |
| `MongoDBDatabaseCollection` | Collection for database CRUD |
| `MongoDBDatabaseCreateOrUpdateContent` | Database creation payload |
| `MongoDBDatabaseResourceInfo` | Database resource metadata |
| `MongoDBCollectionResource` | Represents a MongoDB collection |
| `MongoDBCollectionCollection` | Collection for collection CRUD |
| `MongoDBCollectionCreateOrUpdateContent` | Collection creation payload |
| `MongoDBCollectionResourceInfo` | Collection resource metadata |
