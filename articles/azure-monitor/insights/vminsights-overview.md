---
title: Qu’est-ce qu’Azure Monitor pour machines virtuelles (préversion) ? | Microsoft Docs
description: Azure Monitor pour machines virtuelles est une fonctionnalité d'Azure Monitor qui surveille à la fois l'intégrité et les performances du système d'exploitation de la machine virtuelle Azure. En outre, elle identifie automatiquement les composants des applications et leurs dépendances avec les autres ressources, et mappe leurs communications respectives. Cet article fournit une vue d’ensemble de la fonctionnalité.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2019
ms.author: magoedte
ms.openlocfilehash: 7d86b3fe9aeddd603d0c40b1c760cabdee42e396
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522104"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Qu’est-ce qu’Azure Monitor pour machines virtuelles (préversion) ?

Azure Monitor pour machines virtuelles surveille les machines virtuelles et groupes de machines virtuelles identiques à grande échelle. Il analyse les performances et l’intégrité des machines virtuelles Windows et Linux, et supervise leurs processus et dépendances vis-à-vis d’autres ressources et des processus externes. 

La solution Azure Monitor pour machines virtuelles prend en charge le monitoring des performances et des dépendances d’applications des machines virtuelles hébergées localement ou auprès d’un autre fournisseur de cloud. Trois fonctionnalités clés offrent des insights approfondis :

* **Composants logiques de machines virtuelles Azure sous Windows et Linux** : sont mesurés par rapport aux critères d’intégrité préconfigurés et vous avertissent lorsque la condition évaluée est remplie.  

* **Graphiques prédéfinis de tendances des performances** : affichent les mesures de performances principales du système d’exploitation de la machine virtuelle invitée.

* **Carte des dépendances** : affiche les composants interconnectés avec la machine virtuelle de différents groupes de ressources et abonnements.  

Ces fonctionnalités sont organisées selon trois perspectives :

* Intégrité
* Performances
* Mappage

>[!NOTE]
>À l’heure actuelle, la fonctionnalité de contrôle de l’intégrité est exclusivement proposée pour les machines virtuelles Azure. Les fonctionnalités Performances et Mappage prennent en charge les machines virtuelles Azure, les groupes identiques de machines virtuelles Azure et celles qui sont hébergées dans votre environnement ou auprès d’autres fournisseurs de cloud.

L’intégration avec les journaux d’activité Azure Monitor offre de puissantes fonctionnalités d’agrégation et de filtrage, ainsi que la possibilité d’analyser les tendances des données au fil du temps. Pris individuellement, Azure Monitor ou Service Map ne sont pas suffisants pour effectuer un monitoring aussi complet des charges de travail.  

Vous pouvez afficher directement ces données sur une seule machine virtuelle, ou bien utiliser Azure Monitor pour avoir une vue d’ensemble de vos machines virtuelles. Ce mode d’affichage dépend de la perspective des différentes fonctionnalités :

* **Intégrité** : les machines virtuelles sont associées à un groupe de ressources.
* **Mappage** et **Performances** : les machines virtuelles sont configurées pour rendre compte à un espace de travail Log Analytics spécifique.

![Perspective des insights sur les machines virtuelles sur le Portail Azure](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Azure Monitor pour machines virtuelles peut offrir des performances prévisibles et assurer la disponibilité des applications vitales. Il identifie les événements critiques du système d’exploitation, les goulots d’étranglement des performances et les problèmes réseau. Azure Monitor pour machines virtuelles peut également aider à comprendre si le problème est lié à d’autres dépendances.  

## <a name="data-usage"></a>Utilisation des données 

Lorsque Azure Monitor pour machines virtuelles est déployé, les données recueillies par les machines virtuelles sont ingérées et stockées dans Azure Monitor. Les mesures de critères d’intégrité sont stockées dans Azure Monitor dans une base de données de série chronologique. Les données de performances et de dépendances collectées sont stockées dans un espace de travail Log Analytics. Selon la tarification publiée sur la [page des tarifs d’Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/), Azure Monitor pour machines virtuelles engendre des frais pour :

* les données ingérées et stockées ;
* le nombre de séries chronologiques monitorées pour les critères d’intégrité ;
* les règles d’alerte créées ;
* les notifications envoyées. 

La taille du journal, qui varie en fonction de la longueur des chaînes des compteurs de performances, peut augmenter avec le nombre de disques logiques et de cartes réseau alloué à la machine virtuelle. Si vous disposez déjà d’un espace de travail et que vous collectez ces compteurs, ces frais ne seront pas facturés une deuxième fois. Si vous utilisez déjà Service Map, la seule différence concerne les données de connexion supplémentaires envoyées à Azure Monitor.

## <a name="next-steps"></a>Étapes suivantes
Pour comprendre les exigences et les méthodes de monitoring des machines virtuelles, voir [Déployer Azure Monitor pour machines virtuelles](vminsights-enable-overview.md).
