---
title: Alignement des mots - API de traduction de texte Translator Text
titlesuffix: Azure Cognitive Services
description: Recevez des informations sur l’alignement des mots à partir de l’API de traduction de texte Translator Text.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 3db5e9651e307211e9dccee20bb8d69586bb9ef1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448163"
---
# <a name="how-to-receive-word-alignment-information"></a>Guide pratique pour recevoir des informations concernant l’alignement des mots

## <a name="receiving-word-alignment-information"></a>Réception d’informations concernant l’alignement des mots
Pour recevoir des informations d’alignement, utilisez la méthode de traduction Translate et incluez le paramètre facultatif includeAlignment.

## <a name="alignment-information-format"></a>Format des informations d’alignement
L’alignement est retourné en tant que valeur de chaîne au format suivant pour chaque mot de la source. Les informations pour chaque mot sont séparées par un espace, y compris pour les langues non séparées par des espaces (scripts) comme le chinois :

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Exemple de chaîne d’alignement : « 0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21 ».

En d’autres termes, le signe deux-points sépare les index de début et de fin, le signe tiret sépare les langues et un espace sépare les mots. Un mot peut s’aligner avec zéro, un ou plusieurs mots dans l’autre langue, et les mots alignés peuvent ne pas être contigus. Quand aucune information d’alignement n’est disponible, l’élément Alignment est vide. La méthode ne retourne aucune erreur dans ce cas.

## <a name="restrictions"></a>Restrictions
L’alignement est retourné uniquement pour un sous-ensemble des paires de langues à ce stade :
* de l’anglais vers toute autre langue ;
* de toute langue vers l’anglais, à l’exception du chinois simplifié, du chinois traditionnel et du letton vers l’anglais ;
* du japonais vers le coréen ou du coréen vers le japonais. Vous ne recevrez pas les informations d’alignement si la phrase est une traduction définie. Des traductions définies sont, par exemple, « Ceci est un test », « Je t’aime » et d’autres phrases extrêmement fréquentes.

## <a name="example"></a>Exemples

Exemple JSON

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
