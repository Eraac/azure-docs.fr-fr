---
title: Comment appeler des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur à l’aide des kits de développement logiciel (SDK) Azure Cosmos DB
description: Découvrez comment inscrire et appeler des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur à l’aide des kits de développement logiciel (SDK) Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/21/2019
ms.author: mjbrown
ms.openlocfilehash: e1215441b45e1fdba8d74c9149415f0da5c4c44a
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360387"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Comment inscrire et utiliser des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur dans Azure Cosmos DB

L’API SQL dans Azure Cosmos DB prend en charge l’inscription et l’appel de procédures stockées, déclencheurs et fonctions définies par l’utilisateur (UDF) écrites en JavaScript. Vous pouvez utiliser les SDK API SQL [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [Node.js](sql-api-sdk-node.md) ou [Python](sql-api-sdk-python.md) pour inscrire et appeler les procédures stockées. Après avoir défini des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur, vous pouvez les charger et les afficher dans le [portail Azure](https://portal.azure.com/) à l’aide de l’Explorateur de données.

## <a id="stored-procedures"></a> Comment exécuter des procédures stockées

Les procédures stockées sont écrites à l’aide de JavaScript. Elles peuvent créer, mettre à jour, lire, interroger et supprimer des éléments dans un conteneur Azure Cosmos. Pour plus d’informations concernant l’écriture des procédures stockées dans Azure Cosmos DB, consultez l’article [Comment écrire des procédures stockées dans Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures).

Les exemples suivants montrent comment inscrire et appeler une procédure stockée à l’aide des kits de développement logiciel (SDK) Azure Cosmos DB. Reportez-vous à [Créer un Document](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) puisque la source pour cette procédure stockée est enregistrée en tant que `spCreateToDoItem.js`.

> [!NOTE]
> Pour les conteneurs partitionnés, lorsque vous exécutez une procédure stockée, vous devez fournir une valeur de clé de partition dans les options de requête. Les procédures stockées se limitent toujours à une clé de partition. Les éléments qui ont une valeur de clé de partition différente ne seront pas visibles dans la procédure stockée. Cela s’applique également aux déclencheurs.

### <a name="stored-procedures---net-sdk"></a>Procédures stockées - Kit de développement logiciel (SDK) .NET

L’exemple suivant montre comment inscrire une procédure stockée à l’aide du kit de développement logiciel (SDK) .NET :

```csharp
string storedProcedureId = "spCreateToDoItem";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

L’exemple suivant montre comment appeler une procédure stockée à l’aide du kit de développement logiciel (SDK) .NET :

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, newItem);
var id = result.Response;
```

### <a name="stored-procedures---java-sdk"></a>Procédures stockées - Kit de développement logiciel (SDK) Java

L’exemple suivant montre comment inscrire une procédure stockée à l’aide du kit de développement logiciel (SDK) Java :

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItem'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItem.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

L’exemple suivant montre comment appeler une procédure stockée à l’aide du kit de développement logiciel (SDK) Java :

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItem");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { newItem };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Procédures stockées - Kit de développement logiciel (SDK) JavaScript

L’exemple suivant montre comment inscrire une procédure stockée à l’aide du kit de développement logiciel (SDK) JavaScript

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

L’exemple suivant montre comment appeler une procédure stockée à l’aide du kit de développement logiciel (SDK) JavaScript :

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
const {body: result} = await container.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Procédures stockées - Kit de développement logiciel (SDK) Python

L’exemple suivant montre comment inscrire une procédure stockée à l’aide du kit de développement logiciel (SDK) Python

```python
with open('../js/spCreateToDoItem.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
sproc_definition = {
    'id': 'spCreateToDoItem',
    'serverScript': file_contents,
}
sproc = client.CreateStoredProcedure(container_link, sproc_definition)
```

L’exemple suivant montre comment appeler une procédure stockée à l’aide du kit de développement logiciel (SDK) Python

```python
sproc_link = 'dbs/myDatabase/colls/myContainer/sprocs/spCreateToDoItem'
new_item = [{
    'category':'Personal',
    'name':'Groceries',
    'description':'Pick up strawberries',
    'isComplete': False
}]
client.ExecuteStoredProcedure(sproc_link, new_item, {'partitionKey': 'Personal'}
```

## <a id="pre-triggers"></a>Comment exécuter les pré-déclencheurs

Les exemples suivants montrent comment inscrire et appeler un pré-déclencheur à l’aide des kits de développement logiciel (SDK) Azure Cosmos DB. Reportez-vous à [Exemple de pré-déclencheur](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) puisque la source pour ce pré-déclencheur est enregistrée en tant que `trgPreValidateToDoItemTimestamp.js`.

Lors de l’exécution, les pré-déclencheurs sont transmis dans l’objet RequestOptions en spécifiant `PreTriggerInclude`, puis en passant le nom du déclencheur dans un objet de liste.

> [!NOTE]
> Même si le nom du déclencheur est transmis en tant que liste, vous ne pouvez pas exécuter plus d’un déclencheur par opération.

### <a name="pre-triggers---net-sdk"></a>Pré-déclencheurs - Kit de développement logiciel (SDK) .NET

Le code suivant montre comment inscrire un pré-déclencheur à l’aide du kit de développement logiciel (SDK) .NET :

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Le code suivant montre comment appeler un pré-déclencheur à l’aide du kit de développement logiciel (SDK) .NET :

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---java-sdk"></a>Pré-déclencheurs - Kit de développement logiciel (SDK) Java

Le code suivant montre comment inscrire un pré-déclencheur à l’aide du kit de développement logiciel (SDK) Java :

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

Le code suivant montre comment appeler un pré-déclencheur à l’aide du kit de développement logiciel (SDK) Java :

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>Pré-déclencheurs - Kit de développement logiciel (SDK) JavaScript

Le code suivant montre comment inscrire un pré-déclencheur à l’aide du kit de développement logiciel (SDK) JavaScript :

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

Le code suivant montre comment appeler un pré-déclencheur à l’aide du kit de développement logiciel (SDK) JavaScript :

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>Pré-déclencheurs - Kit de développement logiciel (SDK) Python

Le code suivant montre comment inscrire un pré-déclencheur à l’aide du kit de développement logiciel (SDK) Python :

```python
with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPreValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Pre,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

Le code suivant montre comment appeler un pré-déclencheur à l’aide du kit de développement logiciel (SDK) Python :

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, {
                  'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a id="post-triggers"></a>Comment exécuter les post-déclencheurs

Les exemples suivants montrent comment inscrire un post-déclencheur à l’aide des kits de développement logiciel (SDK) Azure Cosmos DB. Reportez-vous à [Exemple de post-déclencheur](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) puisque la source pour ce post-déclencheur est enregistrée en tant que `trgPostUpdateMetadata.js`.

### <a name="post-triggers---net-sdk"></a>Post-déclencheurs - Kit de développement logiciel (SDK) .NET

Le code suivant montre comment inscrire un post-déclencheur à l’aide du kit de développement logiciel (SDK) .NET :

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Le code suivant montre comment appeler un post-déclencheur à l’aide du kit de développement logiciel (SDK) .NET :

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

RequestOptions options = new RequestOptions { PostTriggerInclude = new List<string> { "trgPostUpdateMetadata" } };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---java-sdk"></a>Post-déclencheurs - Kit de développement logiciel (SDK) Java

Le code suivant montre comment inscrire un post-déclencheur à l’aide du kit de développement logiciel (SDK) Java :

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

Le code suivant montre comment appeler un post-déclencheur à l’aide du kit de développement logiciel (SDK) Java :

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>Post-déclencheurs - Kit de développement logiciel (SDK) JavaScript

Le code suivant montre comment inscrire un post-déclencheur à l’aide du kit de développement logiciel (SDK) JavaScript :

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

Le code suivant montre comment appeler un post-déclencheur à l’aide du kit de développement logiciel (SDK) JavaScript :

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>Post-déclencheurs - Kit de développement logiciel (SDK) Python

Le code suivant montre comment inscrire un post-déclencheur à l’aide du kit de développement logiciel (SDK) Python :

```python
with open('../js/trgPostUpdateMetadata.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
    'id': 'trgPostUpdateMetadata',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Post,
    'triggerOperation': documents.TriggerOperation.Create
}
trigger = client.CreateTrigger(container_link, trigger_definition)
```

Le code suivant montre comment appeler un post-déclencheur à l’aide du kit de développement logiciel (SDK) Python :

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = {'name': 'artist_profile_1023', 'artist': 'The Band',
        'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, {
                  'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a id="udfs"></a> Comment utiliser des fonctions définies par l’utilisateur

Les exemples suivants montrent comment inscrire une fonction définie par l’utilisateur à l’aide des kits de développement logiciel (SDK) Azure Cosmos DB. Reportez-vous à [Exemple de fonction définie par l’utilisateur](how-to-write-stored-procedures-triggers-udfs.md#udfs) puisque la source pour ce post-déclencheur est enregistrée en tant que `udfTax.js`.

### <a name="user-defined-functions---net-sdk"></a>Fonction définie par l’utilisateur - Kit de développement logiciel (SDK) .NET

Le code suivant montre comment inscrire une fonction définie par l’utilisateur à l’aide du kit de développement logiciel (SDK) .NET :

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js"),
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

Le code suivant montre comment appeler une fonction définie par l’utilisateur à l’aide du kit de développement logiciel (SDK) .NET :

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---java-sdk"></a>Fonctions définies par l’utilisateur - Kit de développement logiciel (SDK) Java

Le code suivant montre comment inscrire une fonction définie par l’utilisateur à l’aide du kit de développement logiciel (SDK) Java :

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

Le code suivant montre comment appeler une fonction définie par l’utilisateur à l’aide du kit de développement logiciel (SDK) Java :

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>Fonctions définies par l’utilisateur - Kit de développement logiciel (SDK) JavaScript

Le code suivant montre comment inscrire une fonction définie par l’utilisateur à l’aide du kit de développement logiciel (SDK) JavaScript :

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

Le code suivant montre comment appeler une fonction définie par l’utilisateur à l’aide du kit de développement logiciel (SDK) JavaScript :

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Fonctions définies par l’utilisateur - Kit de développement logiciel (SDK) Python

Le code suivant montre comment inscrire une fonction définie par l’utilisateur à l’aide du kit de développement logiciel (SDK) Python :

```python
with open('../js/udfTax.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
udf_definition = {
    'id': 'Tax',
    'serverScript': file_contents,
}
udf = client.CreateUserDefinedFunction(container_link, udf_definition)
```

Le code suivant montre comment appeler une fonction définie par l’utilisateur à l’aide du kit de développement logiciel (SDK) Python :

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(
    container_link, 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus de concepts et comment écrire et utiliser des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur dans Azure Cosmos DB :

- [Utiliser des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur Azure Cosmos DB dans Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Utiliser l’API de requête avec langage JavaScript intégré dans Azure Cosmos DB](javascript-query-api.md)
- [Comment écrire des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur dans Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)
- [Comment écrire des procédures stockées et des déclencheurs à l’aide de l’API de requête JavaScript dans Azure Cosmos DB](how-to-write-javascript-query-api.md)
