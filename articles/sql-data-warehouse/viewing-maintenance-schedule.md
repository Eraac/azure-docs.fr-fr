---
title: Planifications de maintenance Azure (préversion) | Microsoft Docs
description: Les planifications de maintenance permettent aux clients de planifier les événements de maintenance nécessaires prévus, que le service Azure SQL Data Warehouse utilise pour déployer de nouveaux correctifs, mises à niveau et fonctionnalités.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/27/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 61537a4cb8ca1e6e98f2fa04959861eb03480fe9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61474865"
---
# <a name="view-a-maintenance-schedule"></a>Afficher une planification de maintenance 

## <a name="portal"></a>Portail

Par défaut, toutes les nouvelles instances Azure SQL Data Warehouse ont deux fenêtres de maintenance (une principale et une secondaire) de huit heures, appliquées durant le déploiement. Vous pouvez modifier les fenêtres dès que le déploiement est terminé. Aucune maintenance ne peut avoir lieu en dehors des fenêtres de maintenance définies sans notification préalable.

Pour afficher la planification de maintenance qui a été appliquée à votre entrepôt de données, procédez comme suit :

1.  Connectez-vous au [Portail Azure](https://portal.azure.com/).
2.  Sélectionnez l’entrepôt de données à afficher. 
3.  L’entrepôt de données sélectionné s’ouvre dans le panneau de vue d’ensemble. La planification de maintenance appliquée à l’entrepôt de données s’affiche sous **Planification de la maintenance (préversion)** .

![Panneau Vue d’ensemble](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sur la création, l’affichage et la gestion des alertes à l’aide d’Azure Monitor.
- [En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sur les actions webhook pour les règles d’alerte de journal.
- [En savoir plus](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) sur la création et la gestion de groupes d’actions.
- [En savoir plus](https://docs.microsoft.com/azure/service-health/service-health-overview) sur Azure Service Health.


