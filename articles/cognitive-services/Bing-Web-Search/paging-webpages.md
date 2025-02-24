---
title: Navigation dans les résultats de la recherche - API Recherche Web Bing
titleSuffix: Azure Cognitive Services
description: Découvrez comment naviguer dans les résultats de la recherche à partir de l’API Recherche Web Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: a038dc2706c7cb128751630f8997851409886290
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384809"
---
# <a name="how-to-page-through-results-from-the-bing-web-search-api"></a>Découvrez comment naviguer dans les résultats de la recherche à partir de l’API Recherche Web Bing.

Lorsque vous appelez l’API Recherche Web Bing, Bing retourne une liste de résultats. La liste est un sous-ensemble du nombre total de résultats susceptibles d’être en rapport avec la requête. Pour obtenir une estimation du nombre total de résultats disponibles, accédez au champ [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) de l’objet de la réponse.  

L’exemple suivant illustre le champ `totalEstimatedMatches` qui est inclus dans une réponse web.  

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

Pour paginer les pages web disponibles, utilisez les paramètres de requête [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#count) et [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#offset).  

Le paramètre `count` spécifie le nombre de résultats à renvoyer dans la réponse. Vous pouvez demander jusqu’à 50 résultats dans la réponse. La valeur par défaut est de 10. Le nombre réel renvoyé peut être inférieur à ce que vous avez demandé.

Le paramètre `offset` spécifie le nombre de résultats à ignorer. Le paramètre `offset` est basé sur zéro et doit être inférieur à (`totalEstimatedMatches` - `count`).  

Si vous souhaitez afficher 15 pages web par page, vous devez définir `count` sur 15 et `offset` sur 0 pour obtenir la première page de résultats. Pour chaque page suivante, vous devez incrémenter le paramètre `offset` de 15 (par exemple, 15, 30).  

L’exemple suivant illustre une requête de 15 pages Web avec un paramètre offset de défini sur 45.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Si la valeur par défaut `count` est adaptée à votre implémentation, vous devez uniquement spécifier le paramètre de requête `offset`.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

L’API Recherche Web renvoie des résultats incluant des pages Web et éventuellement des actualités, des vidéos et des images. Lorsque vous paginez les résultats de recherche, vous paginez la réponse [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer), mais pas les autres réponses (par exemple, les images ou les actualités). Par exemple, si vous définissez `count` sur 50, vous obtenez 50 résultats qui correspondent à des pages Web, mais il se peut que la réponse comprenne également des résultats pour les autres réponses. Par exemple, la réponse peut inclure 15 images et 4 articles de presse. Il se peut également que les résultats comprennent des actualités sur la première page, mais pas sur la deuxième et vice-versa.   

Si vous spécifiez le paramètre de requête `responseFilter`, mais que vous n’incluez pas les pages Web dans la liste des filtres, n’utilisez pas les paramètres `count` et `offset`. 

> [!NOTE]
> Le champ `TotalEstimatedAnswers` est une estimation du nombre total de résultats de recherche que vous pouvez obtenir pour la requête actuelle.  Quand vous définissez les paramètres `count` et `offset`, le nombre `TotalEstimatedAnswers` peut changer. 

## <a name="next-steps"></a>Étapes suivantes

* [Quel est l’API de recherche Web Bing](overview.md)?
