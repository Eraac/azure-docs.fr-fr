---
title: Tutoriel de distribution mondiale Azure Cosmos DB pour l’API SQL
description: Découvrez comment configurer la distribution mondiale Azure Cosmos DB à l’aide de l’API SQL.
author: rimman
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 07/15/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: a566094f88ba9ffd25eadd046ae7254e26b9c2cf
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234594"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Configuration de la distribution mondiale Azure Cosmos DB à l’aide de l’API SQL

Dans cet article, nous vous montrons comment utiliser le portail Azure pour configurer la diffusion mondiale d’Azure Cosmos DB, puis établir une connexion à l’aide de l’API SQL.

Cet article décrit les tâches suivantes : 

> [!div class="checklist"]
> * Configurer la diffusion mondiale à l’aide du portail Azure
> * Configurer la distribution mondiale à l’aide des [API SQL](sql-api-introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a>Se connecter à une région de prédilection avec l’API SQL

Pour tirer parti de la [distribution mondiale](distribute-data-globally.md), les applications clientes peuvent spécifier la liste ordonnée de préférences de régions à utiliser pour effectuer des opérations sur les documents. Pour cela, vous devez configurer la stratégie de connexion. Selon la configuration du compte Azure Cosmos DB, la disponibilité régionale actuelle et la liste de préférences spécifiée, le Kit de développement logiciel (SDK) SQL choisit le point de terminaison optimal pour les opérations de lecture et d’écriture.

Cette liste de préférences est spécifiée lors de l’initialisation d’une connexion à l’aide des SDK SQL. Les SDK acceptent un paramètre facultatif « PreferredLocations » qui est une liste ordonnée des régions Azure.

Le SDK envoie automatiquement toutes les écritures vers la région d’écriture en cours.

Toutes les lectures sont envoyées vers la première région disponible dans la liste PreferredLocations. Si la demande échoue, le client passe à la région suivante dans la liste et ainsi de suite.

Les SDK tentent des opérations de lecture uniquement à partir des régions spécifiées dans PreferredLocations. Ainsi, par exemple, si le compte de base de données est disponible dans quatre régions, mais que le client spécifie uniquement deux régions de lecture (sans écriture) pour PreferredLocations, aucune lecture ne sera prise en charge en dehors de la région de lecture si elle n’est pas spécifiée dans PreferredLocations. Si les régions de lecture spécifiées dans PreferredLocations ne sont pas disponibles, les lectures seront prises en charge en dehors de la région d’écriture.

L’application peut vérifier le point de terminaison d’écriture et le point de terminaison de lecture actuels choisis par le SDK en vérifiant deux propriétés, WriteEndpoint et ReadEndpoint, disponibles dans le SDK version 1.8 et ultérieure.

Si la propriété PreferredLocations n’est pas définie, toutes les demandes seront traitées par la zone d’écriture en cours.

## <a name="net-sdk"></a>Kit de développement logiciel (SDK) .NET
Le SDK peut être utilisé sans aucune modification du code. Dans ce cas, le SDK dirige automatiquement les lectures et les écritures vers la région d’écriture en cours.

Dans la version 1.8 et ultérieure du SDK .NET, le paramètre ConnectionPolicy du constructeur DocumentClient comporte une propriété appelée Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Cette propriété est de type Collection `<string>` et doit contenir une liste des noms de région. Les valeurs de chaîne sont mises en forme en fonction de la colonne Nom de la région, dans la page [Régions Azure][regions], sans espaces avant ou après le premier et le dernier caractère, respectivement.

Les points de terminaison d’écriture et de lecture en cours sont disponibles dans DocumentClient.WriteEndpoint et DocumentClient.ReadEndpoint, respectivement.

> [!NOTE]
> Les URL des points de terminaison ne doivent pas être considérées comme des constantes à long terme. Le service peut les mettre à jour à tout moment. Le SDK gère ce changement automatiquement.
>
>

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

## <a name="nodejsjavascript"></a>Node.js/JavaScript

Le SDK peut être utilisé sans aucune modification du code. Dans ce cas, le SDK dirige automatiquement les lectures et les écritures vers la région d’écriture en cours.

Dans la version 1.8 et ultérieure de chaque SDK, le paramètre ConnectionPolicy du constructeur DocumentClient comporte une nouvelle propriété appelée DocumentClient.ConnectionPolicy.PreferredLocations. Ce paramètre est un tableau de chaînes qui prend une liste de noms de régions. Les noms sont mis en forme en fonction de la colonne Nom de la région, dans la page [Régions Azure][regions]. Vous pouvez également utiliser des constantes prédéfinies dans l’objet de commodité AzureDocuments.Regions

Les points de terminaison d’écriture et de lecture en cours sont disponibles dans DocumentClient.getWriteEndpoint et DocumentClient.getReadEndpoint, respectivement.

> [!NOTE]
> Les URL des points de terminaison ne doivent pas être considérées comme des constantes à long terme. Le service peut les mettre à jour à tout moment. Le SDK gère ce changement automatiquement.
>
>

Voici un exemple de code pour Node.js/Javascript.

```JavaScript
// Creating a ConnectionPolicy object
var connectionPolicy = new DocumentBase.ConnectionPolicy();

// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);
```

## <a name="python-sdk"></a>Kit de développement logiciel (SDK) Python

Le code suivant montre comment définir des emplacements préférés à l’aide du SDK Python :

```python

connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v2-sdk"></a>SDK Java V2

Le code suivant montre comment définir des emplacements préférés à l’aide du SDK Java :

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Arrays.asList("East US", "West US", "Canada Central"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKeyOrResourceToken(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy)
                .build();
```

## <a name="rest"></a>REST
Une fois qu’un compte de base de données est mis à disposition dans plusieurs régions, les clients peuvent interroger sa disponibilité en exécutant une requête GET sur l’URI suivant.

    https://{databaseaccount}.documents.azure.com/

Le service renvoie une liste des régions et leurs URI de points de terminaison Azure Cosmos DB correspondants pour les réplicas. La région d’écriture en cours est indiquée dans la réponse. Le client peut ensuite sélectionner le point de terminaison approprié pour toutes les autres requêtes d’API REST, comme suit.

Exemple de réponse

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* Les requêtes PUT, POST et DELETE doivent accéder à l’URI d’écriture indiqué
* Toutes les requêtes GET et autres demandes en lecture seule (par ex., Requêtes) peuvent accéder à n’importe quel point de terminaison choisi par le client

L’écriture de demandes dans les régions en lecture seule échoue avec le code d’erreur HTTP 403 (« Interdit »).

Si la région d’écriture change après la phase de découverte initiale du client, les écritures suivantes dans la région d’écriture précédente échouent avec le code d’erreur HTTP 403 (« Interdit »). Le client doit alors exécuter à nouveau la requête GET sur la liste des régions pour obtenir la région d’écriture mise à jour.

C’est ici que s’achève ce didacticiel. Découvrez comment gérer la cohérence de votre compte répliqué à l’échelle mondiale en lisant l’article [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md). Pour plus d’informations sur le fonctionnement de la réplication de base de données à l’échelle mondiale dans Azure Cosmos DB, voir [Diffuser des données à l’échelle mondiale avec Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez :

> [!div class="checklist"]
> * Configurer la diffusion mondiale à l’aide du portail Azure
> * Configurer la distribution mondiale à l’aide des API SQL

Vous pouvez maintenant passer au didacticiel suivant pour apprendre à développer en local à l’aide de l’émulateur local Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Développer en local avec l’émulateur](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

