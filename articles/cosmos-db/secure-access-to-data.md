---
title: Sécuriser l’accès aux données Azure Cosmos DB
description: Découvrez les concepts du contrôle d’accès dans Azure Cosmos DB, notamment les clés principales, les clés en lecture seule, les utilisateurs et les autorisations.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 6ccb9afb528edf67b29c781217ca1de97b81e017
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969204"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Sécuriser l’accès aux données dans Azure Cosmos DB

Cet article fournit une vue d’ensemble de la sécurisation de l’accès aux données stockées dans [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Azure Cosmos DB utilise deux types de clés pour authentifier les utilisateurs et permettre d’accéder à ses données et à ses ressources. 

|Type de clé|Ressources|
|---|---|
|[Clés principales](#master-keys) |Utilisées pour les ressources d’administration : comptes de base de données, bases de données, utilisateurs et autorisations|
|[Jetons de ressource](#resource-tokens)|Utilisés pour les ressources de l’application : conteneurs, documents, pièces jointes, procédures stockées, déclencheurs et fonctions définies par l’utilisateur|

<a id="master-keys"></a>

## <a name="master-keys"></a>Clés principales 

Les clés principales fournissent un accès à toutes les ressources d’administration du compte de base de données. Clés principales :  
- Fournissent un accès aux comptes, aux bases de données, aux utilisateurs et aux autorisations. 
- Ne peuvent pas être utilisées pour fournir un accès précis aux conteneurs et aux documents.
- Sont créées lors de la création d’un compte.
- Peuvent être régénérées à tout moment.

Chaque compte comporte deux clés principales : une clé primaire et une clé secondaire. L’objectif de ces paires de clés est de pouvoir régénérer ou restaurer des clés tout en fournissant un accès permanent à votre compte et à vos données. 

Outre les deux clés principales du compte Azure Cosmos DB, il existe deux clés en lecture seule. Ces clés en lecture seule autorisent uniquement les opérations de lecture sur le compte. Les clés en lecture seule ne permettent pas de lire les ressources d’autorisation.

Les clés principales primaire, secondaire, en lecture seule et en lecture-écriture peuvent être récupérées et régénérées à l’aide du portail Azure. Pour connaître la procédure, consultez [Affichage, copie et régénération des clés d’accès](manage-with-cli.md#regenerate-account-key).

![Contrôle d’accès (IAM) dans le portail Azure - Démonstration de la sécurité de la base de données NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

Le processus de rotation de votre clé principale est simple. Accédez au portail Azure pour récupérer votre clé secondaire, puis remplacez votre clé primaire par votre clé secondaire dans votre application. Enfin, faites pivoter la clé primaire dans le portail Azure.

![Rotation de clé principale dans le portail Azure - Démonstration de la sécurité de la base de données NoSQL](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>Exemple de code pour utiliser une clé principale

L’exemple de code suivant montre comment utiliser une clé principale et un point de terminaison de compte Azure Cosmos DB pour instancier un DocumentClient et créer une base de données. 

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>Jetons de ressource

Les jetons de ressource fournissent un accès aux ressources d’application au sein d’une base de données. Jetons de ressource :
- Fournissent un accès à des conteneurs, clés de partition, documents, pièces jointes, procédures stockées, déclencheurs et fonctions définies par l’utilisateur spécifiques.
- Sont créés lorsqu’un [utilisateur](#users) dispose des [autorisations](#permissions) sur une ressource spécifique.
- Sont recréés lorsqu’une ressource d’autorisation est exécutée par un appel POST, GET ou PUT.
- Utilisent un jeton de ressource de hachage créé spécifiquement pour l’utilisateur, la ressource et les autorisations.
- Sont liés à une période de validité personnalisable. La durée de validité par défaut est d’une heure. La durée de vie du jeton peut cependant être définie de manière explicite (cinq heures maximum).
- Offrent une alternative sûre pour céder la clé principale. 
- Permettent aux clients de lire, d’écrire et de supprimer des ressources dans le compte Azure Cosmos DB en fonction des autorisations qui leur ont été accordées.

Vous pouvez utiliser un jeton de ressource (en créant des utilisateurs et des autorisations Azure Cosmos DB) lorsque vous voulez fournir un accès aux ressources dans votre compte Azure Cosmos DB à un client qui ne peut pas être approuvé avec la clé principale.  

Les jetons de ressource Azure Cosmos DB offrent une alternative sûre qui permet aux clients de lire, d’écrire et de supprimer des ressources dans votre compte Azure Cosmos DB en fonction des autorisations que vous avez octroyées, sans avoir besoin d’une clé principale ou en lecture seule.

Voici un modèle de conception standard dans le cadre duquel des jetons de ressource peuvent être demandés, générés et fournis aux clients :

1. Un service de niveau intermédiaire est configuré pour servir une application mobile pour partager les photos de l'utilisateur. 
2. Le service de niveau intermédiaire détient la clé principale du compte Azure Cosmos DB.
3. L’application photo est installée sur les appareils mobiles des utilisateurs finaux. 
4. Lors de la connexion, l'application photo établit l'identité de l'utilisateur avec le service de niveau intermédiaire. Ce mécanisme d'identification dépend totalement de l'application.
5. Une fois l'identité établie, le service de niveau intermédiaire demande des autorisations en fonction de l'identité.
6. Le service de niveau intermédiaire renvoie un jeton de ressource à l'application du téléphone.
7. Cette dernière peut continuer à utiliser le jeton de ressource pour accéder directement aux ressources Azure Cosmos DB avec les autorisations définies et pendant l’intervalle autorisé. 
8. À expiration du jeton de ressource, les demandes suivantes reçoivent une exception non autorisée 401.  L'application du téléphone établit alors de nouveau l'identité de l'utilisateur et demande un nouveau jeton de ressource.

    ![Workflow de jetons de ressource Azure Cosmos DB](./media/secure-access-to-data/resourcekeyworkflow.png)

La gestion et la génération des jetons de ressource sont prises en charge par les bibliothèques clientes natives Azure Cosmos DB. Toutefois, si vous utilisez REST, vous devez créer les en-têtes de demande/d’authentification. Pour plus d’informations sur la création d’en-têtes d’authentification pour REST, consultez [Access Control on DocumentDB Resources](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) (Contrôle d’accès aux ressources Azure Cosmos DB) ou le [code source de nos Kits de développement logiciel (SDK)](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js).

Pour obtenir un exemple de service de niveau intermédiaire utilisé pour générer ou répartir les jetons de ressource, consultez [l’application ResourceTokenBroker](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers).

<a id="users"></a>

## <a name="users"></a>Utilisateurs
Les utilisateurs d’Azure Cosmos DB sont associés à une base de données Cosmos DB.  Chaque base de données peut contenir zéro, un ou plusieurs utilisateurs Azure Cosmos DB.  L’exemple de code suivant indique comment créer une ressource utilisateur DocumentDB.

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> Chaque utilisateur Azure Cosmos DB dispose d’une propriété PermissionsLink qui permet de récupérer la liste des [autorisations](#permissions) qui lui sont associées.
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>Autorisations
Une ressource d’autorisation Azure Cosmos DB est associée à un utilisateur Azure Cosmos DB.  Chaque utilisateur peut contenir zéro, une ou plusieurs autorisations Azure Cosmos DB.  Une ressource d'autorisation donne accès à un jeton de sécurité dont l'utilisateur a besoin lorsqu'il tente d'accéder à une ressource d'application spécifique.
Il existe deux niveaux d’accès disponibles qui peuvent être fournis par une ressource d’autorisation :

* Tout : l’utilisateur dispose de toutes les autorisations sur la ressource.
* Lecture : L’utilisateur peut uniquement lire le contenu de la ressource, il ne peut pas procéder à des opérations d’écriture, de mise à jour ou de suppression au niveau de la ressource.

> [!NOTE]
> Pour exécuter des procédures stockées Azure Cosmos DB, l’utilisateur doit disposer de toutes les autorisations sur le conteneur dans lequel la procédure stockée est exécutée.
> 
> 

### <a name="code-sample-to-create-permission"></a>Exemple de code pour créer une autorisation

L’exemple de code suivant indique comment créer une ressource d’autorisation, lire le jeton de ressource de la ressource d’autorisation et associer les autorisations à [l’utilisateur](#users) créé ci-dessus.

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = UriFactory.CreateDocumentCollectionUri("db", "collection"),
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

Si vous avez spécifié une clé de partition pour votre collection, l’autorisation pour les ressources de collection, de document et de pièce jointe doit inclure l’élément ResourcePartitionKey en plus de l’élément ResourceLink.

### <a name="code-sample-to-read-permissions-for-user"></a>Exemple de code pour les autorisations de lecture de l’utilisateur

Pour obtenir facilement toutes les ressources d’autorisation associées à un utilisateur, Azure Cosmos DB met à disposition un flux d’autorisations pour chaque objet utilisateur.  L'extrait de code suivant indique comment récupérer l'autorisation associée à l'utilisateur créé ci-dessus, créer une liste d'autorisations et instancier un nouveau DocumentClient pour l'utilisateur.

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="add-users-and-assign-roles"></a>Ajouter des utilisateurs et attribuer des rôles

Pour ajouter l’accès en lecture aux comptes Azure Cosmos DB à votre compte d’utilisateur, demandez à un propriétaire d’abonnement d’effectuer les étapes suivantes dans le portail Azure.

1. Ouvrez le portail Azure, puis sélectionnez votre compte Azure Cosmos DB.
2. Cliquez sur l’onglet **Contrôle d’accès (IAM)** , puis cliquez sur **+ Ajouter une attribution de rôle**.
3. Dans le volet **Ajouter une attribution de rôle**, dans la zone **Rôle**, sélectionnez **Rôle de lecteur de compte Cosmos DB**.
4. Dans la zone **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou application Azure AD**.
5. Dans votre annuaire, sélectionnez l’utilisateur, le groupe ou l’application qui doit recevoir l’accès.  Dans l’annuaire, vous pouvez rechercher des noms d’affichage, des adresses e-mail et des identificateurs d’objet.
    L’utilisateur, le groupe ou l’application s’affiche alors dans la liste des membres sélectionnés.
6. Cliquez sur **Enregistrer**.

L’entité peut désormais lire les ressources Azure Cosmos DB.

## <a name="delete-or-export-user-data"></a>Supprimer ou exporter des données utilisateur
Azure Cosmos DB vous permet de rechercher, de sélectionner, de modifier et de supprimer des données personnelles, situées dans une base de données ou des collections. Azure Cosmos DB propose des API pour rechercher et supprimer des données personnelles. Toutefois, il vous incombe d’utiliser les API et de définir la logique nécessaire pour effacer les données personnelles. Chaque API multimodèle (SQL, MongoDB, Gremlin, Cassandra, Table) fournit différents kits SDK de langage qui contiennent des méthodes pour rechercher et supprimer les données personnelles. Vous pouvez également activer la fonctionnalité de [durée de vie (TTL)](time-to-live.md) pour supprimer automatiquement les données après une période spécifiée, sans frais supplémentaires.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la sécurité de la base de données Azure Cosmos DB, consultez [Azure Cosmos DB : Sécurité de la base de données](database-security.md).
* Pour savoir comment créer des jetons d’autorisation Azure Cosmos DB, consultez [Access Control on Azure Cosmos DB Resources](https://docs.microsoft.com/rest/api/cosmos-db/access-control-on-cosmosdb-resources) (Contrôle d’accès aux ressources Azure Cosmos DB).
