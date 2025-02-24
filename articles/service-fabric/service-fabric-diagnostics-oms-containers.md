---
title: Superviser les conteneurs dans Azure Service Fabric avec les journaux Azure Monitor | Microsoft Docs
description: Utilisez les journaux Azure Monitor pour superviser les conteneurs qui sont exécutés dans les clusters Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: d03d68560502821b9c343be983d9f7b5a83ed977
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60321894"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Superviser les conteneurs avec les journaux d’activité Azure Monitor
 
Cet article traite des étapes nécessaires pour configurer la solution de supervision de conteneurs Journaux Azure Monitor, qui permet de visualiser les événements de conteneur. Pour configurer votre cluster afin de collecter les événements de conteneur, consultez ce [tutoriel pas à pas](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Configurer la solution de supervision des conteneurs

> [!NOTE]
> Vous devez avoir configuré les journaux Azure Monitor pour votre cluster et déployé l’agent Log Analytics sur vos nœuds. Si ce n’est pas le cas, commencez par suivre les procédures des articles indiquant comment [configurer les journaux Azure Monitor](service-fabric-diagnostics-oms-setup.md) et [ajouter l’agent Log Analytics à un cluster](service-fabric-diagnostics-oms-agent.md).

1. Une fois votre cluster configuré avec les journaux Azure Monitor et l’agent Log Analytics, déployez vos conteneurs. Attendez que vos conteneurs soient déployés avant de passer à l’étape suivante.

2. Dans la Place de marché Azure, recherchez *Solution de supervision de conteneurs*, puis cliquez sur la ressource **Solution de supervision de conteneurs** qui doit s’afficher sous la catégorie Supervision + Gestion.

    ![Ajout de la solution Conteneurs](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Créez la solution dans l’espace de travail qui a été créé pour le cluster. Après cette modification, l’agent se met à collecter des données Docker sur les conteneurs. Au bout de 15 minutes environ, la solution doit s’afficher avec les statistiques et journaux d’activité entrants, comme illustré ci-après.

    ![Tableau de bord Log Analytics de base](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

L’agent permet de collecter plusieurs journaux d’activité propres aux conteneurs qui peuvent être interrogés dans les journaux Azure Monitor ou utilisés pour visualiser les indicateurs de performance. Les types de journaux collectés sont :

* ContainerInventory : affiche des informations sur les images, le nom et l’emplacement du conteneur
* ContainerImageInventory : informations sur les images déployées, ID ou tailles compris
* ContainerLog : journaux d’activité d’erreurs spécifiques, journaux d’activité de docker (stdout, etc.) et autres entrées
* ContainerServiceLog : les commandes de démon docker qui ont été exécutées
* Performances : les compteurs de performances, dont l’utilisation par le conteneur du processeur, de la mémoire, du E/S de disque, du trafic réseau et de mesures personnalisées à partir des machines hôtes



## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur [la solution de conteneurs Journaux Azure Monitor](../azure-monitor/insights/containers.md).
* En savoir plus sur l’orchestration des conteneurs dans Service Fabric : [Service Fabric et conteneurs](service-fabric-containers-overview.md)
* Familiarisez-vous avec les fonctionnalités de [requêtes et recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) proposées par les journaux Azure Monitor
* Configurez les journaux Azure Monitor pour paramétrer des règles d’[alerte automatisée](../log-analytics/log-analytics-alerts.md) afin de faciliter la détection et les diagnostics