---
title: Vue d’ensemble des Insights dans Azure Monitor | Microsoft Docs
description: Les Insights fournissent une expérience de surveillance personnalisée dans Azure Monitor pour des applications et services spécifiques. Cet article fournit une brève description de chacune des informations actuellement disponibles.
services: azure-monitor
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: bwren
ms.openlocfilehash: 8cb39a174c570b7019e872d731f49252a9505406
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66247228"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Vue d’ensemble des Insights dans Azure Monitor
Les Insights offrent une expérience de surveillance personnalisée pour des applications et services spécifiques. Ils stockent des données sur la [plateforme de données Azure Monitor](../platform/data-platform.md) et exploitent d’autres fonctionnalités d’analyse et d’alerte d’Azure Monitor, mais peuvent également collecter des données supplémentaires et offrir une expérience utilisateur unique sur le portail Azure. Vous pouvez accéder aux informations à partir de la section **Insights** du menu du Azure Monitor du portail Azure.

Les sections suivantes fournissent une brève description des informations actuellement disponibles dans Azure Monitor. Pour plus d’informations à ce sujet, voir la documentation détaillée.

## <a name="application-insights"></a>Application Insights
Application Insights est un service extensible de gestion des performances des applications (APM) destiné aux développeurs web sur de multiples plateformes. Utilisez-le pour analyser votre application web en direct. Il fonctionne avec des applications sur un vaste éventail de plateformes, notamment .NET, Node.js et Java EE, qu’elles soient hébergées localement, de manière hybride ou dans n’importe quel cloud public. Il s’intègre aussi avec votre processus DevOps et offre des points de connexion à un vaste éventail d’outils de développement.

Voir [Présentation d’Application Insights](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor pour des conteneurs
Azure Monitor pour les conteneurs supervise les performances des charges de travail de conteneur déployées sur Azure Container Instances ou sur des clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS). La surveillance de vos conteneurs est cruciale, particulièrement lorsque vous exécutez un cluster de production à grande échelle, avec plusieurs applications.

Voir [Vue d’ensemble d’Azure Monitor pour conteneurs](../insights/container-insights-overview.md).

![Azure Monitor pour conteneurs](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure Monitor pour groupes de ressources (préversion)
Azure Monitor pour groupes de ressources facilite la classification triage et le diagnostic des problèmes que rencontrent vos ressources, tout en offrant un contexte en lien avec l’intégrité et les performances du groupe de ressources dans son ensemble.

Voir [Surveiller les groupes de ressources avec Azure Monitor (préversion)](../insights/resource-group-insights.md).

![Azure Monitor pour groupes de ressources](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor pour machines virtuelles (préversion)
Azure Monitor pour machines virtuelles surveille les machines virtuelles et groupes de machines virtuelles identiques à grande échelle. Il analyse les performances et l’intégrité des machines virtuelles Windows et Linux, et supervise leurs processus et dépendances vis-à-vis d’autres ressources et des processus externes.

Voir [Qu’est-ce qu’Azure Monitor pour machines virtuelles ?](vminsights-overview.md)

![Azure Monitor pour machines virtuelles](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>Étapes suivantes
* Apprenez-en davantage sur la [plateforme de données Azure Monitor](../platform/data-platform.md).
* Découvrez les différentes [sources de données utilisées par Azure Monitor](../platform/data-sources.md) et les différentes sortes de données collectées par chaque insight.
