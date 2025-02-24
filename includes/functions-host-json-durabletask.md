---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 2e30184c7273fad2f9bc8adb34834ee14840733b
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608179"
---
Paramètres de configuration de [Fonctions durables](../articles/azure-functions/durable-functions-overview.md).

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

Les noms de hubs de tâches doivent commencer par une lettre et contenir uniquement des lettres et des chiffres. S’il n’est pas spécifié, le nom du hub de tâches par défaut d’une application de fonction est **DurableFunctionsHub**. Pour en savoir plus, consultez la section relative aux [hubs de tâches](../articles/azure-functions/durable-functions-task-hubs.md).

|Propriété  |Default | Description |
|---------|---------|---------|
|hubName|DurableFunctionsHub|D'autres noms de [hub de tâches](../articles/azure-functions/durable-functions-task-hubs.md) peuvent être utilisés pour isoler plusieurs applications Durable Functions les unes des autres, même si elles s'appuient sur le même principal de stockage.|
|controlQueueBatchSize|32|Nombre de messages à extraire de la file d’attente de contrôle en une seule fois.|
|partitionCount |4|Nombre de partitions pour la file d’attente de contrôle. Doit être un entier positif compris entre 1 et 16.|
|controlQueueVisibilityTimeout |5 minutes|Délai d’expiration de la visibilité des messages supprimés de la file d’attente de contrôle.|
|workItemQueueVisibilityTimeout |5 minutes|Délai d’expiration de la visibilité des messages supprimés de la file d’attente des éléments de travail.|
|maxConcurrentActivityFunctions |10 fois le nombre de processeurs sur l’ordinateur actuel|Nombre maximal de fonctions d’activité pouvant être traitées simultanément sur une seule instance d’hôte.|
|maxConcurrentOrchestratorFunctions |10 fois le nombre de processeurs sur l’ordinateur actuel|Nombre maximal de fonctions d’orchestrateur pouvant être traitées simultanément sur une seule instance d’hôte.|
|maxQueuePollingInterval|30 secondes|Intervalle d’interrogation de la file d’attente des éléments de travail et de contrôle maximum, indiqué au format *hh:mm:ss*. Des valeurs plus élevées peuvent entraîner une plus grande latence lors du traitement des messages. Des valeurs plus faibles peuvent entraîner des coûts de stockage plus importants, suite à un nombre plus important de transactions de stockage.|
|azureStorageConnectionStringName |AzureWebJobsStorage|Nom du paramètre d’application qui contient la chaîne de connexion de stockage Azure utilisée pour gérer les ressources de stockage Azure sous-jacentes.|
|trackingStoreConnectionStringName||Nom d’une chaîne de connexion à utiliser pour les tables d’historique et d’instances. S’il n’est pas spécifié, la connexion `azureStorageConnectionStringName` est utilisée.|
|trackingStoreNamePrefix||Préfixe à utiliser pour les tables d’historique et d’instances lorsque le paramètre `trackingStoreConnectionStringName` est spécifié. S’il n’est pas défini, la valeur du préfixe par défaut correspond à `DurableTask`. Si le paramètre `trackingStoreConnectionStringName` n’est pas spécifié, les tables d’historique et d’instances utilisent la valeur `hubName` en tant que préfixe, et tous les paramètres relatifs à `trackingStoreNamePrefix` sont ignorés.|
|traceInputsAndOutputs |false|Valeur indiquant s’il faut tracer les entrées et sorties des appels de fonction. Lors du traçage d’événements d’exécution de fonctions, le nombre d’octets est inclus par défaut dans les entrées et les sorties sérialisées pour les appels de fonction. Ce comportement fournit un minimum d’informations sur l’apparence des entrées et sorties, sans encombrer les journaux d’activité ni exposer accidentellement des informations sensibles. La définition de cette propriété sur True amène la journalisation de la fonction par défaut à consigner la totalité du contenu des entrées et sorties de fonction.|
|logReplayEvents|false|Valeur indiquant s’il faut écrire les événements de relecture d’orchestration à Application Insights.|
|eventGridTopicEndpoint ||URL du point de terminaison de la rubrique personnalisée Azure Event Grid. Lorsque cette propriété est définie, les événements de notification du cycle de vie de l’orchestration sont publiés sur ce point de terminaison. Cette propriété prend en charge la résolution de paramètres de l’application.|
|eventGridKeySettingName ||Nom du paramètre d’application contenant la clé utilisée pour l’authentification à l’aide de la rubrique personnalisée Azure Event Grid sur `EventGridTopicEndpoint`.|
|eventGridPublishRetryCount|0|Nombre de nouvelles tentatives à effectuer en cas d’échec de la publication de la rubrique Event Grid.|
|eventGridPublishRetryInterval|5 minutes|L’intervalle avant nouvelle tentative de publication Event Grid au format *hh:mm:ss*.|
|eventGridPublishEventTypes||Liste des types d’événement à publier sur Event Grid. Si elle n’est pas spécifiée, cela signifie que tous les types d’événement seront publiés. Les valeurs autorisées sont `Started`, `Completed`, `Failed` et `Terminated`.|

La plupart de ces paramètres sont destinés à l’optimisation des performances. Pour plus d’informations, consultez [Performances et mise à l’échelle](../articles/azure-functions/durable-functions-perf-and-scale.md).
