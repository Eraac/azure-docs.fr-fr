---
title: Guide pratique pour configurer les fonctionnalités multimaîtres dans Azure Cosmos DB
description: Découvrez comment configurer les fonctionnalités multimaîtres dans vos applications au sein d’Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: a5232101d4e5d13fb4c95268311e06b56fa81e65
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356220"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Configurer les fonctionnalités multimaîtres dans les applications qui utilisent Azure Cosmos DB

Une fois qu’un compte a été créé avec plusieurs régions d’écriture activées, vous devez apporter deux modifications à votre application pour que ConnectionPolicy et DocumentClient activent les fonctionnalités multimaîtres et multihébergement dans Azure Cosmos DB. Dans ConnectionPolicy, définissez UseMultipleWriteLocations sur true et passez le nom de la région où l’application est déployée à SetCurrentLocation. Cela remplira la propriété PreferredLocations en fonction de la géo-proximité à partir de l’emplacement transmis. Si une nouvelle région est ultérieurement ajoutée au compte, l’application n’aura pas à être mise à jour ou redéployée ; elle détectera automatiquement la région la plus proche et se repliera automatiquement dessus en cas d’événement régional.

> [!Note]
> Les comptes Cosmos initialement configurés avec une seule région d’écriture peuvent être configurés pour plusieurs régions d’écriture (c’est-à-dire multimaîtres) avec un temps de non-disponibilité égal à zéro. Pour en savoir plus, consultez [Configurer plusieurs régions d’écriture](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a id="netv2"></a>Kit SDK .NET v2

Pour configurer la fonctionnalité multimaître dans votre application, définissez `UseMultipleWriteLocations` sur `true`. Définissez également `SetCurrentLocation` en fonction de la région sur laquelle l’application est déployée et où Azure Cosmos DB est répliquée :

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>Kit SDK .NET v3

Pour activer la fonctionnalité multimaître dans votre application, définissez `ApplicationRegion` en fonction de la région sur laquelle l’application est déployée et où Cosmos DB est répliquée :

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Si vous le souhaitez, vous pouvez utiliser `CosmosClientBuilder` et `WithApplicationRegion` pour obtenir le même résultat :

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a id="java"></a>Kit SDK Java Async

Pour activer la fonctionnalité multimaître dans votre application, définissez `policy.setUsingMultipleWriteLocations(true)` et `policy.setPreferredLocations` en fonction de la région sur laquelle l’application est déployée et où Cosmos DB est répliquée :

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a id="javascript"></a>Kits SDK Node.js, JavaScript et TypeScript

Pour activer la fonctionnalité multimaître dans votre application, définissez `connectionPolicy.UseMultipleWriteLocations` sur `true`. Définissez également `connectionPolicy.PreferredLocations` en fonction de la région sur laquelle l’application est déployée et où Cosmos DB est répliquée :

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a id="python"></a>Kit SDK Python

Pour activer la fonctionnalité multimaître dans votre application, définissez `connection_policy.UseMultipleWriteLocations` sur `true`. Définissez également `connection_policy.PreferredLocations` en fonction de la région sur laquelle l’application est déployée et où Cosmos DB est répliquée.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants :

* [Utiliser des jetons de sessions pour gérer la cohérence dans Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Types de conflit et stratégies de résolution dans Azure Cosmos DB](conflict-resolution-policies.md)
* [Haute disponibilité dans Azure Cosmos DB](high-availability.md)
* [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md)
* [Choisir le niveau de cohérence approprié dans Azure Cosmos DB](consistency-levels-choosing.md)
* [Compromis entre cohérence, disponibilité et niveau de performance dans Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Compromis entre disponibilité et performance pour différents niveaux de cohérence](consistency-levels-tradeoffs.md)
* [Mise à l’échelle du débit provisionné au niveau global](scaling-throughput.md)
* [Distribution mondiale : Sous le capot](global-dist-under-the-hood.md)
