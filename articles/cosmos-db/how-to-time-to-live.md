---
title: Découvrir comment configurer et gérer la durée de vie dans Azure Cosmos DB
description: Découvrir comment configurer et gérer la durée de vie dans Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 618e7e19b20f361aa0a8c668e9621a29db43772d
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797751"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Configurer la durée de vie dans Azure Cosmos DB

Dans Azure Cosmos DB, vous pouvez choisir de configurer la durée de vie (TTL) au niveau du conteneur, mais vous pouvez aussi la définir au niveau de l’élément après avoir configuré le conteneur. Vous pouvez configurer la TTL d’un conteneur en utilisant le portail Azure ou les kits SDK propres à chaque langage. Les remplacements de la TTL au niveau de l’élément peuvent être configurés avec les kits SDK.

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Activer la durée de vie sur un conteneur avec le portail Azure

Utilisez les étapes suivantes pour activer la durée de vie sur un conteneur sans date d’expiration. Activez cette option pour autoriser le remplacement de la TTL au niveau de l’élément. Vous pouvez également définir la TTL en entrant une valeur différente de zéro pour les secondes.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

2. Créez un compte Azure Cosmos ou sélectionnez un compte existant.

3. Ouvrez le volet **Explorateur de données**.

4. Sélectionnez un conteneur existant, développez-le et modifiez les valeurs suivantes :

   * Ouvrez la fenêtre **Mise à l’échelle et paramètres**.
   * Sous **Paramètres**, recherchez **Durée de vie**.
   * Sélectionnez **Activée (pas par défaut)** ou sélectionnez **Activée** et définissez une valeur de TTL.
   * Cliquez sur **Enregistrer** pour enregistrer les modifications.

   ![Configuration de la durée de vie dans le portail Azure](./media/how-to-time-to-live/how-to-time-to-live-portal.png)


- Quand DefaultTimeToLive est null, votre Durée de vie est Désactivée
- Lorsque DefaultTimeToLive est -1, votre Durée de vie est Activée (pas par défaut)
- Quand DefaultTimeToLive a une autre valeur Int (à l’exception de 0), votre Durée de vie est Activée

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Activer la durée de vie sur un conteneur à l’aide du kit SDK

### <a id="dotnet-enable-noexpiry"></a>Kit SDK .NET

```csharp
// Create a new collection with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Définir la durée de vie sur un conteneur à l’aide du kit SDK

### <a id="dotnet-enable-withexpiry"></a>Kit SDK .NET

Pour définir la durée de vie sur un conteneur, vous devez fournir un nombre positif non nul qui indique la durée en secondes. Selon la valeur de la TTL configurée, tous les éléments présents dans le conteneur sont supprimés après l’horodatage de la dernière modification de l’élément `_ts`.

```csharp
// Create a new collection with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition,
    new RequestOptions { OfferThroughput = 20000 });
```

### <a id="nodejs-enable-withexpiry"></a>Kit de développement logiciel (SDK) NodeJS

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

## <a name="set-time-to-live-on-an-item"></a>Définir la durée de vie sur un élément

Non seulement vous pouvez définir une durée de vie par défaut sur un conteneur, mais vous pouvez aussi définir une durée de vie pour un élément. La définition de la durée de vie au niveau d’un élément remplace la valeur TTL par défaut de l’élément dans le conteneur donné.

* Pour définir la TTL sur un élément, vous devez fournir un nombre positif non nul qui indique, en secondes, le délai d’expiration de l’élément après l’horodatage de la dernière modification de l’élément `_ts`.

* Si l’élément ne présente aucun champ TTL, la durée de vie définie par défaut dans le conteneur s’applique à l’élément.

* Si la TTL est désactivée au niveau du conteneur, le champ TTL de l’élément est ignoré jusqu’à ce que la TTL soit de nouveau activée sur le conteneur.

### <a id="portal-set-ttl-item"></a>Portail Azure

Utilisez les étapes suivantes pour activer la durée de vie sur un élément :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

2. Créez un compte Azure Cosmos ou sélectionnez un compte existant.

3. Ouvrez le volet **Explorateur de données**.

4. Sélectionnez un conteneur existant, développez-le et modifiez les valeurs suivantes :

   * Ouvrez la fenêtre **Mise à l’échelle et paramètres**.
   * Sous **Paramètres**, recherchez **Durée de vie**.
   * Sélectionnez **Activée (pas par défaut)** ou sélectionnez **Activée** et définissez une valeur de TTL. 
   * Cliquez sur **Enregistrer** pour enregistrer les modifications.

5. Accédez ensuite à l’élément pour lequel vous souhaitez définir une durée de vie, ajoutez la propriété `ttl`, puis sélectionnez **Mettre à jour**. 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a id="dotnet-set-ttl-item"></a>Kit SDK .NET

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a id="nodejs-set-ttl-item"></a>Kit de développement logiciel (SDK) NodeJS

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value", 
          ttl: 2
        };
```


## <a name="reset-time-to-live"></a>Réinitialiser la durée de vie

Vous pouvez réinitialiser la durée de vie sur un élément en effectuant une opération d’écriture ou de mise à jour sur celui-ci. L’opération d’écriture ou de mise à jour définit l’élément `_ts` sur l’heure actuelle, et la durée de vie déclenchant l’expiration de l’élément recommence son décompte. Si vous souhaitez modifier la durée de vie d’un élément, vous pouvez mettre à jour ce champ de la même manière que vous mettriez à jour n’importe quel autre champ.

### <a id="dotnet-extend-ttl-item"></a>Kit SDK .NET

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="turn-off-time-to-live"></a>Suspendre la durée de vie

Si la durée de vie a été définie sur un élément et que vous ne souhaitez plus faire expirer cet élément, vous pouvez récupérer l’élément, supprimer le champ TTL et replacer l’élément sur le serveur. Lorsque le champ TTL est supprimé de l’élément, la valeur de durée de vie par défaut affectée au conteneur est appliquée à l’élément. Définissez la valeur de durée de vie sur -1, pour empêcher l’expiration d’un élément et pour ne pas hériter de la valeur de durée de vie du conteneur.

### <a id="dotnet-turn-off-ttl-item"></a>Kit SDK .NET

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the collection

response = await client.ReplaceDocumentAsync(readDocument);
```

## <a name="disable-time-to-live"></a>Désactiver la durée de vie

Pour désactiver la durée de vie sur un conteneur et arrêter le processus en arrière-plan qui recherche les éléments ayant expiré, la propriété `DefaultTimeToLive` sur le conteneur doit être supprimée. Supprimer cette propriété ne revient pas à la définir sur -1. Lorsque vous la définissez sur -1, les nouveaux éléments ajoutés au conteneur continuent de vivre indéfiniment, mais vous pouvez remplacer cette valeur sur des éléments spécifiques du conteneur. Lorsque vous supprimez du conteneur la propriété de durée de vie, les éléments n’expirent jamais, même si la précédente valeur de durée de vie par défaut a été explicitement remplacée.

### <a id="dotnet-disable-ttl"></a>Kit SDK .NET

```csharp
// Get the collection, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur la durée de vie dans l’article suivant :

* [Durée de vie](time-to-live.md)
