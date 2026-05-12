# SQL Role-Based Access Control (RBAC)

Patterns for managing SQL role definitions and role assignments for data-plane RBAC in Cosmos DB.

## Role Definitions

### Create Custom Role Definition

```csharp
using Azure.ResourceManager.CosmosDB;
using Azure.ResourceManager.CosmosDB.Models;

string roleDefinitionId = Guid.NewGuid().ToString();

var roleDefData = new CosmosDBSqlRoleDefinitionCreateOrUpdateContent
{
    RoleName = "MyCustomReadWriteRole",
    RoleDefinitionType = CosmosDBSqlRoleDefinitionType.CustomRole,
    AssignableScopes =
    {
        $"/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}"
    },
    Permissions =
    {
        new CosmosDBSqlRolePermission
        {
            DataActions =
            {
                "Microsoft.DocumentDB/databaseAccounts/readMetadata",
                "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create",
                "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
                "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace",
                "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete",
                "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
                "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
            }
        }
    }
};

var roleDefCollection = account.GetCosmosDBSqlRoleDefinitions();
var operation = await roleDefCollection.CreateOrUpdateAsync(
    WaitUntil.Completed,
    roleDefinitionId,
    roleDefData);

CosmosDBSqlRoleDefinitionResource roleDef = operation.Value;
```

### Create Read-Only Role

```csharp
var readOnlyRoleDef = new CosmosDBSqlRoleDefinitionCreateOrUpdateContent
{
    RoleName = "ReadOnlyRole",
    RoleDefinitionType = CosmosDBSqlRoleDefinitionType.CustomRole,
    AssignableScopes =
    {
        $"/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}"
    },
    Permissions =
    {
        new CosmosDBSqlRolePermission
        {
            DataActions =
            {
                "Microsoft.DocumentDB/databaseAccounts/readMetadata",
                "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
                "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
                "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
            }
        }
    }
};
```

### List Role Definitions

```csharp
await foreach (var roleDef in account.GetCosmosDBSqlRoleDefinitions())
{
    Console.WriteLine($"Role: {roleDef.Data.RoleName}");
    Console.WriteLine($"  Type: {roleDef.Data.RoleDefinitionType}");
    Console.WriteLine($"  Scopes: {string.Join(", ", roleDef.Data.AssignableScopes)}");

    foreach (var perm in roleDef.Data.Permissions)
    {
        Console.WriteLine($"  Data Actions: {string.Join(", ", perm.DataActions)}");
    }
}
```

### Delete Role Definition

```csharp
var roleDef = await account.GetCosmosDBSqlRoleDefinitionAsync(roleDefinitionId);
await roleDef.Value.DeleteAsync(WaitUntil.Completed);
```

## Role Assignments

### Create Role Assignment

```csharp
string roleAssignmentId = Guid.NewGuid().ToString();

var roleAssignmentData = new CosmosDBSqlRoleAssignmentCreateOrUpdateContent
{
    RoleDefinitionId = new ResourceIdentifier(
        $"/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/sqlRoleDefinitions/{roleDefinitionId}"),
    Scope = $"/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}",
    PrincipalId = Guid.Parse("aaaabbbb-cccc-dddd-eeee-ffffgggghhhh") // AAD principal ID
};

var roleAssignmentCollection = account.GetCosmosDBSqlRoleAssignments();
var operation = await roleAssignmentCollection.CreateOrUpdateAsync(
    WaitUntil.Completed,
    roleAssignmentId,
    roleAssignmentData);

CosmosDBSqlRoleAssignmentResource roleAssignment = operation.Value;
```

### Scoped Role Assignment (Database-Level)

```csharp
var scopedAssignment = new CosmosDBSqlRoleAssignmentCreateOrUpdateContent
{
    RoleDefinitionId = new ResourceIdentifier(
        $"/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/sqlRoleDefinitions/{roleDefinitionId}"),
    // Scope to a specific database
    Scope = $"/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/dbs/{databaseName}",
    PrincipalId = Guid.Parse("aaaabbbb-cccc-dddd-eeee-ffffgggghhhh")
};
```

### List Role Assignments

```csharp
await foreach (var assignment in account.GetCosmosDBSqlRoleAssignments())
{
    Console.WriteLine($"Assignment ID: {assignment.Data.Name}");
    Console.WriteLine($"  Role Definition: {assignment.Data.RoleDefinitionId}");
    Console.WriteLine($"  Principal: {assignment.Data.PrincipalId}");
    Console.WriteLine($"  Scope: {assignment.Data.Scope}");
}
```

### Delete Role Assignment

```csharp
var assignment = await account.GetCosmosDBSqlRoleAssignmentAsync(roleAssignmentId);
await assignment.Value.DeleteAsync(WaitUntil.Completed);
```

## Common Data Actions

| Data Action | Description |
|-------------|-------------|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Read account metadata |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Create items |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Read items |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Replace items |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | Upsert items |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Delete items |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | Execute queries |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Read change feed |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | Run stored procedures |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | Manage conflicts |

## Key Types

| Type | Purpose |
|------|---------|
| `CosmosDBSqlRoleDefinitionResource` | Represents a role definition |
| `CosmosDBSqlRoleDefinitionCreateOrUpdateContent` | Role definition creation payload |
| `CosmosDBSqlRoleDefinitionType` | `BuiltInRole` or `CustomRole` |
| `CosmosDBSqlRolePermission` | Permission set with `DataActions` list |
| `CosmosDBSqlRoleAssignmentResource` | Represents a role assignment |
| `CosmosDBSqlRoleAssignmentCreateOrUpdateContent` | Role assignment creation payload |

## Important Notes

- Role definition and assignment IDs are **GUID strings** (e.g., `Guid.NewGuid().ToString()`)
- `AssignableScopes` and `Scope` use full ARM resource paths
- `RoleDefinitionId` on assignments is a `ResourceIdentifier` (full ARM path)
- `PrincipalId` is a `Guid` representing the AAD object ID (user, service principal, or managed identity)
- Built-in roles (e.g., Cosmos DB Built-in Data Reader/Contributor) exist by default
