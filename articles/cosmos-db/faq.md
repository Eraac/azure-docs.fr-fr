---
title: Questions fréquentes sur les différentes API dans Azure Cosmos DB
description: Obtenez des réponses aux questions fréquemment posées sur Azure Cosmos DB, un service de base de données multimodèle distribué globalement. Découvrez la capacité, les niveaux de performances et la mise à l’échelle.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 89c04a55138d57fd2ff37a96f2bc92b12d1780d9
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341236"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Questions fréquentes sur les différentes API dans Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Quels sont les cas d’utilisation courants d’Azure Cosmos DB ?

Azure Cosmos DB est un choix judicieux pour les nouvelles applications web, mobiles, de jeu et IoT où une mise à l’échelle automatique, des performances prévisibles, des temps de réponse rapides de l’ordre d’une milliseconde et la capacité à exécuter des requêtes sur des données sans schéma sont importants. Azure Cosmos DB permet un développement rapide et prend en charge l’itération continue des modèles de données d’application. Les applications qui gèrent du contenu et des données générés par l’utilisateur sont [des cas d’utilisation courants pour Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Comment Azure Cosmos DB offre-t-il des performances prévisibles ?

Dans Azure Cosmos DB, la mesure du débit est exprimée en [unités de requête](request-units.md) (RU). Un débit de 1 RU correspond au débit de la requête GET d’un document de 1 Ko. Chaque opération dans Azure Cosmos DB, y compris les lectures, les écritures, les requêtes SQL et les exécutions de procédures stockées, comporte une valeur de RU déterministe basée sur le débit nécessaire à l’exécution de l’opération. Au lieu de penser à l’UC, à l’E/S et à la mémoire, ainsi qu’à la façon dont ces éléments affectent le débit de votre application, vous pouvez penser en termes de mesure de RU unique.

Vous pouvez configurer chaque conteneur Azure Cosmos avec un débit provisionné en termes de RU de débit par seconde. Pour les applications de toute échelle, vous pouvez évaluer les requêtes individuelles pour mesurer leur valeur de RU, et approvisionner un conteneur pour gérer la totalité des unités de requête sur l’ensemble des requêtes. Vous pouvez également mettre à l’échelle le débit de votre conteneur à mesure de l’évolution des besoins de votre application. Pour plus d’informations sur les unités de requête et pour obtenir de l’aide afin de déterminer vos besoins en matière de conteneurs, essayez la [calculatrice de débit](https://www.documentdb.com/capacityplanner).

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Comment Azure Cosmos DB prend-il en charge différents modèles de données comme clé/valeur, en colonnes, document et graphe ?

Les modèles de données clé/valeur (table), en colonnes, document et graphe sont tous pris en charge de manière native en raison de la conception ARS (atomes, enregistrements et séquences) sur laquelle repose Azure Cosmos DB. Les atomes, enregistrements et séquences peuvent facilement être mappés et projetés vers différents modèles de données. Les API pour certains modèles sont déjà disponibles (SQL, MongoDB, Table et Gremlin), tandis que d’autres API propres à des modèles de données supplémentaires seront disponibles plus tard.

Azure Cosmos DB a un moteur d’indexation indépendant du point de vue du schéma capable d’indexer automatiquement toutes les données qu’il reçoit sans nécessiter de schéma ou d’index secondaire de la part du développeur. Le moteur s’appuie sur un ensemble de dispositions d’index logiques (inversée, en colonnes, arborescence) qui découplent la disposition de stockage des sous-systèmes de traitement de requêtes et d’index. Cosmos DB peut également prendre en charge un ensemble de protocoles de transmission et d’API de manière extensible, et les traduire efficacement vers le modèle de données principal (1) et les dispositions d’index logiques (2), ce qui en fait une solution unique capable de prendre en charge plus d’un modèle de données en mode natif.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB est-il conforme à la loi HIPAA ?

Oui, Azure Cosmos DB est conforme à la loi HIPAA. HIPAA établit les conditions requises pour l’utilisation, la divulgation et la protection des informations de santé identifiables de façon individuelle. Pour plus d’informations, consultez le [Centre de gestion de la confidentialité de Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Quelles sont les limites de stockage d’Azure Cosmos DB ?

Il n’existe aucune limite à la quantité totale de données qu’un conteneur peut stocker dans Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Quelles sont les limites de débit d’Azure Cosmos DB ?

Il n’existe aucune limite au débit total qu’un conteneur peut prendre en charge dans Azure Cosmos DB. L’idée principale est de distribuer votre charge de travail à peu près uniformément entre un nombre suffisant de clés de partition.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Les modes de connectivité directe et de passerelle sont-ils chiffrés ?

Oui, les deux modes sont toujours entièrement chiffrés.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Combien coûte Azure Cosmos DB ?

Pour plus d’informations, voir [Tarification d’Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Les frais d’utilisation d’Azure Cosmos DB sont déterminés par le nombre de conteneurs approvisionnés, le nombre d’heures durant lequel les conteneurs ont été en ligne et le débit approvisionné pour chaque conteneur.

### <a name="is-a-free-account-available"></a>Un compte gratuit est-il disponible ?

Oui, vous pouvez bénéficier d’un compte à durée limitée sans frais ni engagement. Pour y souscrire, accédez à la page [Essayez gratuitement Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) ou consultez les [questions fréquentes (FAQ) sur Azure Cosmos DB](#try-cosmos-db) pour en savoir plus.

Si vous débutez avec Azure, vous pouvez vous inscrire pour bénéficier d’un [compte Azure gratuit](https://azure.microsoft.com/free/), qui vous donne 30 jours et un crédit pour essayer tous les services Azure. Si vous avez un abonnement Visual Studio, vous pouvez aussi bénéficier de [crédits Azure gratuits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) à utiliser sur n’importe quel service Azure.

Vous pouvez également utiliser l’[émulateur Azure Cosmos DB](local-emulator.md) pour développer et tester votre application localement, sans créer d’abonnement Azure et sans frais. Lorsque vous êtes satisfait du fonctionnement de votre application dans l’émulateur Azure Cosmos DB, vous pouvez commencer à utiliser un compte Azure Cosmos DB dans le cloud.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Comment puis-je obtenir une aide supplémentaire avec Azure Cosmos DB ?

Pour poser une question technique, connectez-vous à l’un de ces forums de questions-réponses :

* [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Consultez Stack Overflow pour les questions sur la programmation. Par souci de clarté et afin d’obtenir une réponse, vérifiez que votre question est [appropriée](https://stackoverflow.com/help/on-topic) et [ fournit un maximum de détails](https://stackoverflow.com/help/how-to-ask).

Pour demander de nouvelles fonctionnalités, faites une nouvelle demande sur [User voice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Pour résoudre un problème relatif à votre compte, enregistrez une [demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) sur le portail Azure.

Vous pouvez soumettre d’autres questions à l’équipe [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com). Cette adresse n’est pas un alias pour le support technique.

## <a id="try-cosmos-db"></a>Souscrire à l’offre Essayez gratuitement Azure Cosmos DB

Vous pouvez désormais profiter pendant une durée limitée de l’expérience Azure Cosmos DB, sans abonnement, libre de frais et d’engagement. Pour souscrire à l’offre Essayez gratuitement Azure Cosmos DB, accédez à la page [Essayez gratuitement Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/). Cette souscription est indépendante de l’offre [Créez votre compte Azure gratuit](https://azure.microsoft.com/free/) et peut être couplée à celle-ci ou à un abonnement Azure payant.

Les souscriptions à l’offre Essayez gratuitement Azure Cosmos DB figurent dans le portail Azure aux côtés des autres abonnements associés à votre ID utilisateur.

Les souscriptions à l’offre Essayez gratuitement Azure Cosmos DB obéissent aux conditions suivantes :

* Un seul [conteneur avec débit provisionné](./set-throughput.md#set-throughput-on-a-container) par abonnement pour les comptes SQL, API Gremlin et Table.
* Jusqu’à trois [collections avec débit provisionné](./set-throughput.md#set-throughput-on-a-container) par abonnement pour les comptes MongoDB.
* Une [base de données avec débit provisionné](./set-throughput.md#set-throughput-on-a-database) par abonnement. Les bases de données avec débit provisionné peuvent inclure un nombre illimité de conteneurs.
* 10 Go de capacité de stockage.
* La réplication globale est disponible dans les [régions Azure](https://azure.microsoft.com/regions/) suivantes : USA Centre, Europe Nord et Asie Sud-Est
* Débit maximal de 5 000 RU/s lors de l’approvisionnement au niveau du conteneur.
* Débit maximal de 20 000 RU/s lors de l’approvisionnement au niveau de la base de données.
* Les abonnements expirent dans un délai de 30 jours et peuvent être prolongés pour une durée totale maximale de 31 jours.
* Il n’est pas possible de créer des tickets de support Azure pour les comptes d’évaluation d’Azure Cosmos DB ; cependant, les abonnés titulaires de plans de support actifs peuvent bénéficier du support.

## <a name="set-up-azure-cosmos-db"></a>Configurer Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Comment s’inscrire pour Azure Cosmos DB ?

Azure Cosmos DB est disponible dans le portail Azure. Tout d’abord, souscrivez un abonnement Azure. Une fois que vous êtes inscrit, vous pouvez ajouter un compte Azure Cosmos DB à votre abonnement Azure.

### <a name="what-is-a-master-key"></a>Qu'est-ce qu’une clé principale ?

Une clé principale est un jeton de sécurité permettant d'accéder à toutes les ressources d'un compte. Les personnes disposant de cette clé ont un accès en lecture et en écriture à toutes les ressources du compte de la base de données. Soyez prudent lorsque vous distribuez des clés principales. La clé principale primaire et la clé principale secondaire sont disponibles dans le panneau **Clés** du [Portail Azure][azure-portal]. Pour plus d’informations sur les clés, consultez la rubrique [Affichage, copie et régénération de clés d’accès](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Quelles sont les régions configurables pour PreferredLocations ?

La valeur de PreferredLocations peut être définie sur toute région Azure dans laquelle Cosmos DB est disponible. Pour obtenir la liste des régions disponibles, voir [Régions Azure](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Y a-t-il quelque chose que je dois savoir concernant la distribution de données à travers le monde via les centres de données Azure ?

Azure Cosmos DB est présent dans toutes les régions Azure, comme l’indique la page [Régions Azure](https://azure.microsoft.com/regions/). Comme il s’agit du service principal, chaque nouveau centre de données a une présence Azure Cosmos DB.

Lorsque vous définissez une région, n’oubliez pas qu’Azure Cosmos DB respecte les clouds souverains et du secteur public. Autrement dit, si vous créez un compte dans une [région souveraine](https://azure.microsoft.com/global-infrastructure/), vous ne pouvez pas répliquer en dehors de cette [région souveraine](https://azure.microsoft.com/global-infrastructure/). De même, vous ne pouvez pas activer la réplication dans d’autres emplacements souverains à partir d’un compte externe.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Est-il possible de passer du provisionnement de débit au niveau du conteneur au provisionnement de débit au niveau de la base de données ? Et inversement ?

Le provisionnement de débit au niveau du conteneur et de la base de données constituent des offres distinctes, et tout changement de l’un vers l’autre nécessite la migration de données de la source vers la destination. Cela signifie donc que vous devez créer une base de données ou une collection, puis migrer les données avec la [bibliothèque de l’exécuteur en bloc](bulk-executor-overview.md) ou [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Azure CosmosDB prend-il en charge l’analyse des séries chronologiques ?

Oui, Azure CosmosDB prend en charge l’analyse des séries chronologiques. Voici un exemple de [modèle de série chronologique](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Cet exemple montre comment utiliser des flux de modification pour créer des affichages agrégés sur les données de série chronologique. Vous pouvez étendre cette approche à l’aide de Spark Streaming ou d’un autre processeur de données de flux.

## <a name="sql-api"></a>API SQL

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Comment commencer à développer par rapport l’API SQL ?

Vous devez d’abord souscrire un abonnement Microsoft Azure. Après avoir souscrit un abonnement Microsoft Azure, vous pouvez ajouter un conteneur d’API SQL à votre abonnement Azure. Pour savoir comment ajouter un compte Azure Cosmos DB, consultez [Créer un compte de base de données Azure Cosmos DB](create-sql-api-dotnet.md#create-account).

[Kits de développement logiciel (SDK)](sql-api-sdk-dotnet.md) sont disponibles pour .NET, Python, Node.js, JavaScript et Java. Les développeurs peuvent également utiliser les [API HTTP RESTful](/rest/api/cosmos-db/) pour interagir avec les ressources Azure Cosmos DB sur différentes plateformes et dans de nombreux langages.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Puis-je accéder à certains exemples prêts à l’emploi pour gagner du temps ?

Des exemples pour les SDK [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md) et [Python](sql-api-python-samples.md) de l’API SQL sont disponibles sur GitHub.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>La base de données de l’API SQL prend-elle en charge les données sans schéma ?

Oui, l’API SQL permet aux applications de stocker des documents JSON arbitraires sans définition ou conseil de schéma. Les données peuvent être interrogées immédiatement via l’interface de requête SQL d’Azure Cosmos DB.

### <a name="does-the-sql-api-support-acid-transactions"></a>L’API SQL prend-elle en charge les transactions ACID ?

Oui, l’API SQL prend en charge les transactions entre documents exprimées sous forme de procédures stockées et de déclencheurs JavaScript. Les transactions sont étendues à une seule partition au sein de chaque conteneur, et exécutées en mode « Tout ou rien » avec des sémantiques ACID, isolément d’autres codes et requêtes utilisateur s’exécutant simultanément. Si des exceptions surviennent lors de l’exécution du code d’application JavaScript côté serveur, la transaction entière est annulée. 

### <a name="what-is-a-container"></a>Qu’est-ce qu’un conteneur ?

Un conteneur est un groupe de documents accompagnés de leur logique d’application JavaScript. Un conteneur est une entité facturable, où le [coût](performance-levels.md) est déterminé par le débit et le stockage utilisé. Des conteneurs peuvent s’étendre à un ou plusieurs serveurs ou partitions, et peuvent être mis à l’échelle pour gérer des volumes de stockage ou de débit pratiquement illimités.

* Pour les comptes d’API SQL et les comptes d’API Azure Cosmos DB pour MongoDB, les conteneurs sont mappés vers des collections.
* Pour les comptes d’API Cassandra et Table, les conteneurs sont mappés vers des tables.
* Pour les comptes d’API Gremlin, les conteneurs sont mappés vers des graphes.

Les conteneurs constituent également les entités de facturation d’Azure Cosmos DB. Chaque conteneur est facturé à l’heure, sur la base du débit provisionné et de l’espace de stockage utilisé. Pour plus d’informations, consultez la [Tarification Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Comment créer une base de données ?

Vous pouvez créer des bases de données à l’aide du [portail Azure](https://portal.azure.com) comme décrit dans [Ajouter une collection](create-sql-api-dotnet.md#create-collection-database), de l’un des [Kits de développement logiciel (SDK) Azure Cosmos DB](sql-api-sdk-dotnet.md) ou d’[API REST](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Comment configurer des utilisateurs et des autorisations ?

Vous pouvez créer des utilisateurs et des autorisations à l’aide de l’un des [SDK d’API Cosmos DB](sql-api-sdk-dotnet.md) ou des [API REST](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>L’API SQL prend-elle en charge SQL ?

Le langage de requête SQL pris en charge par les comptes d’API SQL est un sous-ensemble amélioré des fonctionnalités de requête prises en charge par SQL Server. Le langage de requête SQL d’Azure Cosmos DB fournit des opérateurs hiérarchiques et relationnels enrichis ainsi qu’une extensibilité par le biais de fonctions JavaScript définies par l’utilisateur. La grammaire JSON permet de modéliser des documents JSON en tant qu’arborescences comprenant des nœuds étiquetés qui sont utilisés par les techniques d’indexation automatique et le langage de requête SQL d’Azure Cosmos DB. Pour plus d’informations sur l’utilisation de la grammaire SQL, consultez l’article [Requêtes SQL][query].

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>L’API SQL prend-elle en charge les fonctions d’agrégation SQL ?

L’API SQL prend en charge l’agrégation à faible latence à n’importe quelle échelle par l’intermédiaire des fonctions d’agrégation `COUNT`, `MIN`, `MAX`, `AVG` et `SUM` par le biais de la grammaire SQL. Pour plus d’informations, consultez [Fonctions d’agrégation](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Comment l’API SQL assure-t-elle l’accès concurrentiel ?

L’API SQL prend en charge le contrôle d’accès concurrentiel optimiste par le biais des balises d’entité HTTP (ou ETags). Chaque ressource de l’API SQL est dotée d’une ETag définie sur le serveur à chaque mise à jour d’un document. L’en-tête et la valeur actuelle ETag sont inclus dans tous les messages de réponse. Les ETag peuvent être utilisées avec l’en-tête If-Match pour permettre au serveur de déterminer si une ressource nécessite une mise à jour. La valeur If-Match est la valeur ETag utilisée pour la vérification. Si la valeur ETag correspond à la valeur ETag du serveur, la ressource est mise à jour. Si l’ETag n’est plus actuelle, le serveur rejette l’opération en retournant un code de réponse « HTTP 412 Échec de la condition préalable ». Dans ce cas, le client extrait à nouveau la ressource afin d’obtenir la valeur ETag actuelle pour la ressource. De plus, les ETag peuvent être utilisées avec l’en-tête If-None-Match pour déterminer si une nouvelle extraction d’une ressource est nécessaire.

Pour utiliser l’accès concurrentiel optimiste dans .NET, utilisez la classe [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Pour un exemple .NET, voir [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) dans l’exemple DocumentManagement sur github.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Comment effectuer des transactions dans l’API SQL ?

L’API SQL prend en charge les transactions intégrées au langage par le biais de déclencheurs et de procédures stockées JavaScript. Toutes les opérations de base de données à l’intérieur des scripts sont exécutées en isolement de capture instantanée. S’il s’agit d’une collection à partition unique, l’exécution est étendue à la collection. Si la collection est partitionnée, l’exécution est étendue aux documents ayant la même valeur de clé de partition au sein de la collection. Une capture instantanée des versions des documents (ETags) est prise au début de la transaction et validée uniquement si le script fonctionne. Si le JavaScript renvoie une erreur, la transaction est annulée. Pour plus d’informations, consultez [Programmation en JavaScript côté serveur pour Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Comment insérer des documents en bloc dans Cosmos DB ?

Vous pouvez insérer en bloc des documents dans Azure Cosmos DB de l’une des façons suivantes :

* L’outil de l’exécuteur en bloc, comme décrit dans [Utilisation de la bibliothèque .NET de l’exécuteur en bloc](bulk-executor-dot-net.md) et [Utilisation de la bibliothèque Java de l’exécuteur en bloc](bulk-executor-java.md)
* Avec l’outil de migration de données, comme décrit dans [Outil de migration de base de données pour Azure Cosmos DB](import-data.md).
* Avec des procédures stockées, comme décrit dans [Programmation en JavaScript côté serveur pour Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>L’API SQL prend-elle en charge la mise en cache de lien de ressource ?

Oui. Azure Cosmos DB étant un service RESTful, les liens de ressource sont immuables et peuvent être mis en cache. Les clients de l’API SQL peuvent spécifier un en-tête « If-None-Match » pour des lectures effectuées en comparaison avec des documents ou collections de type ressource, puis mettre à jour leurs copies locales après modification de la version du serveur.

### <a name="is-a-local-instance-of-sql-api-available"></a>Une instance locale de l’API SQL est-elle disponible ?

Oui. L’[émulateur Azure Cosmos DB](local-emulator.md) fournit une émulation haute fidélité du service Cosmos DB. Il prend en charge les mêmes fonctionnalités qu’Azure Cosmos DB, dont la création et l’interrogation de documents JSON, l’approvisionnement et la mise à l’échelle des collections, et l’exécution des procédures stockées et déclencheurs. Vous pouvez développer et tester des applications à l’aide de l’émulateur Azure Cosmos DB, puis les déployer vers Azure à une échelle globale en apportant une simple modification à la configuration du point de terminaison de connexion pour Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Pourquoi les longues valeurs à virgule flottante dans un document sont-elles arrondies quand elles sont affichées à partir de l’Explorateur de données dans le portail ?

Il s’agit d’une limitation propre à JavaScript. JavaScript utilise des nombres de format à virgule flottante double précision comme spécifié dans la norme IEEE 754. Les nombres qu’il peut contenir de la sorte sont compris entre -(2<sup>53</sup> - 1) et 2<sup>53</sup>-1 (c’est-à-dire, 9007199254740991) uniquement.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Où se trouvent les autorisations accordées dans la hiérarchie d’objets ?

La création d’autorisations à l’aide de ResourceTokens est autorisée au niveau du conteneur et de ses descendants (tels que les documents ou les pièces jointes). Cela implique qu’une tentative de création d’autorisation au niveau de la base de données ou du compte n’est pas autorisée actuellement.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>API d’Azure Cosmos DB pour MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Présentation de l’API Azure Cosmos DB pour MongoDB

L’API Azure Cosmos DB pour MongoDB est une couche de compatibilité avec le protocole filaire qui permet aux applications de communiquer facilement et en toute transparence avec le moteur de base de données Azure Cosmos DB natif, au moyen des Kits de développement logiciel (SDK) et des pilotes existants et pris en charge par la communauté pour MongoDB. Les développeurs peuvent désormais utiliser des chaînes d’outils et compétences MongoDB existantes pour créer des applications qui tirent parti d’Azure Cosmos DB. Les développeurs bénéficient des fonctionnalités uniques d’Azure Cosmos DB, entre autres la diffusion mondiale avec réplication multimaître, l’indexation automatique, la maintenance de sauvegarde, les contrats de niveau de service (SLA) avec garantie financière, etc.

### <a name="how-do-i-connect-to-my-database"></a>Comment se connecter à une base de données ?

Pour se connecter à une base de données Cosmos avec l’API Azure Cosmos DB pour MongoDB, la solution la plus rapide consiste à passer par le [portail Azure](https://portal.azure.com). Accédez à votre compte, puis, dans le menu de navigation à gauche, cliquez sur **Démarrage rapide**. Démarrage rapide est le meilleur moyen d’obtenir des extraits de code pour vous connecter à votre base de données.

Azure Cosmos DB applique des normes et des exigences strictes en matière de sécurité. Les comptes Azure Cosmos DB nécessitant une authentification et une communication sécurisée via SSL, veillez à utiliser TLSv1.2.

Pour plus d’informations, consultez [Se connecter à une base de données Cosmos avec l’API Azure Cosmos DB pour MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Existe-t-il des codes d’erreur supplémentaires à gérer durant l’utilisation de l’API Azure Cosmos DB pour MongoDB ?

Parallèlement aux codes d’erreur MongoDB habituels, l’API Azure Cosmos DB pour MongoDB présente ses propres codes d’erreur :

| Error               | Code  | Description  | Solution  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Le nombre total d’unités de requête consommées est supérieur au taux d’unités de requête provisionné pour la collection et a été limité. | Adaptez le débit assigné à un conteneur ou à un ensemble de conteneurs à partir du portail Azure ou renouvelez l’opération. |
| ExceededMemoryLimit | 16501 | En tant que service multi-locataire, l’opération a dépassé l’allocation de mémoire du client. | Réduisez l’étendue de l’opération en fixant un critère de requête plus restrictif, ou contactez le support technique via le [portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Exemple : <em>&nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Est-ce que le pilote Simba pour MongoDB est pris en charge dans l’utilisation de l’API Azure Cosmos DB pour MongoDB ?

Oui, vous pouvez utiliser le pilote Mongo ODBC de Simba avec l’API Azure Cosmos DB pour MongoDB.

## <a id="table"></a>API de table

### <a name="how-can-i-use-the-table-api-offering"></a>Comment utiliser l’offre de l’API Table ?

L’API Table d’Azure Cosmos DB est disponible dans le [Portail Azure][azure-portal]. Vous devez d’abord souscrire un abonnement Microsoft Azure. Une fois inscrit, vous pouvez ajouter un compte d’API Table d’Azure Cosmos DB à votre abonnement Azure, puis ajouter des tables à votre compte.

Vous trouverez les langages pris en charge et les démarrages rapides associés dans [Présentation de l’API Table d’Azure Cosmos DB](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Ai-je besoin d’un nouveau kit SDK pour utiliser l’API Table ?

Non, les kits SDK de stockage existants doivent continuer de fonctionner. Toutefois, nous vous recommandons de vous procurer les derniers kits SDK pour optimiser la prise en charge et, dans de nombreux cas, améliorer les performances. Consultez la liste des langages disponibles dans [Présentation de l’API Table d’Azure Cosmos DB](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Quelles sont les différences de comportement entre l’API Table et le stockage Table Azure ?

Si vous avez déjà créé des tables dans le stockage Table Azure et que vous souhaitez passer à l’API Table d’Azure Cosmos DB, tenez compte des différences de comportement suivantes :

* L’API Table d’Azure Cosmos DB utilise un modèle de capacité réservée pour garantir les performances. Que vous l’utilisez ou non, la capacité vous est donc facturée dès que la table est créée. Dans le cadre du stockage Table Azure, vous payez uniquement la capacité utilisée. Cela explique pourquoi l’API Table peut offrir un contrat de niveau de service (SLA) de 10 ms en lecture et de 15 ms en écriture au 99e centile, alors que le stockage Table Azure offre un SLA de 10 s. Même les tables vides sans requêtes de l’API Table engendrent donc des coûts pour assurer la disponibilité de la capacité et le traitement de toutes les requêtes selon les termes du SLA offert par Azure Cosmos DB.
* Les résultats des requêtes retournés par l’API Table ne sont pas triés dans l’ordre des clés de ligne/partition, alors qu’ils le sont dans le stockage Table Azure.
* Les clés de ligne ne peuvent pas dépasser 255 octets.
* Les lots ne peuvent contenir que jusqu’à 2 Mo
* CORS n’est pas pris en charge actuellement
* Les noms des tables ne respectent pas la casse dans le stockage Table Azure, alors qu’ils la respectent dans l’API Table d’Azure Cosmos DB
* Certains des formats internes Azure Cosmos DB pour les informations de codage, comme les champs binaires, ne sont actuellement pas aussi efficaces que souhaité. Par conséquent, des limitations inattendues sur la taille des données peuvent être observées. Par exemple, il n’est actuellement pas possible d’utiliser le méga-octet complet d’une entité de table pour stocker des données binaires, car l’encodage augmente la taille des données.
* « ID » du nom de la propriété de l’entité actuellement non pris en charge
* TableQuery TakeCount n’est pas limité à 1000

En ce qui concerne l’API REST, il existe un certain nombre d’options liées aux points de terminaison/requêtes qui ne sont pas prises en charge par l’API Table d’Azure Cosmos DB :

| Méthode(s) REST | Option de point de terminaison/requête REST | URL vers la documentation | Explication |
| ------------| ------------- | ---------- | ----------- |
| GET, PUT | /?restype=service@comp=properties| [Définir les propriétés du service Table](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) et [Obtenir les propriétés du service Table](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Ce point de terminaison est utilisé pour définir les règles CORS, la configuration de Storage Analytics et les paramètres de journalisation. CORS n’est pas actuellement pris en charge, et l’analytique et la journalisation sont gérées différemment dans Azure Cosmos DB et dans les tables Stockage Azure. |
| OPTIONS | /\<table-resource-name> | [Requête de table CORS préliminaire](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Cette option fait partie de CORS, qui n’est pas pris en charge par Azure Cosmos DB pour l’instant. |
| GET | /?restype=service@comp=stats | [Obtenir les statistiques du service Table](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Fournit des informations sur la rapidité de la réplication des données entre un emplacement principal et un emplacement secondaire. Cette option est inutile dans Cosmos DB puisque la réplication fait partie des écritures. |
| GET, PUT | /mytable?comp=acl | [Obtenir la liste de contrôle d’accès de la table](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) et [Définir la liste de contrôle d’accès de la table](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Obtient et définit les stratégies d’accès stocké utilisées pour gérer les signatures d’accès partagé. Bien que ces signatures soient prises en charge, elles sont définies et gérées différemment. |

De plus, l’API Table d’Azure Cosmos DB prend uniquement en charge le format JSON (ATOM n’est pas pris en charge).

Si les signatures d‘accès partagé sont prises en charge par Azure Cosmos DB, certaines stratégie ne le sont pas, notamment celles liées aux opérations de gestion comme le droit de créer des tables.

Pour le kit SDK .NET en particulier, certaines classes et méthodes ne sont pas prises en charge par Azure Cosmos DB pour l’instant.

| Classe | Méthode non prise en charge |
|-------|-------- |
| CloudTableClient | \*ServiceProperties* |
|                  | \*ServiceStats* |
| CloudTable | SetPermissions* |
|            | GetPermissions* |
| TableServiceContext | * (cette classe est dépréciée) |
| TableServiceContext | " " |
| TableServiceExtensions | " " |
| TableServiceContext | " " |

Si l’une de ces différences constitue un obstacle pour votre projet, contactez [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Comment formuler des commentaires sur le Kit de développement logiciel (SDK) ou des bogues ?

Vous pouvez partager vos commentaires par les biais suivants :

* [User voice](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Consultez Stack Overflow pour les questions sur la programmation. Par souci de clarté et afin d’obtenir une réponse, vérifiez que votre question est [appropriée](https://stackoverflow.com/help/on-topic) et [ fournit un maximum de détails](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Quelle chaîne de connexion faut-il utiliser pour se connecter à l’API Table ?

La chaîne de connexion est :

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Vous pouvez obtenir la chaîne de connexion dans la page Chaîne de connexion du portail Azure.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Comment faire pour remplacer les paramètres de configuration des options de requête dans le kit SDK .NET pour l’API Table ?

Certains paramètres sont gérés sur la méthode de CreateCloudTableClient et d’autres par le biais du fichier app.config dans la section appSettings de l’application cliente. Pour plus d’informations sur les paramètres de configuration, voir [Fonctionnalités d’Azure Cosmos DB](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Y a-t-il des changements pour les clients qui utilisent les kits SDK de stockage Table Azure existants ?

Aucune. Il n’y a aucune modification pour les clients existants ou nouveaux utilisant le Kit de développement logiciel (SDK) de stockage Table Azure existant.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Comment faire pour afficher les données de table qui sont stockées dans Azure Cosmos DB afin de les utiliser avec l’API Table ?

Vous pouvez utiliser le portail Azure pour parcourir les données. Vous pouvez également utiliser le code ou les outils de l’API Table mentionnés dans la réponse suivante.

### <a name="which-tools-work-with-the-table-api"></a>Quels sont les outils qui fonctionnent avec l’API Table ?

Vous pouvez utiliser [l’Explorateur Stockage Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Les outils offrant la flexibilité nécessaire pour prendre une chaîne de connexion au format spécifié précédemment peuvent prendre en charge la nouvelle API Table. Vous trouverez une liste des outils de table dans la page [Outils clients d’Azure Storage](../storage/common/storage-explorers.md).

### <a name="is-the-concurrency-on-operations-controlled"></a>L’accès concurrentiel sur les opérations est-il contrôlé ?

Oui, l’accès concurrentiel optimiste est fourni via l’utilisation du mécanisme ETag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Le modèle de requête OData est-il pris en charge pour les entités ?

Oui, l’API Table prend en charge les requêtes OData et LINQ.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Puis-je me connecter côte à côte au stockage Table Azure et à l’API Table d’Azure Cosmos DB dans la même application ?

Oui, vous pouvez vous connecter en créant deux instances distinctes de CloudTableClient, chacune pointant vers son propre URI via la chaîne de connexion.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Comment faire pour migrer une application de stockage Table Azure existante vers cette offre ?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) et [l‘outil de migration de données Azure Cosmos DB](import-data.md) sont tous les deux pris en charge.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Comment l’extension de la taille de stockage est-elle gérée pour ce service, par exemple, si je commence par *n* Go de données et que le volume de celles-ci passe à 1 To au fil du temps ?

Azure Cosmos DB est conçu pour offrir une capacité de stockage illimitée via l’utilisation de la mise à l’échelle horizontale. Le service peut surveiller votre stockage et en augmenter efficacement le volume.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Comment faire pour surveiller l’offre de l’API Table ?

Le volet **Métriques** de l’API Table permet de surveiller les requêtes et l’utilisation du stockage.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Comment calculer le débit dont j’ai besoin ?

Vous pouvez utiliser l’estimateur de capacité pour calculer le débit de table requis pour les opérations. Pour plus d’informations, voir [Estimer les unités de requête et le stockage de données](https://www.documentdb.com/capacityplanner). En général, vous pouvez afficher votre entité au format JSON, et fournir les valeurs pour vos opérations.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Puis-je utiliser le kit SDK de l’API Table localement avec l’émulateur ?

Pas pour l'instant.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Mon application existante peut-elle fonctionner avec l’API Table ?

Oui, la même API est prise en charge.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Dois-je migrer mes applications de stockage Table Azure existantes vers le nouveau kit SDK si je ne souhaite pas utiliser les fonctionnalités de l’API Table ?

Non, vous pouvez créer et utiliser des ressources de stockage Table Azure existantes sans la moindre interruption. Toutefois, si vous n’utilisez pas l’API Table, vous ne pouvez pas bénéficier de l’indexation automatique, de l’option de cohérence supplémentaire ou de la distribution mondiale.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Comment ajouter la réplication des données de l’API Table dans plusieurs régions Azure ?

Vous pouvez utiliser les [paramètres de réplication globale](tutorial-global-distribution-sql-api.md#portal) du portail Azure Cosmos DB pour ajouter des régions appropriées pour votre application. Pour développer une application distribuée globalement, vous devez également ajouter votre application avec les informations PreferredLocation définies sur la région locale afin de bénéficier d’une faible latence de lecture.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Comment faire pour changer la région d’écriture principale du compte dans l’API Table ?

Vous pouvez utiliser le volet du portail de réplication globale d’Azure Cosmos DB pour ajouter une région, puis basculer vers la région requise. Pour des instructions, voir [Développement avec des comptes Azure Cosmos DB multirégions](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Comment configurer mes régions de lecture préférées pour une faible latence lors de la distribution de mes données ?

Pour faciliter la lecture à partir de l’emplacement local, utilisez la clé PreferredLocation dans le fichier app.config. Pour les applications existantes, l’API Table génère une erreur si LocationMode est défini. Supprimez ce code, car l’API Table extrait ces informations du fichier app.config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>En quoi consistent les niveaux de cohérence dans l’API Table ?

Azure Cosmos DB adopte des compromis bien pensés entre cohérence, disponibilité et latence. Azure Cosmos DB offre cinq niveaux de cohérence aux développeurs de l’API Table. Vous pouvez donc choisir le modèle de cohérence approprié au niveau de la table, et effectuer des demandes individuelles lors de l’interrogation des données. Quand un client se connecte, il peut spécifier un niveau de cohérence. Vous pouvez changer le niveau à l’aide de l’argument consistencyLevel de CreateCloudTableClient.

L’API Table permet d’effectuer des lectures à faible latence avec l’option « Lire vos écritures » et le niveau de cohérence Obsolescence limitée par défaut. Pour plus d’informations, consultez [Niveaux de cohérence](consistency-levels.md).

Par défaut, Stockage de table Azure offre une Cohérence forte au sein d’une région et une Cohérence éventuelle dans les sites secondaires.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>L’API Table d’Azure Cosmos DB offre-t-elle davantage de niveaux de cohérence que le stockage Table Azure ?

Oui, pour plus d’informations sur la façon de tirer parti de la nature distribuée d’Azure Cosmos DB, voir [Niveaux de cohérence](consistency-levels.md). Des garanties étant fournies pour les niveaux de cohérence, vous pouvez utiliser ceux-ci en toute confiance.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Une fois la distribution globale activée, combien de temps faut-il pour répliquer les données ?

Azure Cosmos DB valide les données durablement dans la région locale, et envoie les données vers les autres régions immédiatement, en quelques millisecondes. Cette réplication dépend uniquement de la durée des boucles (RTT) du centre de données. Pour en savoir plus sur la fonctionnalité de distribution globale d’Azure Cosmos DB, voir [Azure Cosmos DB : Un service de base de données mondialement distribué sur Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Est-il possible de modifier le niveau cohérence des demandes de lecture ?

Azure Cosmos DB permet de définir le niveau de cohérence au niveau du conteneur (sur la table). Le kit SDK .NET vous permet de changer le niveau en fournissant la valeur de la clé TableConsistencyLevel dans le fichier app.config. Les valeurs possibles sont les suivantes : Forte, Obsolescence limitée, Session, Préfixe cohérent et Éventuelle. Pour plus d’informations, voir [Niveaux de cohérence ajustables dans Azure Cosmos DB](consistency-levels.md). L’idée clé est que vous ne pouvez pas définir un niveau de cohérence de requête HTTP supérieur à celui défini pour la table. Par exemple, vous ne pouvez pas définir le niveau de cohérence pour la table sur Éventuelle et le niveau de cohérence de requête sur Forte.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Comment l’API Table gère-t-elle le basculement en cas de défaillance d’une région ?

L’API Table tire parti de la plateforme mondialement distribuée d’Azure Cosmos DB. Pour vous assurer que votre application peut tolérer des temps d’arrêt de centre de données, activez au moins une région supplémentaire pour le compte dans le portail Azure Cosmos DB (voir [Développement avec des comptes Azure Cosmos DB multirégions](high-availability.md)). Vous pouvez définir la priorité de la région à l’aide du portail (voir [Developing with multi-region Azure Cosmos DB accounts](high-availability.md) (Développement avec des comptes Azure Cosmos DB multirégions)).

Vous pouvez ajouter autant de régions que vous le souhaitez pour le compte, et contrôler l’emplacement de basculement en indiquant une priorité. Pour utiliser la base de données, vous devez également fournir une application à cet emplacement. Si vous procédez de la sorte, vos clients ne subissent aucun temps d’arrêt. À la différence des autres kits SDK, la [dernière version du kit SDK client .NET](table-sdk-dotnet.md) prend en charge l’hébergement automatique. Autrement dit, il peut détecter la région défaillante et basculer automatiquement vers la nouvelle région.

### <a name="is-the-table-api-enabled-for-backups"></a>L’API Table prend-elle en charge les sauvegardes ?

Oui, l’API Table tire parti de la plateforme d’Azure Cosmos DB pour les sauvegardes. Les sauvegardes sont effectuées automatiquement. Pour plus d’informations, voir [Sauvegarde et restauration en ligne automatiques avec Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>L’API Table indexe-t-elle par défaut tous les attributs d’une entité ?

Oui, tous les attributs d’une entité sont indexés par défaut. Pour plus d’informations, voir [Azure Cosmos DB : Stratégies d’indexation](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Cela signifie-t-il que je n’ai pas besoin de créer plusieurs index pour satisfaire les requêtes ?

Oui, l’API Table d’Azure Cosmos DB assure l’indexation automatique de tous les attributs sans aucune définition de schéma. Cette automatisation permet aux développeurs de se concentrer sur l’application plutôt que sur la création et la gestion d’index. Pour plus d’informations, voir [Azure Cosmos DB : Stratégies d’indexation](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Puis-je modifier la stratégie d’indexation ?

Oui, vous pouvez modifier la stratégie d’indexation en fournissant la définition d’index. Vous devez correctement encoder et placer dans une séquence d’échappement les paramètres.

Pour les kits SDK non-.NET, la stratégie d’indexation peut uniquement être définie dans le portail au niveau de l’**Explorateur de données**. Naviguez jusqu’à la table à changer, accédez à **Mise à l‘échelle et paramètres** -> Stratégie d‘indexation, effectuez le changement souhaité, puis cliquez sur **Enregistrer**.

À partir du kit SDK .NET, vous pouvez la soumettre dans le fichier app.config :

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB en tant que plateforme semble avoir de nombreuses fonctionnalités, telles que le tri, l’agrégation, la hiérarchisation et autres. Allez-vous ajouter ces fonctionnalités à l’API Table ?

L’API Table offre les mêmes fonctionnalités de requête que le stockage Table Azure. Azure Cosmos DB prend également en charge le tri, les agrégats, les requêtes géospatiales, les hiérarchies et un large éventail de fonctions intégrées. Pour plus d’informations, consultez la section [Requêtes SQL](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Quand dois-je changer le débit de table (TableThroughput) pour l’API Table ?

Vous devez modifier débit de table (TableThroughput) dans les situations suivantes :

* Vous effectuez une opération d’extraction, transformation et chargement (ETL) de données, ou souhaitez charger une grande quantité de données dans un laps de temps court.
* Vous avez besoin d’un débit supérieur du conteneur ou d’un ensemble de conteneurs sur le backend. Par exemple, vous voyez que le débit utilisé est supérieur au débit provisionné, et vous êtes limité. Pour plus d’informations, consultez [Définir le débit des conteneurs Azure Cosmos DB](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Puis-je augmenter ou réduire le débit ma table d’API Table ?

Oui, vous pouvez utiliser le volet de mise à l’échelle du portail Azure Cosmos DB pour régler le débit. Pour plus d’informations, voir [Définir le débit](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Un débit de table (TableThroughput) par défaut est-il défini pour les nouvelles tables approvisionnées ?

Oui, si vous ne remplacez pas le débit de table (TableThroughput) dans le fichier app.config et n’utilisez pas de conteneur créé préalablement dans Azure Cosmos DB, le service crée une table dont le débit est défini sur 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Les prix varient-ils pour les clients existants du service de stockage Table Azure ?

Aucune. Le tarif reste inchangé pour les clients existants du stockage Table Azure.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Comment le prix est-il calculé pour l’API Table ?

Cela dépend du débit de table (TableThroughput) alloué.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Comment faire pour gérer les limitations de taux qui affectent les tables dans l’offre API Table ?

Si le taux de requêtes dépasse la capacité du débit provisionné pour le conteneur sous-jacent ou un ensemble de conteneurs, vous recevez une erreur et le SDK essaie de renouveler l’appel en appliquant la stratégie de nouvelle tentative.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Pourquoi dois-je choisir un débit en dehors de PartitionKey et de RowKey pour tirer parti de l’offre API Table d’Azure Cosmos DB ?

Si vous ne spécifiez pas de débit par défaut pour votre conteneur dans le fichier app.config ou par le biais du portail, Azure Cosmos DB en définit un.

Azure Cosmos DB fournit des garanties de performances et de latence avec des limites supérieures pour les opérations. Cette garantie est possible lorsque le moteur peut assurer la gouvernance des opérations du client. La définition du débit de table (TableThroughput) est pour vous l’assurance de bénéficier du débit et de la latence garantis, car la plateforme réserve cette capacité et garantit le succès des opérations.

La spécification du débit vous permet de modifier celui-ci en souplesse pour tirer parti du caractère saisonnier de votre application, répondre aux besoins de débit et réduire les coûts.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Le stockage Table Azure était peu onéreux pour moi, car je payais uniquement le stockage des données et n’effectuais que rarement des requêtes. L’offre API Table d’Azure Cosmos DB semble me facturer des frais alors que je n’ai pas effectué la moindre transaction ou stocké quoi que ce soit. Pouvez-vous m’expliquer pourquoi ?

Azure Cosmos DB est conçu pour être un système distribué globalement basé sur un contrat de niveau de service (SLA) offrant des garanties en matière de disponibilité, de latence et de débit. Le débit que vous réservez dans Azure Cosmos DB est garanti, à la différence du débit d’autres systèmes. Azure Cosmos DB offre des fonctionnalités supplémentaires qui ont été demandées par les clients, telles que les index secondaires et la distribution globale.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Je ne reçois jamais de notification de « quota complet » (indiquant qu’une partition est pleine) lorsque j’ingère des données dans le stockage Table Azure. Avec l’API Table, je reçois ce message. Cette offre me limite-t-elle et m’oblige-t-elle à modifier mon application existante ?

Azure Cosmos DB est un système basé sur un contrat de niveau de service (SLA) permettant une mise à l’échelle illimitée, avec des garanties en matière de latence, de débit, de disponibilité et de cohérence. Pour être certain de bénéficier des performances premium garanties, assurez-vous que la taille de vos données et votre index sont gérables et extensibles. La limite de 10 Go appliquée au nombre d’entités ou d’éléments par clé de partition nous permet de garantir des performances de recherche et de requête exceptionnelles. Pour vous assurer que la mise à l’échelle de votre application fonctionne correctement, même pour le stockage Azure, nous vous recommandons de ne *pas* créer de partition sensible en stockant toutes les informations dans une seule partition et en interrogeant celle-ci.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Si je comprends bien, une clé de partition et une clé de ligne sont toujours exigées avec l’API Table ?

Oui. Comme la surface d’exposition de l’API Table est semblable à celle du kit SDK Stockage Table Azure, la clé de partition constitue un moyen efficace de distribuer les données. La clé de ligne est unique au sein de cette partition. La clé de ligne doit être présente et ne peut pas avoir la valeur null comme dans le Kit de développement logiciel (SDK) standard. La longueur de la clé de ligne est de 255 octets, et celle de la clé de partition de 1 kilo-octet.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Quels sont les messages d’erreur relatifs à l’API Table ?

Étant donné que le stockage Table Azure et l’API Table d’Azure Cosmos DB utilisent les mêmes SDK, la plupart des erreurs sont identiques.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Pourquoi suis-je limité quand je tente de créer successivement un grand nombre de tables dans l’API Table ?

Azure Cosmos DB est un système basé sur un contrat de niveau de service (SLA) qui offre des garanties en matière de latence, de débit, de disponibilité et de cohérence. Comme il s’agit d’un système provisionné, il réserve des ressources afin de garantir le respect de ces exigences. La vitesse de création des tables est détectée et limitée. Nous vous recommandons de consulter le taux de création de tables et de le réduire à moins de 5 par minute. N’oubliez pas que l’API Table est un système provisionné. Dès que vous le provisionnez, vous commencez à payer.

## <a name="gremlin-api"></a>API Gremlin

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Pour le développement en C#/.NET, dois-je utiliser le package Microsoft.Azure.Graphs ou Gremlin.NET ?

L’API Gremlin d’Azure Cosmos DB se sert des pilotes open source comme connecteurs principaux pour le service. Nous recommandons alors d’utiliser les [pilotes qui sont pris en charge par Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Comment les RU/s sont-elles facturées lors de l’exécution des requêtes sur une base de données de graphique ?

Tous les objets, sommets et arêtes des graphes sont affichés sous forme de documents JSON dans le back-end. Dans la mesure où une seule requête Gremlin peut modifier un ou plusieurs objets de graphe à la fois, le coût associé est directement lié aux objets ou arêtes traités par la requête. Azure Cosmos DB utilise le même processus pour toutes les autres API. Pour plus d’informations, voir [Unités de requête dans Azure Cosmos DB](request-units.md).

Les frais de RU sont basés sur le jeu de données de travail de la traversée, et non sur le jeu de résultats. Par exemple, si une requête vise à obtenir un seul vertex en résultat mais doit traverser plusieurs autres objets sur le chemin, le coût sera basé sur tous les objets de graphe utilisés pour calculer cet unique vertex de résultat.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Quelle est la mise à l’échelle maximale qu’une base de données de graphique peut avoir dans l’API Gremlin d’Azure Cosmos DB ?

Azure Cosmos DB utilise le [partitionnement horizontal](partition-data.md) pour répondre automatiquement aux exigences d’augmentation de stockage et de débit. Le débit et la capacité de stockage maximum d’une charge de travail sont déterminés par le nombre de partitions associées à une collection donnée. Toutefois, une collection d’API Gremlin comporte un ensemble spécifique d’instructions pour garantir une expérience de performances à l’échelle correcte. Pour plus d’informations sur le partitionnement de données et les meilleurs pratiques, voir l’article sur le [partitionnement dans Azure Cosmos DB](partition-data.md).

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Comment puis-je me protéger contre les attaques par injection à l’aide de pilotes Gremlin ?

La majorité des pilotes Apache Tinkerpop Gremlin natifs offrent la possibilité de fournir un dictionnaire de paramètres pour l’exécution des requêtes. Voici un exemple montrant comment le faire sous [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) et [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Pourquoi est-ce que je reçois le message d’erreur « Erreur de compilation de la requête Gremlin : Impossible de trouver une méthode » ?

L’API Gremlin d’Azure Cosmos DB implémente un sous-ensemble des fonctionnalités définies dans la surface d’exposition de Gremlin. Pour voir les étapes prises en charge ou pour plus d’informations, consultez l’article [Prise en charge de Gremlin](gremlin-support.md).

La meilleure solution de contournement consiste à récrire les étapes Gremlin nécessaires avec la fonctionnalité prise en charge puisque toutes les étapes Gremlin essentielles sont prises en charge par Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Pourquoi est-ce que je reçois le message d’erreur « WebSocketException : Le serveur a retourné le code d’état "200" alors que le code d’état attendu était "101" » ?

Cette erreur survient probablement lorsqu’un point de terminaison incorrect est utilisé. Le point de terminaison qui génère cette erreur a le modèle suivant :

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

C’est le point de terminaison de documents pour votre base de données de graphique.  Le bon point de terminaison à utiliser est le point de terminaison Gremlin qui a le format suivant :

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Pourquoi reçois-je le message d’erreur « RequestRateIsTooLarge » ?

Cette erreur signifie que les unités de requête par seconde allouées ne sont pas suffisantes pour traiter la requête. Cette erreur se produit généralement lorsque vous exécutez une requête qui obtient tous les vertex :

```
// Query example:
g.V()
```

Cette requête va tenter de récupérer tous les vertex du graphique. Par conséquent, le coût de cette requête sera au moins égal au nombre de vertex en termes d’unités de requête. Le paramètre RU/s doit être ajusté pour répondre à cette requête.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Pourquoi mes connexions de pilote Gremlin finissent par être supprimées ?

Une connexion Gremlin est établie via une connexion WebSocket. Bien que les connexions WebSocket ne disposent pas d’une durée de vie spécifique, l’API Gremlin Azure Cosmos DB terminera les connexions inactives après 30 minutes d’inactivité.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Pourquoi ne puis-je pas utiliser d’appels d’API Fluent dans les pilotes Gremlin natifs ?

Les appels d’API Fluent ne sont pas encore pris en charge par l’API Gremlin d’Azure Cosmos DB. Les appels d’API Fluent nécessitent une fonctionnalité de mise en forme interne, appelée la « prise en charge bytecode », qui n’est actuellement pas prise en charge par l’API Gremlin d’Azure Cosmos DB. Pour la même raison, le dernier pilote Gremlin-JavaScript n’est pas pris en charge actuellement.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Comment puis-je évaluer l’efficacité de mes requêtes Gremlin ?

L’étape de l’aperçu **executionProfile()** peut être utilisée pour fournir une analyse du plan d'exécution de la requête. Cette étape doit être ajoutée à la fin de n’importe quelle requête Gremlin comme illustré dans l’exemple suivant :

**Exemple de requête**

```
g.V('mary').out('knows').executionProfile()
```

**Exemple de sortie**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

La sortie du profil ci-dessus indique combien de temps est consacré à l’obtention des objets de vertex, des objets d’arête, ainsi que la taille du jeu de données de travail. Cela concerne les mesures de coût standard pour les requêtes Azure Cosmos DB.

## <a id="cassandra"></a> API Cassandra

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Quelle est la version de protocole prise en charge par l’API Cassandra de Azure Cosmo DB ? Prévoyez-vous de prendre en charge d’autres protocoles ?

L’API Apache Cassandra pour Azure Cosmos DB prend désormais en charge CQL version 4. Si vous avez des commentaires sur la prise en charge d’autres protocoles, faites-le nous savoir via les [commentaires user voice](https://feedback.azure.com/forums/263030-azure-cosmos-db) ou envoyez un e-mail à [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Pourquoi est-il nécessaire de choisir un débit pour une table ?

Azure Cosmos DB définit le débit par défaut pour votre conteneur en fonction de l’emplacement à partir duquel vous créez la table (portail ou CQL).
Azure Cosmos DB fournit des garanties de performances et de latence avec des limites supérieures pour les opérations. Cette garantie est possible lorsque le moteur peut assurer la gouvernance des opérations du client. La définition du débit est pour vous l’assurance de bénéficier du débit et de la latence garantis, car la plateforme réserve cette capacité et garantit le succès des opérations.
Vous avez la possibilité de modifier le débit de façon élastique pour profiter de la saisonnalité de votre application et réaliser des économies.

Le concept de débit est expliqué dans l’article [Unités de requête dans Azure Cosmos DB](request-units.md). Le débit d’une table est réparti de manière équilibrée entre les partitions physiques sous-jacentes.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Quel est le nombre d’unités de requête par seconde d’une table créée via CQL ? Comment faire pour le changer ?

Azure Cosmos DB utilise les unités de requête par seconde (RU/s) comme devise pour fournir le débit. Les tables créées via CQL contiennent 400 unités de requêtes. Vous pouvez modifier ce nombre à partir du portail.

CQL

```
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Que se passe-t-il quand tout le débit a été utilisé ?

Azure Cosmos DB fournit des garanties de performances et de latence avec des limites supérieures pour les opérations. Cette garantie est possible lorsque le moteur peut assurer la gouvernance des opérations du client. Cela dépend de la définition du débit, qui est pour vous l’assurance de bénéficier du débit et de la latence garantis, car la plateforme réserve cette capacité et garantit le succès des opérations.
Quand vous dépassez cette capacité, vous obtenez un message d’erreur de surcharge indiquant que toute la capacité a été utilisée.
Surcharge 0x1001 : impossible de traiter la requête, car « Le taux de demandes est élevé ». Il est alors indispensable d’identifier les opérations et les volumes à l’origine de ce problème. Vous pouvez avoir une idée de la capacité consommée dépassant la capacité provisionnée grâce aux indicateurs de performances dans le portail. Vous devez ensuite vérifier que la capacité est consommée de manière équilibrée entre toutes les partitions sous-jacentes. Si vous constatez que la plupart du débit est consommé par une seule partition, il y a une asymétrie de la charge de travail.

Des indicateurs de performance sont disponibles pour vous montrer l’utilisation du débit au fil des heures, jours et semaines, sur les différentes partitions, ou sous forme agrégée. Pour plus d’informations, consultez la section [Surveillance et débogage à l’aide de métriques dans Azure Cosmos DB](use-metrics.md).

Les journaux de diagnostic sont expliquées dans l’article [Journalisation des diagnostics Azure Cosmos DB](logging.md).

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>La clé primaire est-elle mappée sur le concept de clé de partition d’Azure Cosmos DB ?

Oui, la clé de partition est utilisée pour placer l’entité au bon emplacement. Dans Azure Cosmos DB, elle est utilisée pour trouver la bonne partition logique qui est stockée sur une partition physique. Le concept de partitionnement est également expliqué dans l’article [Partitionner et mettre à l’échelle dans Azure Cosmos DB](partition-data.md). Ce qu’il faut retenir, c’est qu’une partition logique ne doit pas dépasser la limite de 10 Go aujourd’hui.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Que se passe-t-il lorsque je reçois une notification de « quota complet » indiquant qu’une partition est pleine ?

Azure Cosmos DB est un système basé sur un contrat de niveau de service (SLA) permettant une mise à l’échelle illimitée, avec des garanties en matière de latence, de débit, de disponibilité et de cohérence. Cet espace de stockage illimité est basé sur un scale-out horizontal des données utilisant le partitionnement comme concept principal. Le concept de partitionnement est également expliqué dans l’article [Partitionner et mettre à l’échelle dans Azure Cosmos DB](partition-data.md).

Limite de 10 Go sur le nombre d’entités ou d’éléments par partition logique que vous devez respecter. Pour vous assurer que la mise à l’échelle de votre application fonctionne correctement, nous vous recommandons de ne *pas* créer de partition sensible en stockant toutes les informations dans une seule partition et en interrogeant celle-ci. Cette erreur ne se produit qu’en cas de données asymétriques, c’est-à-dire que vous avez trop de données pour une seule clé de partition (plus de 10&nbsp;Go). Vous pouvez consulter la répartition des données sur le portail de stockage. Pour corriger cette erreur, vous pouvez recréer la table et choisir une clé principale granulaire (clé de partition), qui permet une meilleure répartition des données.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Est-il possible d’utiliser l’API Cassandra en tant que stockage de valeurs de clé avec des millions, voire des milliards de clés de partition individuelles ?

Azure Cosmos DB peut stocker des données illimitées en effectuant un scale-out du stockage. Ceci est indépendant du débit. Oui vous pouvez toujours utiliser l’API Cassandra pour stocker et récupérer des clés/valeurs en spécifiant la bonne clé primaire/de partition. Ces clés individuelles obtiennent leur propre partition logique et résident sur une partition physique sans problème.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Est-il possible de créer plus d’une table avec l’API Apache Cassandra d’Azure Cosmos DB ?

Oui, il est possible de créer plus d’une table avec l’API Apache Cassandra. Chacune de ces tables est traitée en tant qu’unité de débit et de stockage.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Est-il possible de créer plus d’une table successivement ?

Azure Cosmos DB est un système de gouvernance des ressources pour les activités liées au plan de données et au plan de contrôle. Les conteneurs comme les collections et les tables sont des entités de runtime qui sont provisionnées pour une certaine capacité de débit. La création de ces conteneurs en succession rapide n’est pas une activité prévue et est limitée. Si vous avez des tests qui suppriment/créent des tables immédiatement, essayez de les espacer.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Quel est le nombre maximal de tables qu’il est possible de créer ?

Il n’existe aucune limite physique au nombre de tables ; envoyez un e-mail à [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) si vous avez un grand nombre de tables à créer (où la taille stable totale dépasse 10 To de données), au lieu des quelques dizaines ou centaines habituelles.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Quel est le nombre maximal d’espaces de clés que nous pouvons créer ?

Il n’existe aucune limite physique au nombre d’espaces de clés dans la mesure où il s’agit de conteneurs de métadonnées ; envoyez un e-mail à [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) si, pour une raison quelconque, vous avez un grand nombre d’espaces de clés à créer.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Puis-je importer un volume important de données si j’ai commencé dans une table normale ?

La capacité de stockage est gérée automatiquement et augmente à mesure que vous intégrez des données supplémentaires. Par conséquent, vous pouvez importer sereinement autant de données que vous le souhaitez sans avoir à gérer et provisionner des nœuds, et autres.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Puis-je fournir les paramètres du fichier yaml pour configurer le comportement de l’API Apache Casssandra d’Azure Cosmos DB ?

L’API Apache Cassandra d’Azure Cosmos DB est un service de plateforme. Elle assure la compatibilité au niveau du protocole pour l’exécution des opérations. Elle masque la complexité des opérations de gestion, de supervision et de configuration. En tant que développeur/utilisateur, vous n’avez pas à vous soucier de la disponibilité, des objets tombstone, du cache de clés, du cache de lignes, des filtres de Bloom et d’une multitude d’autres paramètres. L’API Apache Cassandra d’Azure Cosmos DB s’attache à fournir les performances en lecture et en écriture dont vous avez besoin, et ce sans la surcharge engendrée par la configuration et la gestion.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>L’API Apache Cassandra pour Azure Cosmos DB prend-elle en charge les commandes d’ajout de nœuds/d’état du cluster/d’état du nœud ?

L’API Apache Cassandra est un service de plateforme qui simplifie le processus de planification de la capacité et celui de réponse aux demandes d’élasticité pour le débit et le stockage. Azure Cosmos DB provisionne le débit dont vous avez besoin. Vous pouvez ensuite augmenter ou réduire le débit autant de fois par jour que vous le souhaitez, sans vous soucier des tâches d’ajout, de suppression ou de gestion des nœuds. Cela implique que vous n’avez pas besoin d’utiliser l’outil de gestion des clusters et des nœuds.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Qu’en est-il des différents paramètres de configuration relatifs à la création des espaces clés (simple/réseau) ?

Azure Cosmos DB offre des fonctionnalités de distribution mondiale prêtes à l’emploi à haute disponibilité et faible latence. Vous n’avez pas besoin de configurer des réplicas ou d’autres choses. Toutes les écritures sont toujours validées durablement dans un quorum dans toute région où vous écrivez des données, le tout avec des garanties de performance.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gcgrace-compression-memtableflushperiod-and-more"></a>Qu’en est-il des différents paramètres relatifs aux métadonnées de table, comme le filtre de Bloom, la mise en cache, le changement de réparation des lectures, la période de grâce du garbage collection et la période de vidage des memtables pour la compression, et autres ?

Azure Cosmos DB offre des performances en termes de débit et d’opérations en lecture/écriture, sans aucun changement à apporter aux paramètres de configuration, ce qui réduit les risques liés à toute manipulation accidentelle.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Les tables Cassandra prennent-elles en charge la durée de vie (TTL) ?

Oui, TTL est pris en charge.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Est-il possible de surveiller l’état du nœud, l’état du réplica, le garbage collection et les paramètres du système d’exploitation en amont avec différents outils ? Quels éléments sont dorénavant à surveiller ?

Azure Cosmos DB est un service de plateforme qui vous permet d’augmenter votre productivité sans vous soucier des opérations de gestion et de surveillance de l’infrastructure. Vous devez simplement vous soucier du débit relevé dans les indicateurs de performance accessibles sur le portail, pour vérifier si vous approchez de la limite et devez augmenter ou réduire ce débit en conséquence.
Analyser les [contrats de niveau de service](monitor-accounts.md)
Utiliser les [indicateurs de performance](use-metrics.md) Utiliser les [journaux de diagnostic](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Quels sont les kits SDK clients qui fonctionnent avec l’API Apache Cassandra d’Azure Cosmos DB ?

Les pilotes clients du kit SDK Apache Cassandra reposant sur CQLv3 ont été utilisés pour les programmes clients. Si vous utilisez d’autres pilotes ou si vous rencontrez des difficultés, envoyez un e-mail à l’adresse [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="is-composite-partition-key-supported"></a>Les clés de partition composites sont-elles prises en charge ?

Oui, vous pouvez utiliser la syntaxe régulière pour créer une clé de partition composite.

### <a name="can-i-use-stable-loader-for-data-loading"></a>Puis-je utiliser le chargeur stable pour le chargement des données ?

Non, le chargeur stable n’est pas pris en charge.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Est-il possible d’associer un cluster Apache Cassandra local à l’API Cassandra d’Azure Cosmos DB ?

Actuellement, Azure Cosmos DB offre une expérience optimisée pour l’environnement cloud, sans la surcharge engendrée par les opérations. Si vous avez besoin de procéder à une association, envoyez un e-mail à l’adresse [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) avec une description de votre scénario. Nous travaillons à la facilitation de l’association du cluster Cassandra sur site/sur un autre cloud avec l’API Cassandra Cosmos DB.

### <a name="does-cassandra-api-provide-full-backups"></a>L’API Cassandra fournit-elle des sauvegardes complètes ?

Azure Cosmos DB réalise actuellement deux sauvegardes complètes gratuites toutes les quatre heures, sur toutes les API. Il est donc inutile de configurer une planification de sauvegarde ou d’autres choses.
Si vous souhaitez modifier la rétention et la fréquence, envoyez un e-mail à l’adresse [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) ou soumettez une demande d’assistance. Pour en savoir plus sur la fonctionnalité de sauvegarde, consultez l’article [Sauvegarde et restauration en ligne automatiques avec Azure Cosmos DB](online-backup-and-restore.md).

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Comment le compte d’API Cassandra gère-t-il le basculement en cas de défaillance d’une région ?

L’API Cassandra d’Azure Cosmos DB utilise la plateforme distribuée mondialement d’Azure Cosmos DB. Pour vous assurer que votre application peut tolérer des temps d’arrêt de centre de données, activez au moins une région supplémentaire pour le compte dans le portail Azure Cosmos DB (voir [Développement avec des comptes Azure Cosmos DB multirégions](high-availability.md)). Vous pouvez définir la priorité de la région à l’aide du portail (voir [Developing with multi-region Azure Cosmos DB accounts](high-availability.md) (Développement avec des comptes Azure Cosmos DB multirégions)).

Vous pouvez ajouter autant de régions que vous le souhaitez pour le compte, et contrôler l’emplacement de basculement en indiquant une priorité. Pour utiliser la base de données, vous devez également fournir une application à cet emplacement. Si vous procédez de la sorte, vos clients ne subissent aucun temps d’arrêt.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>L’API Apache Cassandra indexe-t-elle par défaut tous les attributs d’une entité ?

L’API Cassandra devrait prendre en charge l’indexation secondaire afin d’aider à créer des index sélectifs sur certains attributs. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Puis-je utiliser le nouveau kit SDK de l’API Cassandra localement avec l’émulateur ?

Oui, cette méthode est prise en charge.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Azure Cosmos DB en tant que plateforme semble offrir de nombreuses fonctionnalités, telles que le flux de modification. Ces fonctionnalités seront-elles ajoutées à l’API Cassandra ?

L’API Apache Cassandra fournit les mêmes fonctionnalités CQL qu’Apache Cassandra. Nous envisageons effectivement d’étudier la possibilité de prendre en charge d’autres fonctionnalités à l’avenir.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>La fonctionnalité x de l’API Cassandra normale ne fonctionne pas pour le moment. Où puis-je adresser mes commentaires ?

Partagez vos commentaires via les [commentaires user voice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
