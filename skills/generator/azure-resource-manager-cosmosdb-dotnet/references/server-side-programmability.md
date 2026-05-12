# SQL Server-Side Programmability

Patterns for managing stored procedures, triggers, and user-defined functions in SQL containers.

## Stored Procedures

### Create Stored Procedure

```csharp
using Azure.ResourceManager.CosmosDB;
using Azure.ResourceManager.CosmosDB.Models;

var spData = new CosmosDBSqlStoredProcedureCreateOrUpdateContent(
    AzureLocation.WestUS,
    new CosmosDBSqlStoredProcedureResourceInfo("myStoredProc")
    {
        Body = @"function () {
    var context = getContext();
    var response = context.getResponse();
    response.setBody('Hello World');
}"
    });

var spCollection = container.GetCosmosDBSqlStoredProcedures();
var operation = await spCollection.CreateOrUpdateAsync(
    WaitUntil.Completed,
    "myStoredProc",
    spData);

CosmosDBSqlStoredProcedureResource storedProc = operation.Value;
```

### Update Stored Procedure

```csharp
var updateData = new CosmosDBSqlStoredProcedureCreateOrUpdateContent(
    AzureLocation.WestUS,
    new CosmosDBSqlStoredProcedureResourceInfo("myStoredProc")
    {
        Body = @"function () {
    var context = getContext();
    var collection = context.getCollection();
    var response = context.getResponse();
    var createdItem = collection.createDocument(
        collection.getSelfLink(),
        { id: 'newDoc', message: 'Created by stored procedure' },
        function (err, doc) {
            if (err) throw err;
            response.setBody(doc);
        });
    if (!createdItem) throw new Error('Document not created');
}"
    });

await spCollection.CreateOrUpdateAsync(
    WaitUntil.Completed, "myStoredProc", updateData);
```

### List Stored Procedures

```csharp
await foreach (var sp in container.GetCosmosDBSqlStoredProcedures())
{
    Console.WriteLine($"Stored Procedure: {sp.Data.Name}");
    Console.WriteLine($"  Body: {sp.Data.Resource.Body?.Substring(0, 50)}...");
}
```

### Delete Stored Procedure

```csharp
var sp = await container.GetCosmosDBSqlStoredProcedureAsync("myStoredProc");
await sp.Value.DeleteAsync(WaitUntil.Completed);
```

## Triggers

### Create Pre-Trigger

```csharp
var triggerData = new CosmosDBSqlTriggerCreateOrUpdateContent(
    AzureLocation.WestUS,
    new CosmosDBSqlTriggerResourceInfo("myPreTrigger")
    {
        TriggerOperation = CosmosDBSqlTriggerOperation.All,
        TriggerType = CosmosDBSqlTriggerType.Pre,
        Body = @"function () {
    var context = getContext();
    var request = context.getRequest();
    var item = request.getBody();
    
    // Validate required fields
    if (!item.createdAt) {
        item.createdAt = new Date().toISOString();
    }
    
    request.setBody(item);
}"
    });

var triggerCollection = container.GetCosmosDBSqlTriggers();
var operation = await triggerCollection.CreateOrUpdateAsync(
    WaitUntil.Completed,
    "myPreTrigger",
    triggerData);

CosmosDBSqlTriggerResource trigger = operation.Value;
```

### Create Post-Trigger

```csharp
var triggerData = new CosmosDBSqlTriggerCreateOrUpdateContent(
    AzureLocation.WestUS,
    new CosmosDBSqlTriggerResourceInfo("myPostTrigger")
    {
        TriggerOperation = CosmosDBSqlTriggerOperation.Create,
        TriggerType = CosmosDBSqlTriggerType.Post,
        Body = @"function () {
    var context = getContext();
    var response = context.getResponse();
    var item = response.getBody();
    
    // Log creation
    item.log = 'Created at ' + new Date().toISOString();
    response.setBody(item);
}"
    });
```

### Trigger Operations

| `CosmosDBSqlTriggerOperation` | Description |
|-------------------------------|-------------|
| `All` | All operations |
| `Create` | Create only |
| `Update` | Update only |
| `Delete` | Delete only |
| `Replace` | Replace only |

### Trigger Types

| `CosmosDBSqlTriggerType` | Description |
|--------------------------|-------------|
| `Pre` | Runs before the operation |
| `Post` | Runs after the operation |

### List & Delete Triggers

```csharp
// List
await foreach (var trigger in container.GetCosmosDBSqlTriggers())
{
    Console.WriteLine($"Trigger: {trigger.Data.Name}");
    Console.WriteLine($"  Type: {trigger.Data.Resource.TriggerType}");
    Console.WriteLine($"  Operation: {trigger.Data.Resource.TriggerOperation}");
}

// Delete
var trigger = await container.GetCosmosDBSqlTriggerAsync("myPreTrigger");
await trigger.Value.DeleteAsync(WaitUntil.Completed);
```

## User-Defined Functions (UDFs)

### Create UDF

```csharp
var udfData = new CosmosDBSqlUserDefinedFunctionCreateOrUpdateContent(
    AzureLocation.WestUS,
    new CosmosDBSqlUserDefinedFunctionResourceInfo("toUpperCase")
    {
        Body = @"function (input) {
    return input.toUpperCase();
}"
    });

var udfCollection = container.GetCosmosDBSqlUserDefinedFunctions();
var operation = await udfCollection.CreateOrUpdateAsync(
    WaitUntil.Completed,
    "toUpperCase",
    udfData);

CosmosDBSqlUserDefinedFunctionResource udf = operation.Value;
```

### List & Delete UDFs

```csharp
// List
await foreach (var udf in container.GetCosmosDBSqlUserDefinedFunctions())
{
    Console.WriteLine($"UDF: {udf.Data.Name}");
}

// Delete
var udf = await container.GetCosmosDBSqlUserDefinedFunctionAsync("toUpperCase");
await udf.Value.DeleteAsync(WaitUntil.Completed);
```

## Key Types

| Type | Purpose |
|------|---------|
| `CosmosDBSqlStoredProcedureResource` | Represents a stored procedure |
| `CosmosDBSqlStoredProcedureCreateOrUpdateContent` | Stored procedure creation payload |
| `CosmosDBSqlStoredProcedureResourceInfo` | Stored procedure metadata (includes Body) |
| `CosmosDBSqlTriggerResource` | Represents a trigger |
| `CosmosDBSqlTriggerCreateOrUpdateContent` | Trigger creation payload |
| `CosmosDBSqlTriggerResourceInfo` | Trigger metadata (includes Body, TriggerOperation, TriggerType) |
| `CosmosDBSqlUserDefinedFunctionResource` | Represents a UDF |
| `CosmosDBSqlUserDefinedFunctionCreateOrUpdateContent` | UDF creation payload |
| `CosmosDBSqlUserDefinedFunctionResourceInfo` | UDF metadata (includes Body) |
