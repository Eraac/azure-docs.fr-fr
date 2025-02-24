---
title: Comment surveiller l’état et les résultats d’un indexeur – Recherche Azure
description: Surveillez l’état, la progression et les résultats des indexeurs Recherche Azure dans le Portail Azure (en utilisant l’API REST) ou le SDK .NET.
ms.date: 06/28/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 07b4fe2ef830c3ce09b655cf4b433d14923229a9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486181"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>Comment surveiller l’état et les résultats d’un indexeur Recherche Azure

Recherche Azure fournit des informations sur l’état et le suivi des exécutions actuelles et historiques de chaque indexeur.

La surveillance de l’indexeur est utile lorsque vous souhaitez :

* Suivre la progression d’un indexeur durant une exécution.
* Passer en revue les résultats de l’exécution en cours ou précédente de l’indexeur.
* Identifier les erreurs de l’indexeur de niveau supérieur et les erreurs ou avertissements concernant l’indexation de documents individuels.

## <a name="find-indexer-status-and-history-details"></a>Rechercher l’état et les informations historiques des indexeurs

Vous pouvez accéder aux informations de surveillance de l’indexeur de différentes manières, notamment :

* Dans le [portail Azure](#portal) :
* À l’aide de l’[API REST](#restapi)
* À l’aide du [SDK .NET](#dotnetsdk)

Les informations de surveillance de l’indexeur disponibles comprennent toutes les informations suivantes (bien que les formats de données diffèrent en fonction de la méthode d’accès utilisée) :

* Informations d’état de l’indexeur lui-même
* Informations sur l’exécution la plus récente de l’indexeur, y compris son état, les heures de début et de fin et les erreurs et avertissements détaillés.
* Liste des exécutions de l’indexeur d’historique, ainsi que leurs états, résultats, erreurs et avertissements.

L’exécution des indexeurs qui traitent de grands volumes de données peut être longue. Par exemple, les indexeurs qui traitent des millions de documents sources peuvent fonctionner pendant 24 heures, puis redémarrer presque immédiatement. Les indexeurs traitant de grands volumes de données peuvent se trouver encore à l’état **En cours** dans le portail. Même lorsqu’un indexeur est en cours d’exécution, des informations détaillées sont disponibles sur la progression en cours et les exécutions précédentes.

<a name="portal"></a>

## <a name="monitor-indexers-in-the-portal"></a>Surveiller les indexeurs dans le portail

L’état actuel de tous vos indexeurs figure dans la liste **Indexeurs** de la page Vue d’ensemble de votre service de recherche.

   ![Liste des indexeurs](media/search-monitor-indexers/indexers-list.png "Liste des indexeurs")

Lorsqu’un indexeur est en cours d’exécution, la liste affiche l’état **En cours** et la valeur de **Documents ayant réussi** indique le nombre de documents traités à ce jour. Le portail peut prendre quelques minutes pour mettre à jour les valeurs d’état de l’indexeur et le nombre de documents.

Un indexeur dont l’exécution la plus récente a réussi affiche **Réussite**. Une exécution de l’indexeur peut réussir même si des documents individuels contiennent des erreurs, car le nombre d’erreurs est inférieur à la valeur du paramètre **Nombre maximal d’éléments en échec** de l’indexeur.

Si la dernière exécution s’est terminée avec une erreur, l’état affiche **Échec**. **Réinitialiser** signifie que l’état de suivi des modifications de l’indexeur a été réinitialisé.

Cliquez sur un indexeur dans la liste pour afficher plus de détails sur les exécutions actuelles et récentes de l’indexeur.

   ![Résumé de l’indexeur et historique d’exécution](media/search-monitor-indexers/indexer-summary.png "Résumé de l’indexeur et historique d’exécution")

Le graphique **Résumé de l’indexeur** indique le nombre de documents traités lors des exécutions les plus récentes de l’indexeur.

La liste **Détails de l’exécution** affiche jusqu’à 50 des résultats d’exécution les plus récents.

Cliquez sur un résultat d’exécution dans la liste pour afficher les détails correspondants. Cela inclut les heures de début et de fin, ainsi que les erreurs et les avertissements qui se sont produits.

   ![Détails de l’exécution de l’indexeur](media/search-monitor-indexers/indexer-execution.png "Détails de l’exécution de l’indexeur")

Si vous rencontrez des problèmes spécifiques au document pendant l’exécution, ils seront signalés dans les champs Erreurs et Avertissements.

   ![Détails de l’indexeur avec des erreurs](media/search-monitor-indexers/indexer-execution-error.png "Détails de l’indexeur avec des erreurs")

Les avertissements sont courants avec certains types d’indexeurs et n’indiquent pas toujours un problème. Par exemple, les indexeurs qui utilisent des services cognitifs peuvent signaler des avertissements lorsque les fichiers image ou PDF ne contiennent pas de texte à traiter.

Pour plus d’informations sur l’analyse des erreurs et des avertissements de l’indexeur, consultez [Résoudre les problèmes courants des indexeurs dans la Recherche Azure](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-indexers-using-the-rest-api"></a>Surveiller des indexeurs à l’aide de l’API REST

Vous pouvez récupérer l’état et l’historique d’exécution d’un indexeur à l’aide de la [commande Get Indexer Status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) :

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

La réponse contient l'état d'intégrité global de l'indexeur, le dernier appel de l'indexeur (ou celui en cours), ainsi que l'historique des appels récents de l'indexeur.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

L’historique d’exécution contient les 50 exécutions les plus récentes, classées par ordre chronologique inverse (la plus récente en premier).

Notez qu’il existe deux valeurs d’état différentes. L’état de niveau supérieur s’applique à l’indexeur lui-même. Un état d’indexeur **En cours** signifie que l’indexeur est correctement configuré et disponible pour être exécuté mais qu’il n’est pas actuellement en cours d’exécution.

Chaque exécution de l’indexeur possède également un état qui indique si cette exécution spécifique est en cours d’exécution (**running**) ou déjà terminée avec l’état **success**, **transientFailure** ou **persistentFailure**. 

Lorsque l’on réinitialise un indexeur pour actualiser son état de suivi des modifications, une entrée distincte indiquant l’état **Réinitialiser** est ajoutée à l’historique des exécutions.

Pour plus d’informations sur les codes d’état et les données de surveillance de l’indexeur, consultez [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-indexers-using-the-net-sdk"></a>Surveiller des indexeurs à l’aide du SDK .NET

Vous pouvez configurer la planification d’un indexeur à l’aide du kit de développement logiciel (SDK) .NET Recherche Azure. Pour ce faire, ajoutez la propriété **schedule** lors de la création ou de la mise à jour d’un indexeur.

L’exemple C# suivant écrit des informations sur l’état d’un indexeur et les résultats de son exécution la plus récente (ou en cours) à la console.

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

Sur la console, vous obtenez un résultat semblable à ceci :

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Notez qu’il existe deux valeurs d’état différentes. L’état de niveau supérieur est l’état de l’indexeur lui-même. Un état d’indexeur **En cours** signifie que l’indexeur est correctement configuré et disponible pour être exécuté mais qu’il n’est pas actuellement en cours d’exécution.

Chaque exécution de l’indexeur possède également un état qui indique si cette exécution spécifique est en cours d’exécution (**En cours**) ou déjà terminée avec l’état **Réussite** ou **TransientError**. 

Lorsque l’on réinitialise un indexeur pour actualiser son état de suivi des modifications, une entrée distincte indiquant l’état **Réinitialiser** est ajoutée à l’historique.

Pour plus d’informations sur les codes d’état et les données de surveillance de l’indexeur, consultez [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) dans l’API REST.

Il est possible de récupérer les détails sur les erreurs ou les avertissements spécifiques aux documents en énumérant les listes `IndexerExecutionResult.Errors` et `IndexerExecutionResult.Warnings`.

Pour plus d’informations sur les classes du SDK .NET permettant de surveiller les indexeurs, consultez [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) et [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).
