---
title: Informations de référence sur le fichier host.json pour Azure Functions 1.x
description: Documentation de référence pour le fichier host.json d’Azure Functions avec le runtime v1.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: glenga
ms.openlocfilehash: 44bc5a245d1bcbc8ff53991af4193ef86f7cd704
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62107072"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>Informations de référence sur le fichier host.json pour Azure Functions 1.x

> [!div class="op_single_selector" title1="Sélectionnez la version du runtime Azure Functions que vous utilisez : "]
> * [Version 1](functions-host-json-v1.md)
> * [Version 2](functions-host-json.md)

Le fichier de métadonnées *host.json* contient les options de configuration globale qui affectent l’ensemble des fonctions d’une application de fonction. Cet article répertorie les paramètres qui sont disponibles pour le runtime v1. Le schéma JSON est sur http://json.schemastore.org/host.

> [!NOTE]
> Cet article concerne Azure Functions 1.x.  Pour obtenir une référence de host.json dans Functions 2.x, consultez [Informations de référence sur le fichier host.json pour Azure Functions 2.x](functions-host-json.md).

Les autres options de configuration d’application de fonction sont managées dans vos [paramètres d’application](functions-app-settings.md).

Certains paramètres host.json sont uniquement utilisés lors de l’exécution locale dans le fichier [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="sample-hostjson-file"></a>Exemple de fichier host.json

L’exemple de fichier *host.json* suivant contient toutes les options possibles spécifiées.


```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

Les sections suivantes de cet article expliquent chaque propriété de niveau supérieur. Toutes les autres propriétés sont facultatives sauf indication contraire.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Paramètres de configuration pour les [déclencheurs et liaisons Event Hub](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

Liste des fonctions que l’hôte de travail exécute. Un tableau vide désigne l’exécution de toutes les fonctions. Utilisée uniquement pour une [exécution locale](functions-run-local.md). Dans les applications de fonction dans Azure, vous devez plutôt suivre la procédure décrite dans [Guide pratique pour désactiver des fonctions dans Azure Functions](disable-function.md) pour désactiver des fonctions spécifiques au lieu d’utiliser ce paramètre.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indique la durée avant expiration du délai de toutes les fonctions. Dans les plans de consommation serverless, la plage valide est comprise entre 1 seconde et 10 minutes, et la valeur par défaut est de 5 minutes. Dans un plan App Service, il n’existe aucune limite globale et la valeur par défaut dépend de la version du runtime.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Paramètres de configuration de l’[analyse d’intégrité d’hôtes](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|enabled|true|Indique si la fonctionnalité est activée. | 
|healthCheckInterval|10 secondes|L’intervalle de temps entre les contrôles d’intégrité périodiques en arrière-plan. | 
|healthCheckWindow|2 minutes|Une fenêtre de temps coulissante utilisée conjointement au paramètre `healthCheckThreshold`.| 
|healthCheckThreshold|6|Nombre maximal de fois où le contrôle d’intégrité peut échouer avant le lancement d’un recyclage de l’hôte.| 
|counterThreshold|0.80|Le seuil auquel un compteur de performance est considéré comme non intègre.| 

## <a name="http"></a>http

Paramètre de configuration pour les [déclencheurs et liaisons http](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*Version 1.x uniquement.*

ID unique d’un hôte de travail. Il peut s’agir d’un GUID en minuscules dont les tirets ont été supprimés. Requis lors d’une exécution locale. Lors de l’exécution dans Azure, nous vous recommandons de pas définir de valeur d’ID. Un ID est généré automatiquement dans Azure lorsque `id` est omis. 

Si vous partagez un compte de stockage entre plusieurs applications de fonction, assurez-vous que chaque application de fonction a une `id` différente. Vous pouvez omettre la propriété `id` ou définir manuellement chaque `id` de l’application de fonction sur une autre valeur. Le déclencheur du minuteur utilise un verrou de stockage pour vous assurer qu’il n’y aura qu’une seule instance du minuteur lorsqu’une application de fonction augmentera la taille de plusieurs instances. Si deux applications de fonction partagent le même `id` et que chacune utilise un déclencheur de minuteur, un seul minuteur s’exécutera.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

Contrôle le filtrage des journaux d’activité écrits par un [objet ILogger](functions-monitoring.md#write-logs-in-c-functions) ou par [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|categoryFilter|n/a|Spécifie un filtrage par catégorie.| 
|defaultLevel|Information|Pour toutes les catégories non spécifiées dans le tableau `categoryLevels`, envoie les journaux d’activité de ce niveau et des niveaux supérieurs à Application Insights.| 
|categoryLevels|n/a|Tableau des catégories qui spécifie le niveau de journalisation minimal à envoyer à Application Insights pour chaque catégorie. La catégorie indiquée ici contrôle toutes les catégories qui commencent par la même valeur, et les valeurs plus longues sont prioritaires. Dans l’exemple de fichier *host.json* précédent, toutes les catégories qui commencent par « Host.Aggregator » journalisent au niveau `Information`. Toutes les autres catégories commençant par « Host », comme « Host.Executor », journalisent au niveau `Error`.| 

## <a name="queues"></a>queues

Paramètre de configuration pour les [déclencheurs et liaisons de file d’attente de stockage](functions-bindings-storage-queue.md).

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|maxPollingInterval|60000|Intervalle maximal (en millisecondes) entre les interrogations de la file d’attente.| 
|visibilityTimeout|0|Intervalle de temps entre les nouvelles tentatives en cas d’échec du traitement d’un message.| 
|batchSize|16|Le nombre de messages de file d’attente que le runtime Functions récupère simultanément et traite en parallèle. Quand le nombre de messages en cours de traitement descend à `newBatchThreshold`, le runtime obtient un autre lot et commence à traiter ces messages. Par conséquent, le nombre maximal de messages traités simultanément par fonction est `batchSize` plus `newBatchThreshold`. Cette limite s’applique séparément à chaque fonction déclenchée par une file d’attente. <br><br>Si vous souhaitez éviter les exécutions parallèles pour les messages reçus sur une file d’attente, vous pouvez définir `batchSize` sur 1. Toutefois, ce paramètre évite les opérations simultanées uniquement pendant l’exécution de votre application de fonction sur une machine virtuelle unique. Si l’application de fonction augmente la taille des instances sur plusieurs machines virtuelles, chaque machine virtuelle peut exécuter une instance de chaque fonction déclenchée par une file d’attente.<br><br>La valeur `batchSize` maximale est de 32. | 
|maxDequeueCount|5|Nombre de tentatives de traitement d’un message avant de le placer dans la file d’attente de messages incohérents.| 
|newBatchThreshold|batchSize/2|Quand le nombre de messages traités simultanément passe en dessous de cette valeur, le runtime récupère un autre lot.| 

## <a name="servicebus"></a>serviceBus

Paramètre de configuration pour les [déclencheurs et liaisons Service Bus](functions-bindings-service-bus.md).

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|maxConcurrentCalls|16|Nombre maximal d’appels simultanés pour le rappel que la pompe de messages doit initier. Par défaut, le runtime Functions traite plusieurs messages simultanément. Pour que le runtime ne traite qu’un message de file d’attente ou de rubrique à la fois, définissez `maxConcurrentCalls` sur 1. | 
|prefetchCount|n/a|Valeur PrefetchCount par défaut qui est utilisée par l’instance MessageReceiver sous-jacente.| 
|autoRenewTimeout|00:05:00|Durée maximale pendant laquelle le verrouillage de message doit être renouvelé automatiquement.| 

## <a name="singleton"></a>singleton

Paramètres de configuration du comportement de verrouillage Singleton. Pour plus d’informations, consultez l’article sur le [problème de GitHub relatif à la prise en charge de singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|lockPeriod|00:00:15|Période pendant laquelle des verrous sont établis au niveau des fonctions. Les verrous se renouvellent automatiquement.| 
|listenerLockPeriod|00:01:00|Période pendant laquelle des verrous sont établis au niveau des écouteurs.| 
|listenerLockRecoveryPollingInterval|00:01:00|Intervalle de temps utilisé pour la récupération des verrous d’écouteurs si un verrou de ce type n’a pas pu être acquis au démarrage.| 
|lockAcquisitionTimeout|00:01:00|Période de temps maximale pendant laquelle le runtime essaie d’acquérir un verrou.| 
|lockAcquisitionPollingInterval|n/a|Intervalle écoulé entre les tentatives d’acquisition de verrou.| 

## <a name="tracing"></a>tracing

*Version 1.x*

Paramètres de configuration des journaux d’activité que vous créez à l’aide d’un objet `TraceWriter`. Consultez les sections relatives à la [journalisation en C#](functions-reference-csharp.md#logging) et à la [journalisation Node.js](functions-reference-node.md#writing-trace-output-to-the-console).

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------| 
|consoleLevel|info|Niveau de suivi pour la journalisation de la console. Options : `off`, `error`, `warning`, `info` et `verbose`.|
|fileLoggingMode|debugOnly|Niveau de suivi pour la journalisation des fichiers. Options : `never`, `always`, `debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

Ensemble de [répertoires de code partagé](functions-reference-csharp.md#watched-directories) dont les modifications doivent être surveillées.  Garantit que lorsque le code contenu dans ces répertoires est modifié, les changements sont récupérés par vos fonctions.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment mettre à jour le fichier host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Consultez les paramètres globaux des variables d’environnement](functions-app-settings.md)
