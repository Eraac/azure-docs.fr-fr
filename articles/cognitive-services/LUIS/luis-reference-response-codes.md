---
title: Codes de réponse HTTP d’API
titleSuffix: Azure
description: Comprendre les codes de réponse HTTP retournés par les API de point de terminaison et de création LUIS
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: f6742bf64ce26e6cce93dfcdfd06756f3c340d9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522968"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Codes de réponse d’API courants et leur signification

Les API de [création](https://go.microsoft.com/fwlink/?linkid=2092087) et de [point de terminaison](https://go.microsoft.com/fwlink/?linkid=2092356) retournent des codes de réponse HTTP. Bien que les messages de réponse contiennent des informations propres à une requête, le code d’état de réponse HTTP est général. 

## <a name="common-status-codes"></a>Codes d’état courants
Le tableau suivant liste certains des codes d’état de réponse HTTP les plus courants pour les API de [création](https://go.microsoft.com/fwlink/?linkid=2092087) et de [point de terminaison](https://go.microsoft.com/fwlink/?linkid=2092356) :

|Code|API|Explication|
|:--|--|--|
|400|Création, Point de terminaison|Les paramètres de la requête sont incorrects, ce qui signifie que les paramètres obligatoires sont manquants, incorrects ou trop grands|
|400|Création, Point de terminaison|Le corps de la requête est incorrect, ce qui signifie que l’objet JSON est manquant, mal formé ou trop grand|
|401|Création|Vous avez utilisé une clé d’abonnement de point de terminaison au lieu d’une clé de création|
|401|Création, Point de terminaison|La clé est vide, mal formée ou non valide|
|401|Création, Point de terminaison| La clé ne correspond pas à la région|
|401|Création|Vous n’êtes pas propriétaire ou collaborateur|
|401|Création|L’ordre des appels d’API n’est pas valide|
|403|Création, Point de terminaison|La limite de quota mensuel de clés est dépassée|
|409|Point de terminaison|L’application est encore en cours de chargement|
|410|Point de terminaison|L’application doit être ré-entraînée et republiée|
|414|Point de terminaison|La requête dépasse la limite maximale du nombre de caractères|
|429|Création, Point de terminaison|Le débit maximal est dépassé (requêtes/seconde)|

## <a name="next-steps"></a>Étapes suivantes

* Documentation sur la [création](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) et le [point de terminaison](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) d’API REST
