---
title: Indexer des objets blob CSV avec l’indexeur Blob de la Recherche Azure – Recherche Azure
description: Analysez des objets blob CSV dans le Stockage Blob Azure pour la recherche en texte intégral à l’aide d’un index de Recherche Azure. Les indexeurs automatisent l’ingestion des données pour certaines sources de données, comme le Stockage Blob Azure.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: e7d959e77d27fb04b18f402e4056d4dea1607039
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522881"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexation d’objets blob CSV avec l’indexeur d’objets blob Azure Search

> [!Note]
> Le mode d’analyse delimitedText est en préversion et n’a pas été conçue pour la production. L’[API REST version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Il n’y a pas de prise en charge de .NET SDK pour l’instant.
>

Par défaut, l’ [indexeur d’objets blob Azure Search](search-howto-indexing-azure-blob-storage.md) analyse les objets blob de texte délimité comme un bloc de texte unique. Toutefois, avec des objets blob contenant des données CSV, vous souhaitez généralement traiter chaque ligne dans l’objet blob comme un document distinct. Par exemple, vous pouvez analyser le texte délimité suivant dans deux documents contenant chacun les champs « id », « datePublished » et « tags » : 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Dans cet article, vous découvrirez comment analyser les objets blob CSV avec un indexeur d’objets blob Recherche Azure en définissant le mode d’analyse `delimitedText`. 

> [!NOTE]
> Suivez les recommandations de configuration de l’indexeur dans [Indexation un-à-plusieurs](search-howto-index-one-to-many-blobs.md) pour générer plusieurs documents de recherche à partir d’un objet blob Azure.

## <a name="setting-up-csv-indexing"></a>Configuration de l’indexation CSV
Pour indexer des objets blob CSV, créez ou mettez à jour une définition d’indexeur avec le mode d’analyse `delimitedText` sur une demande [Créer un indexeur](https://docs.microsoft.com/rest/api/searchservice/create-indexer) :

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` Indique que la première ligne (non vide) de chaque objet blob contient des en-têtes.
Si les objets blob ne contiennent pas de ligne d’en-tête initiale, les en-têtes doivent être spécifiés dans la configuration de l’indexeur : 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Vous pouvez personnaliser le caractère délimiteur à l’aide du paramètre de configuration `delimitedTextDelimiter`. Par exemple :

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Actuellement, seul le format UTF-8 est pris en charge. Si vous devez prendre en charge d’autres encodages, faites-le-nous savoir sur [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Lorsque vous utilisez le mode d’analyse de texte délimité, Azure Search suppose que tous les objets blob dans votre source de données seront de type CSV. Si vous devez prendre en charge une combinaison d’objets blob CSV et autres dans la même source de données, faites-le nous savoir sur [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Exemples de requête
En résumé, voici des exemples complets de charges utiles. 

Source de données : 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexeur :

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Aidez-nous à améliorer Azure Search
Si vous avez des suggestions de fonctionnalités ou des idées d’amélioration, faites-le-nous savoir [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

