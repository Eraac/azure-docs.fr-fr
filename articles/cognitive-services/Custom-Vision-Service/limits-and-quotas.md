---
title: Limites et quotas – Service Vision personnalisée
titlesuffix: Azure Cognitive Services
description: Découvrez les limites et quotas applicables au service Vision personnalisée.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 9cff5fdac39be2338305cd37a4b2328a28a48255
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269262"
---
# <a name="limits-and-quotas"></a>Limites et quotas

Il existe deux niveaux de clés pour le service Vision personnalisée. Vous pouvez souscrire à un abonnement F0 (gratuit) ou S0 (standard) via le portail Azure. Consultez la page [Tarification de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) correspondante pour en savoir plus sur la tarification et les transactions.

Le nombre d’images d’apprentissage par projet et le nombre de balises par projet sont censés augmenter au fil du temps pour les projets S0.

||**F0**|**S0**|
|-----|-----|-----|
|Projets|2|100|
|Images d’apprentissage par projet |5 000|100 000|
|Prédictions/mois|10 000 |Illimité|
|Balises/projet|50|500|
|Itérations |10|10|
|Nombre minimal d’images étiquetées par balise, classification (recommandation : plus de 50) |5\.|5\.|
|Nombre minimal d’images étiquetées par balise, détection d’objets (recommandation : plus de 50)|15|15|
|Durée de stockage des images de prédiction|30 jours|30 jours|
|Opérations de [prédiction](https://go.microsoft.com/fwlink/?linkid=865445) avec stockage (transactions par seconde)|2|10|
|Opérations de [prédiction](https://go.microsoft.com/fwlink/?linkid=865445) sans stockage (transactions par seconde)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (appels d’API par seconde)|2|10|
|[Autres appels d’API](https://go.microsoft.com/fwlink/?linkid=865446) (transactions par seconde)|10|10|
|Taille maximale de l’image (chargement de l’image d’apprentissage) |6 Mo|6 Mo|
|Taille maximale de l’image (prédiction)|4 Mo|4 Mo|
|Nombre maximal de régions par image d’entraînement de détection d’objets|200|200|
|Nombre maximal de balises par image de classification|30|30|
