---
title: 'Azure Cosmos DB : API SQL .NET, SDK et ressources'
description: Tout savoir sur l’API SQL .NET et le Kit de développement logiciel (SDK), y compris les dates de sortie, les dates de déclassement et les modifications effectuées entre chaque version du Kit de développement logiciel (SDK) .NET Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: 4f502984a09f81b5aaf0568c84b75832f8164151
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541048"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Kit de développement logiciel .NET Azure Cosmos DB pour l’API SQL : Téléchargement et notes de publication
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Flux de modification .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Java asynchrone](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [API REST Resource Provider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Téléchargement du Kit de développement logiciel (SDK)**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**Documentation de l’API**|[Documentation de référence sur l’API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Exemples**|[Exemples de code .NET](sql-api-dotnet-samples.md)|
|**Prise en main**|[Prise en main du Kit de développement logiciel (SDK) .NET Azure Cosmos DB](sql-api-get-started.md)|
|**Didacticiel d’application web**|[Développement d’applications web avec Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Infrastructure actuellement prise en charge**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>Notes de publication

### <a name="a-name3001-preview3001-preview"></a><a name="3.0.0.1-preview"/>3.0.0.1-preview
* Préversion 1 de la [version 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) du kit SDK .NET pour la préversion publique.
* .NET Standard cible, prenant en charge .NET framework 4.6.1+ et .NET Core 2.0+
* Nouveau modèle d’objet, avec le CosmosClient de niveau supérieur et les méthodes réparties entre les classes CosmosDatabases, CosmosContainers et CosmosItems appropriées. 
* Prise en charge des flux. 
* Mise à jour de CosmosResponseMessage à partir du serveur pour renvoyer le code d’état, et levée d’une seule exception si aucune réponse n’est retournée. 

### <a name="a-name251251"></a><a name="2.5.1"/>2.5.1

* La version System.Net.Http du Kit de développement logiciel (SDK) correspond désormais à celle définie dans le package NuGet.
* Les demandes d’écriture sont autorisées à basculer vers une autre région en cas de défaillance de la région d’origine.
* Une stratégie de nouvelles tentatives dans une session a été ajoutée pour une requête d’écriture.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1

* La condition de concurrence de traçage a été corrigée pour les requêtes ayant généré des pages vides

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

* Augmentation de taille de précision décimale pour les requêtes LINQ.
* Ajout de nouvelles classes CompositePath, CompositePathSortOrder, SpatialSpec, SpatialType et PartitionKeyDefinitionVersion
* Ajout de TimeToLivePropertyPath à DocumentCollection
* Ajout de CompositeIndexes et SpatialIndexes à IndexPolicy
* Ajout de Version à PartitionKeyDefinition
* Ajout de None à PartitionKey

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0

 * Ajout de IdleTcpConnectionTimeout, OpenTcpConnectionTimeout, MaxRequestsPerTcpConnection et MaxTcpConnectionsPerEndpoint à ConnectionPolicy.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* Amélioration des diagnostics

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* Ajout du paramètre de variable d'environnement « POCOSerializationOnly ».

* Suppression de DocumentDB.Spatial.Sql.dll et ajout de Microsoft.Azure.Documents.ServiceInterop.dll

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* Amélioration dans la logique de nouvelle tentative lors du basculement pour les appels d’exécution de procédure stockée.

* DocumentClientEventSource est devenu un singleton. 

* Correction du délai d’expiration de GatewayAddressCache qui ne respectait pas le délai d’expiration des requêtes de la stratégie de connexion.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* Pour les diagnostics de transport direct/TCP, ajout de TransportException, un type d’exception interne du SDK. Lorsqu’il est présent dans les messages d’exception, ce type affiche des informations supplémentaires pour la résolution des problèmes de connectivité client.

* Ajout d’une nouvelle surcharge de constructeur acceptant un HttpMessageHandler, une pile de gestionnaire HTTP à utiliser pour envoyer des demandes HttpClient (par exemple, HttpClientHandler).

* Correction de bogue où un en-tête avec des valeurs null n’est pas traité correctement.

* Validation améliorée du cache de collection.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* System.Net.Security mis à jour à 4.3.2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Améliorations du suivi des diagnostics

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1

* Ajout de davantage de résilience aux erreurs temporaires sur les requêtes multirégions.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Ajout de la prise en charge des écritures multirégions.
* Amélioration des performances des requêtes sur les partitions croisées avec TOP et MaxBufferedItemCount.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Ajout de la prise en charge de l’annulation de demande.
* Ajout de SetCurrentLocation à ConnectionPolicy, qui remplit automatiquement les emplacements par défaut en fonction de la région.
* Correction de bogue dans les requêtes entre plusieurs partitions avec Min/Max et un filtre qui correspond à Aucun document sur une partition individuelle.
* Les méthodes DocumentClient ont désormais une parité avec IDocumentClient.
* Mise à jour de la pile de transport TCP direct pour réduire le nombre de connexions établies.
* Ajout de la prise en charge du mode direct TCP pour les clients autres que Windows.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Ajout de la prise en charge de l’annulation de demande.
* Ajout de SetCurrentLocation à ConnectionPolicy, qui remplit automatiquement les emplacements par défaut en fonction de la région.
* Correction de bogue dans les requêtes entre plusieurs partitions avec Min/Max et un filtre qui correspond à Aucun document sur une partition individuelle.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* Les méthodes DocumentClient ont désormais une parité avec IDocumentClient.
* Mise à jour de la pile de transport TCP direct pour réduire le nombre de connexions établies.
* Ajout de la prise en charge du mode direct TCP pour les clients autres que Windows.

### <a name="a-name12201220"></a><a name="1.22.0"/>1.22.0

* Ajout de la propriété ConsistencyLevel à FeedOptions.
* Ajout de JsonSerializerSettings à RequestOptions et FeedOptions.
* Ajout d’EnableReadRequestsFallback à ConnectionPolicy.

### <a name="a-name12111211"></a><a name="1.21.1"/>1.21.1

* Correction de KeyNotFoundException pour une commande entre partition par requête dans des cas extrêmes.
* Correction du bogue lié au non-respect de l’attribut JsonProperty dans la clause Select pour les requêtes LINQ.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Correction du bogue survenant dans certaines conditions de concurrence, générant des erreurs « Microsoft.Azure.Documents.NotFoundException: The read session is not available for the input session token » lors de l’utilisation du niveau de cohérence de session.

### <a name="a-name12011201"></a><a name="1.20.1"/>1.20.1

* Régression corrigée où FeedOptions.MaxItemCount = -1 levait une exception System.ArithmeticException : taille de la page est négative.
* Nouvelle fonction ToString() ajoutée à QueryMetrics.
* Statistiques de partition exposées sur la lecture des collections.
* Propriété PartitionKey ajoutée à ChangeFeedOptions.
* Correctifs de bogues mineurs.

### <a name="a-name11911191"></a><a name="1.19.1"/>1.19.1

* Ajoute la possibilité de spécifier des index uniques pour les documents à l’aide de la propriété UniqueKeyPolicy sur la collection DocumentCollection.
* Correction d’un bogue par lequel les paramètres personnalisés de JsonSerializer n’étaient pas respectés pour certaines requêtes et l’exécution de la procédure stockée.

### <a name="a-name11901190"></a><a name="1.19.0"/>1.19.0

* Remplacement du nom Azure DocumentDB par le nom Azure Cosmos DB dans la documentation de référence des API, les informations de métadonnées des assemblys et le package NuGet. 
* Exposition des informations de diagnostic et de la latence à partir de la réponse aux requêtes envoyées en mode de connexion directe. Les noms des propriétés sont RequestDiagnosticsString et RequestLatency dans la classe ResourceResponse.
* Cette version du kit de développement logiciel (SDK) nécessite la dernière version de l’émulateur Azure Cosmos DB, que vous pouvez télécharger à l’adresse https://aka.ms/cosmosdb-emulator. 

### <a name="a-name11811181"></a><a name="1.18.1"/>1.18.1 

* Changements internes concernant les assemblys friend Microsoft.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Ajout de plusieurs améliorations et correctifs de fiabilité.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Ajout de la prise en charge du paramètre PartitionKeyRangeId en tant qu’option FeedOption pour la définition de l’étendue des résultats des requêtes sur une valeur de plage de clés de partition spécifique. 
* Ajout de la prise en charge du paramètre StartTime en tant qu’option ChangeFeedOption pour le démarrage de la recherche de modifications après cette heure.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Correction d’un problème dans la classe JsonSerializable, susceptible de provoquer une exception de dépassement de la capacité de la pile.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Correction d’un problème nécessitant la recompilation de l’application en raison de l’introduction de JsonSerializerSettings comme paramètre facultatif dans le constructeur DocumentClient.
* Marquage de l’obsolescence du constructeur DocumentClient nécessitant JsonSerializerSettings en tant que dernier paramètre pour autoriser les valeurs par défaut des paramètres ConnectionPolicy et ConsistencyLevel lors de la transition vers le paramètre JsonSerializerSettings.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Ajout de la prise en charge de la spécification des JsonSerializerSettings personnalisés lors de l’instanciation de [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Correction d’un problème concernant les ordinateurs x64 qui ne prennent pas en charge l’instruction SSE4 et génèrent une SEHException lors de l’exécution de requêtes SQL Azure Cosmos DB.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Prise en charge ajoutée pour un nouveau niveau de cohérence nommé ConsistentPrefix.
*   Prise en charge ajoutée pour les mesures de requête liées aux partitions individuelles.
*   Prise en charge ajoutée pour la limitation de la taille du jeton de continuation concernant les requêtes.
*   Prise en charge ajoutée pour un suivi plus détaillé des demandes ayant échoué.
*   Améliorations des performances du Kit de développement logiciel (SDK).

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Fonctionnalités identiques à 1.13.3. Plusieurs modifications internes.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Fonctionnalités identiques à 1.13.2. Plusieurs modifications internes.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Résolution du problème qui ignorait la valeur PartitionKey fournie dans FeedOptions pour les requêtes d’agrégation.
* Résolution du problème de gestion transparente des partitions pendant l’exécution d’une requête Order By (Trier par) entre partitions intermédiaire.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Résolution du problème qui provoquait des blocages parmi certaines API asynchrones lorsqu’elles sont utilisées dans le contexte ASP.NET.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Correctifs pour augmenter la résistance du Kit de développement logiciel (SDK) au basculement automatique dans certaines conditions.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Correction d’un bogue entraînant parfois une erreur WebException: Impossible de résoudre le nom distant.
* Ajout de la prise en charge permettant de lire directement un document tapé en ajoutant de nouvelles surcharges à l’API ReadDocumentAsync.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* Ajout de la prise en charge LINQ des requêtes d’agrégation (COUNT, MIN, MAX, SUM et AVG).
* Correction d’un problème de fuite de mémoire pour l’objet ConnectionPolicy dû à l’utilisation du Gestionnaire d’événements.
* Correction d’un problème de fonctionnement d’UpsertAttachmentAsync avec l’utilisation d’ETag.
* Correction d’un problème de fonctionnement de la liaison des requêtes ORDER BY entre les partitions lors d’un tri sur un champ de chaîne.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Ajout de la prise en charge des requêtes d’agrégation (COUNT, MIN, MAX, SUM et AVG). Consultez l’article [Aggregation support (Prise en charge de l’agrégation)](sql-query-aggregates.md).
* Débit minimal réduit sur les collections partitionnées de 10 100 unités de demande/s à 2 500 unités de demande/s.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Résolution d’un problème causant l’échec de certaines requêtes entre partitions dans le processus hôte 32 bits.
* Résolution d’un problème empêchant la mise à jour du conteneur de session à l’aide du jeton pour les requêtes ayant échoué en mode passerelle.
* Résolution du problème causant l’échec ponctuel d’une requête avec des appels de fonctions définies par l’utilisateur en projection.
* Correctifs des performances côté client permettant d’augmenter le débit de lecture et d’écriture des requêtes.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Résolution d’un problème empêchant la mise à jour du conteneur de session à l’aide du jeton pour les requêtes ayant échoué.
* Prise en charge du Kit de développement logiciel (SDK) dans les processus hôtes 32 bits. Notez que si vous utilisez des requêtes entre les partitions, le processus hôte 64 bits est recommandé pour améliorer les performances.
* Amélioration des performances pour les scénarios impliquant des requêtes avec un grand nombre de valeurs de clé de partition dans une expression IN.
* Remplissage de diverses statistiques de quota de ressources dans ResourceResponse pour les requêtes de lecture sur la collection de documents lorsque l’option de requête PopulateQuotaInfo est définie.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Correction mineure des performances de l’API CreateDocumentCollectionIfNotExistsAsync introduit dans la version 1.11.0.
* Correction des performances dans le Kit de développement logiciel (SDK) pour les scénarios impliquant un degré élevé de demandes simultanées.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Prise en charge de nouvelles classes et méthodes pour traiter le [flux de modification](change-feed.md) de documents dans une collection.
* Prise en charge de la continuité des requêtes sur plusieurs partitions et de quelques améliorations des performances pour les requêtes entre partitions.
* Ajout des méthodes CreateDatabaseIfNotExistsAsync et CreateDocumentCollectionIfNotExistsAsync.
* Prise en charge LINQ des fonctions système : IsDefined, IsNull et IsPrimitive.
* Correction du placement automatique des assemblys Microsoft.Azure.Documents.ServiceInterop.dll et DocumentDB.Spatial.Sql.dll dans le dossier bin de l’application lorsque le package NuGet est utilisé avec des projets qui disposent des outils project.json.
* Prise en charge de l’émission de traces ETW de côté client qui pourraient être utiles dans les scénarios de débogage.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Ajout de la prise en charge de la connectivité directe pour les collections partitionnées.
* Amélioration des performances pour le niveau de cohérence en fonction de l’obsolescence.
* Ajout des types de données Polygone et LineString lors de la spécification de la stratégie d’indexation de collection pour les requêtes spatiales à délimitation géographique.
* Ajout de la prise en charge de LINQ pour StringEnumConverter, IsoDateTimeConverter et UnixDateTimeConverter lors de la traduction de prédicats.
* Divers correctifs de bogues de kits de développement logiciel (SDK).

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Correction d’un bogue qui provoquait l’exception NotFoundException suivante : The read session is not available for the input session token. Cette exception se produisait dans certains cas lors de l’interrogation de la région de lecture d’un compte géo-distribué.
* Exposition de la propriété ResponseStream dans la classe ResourceResponse, qui permet d’accéder directement au flux sous-jacent à partir d’une réponse.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Modification des classes ResourceResponse, FeedResponse, StoredProcedureResponse et MediaResponse pour implémenter l’interface publique correspondante afin de pouvoir les simuler facilement pour les déploiements basés sur les tests (TDD).
* Résolution du problème qui provoquait la malformation d’un en-tête de clé de partition lors de l’utilisation d’un objet JsonSerializerSettings personnalisé pour la sérialisation des données.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Correction d’un bogue qui provoquait l’échec des requêtes longues avec l’erreur : Authorization token is not valid at the current time.
* Résolution du problème qui a supprimé la classe SqlParameterCollection d’origine des requêtes Top/Order-by inter-partition.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Prise en charge ajoutée de requêtes parallèles pour les collections partitionnées.
* Prise en charge ajoutée des requêtes ORDER BY et TOP sur plusieurs partitions pour les collections partitionnées.
* Résolution des références manquantes à DocumentDB.Spatial.Sql.dll et à Microsoft.Azure.Documents.ServiceInterop.dll qui sont nécessaires lors du référencement d’un projet Azure Cosmos DB avec une référence au package Azure Cosmos DB Nuget.
* Résolution des problèmes liés à la possibilité d’utiliser des paramètres de types différents lors de l’utilisation de fonctions définies par l’utilisateur par le dans LINQ. 
* Correction d’un bogue pour les comptes de réplication globale lorsque des appels Upsert étaient transmis vers des emplacements de lecture au lieu d’emplacements d’écriture.
* Méthodes ajoutées à l’interface IDocumentClient qui étaient absentes : 
  * Méthode UpsertAttachmentAsync qui accepte mediaStream et les options en tant que paramètres
  * Méthode CreateAttachmentAsync qui accepte les options en tant que paramètres
  * Méthode CreateOfferQuery qui accepte querySpec en tant que paramètre.
* Des classes publiques non scellées qui sont exposées dans l’interface IDocumentClient.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Ajout de la prise en charge des comptes de base de données de plusieurs régions.
* Ajout de la prise en charge d’une nouvelle tentative pour les requêtes limitées.  L’utilisateur peut personnaliser le nombre de nouvelles tentatives et le temps d’attente maximal en configurant la propriété ConnectionPolicy.RetryOptions.
* Ajout d'une nouvelle interface IDocumentClient qui définit les signatures de toutes les propriétés et méthodes DocumentClient.  Dans le cadre de cette modification, les méthodes d’extension qui créent des propriétés IQueryable et IOrderedQueryable ont également été converties en méthodes de la classe DocumentClient.
* Ajout d’une option de configuration permettant de définir la propriété ServicePoint.ConnectionLimit pour un URI de point de terminaison Azure Cosmos DB donné.  Utilisez ConnectionPolicy.MaxConnectionLimit pour modifier la valeur par défaut, définie sur 50.
* Propriété IPartitionResolver déconseillée et son implémentation.  La prise en charge de IPartitionResolver est désormais obsolète. Il est recommandé d’utiliser des collections partitionnées pour bénéficier d’un niveau de stockage et de débit supérieur.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Ajout d’une surcharge à l’URI basée sur la méthode ExecuteStoredProcedureAsync qui prend RequestOptions comme paramètre.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Ajout de la prise en charge de la durée de vie (TTL) pour les documents.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Correction d’un bogue dans l’empaquetage Nuget du Kit de développement logiciel (SDK) .NET pour son empaquetage en tant que partie d’une solution Azure Service Cloud.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Implémentation des [collections partitionnées](partition-data.md) et des [niveaux de performances définis par l’utilisateur](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Résolu]**  L’interrogation du point de terminaison de requêtes Azure Cosmos DB génère : 'System.Net.Http.HttpRequestException: Error while copying content to a stream'.

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* Extension de la prise en charge de LINQ, y compris de nouveaux opérateurs pour la pagination, les expressions conditionnelles et la comparaison de plages.
  * Opérateur Take pour activer le comportement SELECT TOP dans LINQ
  * Opérateur CompareTo pour activer les comparaisons de plages de chaînes
  * Opérateurs Conditional (?) et Coalesce (??)
* **[Résolu]** ArgumentOutOfRangeException en cas de combinaison de la projection de modèle avec Where-In dans la requête LINQ. [No.81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Résolu]** Si la dernière expression n’est pas Select, le fournisseur LINQ n’effectuait aucune projection et créait une expression SELECT * incorrecte.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Mise en œuvre de l’opération Upsert, ajout des méthodes UpsertXXXAsync
* Améliorations des performances pour toutes les requêtes
* Prise en charge par le fournisseur LINQ des méthodes conditionnelles, de fusion et CompareTo pour les chaînes
* **[Résolu]** Fournisseur LINQ --&gt; Implémentation de la méthode Contains sur List pour générer le même SQL que sur IEnumerable et Array
* **[Résolu]** BackoffRetryUtility utilise de nouveau le même HttpRequestMessage au lieu d’en créer un nouveau à chaque tentative
* **[Obsolète]** UriFactory.CreateCollection --&gt; doit désormais utiliser UriFactory.CreateDocumentCollection

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Résolu]** Erreurs de localisation lors de l’utilisation d’autres langues que l’anglais, comme nl-NL, etc. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Ajout du routage basé sur l’ID
  * Nouvelle application auxiliaire UriFactory pour faciliter la construction de liens de ressources basés sur l’ID
  * Nouvelles surcharges sur DocumentClient pour prendre en compte l’URI
* Ajout de IsValid() et IsValidDetailed() dans LINQ pour les données géospatiales
* Amélioration de la prise en charge du fournisseur LINQ :
  * **Mathématiques** - Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate
  * **Chaîne** - Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper
  * **Tableau** - Concat, Contains, Count
  * **IN**

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Ajout de la prise en charge de la modification des stratégies d’indexation.
  * Nouvelle méthode ReplaceDocumentCollectionAsync dans DocumentClient
  * Nouvelle propriété IndexTransformationProgress dans ResourceResponse<T> pour le suivi de l’évolution du pourcentage de modification de la stratégie d’indexation
  * DocumentCollection.IndexingPolicy est désormais mutable
* Ajout de la prise en charge de l’indexation et des requêtes spatiales.
  * Nouvel espace de noms Microsoft.Azure.Documents.Spatial pour la sérialisation/désérialisation des types de données spatiales comme Point et Polygon
  * Nouvelle classe SpatialIndex pour l’indexation des données GeoJSON stockées dans Cosmos DB
* **[Résolu]** Requête SQL incorrecte générée à partir d’une expression LINQ [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Ajout d’une dépendance sur Newtonsoft.Json v5.0.7.
* Modifications pour prendre en charge la clause Order By :
  
  * Prise en charge du fournisseur LINQ pour OrderBy() ou OrderByDescending()
  * IndexingPolicy pour prendre en charge la clause Order By 
    
    **Modification avec rupture possible** 
    
    Si vous avez un code qui configure les collections avec une stratégie d’indexation personnalisée, vous devez le mettre à jour de sorte qu’il prenne en charge la nouvelle classe IndexingPolicy. Si vous n'avez pas de stratégie d'indexation personnalisée, cette modification ne vous affecte pas.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Ajout de la prise en charge du partitionnement des données à l’aide des nouvelles classes HashPartitionResolver et RangePartitionResolver et de IPartitionResolver.
* Ajout de la sérialisation DataContract.
* Ajout de la prise en charge de l’identificateur global unique dans le fournisseur LINQ.
* Ajout de la prise en charge d’UDF dans LINQ.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Kit SDK GA

## <a name="release--retirement-dates"></a>Dates de lancement et de suppression
Microsoft envoie une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit SDK actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du SDK dès que possible. 

Toute requête envoyée à Azure Cosmos DB à l’aide d’un Kit de développement logiciel (SDK) supprimé est rejetée par le service.

<br/>

| Version | Date de lancement | Date de suppression |
| --- | --- | --- |
| [2.5.1](#2.5.1) |2 juillet 2019 |--- |
| [2.4.1](#2.4.1) |20 juin 2019 |--- |
| [2.4.0](#2.4.0) |5 mai 2019 |--- |
| [2.3.0](#2.3.0) |4 avril 2019 |--- |
| [2.2.3](#2.2.3) |11 février 2019 |--- |
| [2.2.2](#2.2.2) |06 février 2019 |--- |
| [2.2.1](#2.2.1) |24 décembre 2018 |--- |
| [2.2.0](#2.2.0) |7 décembre 2018 |--- |
| [2.1.3](#2.1.3) |15 octobre 2018 |--- |
| [2.1.2](#2.1.2) |4 octobre 2018 |--- |
| [2.1.1](#2.1.1) |27 septembre 2018 |--- |
| [2.1.0](#2.1.0) |21 septembre 2018 |--- |
| [2.0.0](#2.0.0) |07 septembre 2018 |--- |
| [1.22.0](#1.22.0) |19 avril 2018 |--- |
| [1.21.1](#1.20.1) |09 mars 2018 |--- |
| [1.20.2](#1.20.1) |21 février 2018 |--- |
| [1.20.1](#1.20.1) |05 février 2018 |--- |
| [1.19.1](#1.19.1) |16 novembre 2017 |--- |
| [1.19.0](#1.19.0) |10 novembre 2017 |--- |
| [1.18.1](#1.18.1) |7 novembre 2017 |--- |
| [1.18.0](#1.18.0) |17 octobre 2017 |--- |
| [1.17.0](#1.17.0) |10 août 2017 |--- |
| [1.16.1](#1.16.1) |7 août 2017 |--- |
| [1.16.0](#1.16.0) |2 août 2017 |--- |
| [1.15.0](#1.15.0) |30 juin 2017 |--- |
| [1.14.1](#1.14.1) |23 mai 2017 |--- |
| [1.14.0](#1.14.0) |10 mai 2017 |--- |
| [1.13.4](#1.13.4) |09 mai 2017 |--- |
| [1.13.3](#1.13.3) |06 mai 2017 |--- |
| [1.13.2](#1.13.2) |19 avril 2017 |--- |
| [1.13.1](#1.13.1) |29 mars 2017 |--- |
| [1.13.0](#1.13.0) |24 mars 2017 |--- |
| [1.12.2](#1.12.2) |20 mars 2017 |--- |
| [1.12.1](#1.12.1) |14 mars 2017 |--- |
| [1.12.0](#1.12.0) |15 février 2017 |--- |
| [1.11.4](#1.11.4) |06 février 2017 |--- |
| [1.11.3](#1.11.3) |26 janvier 2017 |--- |
| [1.11.1](#1.11.1) |21 décembre 2016 |--- |
| [1.11.0](#1.11.0) |8 décembre 2016 |--- |
| [1.10.0](#1.10.0) |27 septembre 2016 |--- |
| [1.9.5](#1.9.5) |1er septembre 2016 |--- |
| [1.9.4](#1.9.4) |24 août 2016 |--- |
| [1.9.3](#1.9.3) |15 août 2016 |--- |
| [1.9.2](#1.9.2) |23 juillet 2016 |--- |
| [1.8.0](#1.8.0) |14 juin 2016 |--- |
| [1.7.1](#1.7.1) |6 mai 2016 |--- |
| [1.7.0](#1.7.0) |26 avril 2016 |--- |
| [1.6.3](#1.6.3) |8 avril 2016 |--- |
| [1.6.2](#1.6.2) |29 mars 2016 |--- |
| [1.5.3](#1.5.3) |19 février 2016 |--- |
| [1.5.2](#1.5.2) |14 décembre 2015 |--- |
| [1.5.1](#1.5.1) |23 novembre 2015 |--- |
| [1.5.0](#1.5.0) |5 octobre 2015 |--- |
| [1.4.1](#1.4.1) |25 août 2015 |--- |
| [1.4.0](#1.4.0) |13 août 2015 |--- |
| [1.3.0](#1.3.0) |5 août 2015 |--- |
| [1.2.0](#1.2.0) |6 juillet 2015 |--- |
| [1.1.0](#1.1.0) |30 avril 2015 |--- |
| [1.0.0](#1.0.0) |8 avril 2015 |--- |


## <a name="faq"></a>Forum Aux Questions
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

