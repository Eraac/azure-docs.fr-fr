---
title: Point de terminaison d’aperçu de l’URL du projet
titlesuffix: Azure Cognitive Services
description: Résumé du point de terminaison d’aperçu de l’URL.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: 43254db734a48f3e7aaa5a26a7fbf3981c9e9d87
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592868"
---
# <a name="project-url-preview-endpoint"></a>Point de terminaison d’aperçu de l’URL du projet

L’API d’aperçu de l’URL inclut un point de terminaison.

## <a name="endpoint"></a>Point de terminaison
Pour obtenir un aperçu de l’URL, envoyez une requête au point de terminaison suivant. Utilisez les en-têtes et les paramètres d’URL pour définir d’autres spécifications.

GET :
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>Paramètres de requête
|Nom|Valeur|Type|Obligatoire|  
|----------|-----------|----------|--------------|  
|q|URL servant à afficher l’aperçu|Chaîne |OUI|
|safeSearch|Le contenu pour adultes jugé non conforme, ou le contenu piraté, est bloqué avec pour code d’erreur 400 et aucun indicateur *isFamilyFriendly* n’est renvoyé. <p>Voici par contre le comportement concernant le contenu pour adultes considéré comme légal. Le système renvoie le code d’état 200 et l’indicateur *isFamilyFriendly* est défini sur false.<ul><li>safeSearch=strict : Le titre, la description, l’URL et l’image ne seront pas renvoyés.</li><li>safeSearch=moderate : vous obtenez le titre, l’URL et la description, mais pas l’image descriptive.</li><li>safeSearch=off : vous obtenez tous les objets/éléments de la réponse (titre, URL, description et image).</li></ul> |Chaîne|Non requis. </br> La valeur par défaut est safeSearch=strict.| 

## <a name="response-object"></a>Objet Réponse

La réponse inclut les en-têtes HTTP et l’objet WebPage avec des attributs, comme indiqué dans l’exemple suivant : `name`, `url`, `description`, `isFamilyFriendly` et `primaryImageOfPage`.

```
BingAPIs-TraceId: 15AFE52A97AA422F960433A94803F6CE
BingAPIs-SessionId: 40587764F42142D3A8BA99F66B2B3BB6
X-MSEdge-ClientID: 0389E3EDED106B5E1424E82FEC436A56
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 15AFE52A97AA422F960433A94803F6CE Ref B: PAOEDGE0418 Ref C: 2018-03-30T16:36:27Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile ...",
  "url": "https://swiftkey.com/",
   "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

## <a name="next-steps"></a>Étapes suivantes
- [Démarrage rapide C#](csharp.md)
- [Démarrage rapide Java](java-quickstart.md)
- [Démarrage rapide JavaScript](javascript.md)
- [Démarrage rapide Node](node-quickstart.md)
- [Démarrage rapide Python](python-quickstart.md)
