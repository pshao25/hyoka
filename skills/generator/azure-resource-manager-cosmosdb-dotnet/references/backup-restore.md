# Backup and Point-In-Time Restore

Patterns for configuring continuous backup and restoring Cosmos DB accounts to a specific point in time.

## Continuous Backup Configuration

### Create Account with Continuous Backup

```csharp
using Azure.ResourceManager.CosmosDB;
using Azure.ResourceManager.CosmosDB.Models;

var accountData = new CosmosDBAccountCreateOrUpdateContent(
    AzureLocation.WestUS,
    new List<CosmosDBAccountLocation>
    {
        new CosmosDBAccountLocation { LocationName = AzureLocation.WestUS }
    })
{
    Kind = CosmosDBAccountKind.GlobalDocumentDB,
    BackupPolicy = new ContinuousModeBackupPolicy
    {
        ContinuousModeTier = ContinuousTier.Continuous7Days
    }
};

var accountCollection = resourceGroup.GetCosmosDBAccounts();
var operation = await accountCollection.CreateOrUpdateAsync(
    WaitUntil.Completed,
    "my-continuous-backup-account",
    accountData);
```

### Continuous Mode Tiers

| `ContinuousTier` | Retention | Description |
|-------------------|-----------|-------------|
| `Continuous7Days` | 7 days | Lower cost, 7-day restore window |
| `Continuous30Days` | 30 days | Extended retention, 30-day restore window |

## Listing Restorable Accounts

### Get Restorable Accounts

```csharp
var subscription = await client.GetDefaultSubscriptionAsync();

await foreach (var restorableAccount in subscription.GetRestorableCosmosDBAccounts().GetAllAsync())
{
    Console.WriteLine($"Account: {restorableAccount.Data.AccountName}");
    Console.WriteLine($"  API Type: {restorableAccount.Data.ApiType}");
    Console.WriteLine($"  Creation Time: {restorableAccount.Data.CreatedOn}");
    Console.WriteLine($"  Deletion Time: {restorableAccount.Data.DeletedOn}");
    Console.WriteLine($"  Location: {restorableAccount.Data.Location}");
}
```

## Point-In-Time Restore

### Restore Account

```csharp
var restoreTimestamp = DateTimeOffset.UtcNow.AddHours(-1);
var sourceAccountId = $"/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{restorableAccountId}";

var restoreAccountData = new CosmosDBAccountCreateOrUpdateContent(
    AzureLocation.WestUS,
    new List<CosmosDBAccountLocation>
    {
        new CosmosDBAccountLocation { LocationName = AzureLocation.WestUS }
    })
{
    Kind = CosmosDBAccountKind.GlobalDocumentDB,
    CreateMode = CosmosDBAccountCreateMode.Restore,
    RestoreParameters = new CosmosDBAccountRestoreParameters
    {
        RestoreMode = "PointInTime",
        RestoreTimestampInUtc = restoreTimestamp,
        RestoreSource = sourceAccountId,
        IsRestoreWithTtlDisabled = false
    },
    BackupPolicy = new ContinuousModeBackupPolicy
    {
        ContinuousModeTier = ContinuousTier.Continuous7Days
    }
};

var operation = await accountCollection.CreateOrUpdateAsync(
    WaitUntil.Completed,
    "my-restored-account",
    restoreAccountData);
```

### Restore Specific Databases and Collections

```csharp
var restoreParams = new CosmosDBAccountRestoreParameters
{
    RestoreMode = "PointInTime",
    RestoreTimestampInUtc = DateTimeOffset.UtcNow.AddHours(-2),
    RestoreSource = sourceAccountId,
    DatabasesToRestore =
    {
        new DatabaseRestoreResourceInfo
        {
            DatabaseName = "myDatabase",
            CollectionNames = { "container1", "container2" }
        },
        new DatabaseRestoreResourceInfo
        {
            DatabaseName = "anotherDatabase"
            // Omit CollectionNames to restore all collections
        }
    }
};
```

### Restore with TTL Disabled

```csharp
var restoreParams = new CosmosDBAccountRestoreParameters
{
    RestoreMode = "PointInTime",
    RestoreTimestampInUtc = DateTimeOffset.UtcNow.AddMinutes(-30),
    RestoreSource = sourceAccountId,
    IsRestoreWithTtlDisabled = true  // Prevents TTL from expiring restored data
};
```

## Periodic Backup (Default)

### Create Account with Periodic Backup

```csharp
var accountData = new CosmosDBAccountCreateOrUpdateContent(
    AzureLocation.WestUS,
    new List<CosmosDBAccountLocation>
    {
        new CosmosDBAccountLocation { LocationName = AzureLocation.WestUS }
    })
{
    Kind = CosmosDBAccountKind.GlobalDocumentDB,
    BackupPolicy = new PeriodicModeBackupPolicy
    {
        PeriodicModeProperties = new PeriodicModeProperties
        {
            BackupIntervalInMinutes = 240,     // Every 4 hours
            BackupRetentionIntervalInHours = 8, // Keep for 8 hours
            BackupStorageRedundancy = CosmosDBBackupStorageRedundancy.Geo
        }
    }
};
```

### Backup Storage Redundancy Options

| `CosmosDBBackupStorageRedundancy` | Description |
|-----------------------------------|-------------|
| `Geo` | Geo-redundant backup storage |
| `Local` | Locally redundant backup storage |
| `Zone` | Zone-redundant backup storage |

## Key Types

| Type | Purpose |
|------|---------|
| `ContinuousModeBackupPolicy` | Continuous backup with point-in-time restore |
| `ContinuousTier` | `Continuous7Days` or `Continuous30Days` |
| `PeriodicModeBackupPolicy` | Periodic snapshot backups |
| `PeriodicModeProperties` | Interval, retention, and storage redundancy |
| `CosmosDBAccountRestoreParameters` | Restore configuration (mode, timestamp, source) |
| `CosmosDBAccountCreateMode` | `Default` or `Restore` |
| `DatabaseRestoreResourceInfo` | Specific database/collections to restore |
| `RestorableCosmosDBAccountResource` | A restorable account entry |
| `CosmosDBBackupStorageRedundancy` | Backup storage redundancy level |

## Important Notes

- `CreateMode = CosmosDBAccountCreateMode.Restore` is required when restoring
- `RestoreSource` must be the full ARM path to the restorable database account
- `RestoreTimestampInUtc` must be within the retention window of the backup tier
- Accounts with continuous backup can be restored to any point within the retention period
- Periodic backup accounts cannot be restored via ARM — use Azure support
- Set `IsRestoreWithTtlDisabled = true` to prevent TTL from expiring data immediately after restore
