---
title: Importation de données pour ingestion dans un index de recherche - Recherche Azure
description: Remplissez et chargez des données dans un index dans Recherche Azure à partir de sources de données externes.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b56a31a58937ddbea08ff22c3d1c0c71942f47f1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445392"
---
# <a name="data-import-overview---azure-search"></a>Vue d’ensemble de l’importation des données - Recherche Azure

Dans Recherche Azure, les requêtes s’exécutent sur le contenu chargé et enregistré dans un [index de recherche](search-what-is-an-index.md). Cet article examine les deux méthodes de base pour remplir un index : *envoyer* les données dans l’index par programme ou pointer un [indexeur Recherche Azure](search-indexer-overview.md) à une source de données prise en charge pour *extraire* les données.

Ces deux approches ont pour objectif de *charger des données* entre une source de données externe et un index Recherche Azure. Recherche Azure vous permet de créer un index vide, mais ce dernier ne pourra être interrogé qu'après envoi (push) ou extraction de données.

## <a name="pushing-data-to-an-index"></a>Envoyer des données à un index
Le modèle d’émission, utilisé pour envoyer vos données Recherche Azure par programme, est l’approche la plus flexible. Tout d’abord, il n’y a pas de restrictions sur le type de source de données. Tout jeu de données composé de documents JSON peut être appliqué à un index Recherche Azure, en supposant que chaque document dans le jeu de données possède des champs mappant des champs définis dans votre schéma d’index. En second lieu, il n’y a aucune restriction sur la fréquence d’exécution. Vous pouvez transmettre des modifications à un index aussi souvent que vous le souhaitez. Pour les applications ayant des exigences à très faible latence (par exemple, si vous devez synchroniser les opérations de recherche avec les bases de données d’inventaire dynamiques), le modèle d’émission est la seule option.

Cette approche est plus flexible que le modèle d’extraction, car vous pouvez charger des documents individuellement ou par lots (jusqu’à 1 000 par lot ou 16 Mo, quelle que soit la limite atteinte en premier). Le modèle d’émission vous permet également de charger des documents dans Azure Search indépendamment de l’emplacement des données.

### <a name="how-to-push-data-to-an-azure-search-index"></a>Comment envoyer des données à un index Recherche Azure

Vous pouvez utiliser les API suivantes pour charger un ou plusieurs documents dans un index :

