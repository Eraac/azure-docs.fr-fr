---
title: États des tâches Azure Stream Analytics
description: Cet article décrit les différents états d’une tâche Stream Analytics
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: bef21dc35bbd2b9b50cf7b362624321866773bfe
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331337"
---
# <a name="azure-stream-analytics-job-states"></a>États des tâches Azure Stream Analytics

Une tâche Stream Analytics peut avoir l’un de quatre états à un moment donné. Vous pouvez trouver l’état de votre tâche sur la page Vue d’ensemble de la tâche Stream Analytics dans le portail Azure. 

| État | Description | Actions recommandées |
| --- | --- | --- |
| **Exécution** | Votre tâche est en cours d’exécution sur des événements de lecture Azure provenant des sources d’entrée définies, elle les traite et écrit les résultats dans les récepteurs de sortie configurés. | Il est recommandé de suivre les performances de votre tâche en surveillant les [mesures clés](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Arrêté** | Votre tâche est arrêtée et ne traite pas les événements. | N/D | 
| **Détérioré** | Il peut y avoir des problèmes intermittents avec les connexions entrantes et sortantes. Ces erreurs sont appelées des erreurs temporaires ; elles peuvent faire entrer votre travail dans un état Détérioré. Stream Analytics va immédiatement tenter de récupérer de ces erreurs pour revenir à un état Exécution (en l’espace de quelques minutes). Ces erreurs peuvent se produire en raison de problèmes réseau, de la disponibilité d’autres ressources Azure, d’erreurs de désérialisation etc. Les performances de votre tâche peuvent être affectées lorsque la tâche est en état Détérioré.| Vous pouvez examiner les [journaux de diagnostic ou d’activité](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) pour en savoir plus sur la cause de ces erreurs temporaires. Dans le cas des erreurs de désérialisation, il est recommandé d’entreprendre une action corrective pour garantir que les événements ne soient pas mal formés. Si le tâche continue d’atteindre la limite d’utilisation des ressources, essayez d’augmenter le nombre d’unités de recherche ou [parallélisez votre tâche](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). Dans d’autres cas où vous ne pouvez pas intervenir, Stream Analytics va tenter de récupérer un état *Exécution*. <br> Vous pouvez utiliser la métrique du [délai en filigrane](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) pour savoir si ces erreurs temporaires ont une incidence sur les performances de votre travail.|
| **Échec** | Votre tâche a rencontré une erreur critique résultant en un état d’échec. Les événements ne sont pas lus ni traités. Les erreurs de runtime sont une cause courante entraînant l’état d’échec des tâches. | Vous pouvez [configurer des alertes](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) afin d’être averti lorsque la tâche entre en état d’échec. <br> <br>Vous pouvez déboguer à l’aide des [journaux de diagnostic et d’activité](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) pour identifier la cause et résoudre le problème.|

## <a name="next-steps"></a>Étapes suivantes
* [Configurer des alertes pour les travaux Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Mesures disponibles dans Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Résoudre les problèmes à l’aide des journaux de diagnostic et d’activité](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
