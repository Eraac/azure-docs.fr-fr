---
title: 'Modèle de données de télémétrie d’Azure Application Insights : télémétrie des événements | Microsoft Docs'
description: Modèle de données Application Insights pour la télémétrie des événements
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 8e519e5a5e5fe6b893fa5663e1426630f46f885f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60900848"
---
# <a name="event-telemetry-application-insights-data-model"></a>Télémétrie des événements : Modèle de données Application Insights

Vous pouvez créer des éléments de télémétrie (dans [Application Insights](../../azure-monitor/app/app-insights-overview.md)) pour représenter un événement qui s’est produit dans votre application. En général, il s’agit d’une interaction utilisateur comme un clic sur un bouton ou une validation de commande. Il peut aussi s’agir d’un événement lié au cycle de vie de l’application, comme une initialisation ou une mise à jour de configuration. 

Sur le plan sémantique, les événements ne sont pas forcément corrélés aux requêtes. Toutefois, si la télémétrie des événements est utilisée correctement, elle est plus importante que les requêtes ou les traces. Les événements représentent la télémétrie métier et doivent faire l’objet d’un [échantillonnage](../../azure-monitor/app/api-filtering-sampling.md) distinct, moins agressif.

## <a name="name"></a>Nom

Nom de l’événement. Pour permettre le regroupement approprié et des mesures utiles, limitez votre application afin qu’elle génère un petit nombre de noms d’événements distincts. Par exemple, n’utilisez pas un nom distinct pour chaque instance générée d’un événement.

Longueur max : 512 caractères

## <a name="custom-properties"></a>Propriétés personnalisées

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Mesures personnalisées

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les types et les modèles de données Application Insights, consultez [Modèle de données](data-model.md).
- [Écrire des données de télémétrie d’événement personnalisées](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Découvrez quelles [plateformes](../../azure-monitor/app/platforms.md) sont prises en charge par Application Insights.