+ [Ajout, mise à jour ou suppression de documents (API REST)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [classe indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) ou [classe indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Il n’existe actuellement aucune prise en charge de l’outil de diffusion de données via le portail.

Pour une présentation de chaque méthode, consultez [Guide de démarrage rapide : Créer un index Recherche Azure à l’aide de PowerShell](search-create-index-rest-api.md) ou [Guide de démarrage rapide C# : Création d’un index Recherche Azure à l’aide du Kit de développement logiciel (SDK) .NET](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Actions d’indexation : upload, merge, mergeOrUpload, supprimer

Vous pouvez contrôler le type d’action d’indexation par document, en spécifiant si le document doit être chargé en intégralité, fusionné avec du contenu de document existant ou supprimé.

Dans l’API REST, émettez des requêtes HTTP POST avec un corps de requête JSON à l’URL de point de terminaison de votre index Recherche Azure. Chaque objet JSON du tableau « valeur » contient la clé du document et spécifie qu’une action d’indexation doit ajouter, mettre à jour ou supprimer le contenu d’un document. Pour un exemple de code, consultez [Charger des documents](search-get-started-dotnet.md#load-documents).

Dans le kit de développement logiciel (SDK) .NET, empaquetez vos données dans un objet `IndexBatch`. Un `IndexBatch` encapsule une collection d’objets `IndexAction`, chacun d’entre eux contenant un document et une propriété qui indique à Recherche Azure les actions à effectuer sur ce document. Pour obtenir un exemple de code, consultez le [guide de démarrage rapide C#](search-get-started-dotnet.md).


| @search.action | Description | Champs requis pour chaque document | Notes |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Une action `upload` est similaire à celle d’un « upsert », où le document est inséré s’il est nouveau et mis à jour/remplacé s’il existe déjà. |une clé, ainsi que tout autre champ que vous souhaitez définir |Lors de la mise à jour ou du remplacement d’un document existant, un champ qui n’est pas spécifié dans la requête sera défini sur la valeur `null`, y compris lorsque le champ a été précédemment défini sur une valeur non null. |
| `merge` |Met à jour un document existant avec les champs spécifiés. Si le document n’existe pas dans l’index, la fusion échoue. |une clé, ainsi que tout autre champ que vous souhaitez définir |N'importe quel champ que vous spécifiez dans une fusion remplace le champ existant dans le document. Dans le kit de développement logiciel (SDK), cela inclut les champs de type `DataType.Collection(DataType.String)`. Dans l'API REST, cela inclut les champs de type `Collection(Edm.String)`. Par exemple, si le document contient un champ `tags` avec la valeur `["budget"]` et que vous exécutez une fusion avec la valeur `["economy", "pool"]` pour le champ `tags`, la valeur finale du champ `tags` sera `["economy", "pool"]`, et non `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Cette action est similaire à celle d’une action `merge` s’il existe déjà dans l’index un document comportant la clé spécifiée. Dans le cas contraire, elle exécutera une action `upload` avec un nouveau document. |une clé, ainsi que tout autre champ que vous souhaitez définir |- |
| `delete` |Cette action supprime de l’index le document spécifié. |clé uniquement |Tous les champs que vous spécifiez en dehors du champ de clé sont ignorés. Si vous souhaitez supprimer un champ individuel dans un document, utilisez plutôt `merge` et définissez simplement le champ de manière explicite sur la valeur null. |

## <a name="decide-which-indexing-action-to-use"></a>Déterminer l’action d’indexation à utiliser
Pour importer des données à l’aide du kit de développement logiciel (SDK) .NET, (upload, merge, delete et mergeOrUpload). Selon le type d’action que vous allez choisir, seuls certains champs doivent être inclus dans chaque document :


### <a name="formulate-your-query"></a>Formuler votre requête
Deux méthodes permettent d’effectuer une [recherche dans un index à l’aide de l’API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). L’une consiste à émettre une requête HTTP POST, dans laquelle vos paramètres de requête sont définis dans un objet JSON contenu dans le corps de la requête. L’autre consiste à émettre une requête HTTP GET, dans laquelle vos paramètres de requête seront définis à l’intérieur de l’URL de requête. Notez que les limites en matière de taille des paramètres de requête sont [plus souples](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) pour la méthode POST que pour la méthode GET. Pour cette raison, nous vous recommandons d’utiliser POST, à moins que la situation justifie l’utilisation de GET.

Pour les méthodes POST et GET, vous devez indiquer dans l’URL de la demande le *nom de votre service*, le *nom de l’index* ainsi que la *version d’API* appropriée (la version actuelle de l’API est celle du `2019-05-06` au moment de la publication de ce document). Pour la méthode GET, vous renseignez les paramètres de requête au niveau de la *chaîne de requête* à la fin de l’URL. Voici le format URL à utiliser :

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

La méthode POST suit un format identique, mais seule la version d’API figure dans les paramètres de chaîne de requête.


## <a name="pulling-data-into-an-index"></a>Extraction de données dans un index
Le modèle d’extraction analyse une source de données prise en charge et charge automatiquement les données dans votre index. Dans Recherche Azure, cette fonctionnalité est implémentée via des *indexeurs*, actuellement disponibles pour ces plateformes :

+ [Stockage Blob](search-howto-indexing-azure-blob-storage.md)
+ [Stockage Table](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Base de données Azure SQL et SQL Server sur les machines virtuelles Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Les indexeurs connectent un index à une source de données (généralement une table, une vue ou une structure équivalente) et mappent les champs source aux champs équivalents de l’index. Pendant l’exécution, l’ensemble de lignes est automatiquement transformé en JSON et chargé dans l’index spécifié. Tous les indexeurs prennent en charge la planification de sorte que vous puissiez spécifier la fréquence à laquelle les données sont à actualiser. La plupart des indexeurs fournissent le suivi des modifications si la source de données le prend en charge. En suivant les modifications et les suppressions effectuées dans les documents existants, et en reconnaissant les nouveaux documents, les indexeurs suppriment la nécessité de gérer activement les données de votre index. 


### <a name="how-to-pull-data-into-an-azure-search-index"></a>Comment extraire des données dans un index Recherche Azure

La fonctionnalité de l’indexeur est exposée dans le [Portail Azure](search-import-data-portal.md), ainsi que dans [l’API REST](/rest/api/searchservice/Indexer-operations) et le [Kit de développement logiciel (SDK) .NET](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

L’avantage du portail est qu’Azure Search peut générer un schéma d’index par défaut pour vous en lisant les métadonnées du jeu de données source. Vous pouvez modifier l’index généré jusqu’à ce que l’index soit traité, après quoi les seules modifications de schéma autorisées sont celles qui ne nécessitent pas la réindexation. Si les modifications que vous souhaitez apporter impactent directement le schéma, vous devez reconstruire l’index. 

## <a name="verify-data-import-with-search-explorer"></a>Vérifier l’importation de données avec l’Explorateur de recherche

Un moyen rapide d’effectuer une vérification préliminaire sur le téléchargement de document consiste à utiliser **l’Explorateur de recherche** dans le portail. L’Explorateur vous permet d’interroger un index sans avoir à écrire du code. L’expérience de recherche est basée sur les paramètres par défaut, tels que la [syntaxe simple](/rest/api/searchservice/simple-query-syntax-in-azure-search) et le [paramètre de requête searchMode](/rest/api/searchservice/search-documents). Les résultats sont retournés au format JSON afin que vous puissiez inspecter le document dans son intégralité.

> [!TIP]
> De nombreux [exemples de code Azure Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) incluent des jeux de données incorporés ou disponibles rapidement, offrant ainsi une prise en main simplifiée. Le portail fournit également un exemple d’indexeur et de source de données composée d’un petit jeu de données immobilières (nommé « realestate-us-sample »). Lorsque vous exécutez l’indexeur préconfiguré sur l’exemple de source de données, un index est créé et chargé avec des documents qui peuvent ensuite être interrogés dans l’Explorateur de recherche ou par le code que vous écrivez.

## <a name="see-also"></a>Voir aussi

+ [Présentation de l’indexeur](search-indexer-overview.md)
+ [Procédure pas à pas dans le portail : créer, charger, interroger un index](search-get-started-portal.md)
