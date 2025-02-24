---
title: Surveiller les indicateurs de performance de serveur de Azure Analysis Services | Microsoft Docs
description: Découvrez comment surveiller les indicateurs de performance de serveur Analysis Services dans un portail Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cdffa8e138062a91bd1876ac6e44728c47d9cdd7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61065046"
---
# <a name="monitor-server-metrics"></a>Surveiller les indicateurs de performances du serveur

Analysis Services fournit des indicateurs de performances vous aidant à surveiller les performances et l’intégrité de vos serveurs. Par exemple, vous pouvez surveiller l’utilisation de la mémoire et du processeur, le nombre de connexions clientes et la consommation de ressources de requête. Analysis Services utilise la même infrastructure de surveillance que la plupart des autres services Azure. Pour en savoir plus, consultez [Indicateurs de performances dans Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Pour effectuer des diagnostics plus approfondis, suivre des performances et identifier les tendances de plusieurs ressources de service au sein d’un groupe de ressources ou d’un abonnement, utilisez [Azure Monitor](https://azure.microsoft.com/services/monitor/). Azure Monitor (service) peut entraîner un service facturable.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Pour surveiller les indicateurs de performance sur un serveur Analysis Services

1. Dans le portail Azure, sélectionnez **Indicateurs de performances**.

    ![Surveiller dans le portail Azure](./media/analysis-services-monitor/aas-monitor-portal.png)

2. Dans **Indicateurs de performances disponibles**, sélectionnez les indicateurs de performances à inclure sur votre graphique. 

    ![Graphique de surveillance](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Indicateurs de performances de serveur

Ce tableau permet de déterminer les meilleurs indicateurs de performances pour votre scénario d’analyse. Seuls ceux appartenant à la même unité peuvent être affichés sur un même graphique.

|Métrique|Nom d’affichage de la métrique|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Longueur de la file d’attente des travaux du pool de commandes|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads de commandes.|
|CurrentConnections|Connexion : Connexions en cours|Nombre|Moyenne|Nombre actuel de connexions client établies.|
|CurrentUserSessions|Sessions utilisateur actuelles|Nombre|Moyenne|Nombre actuel de sessions utilisateur établies.|
|mashup_engine_memory_metric|Mémoire du moteur M|Octets|Moyenne|Utilisation de la mémoire par les processus de moteur mashup|
|mashup_engine_qpu_metric|QPU du moteur M|Nombre|Moyenne|Utilisation des QPU par les processus de moteur mashup|
|memory_metric|Mémoire|Octets|Moyenne|Mémoire. Plage de 0 à 25 Go pour S1, de 0 à 50 Go pour S2 et de 0 à 100 Go pour S4|
|memory_thrashing_metric|Vidage de mémoire|Pourcentage|Moyenne|Vidage de mémoire moyenne.|
|CleanerCurrentPrice|Mémoire : prix actuel du nettoyage|Nombre|Moyenne|Prix actuel de la mémoire, $/octet/temps, normalisé à 1000.|
|CleanerMemoryNonshrinkable|Mémoire : mémoire de nettoyage non réductible|Octets|Moyenne|Quantité de mémoire, en octets, qui ne doit pas être vidée par le nettoyage en arrière-plan.|
|CleanerMemoryShrinkable|Mémoire : mémoire de nettoyage réductible|Octets|Moyenne|Quantité de mémoire, en octets, qui doit être vidée par le nettoyage en arrière-plan.|
|MemoryLimitHard|Mémoire : limite inconditionnelle de la mémoire|Octets|Moyenne|Limite de mémoire physique, du fichier de configuration.|
|MemoryLimitHigh|Mémoire : limite haute de la mémoire|Octets|Moyenne|Limite de mémoire élevée, du fichier de configuration.|
|MemoryLimitLow|Mémoire : limite basse de la mémoire|Octets|Moyenne|Limite de mémoire basse, du fichier de configuration.|
|MemoryLimitVertiPaq|Mémoire : limite de la mémoire VertiPaq|Octets|Moyenne|Limite en mémoire, du fichier de configuration.|
|MemoryUsage|Mémoire : Utilisation de la mémoire|Octets|Moyenne|Utilisation de la mémoire du processus serveur telle qu’utilisée dans le calcul du coût de la mémoire de nettoyage. Équivaut au compteur Process\PrivateBytes, plus la taille des données mappées en mémoire, en ignorant la mémoire mappée ou allouée par le moteur d’analyse de mémoire (VertiPaq) dépassant la limite de mémoire du moteur.|
|Quota|Mémoire : Quota|Octets|Moyenne|Quota de mémoire actuel, en octets. Le quota de mémoire est également appelé réserve de mémoire ou d’allocation.|
|QuotaBlocked|Mémoire : quota bloqué|Nombre|Moyenne|Nombre actuel de requêtes de quota qui sont bloquées en attendant la libération d’autres quotas de mémoire.|
|VertiPaqNonpaged|Mémoire : mémoire non paginée VertiPaq|Octets|Moyenne|Octets de mémoire verrouillée dans la plage de travail pour utilisation par le moteur en mémoire.|
|VertiPaqPaged|Mémoire : mémoire paginée VertiPaq|Octets|Moyenne|Octets de mémoire paginée utilisée pour les données en mémoire.|
|ProcessingPoolJobQueueLength|Longueur de la file d’attente des travaux du pool de traitement|Nombre|Moyenne|Nombre de travaux autres que d’E/S contenus dans la file d’attente du pool de threads de traitement.|
|RowsConvertedPerSec|Traitement : lignes converties par seconde|CountPerSecond|Moyenne|Taux de lignes converties lors du traitement.|
|RowsReadPerSec|Traitement : lignes lues par seconde|CountPerSecond|Moyenne|Taux de lignes lues à partir de toutes les bases de données relationnelles.|
|RowsWrittenPerSec|Traitement : lignes écrites par seconde|CountPerSecond|Moyenne|Taux de lignes écrites lors du traitement.|
|qpu_metric|QPU|Nombre|Moyenne|QPU. Plage de 0 à 100 pour S1, de 0 à 200 pour S2 et de 0 à 400 pour S4|
|QueryPoolBusyThreads|Threads occupés du pool de threads de requêtes|Nombre|Moyenne|Nombre de threads occupés dans le pool de threads de requêtes.|
|SuccessfullConnectionsPerSec|Connexions réussies par seconde|CountPerSecond|Moyenne|Taux de connexions terminées réussies.|
|CommandPoolBusyThreads|Threads : threads occupés du pool de commandes|Nombre|Moyenne|Nombre de threads occupés dans le pool de threads de commandes.|
|CommandPoolIdleThreads|Threads : threads inactifs du pool de commandes|Nombre|Moyenne|Nombre de threads inactifs dans le pool de threads de commandes.|
|LongParsingBusyThreads|Threads : threads occupés d'analyse longue|Nombre|Moyenne|Nombre de threads occupés dans le pool de threads d’analyse longue.|
|LongParsingIdleThreads|Threads : threads inactifs d'analyse longue|Nombre|Moyenne|Nombre de threads inactifs dans le pool de threads d’analyse longue.|
|LongParsingJobQueueLength|Threads : longueur de la file d'attente des travaux d'analyse longue|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads d’analyse longue.|
|ProcessingPoolIOJobQueueLength|Threads : longueur de la file des travaux d'E/S du pool de traitement|Nombre|Moyenne|Nombre de travaux d’E/S contenus dans la file d’attente du pool de threads de traitement.|
|ProcessingPoolBusyIOJobThreads|Threads : threads des travaux d'E/S occupés du pool de traitement|Nombre|Moyenne|Nombre de threads pour les travaux d’E/S en cours d’exécution dans le pool de threads de traitement.|
|ProcessingPoolBusyNonIOThreads|Threads : threads autres que les threads d'E/S occupés du pool de traitement|Nombre|Moyenne|Nombre de threads pour les travaux autres que d’E/S en cours d’exécution dans le pool de threads de traitement.|
|ProcessingPoolIdleIOJobThreads|Threads : threads des travaux d'E/S inactifs du pool de traitement|Nombre|Moyenne|Nombre de threads inactifs pour les travaux d’E/S le pool de threads de traitement.|
|ProcessingPoolIdleNonIOThreads|Threads : threads autres que les threads d'E/S inactifs du pool de traitement|Nombre|Moyenne|Nombre de threads inactifs le pool de threads de traitement dédiés aux travaux autres qu’E/S.|
|QueryPoolIdleThreads|Threads : threads inactifs du pool de requêtes|Nombre|Moyenne|Nombre de threads inactifs pour les travaux d’E/S le pool de threads de traitement.|
|QueryPoolJobQueueLength|Threads : longueur de la file d'attente des travaux du pool de requêtes|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads de requêtes.|
|ShortParsingBusyThreads|Threads : threads occupés d'analyse courte|Nombre|Moyenne|Nombre de threads occupés dans le pool de threads d’analyse courte.|
|ShortParsingIdleThreads|Threads : threads inactifs d'analyse courte|Nombre|Moyenne|Nombre de threads inactifs dans le pool de threads d’analyse courte.|
|ShortParsingJobQueueLength|Threads : longueur de la file d'attente des travaux d'analyse courte|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads d’analyse courte.|
|TotalConnectionFailures|Nombre total d’échecs de connexion|Nombre|Moyenne|Total des échecs de tentatives de connexion.|
|TotalConnectionRequests|Nombre total de demandes de connexion|Nombre|Moyenne|Nombre total de demandes de connexion. |

## <a name="next-steps"></a>Étapes suivantes
[Surveillance dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md)   
[Indicateurs de performances dans Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Indicateurs de performances dans l’API REST Azure Monitor](/rest/api/monitor/metrics)
