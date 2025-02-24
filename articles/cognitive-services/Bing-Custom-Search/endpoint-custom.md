---
title: Point de terminaison Recherche personnalisée Bing
titleSuffix: Azure Cognitive Services
description: Synthèse du point de terminaison de l’API Recherche personnalisée Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: e3b544d8d655b653383cc999de233e2163433d85
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405076"
---
# <a name="custom-search"></a>Recherche personnalisée
La Recherche personnalisée Bing vous permet de créer des expériences de recherche adaptées pour les sujets qui vous intéressent. Vos utilisateurs consultent des résultats de recherche adaptés au contenu qui les intéresse au lieu de parcourir des résultats de recherche dont le contenu n’est pas approprié.

## <a name="custom-search-endpoint"></a>Point de terminaison Recherche personnalisée
Pour obtenir des résultats à l’aide de l’API Recherche personnalisée Bing, envoyez une requête `GET` au point de terminaison suivant. Utilisez les en-têtes et les paramètres d’URL pour définir d’autres spécifications.

Point de terminaison : Renvoie les suggestions de recherche en tant que résultats JSON qui correspondent à l’entrée utilisateur définie par `?q=""`.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Pour obtenir des exemples qui décrivent comment configurer des sources Recherche personnalisée, consultez le [didacticiel](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). Pour plus d’informations sur les en-têtes, les paramètres, les codes de marché, les objets de réponse, les erreurs, etc., consultez [Custom Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference) (Référence sur l’API Recherche personnalisée v7).

## <a name="custom-search-response-json"></a>Réponse Recherche personnalisée JSON
Une requête de recherche personnalisée renvoie des résultats sous forme d’objets JSON ; consultez [Response objects](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects) (Objets de réponse). 

## <a name="custom-autosuggest"></a>Suggestion automatique personnalisée
L’API Suggestion automatique personnalisée vous permet d’envoyer un terme de requête de recherche partielle à Bing pour obtenir une liste de requêtes suggérées que vous pouvez configurer. Avec Suggestion automatique personnalisée, vous ajoutez des suggestions renvoyées par l’API et spécifiez éventuellement si vous souhaitez inclure les suggestions générées par Bing.

## <a name="custom-autosuggest-endpoint"></a>Point de terminaison Suggestion automatique personnalisée
Pour demander des suggestions de requête personnalisée, envoyez une requête GET à :

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Pour plus d’informations sur la définition des suggestions personnalisées, consultez [Configure your custom autosuggest experience](define-custom-suggestions.md) (Configurer votre expérience de suggestion automatique personnalisée).

## <a name="custom-image-search"></a>Recherche d’images personnalisée
L’API Recherche d’images personnalisée vous permet d’envoyer une requête de recherche à Bing pour obtenir une liste d’images appropriées à partir de votre instance Recherche personnalisée.

## <a name="custom-image-search-endpoint"></a>Point de terminaison Recherche d’images personnalisée
Pour demander des images à partir de votre instance Recherche personnalisée, envoyez une requête GET à l’URL suivante :

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Pour plus d’informations sur la configuration d’une instance Recherche personnalisée, consultez [Configure your custom search experience](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view) (Configurer votre expérience de recherche personnalisée).

## <a name="next-steps"></a>Étapes suivantes
Les API **Bing** prennent en charge les actions de recherche qui renvoient des résultats en fonction de leur type. Tous les points de terminaison de recherche renvoient des résultats en tant qu’objets de réponse JSON.  Tous les points de terminaison prennent en charge les requêtes qui renvoient une langue et/ou un emplacement spécifique par longitude, latitude et rayon de recherche.

Pour plus d’informations sur les paramètres pris en charge par chaque point de terminaison, consultez les pages de référence pour chaque type.
Pour obtenir des exemples de requêtes de base à l’aide de l’API Recherche personnalisée, consultez [Bing Custom Search Documentation](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) (Documentation sur Recherche personnalisée Bing)
