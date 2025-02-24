---
title: Découvrez comment gérer la cohérence dans Azure Cosmos DB
description: Découvrez comment gérer la cohérence dans Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: 2617aba0d790209d83f410ee632ffad43c952d55
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356425"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Gérer les niveaux de cohérence dans Azure Cosmos DB

Cet article explique comment gérer les niveaux de cohérence dans Azure Cosmos DB. Vous apprenez à configurer le niveau de cohérence par défaut, à remplacer la cohérence par défaut, à gérer manuellement les jetons de session et à comprendre la métrique PBS (Probabilistically Bounded Staleness).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Configurer le niveau de cohérence par défaut

Le [niveau de cohérence par défaut](consistency-levels.md) est le niveau de cohérence que les clients utilisent par défaut. Les clients peuvent toujours le remplacer.

### <a name="cli"></a>Interface de ligne de commande

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Eventual
```

### <a name="powershell"></a>PowerShell

Cet exemple crée un compte Azure Cosmos DB avec plusieurs régions d’écriture activées, dans les régions USA Est et USA Ouest. Le niveau de cohérence par défaut est défini sur la cohérence *Session*.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="Session"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="azure-portal"></a>Portail Azure

Pour afficher ou modifier le niveau de cohérence par défaut, connectez-vous au portail Azure. Recherchez votre compte Azure Cosmos, puis ouvrez le volet **Cohérence par défaut**. Sélectionnez le niveau de cohérence que vous souhaitez utiliser comme nouvelle valeur par défaut, puis sélectionnez **Enregistrer**. Le portail Azure permet également de visualiser les différents niveaux de cohérence grâce à des notes de musique. 

![Menu Cohérence dans le portail Azure](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Remplacer le niveau de cohérence par défaut

Les clients peuvent remplacer le niveau de cohérence par défaut, qui est défini par le service. Le niveau de cohérence peut être défini pour chaque demande, ce qui a pour effet de remplacer le niveau de cohérence par défaut défini au niveau du compte.

### <a id="override-default-consistency-dotnet"></a>Kit SDK .NET V2

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-dotnet-v3"></a>Kit SDK .NET V3

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item, 
        new PartitionKey(itemPartitionKey), 
        requestOptions);
```

### <a id="override-default-consistency-java-async"></a>Kit SDK Java Async

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

### <a id="override-default-consistency-java-sync"></a>Kit SDK Java Sync

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```

### <a id="override-default-consistency-javascript"></a>Kit SDK Node.js/JavaScript/TypeScript

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Kit SDK Python

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Utiliser des jetons de session

Parmi les différents niveaux de cohérence rencontrés dans Azure Cosmos DB figure la cohérence *Session*. Il s’agit du niveau appliqué par défaut aux comptes Cosmos. Quand vous utilisez la cohérence *Session*, le client utilise un jeton de session en interne à chaque demande de lecture/requête pour faire en sorte que le niveau de cohérence défini soit maintenu.

Pour gérer les jetons de session manuellement, obtenez le jeton de session à partir de la réponse, puis définissez-les par requête. Si vous n’avez pas besoin de gérer des jetons de session manuellement, les exemples ci-dessous ne vous sont pas utiles. Le kit SDK effectue le suivi des jetons de session automatiquement. Si vous ne définissez pas le jeton de session manuellement, par défaut, le kit SDK utilise le jeton de session le plus récent.

### <a id="utilize-session-tokens-dotnet"></a>Kit SDK .NET V2

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-dotnet-v3"></a>Kit SDK .NET V3

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```

### <a id="utilize-session-tokens-java-async"></a>Kit SDK Java Async

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

### <a id="utilize-session-tokens-java-sync"></a>Kit SDK Java Sync

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>Kit SDK Node.js/JavaScript/TypeScript

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>Kit SDK Python

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>Surveiller la métrique de probabilités en fonction de l’obsolescence limitée (PBS)

Quel est le degré d’éventualité de la cohérence éventuelle ? Pour le cas moyen, pouvons-nous offrir des limites d’obsolescence par rapport à l’historique de version et au moment ? La métrique [**PBS (Probabilistically Bounded Staleness)** ](https://pbs.cs.berkeley.edu/) essaie de quantifier la probabilité d’obsolescence et l’affiche sous forme de métrique. Pour afficher la métrique PBS, accédez à votre compte Azure Cosmos DB sur le portail Azure. Ouvrez le volet **Métriques**, puis sélectionnez l’onglet **Cohérence**. Examinez le graphe intitulé **Probability of strongly consistent reads based on your workload (see PBS)** .

![Graphe PBS dans le portail Azure](./media/how-to-manage-consistency/pbs-metric.png)


## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail la gestion des conflits de données ou passez au concept clé suivant dans Azure Cosmos DB. Consultez les articles suivants :

* [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md)
* [Gérer les conflits entre les régions](how-to-manage-conflicts.md)
* [Partitionnement et distribution des données](partition-data.md)
* [Compromis en matière de cohérence dans la conception des systèmes de base de données distribuées modernes](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Haute disponibilité](high-availability.md)
* [SLA pour Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
