---
title: Limites, quotas et seuils dans Azure Scheduler
description: Découvrez les limites, quotas, valeurs par défaut et seuils de limitation applicables dans Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 478afb20f3dabec74d66d00bec325408ce6604fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64713742"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Limites, quotas et seuils de limitation dans Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) remplace Azure Scheduler, qui est actuellement mis hors service. Pour planifier des travaux, [utilisez plutôt Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="limits-quotas-and-thresholds"></a>Limites, quotas et seuils

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>L’en-tête x-ms-request-id

Chaque requête adressée au service Scheduler retourne un en-tête de réponse nommé **x-ms-request-id**. Cet en-tête contient une valeur opaque qui identifie de façon unique la requête. Par conséquent, si une requête échoue constamment et que vous avez vérifié qu’elle est formulée correctement, vous pouvez signaler l’erreur à Microsoft en indiquant la valeur de l’en-tête de réponse **x-ms-request-id** ainsi que les informations suivantes : 

* La valeur de l’en-tête **x-ms-request-id**
* L’heure approximative de la requête effectuée 
* Les identificateurs de l’abonnement Azure, de la collection de travaux et du travail 
* Le type d’opération tentée par la requête

## <a name="see-also"></a>Voir aussi

* [Présentation d’Azure Scheduler](scheduler-intro.md)
* [Concepts, terminologie et hiérarchie d’entités d’Azure Scheduler](scheduler-concepts-terms.md)
