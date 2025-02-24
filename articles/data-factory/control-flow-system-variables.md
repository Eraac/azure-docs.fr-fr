---
title: Variables système dans Azure Data Factory | Microsoft Docs
description: Cet article décrit les variables système prises en charge par Azure Data Factory. Vous pouvez utiliser ces variables dans des expressions lors de la définition des entités Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 93a83545699e3536eb0045d538225d01cd1a96a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65235640"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Variables système prises en charge par Azure Data Factory
Cet article décrit les variables système prises en charge par Azure Data Factory. Vous pouvez utiliser ces variables dans des expressions lors de la définition des entités Data Factory.

## <a name="pipeline-scope"></a>Étendue du pipeline
Ces variables système peuvent être référencées n’importe où dans le code JSON de pipeline.

| Nom de la variable | Description |
| --- | --- |
| @pipeline().DataFactory |Nom de la fabrique de données dans laquelle le pipeline s’exécute |
| @pipeline().Pipeline |Nom du pipeline |
| @pipeline().RunId | ID de l’exécution du pipeline spécifique |
| @pipeline().TriggerType | Type de déclencheur qui a appelé le pipeline (manuel, planificateur) |
| @pipeline().TriggerId| ID du déclencheur qui appelle le pipeline |
| @pipeline().TriggerName| Nom du déclencheur qui appelle le pipeline |
| @pipeline().TriggerTime| Heure à laquelle le déclencheur a appelé le pipeline. L’heure de déclenchement correspond à l’heure de déclenchement réelle, et non à l’heure planifiée. Par exemple, `13:20:08.0149599Z` est retourné au lieu de `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Étendue de déclencheur de planification
Ces variables système peuvent être référencées n’importe où dans le déclencheur JSON si le déclencheur est de type : « ScheduleTrigger ».

| Nom de la variable | Description |
| --- | --- |
| @trigger().scheduledTime |Heure à laquelle le déclencheur a été planifié pour appeler l’exécution du pipeline. Par exemple, pour un déclencheur qui se déclenche toutes les 5 min, cette variable retournerait `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z`, respectivement.|
| @trigger().startTime |Heure à laquelle le déclencheur a été **effectivement** déclenché pour appeler l’exécution du pipeline. Par exemple, pour un déclencheur qui se déclenche toutes les 5 min, cette variable pourrait retourner des valeurs similaires à `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z`, respectivement. (Remarque : l’horodatage est par défaut au format ISO 8601.)|

## <a name="tumbling-window-trigger-scope"></a>Étendue de déclencheur de fenêtre bascule
Ces variables système peuvent être référencées n’importe où dans le déclencheur JSON si le déclencheur est de type : « TumblingWindowTrigger ».
(Remarque : l’horodatage est par défaut au format ISO 8601.)

| Nom de la variable | Description |
| --- | --- |
| @trigger().outputs.windowStartTime |Début de la fenêtre pendant laquelle le déclencheur a été planifié pour appeler l’exécution du pipeline. Si le déclencheur de la fenêtre bascule a une fréquence « horaire », cela correspond au début de l’heure.|
| @trigger().outputs.windowEndTime |Fin de la fenêtre pendant laquelle le déclencheur a été planifié pour appeler l’exécution du pipeline. Si le déclencheur de la fenêtre bascule a une fréquence « horaire », cela correspond à la fin de l’heure.|
## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’utilisation de ces variables dans les expressions, consultez [Langage et fonctions des expressions](control-flow-expression-language-functions.md).
