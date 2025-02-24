---
title: Envoi de demandes à l’API Vérification orthographique Bing
titleSuffix: Azure Cognitive Services
description: Découvrez les modes de vérification orthographique Bing, les paramètres et d’autres informations relatives à l’API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: ef5d812b1e55713f30928582d9aa9e61bba7c7a6
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423542"
---
# <a name="sending-requests-to-the-bing-spell-check-api"></a>Envoi de demandes à l’API Vérification orthographique Bing

Pour vérifier l’orthographe et la grammaire d’une chaîne de texte, vous envoyez une requête GET au point de terminaison suivant :  

```
https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
```  
  
La requête doit utiliser le protocole HTTPS.

Nous vous recommandons de générer toutes les requêtes à partir d’un serveur. Si vous diffusez la clé dans le cadre d’une application client, vous prenez le risque qu’un tiers malveillant puisse y accéder. Un serveur fournit également un unique point de mise à niveau pour les prochaines versions de l’API.

La requête doit indiquer le paramètre de requête [texte](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#text) qui contient la chaîne à vérifier. La requête peut également indiquer le paramètre de requête [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#mkt) facultatif, qui vous permet de choisir le marché d’où proviennent les résultats. Pour obtenir la liste des paramètres de requête facultatifs tels que `mode` et [, consultez la page ](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#query-parameters)Query Parameters (Paramètres de requête). Toutes les valeurs de paramètres de requête doivent être codées au format URL.  
  
La requête doit indiquer l’en-tête [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#subscriptionkey). Nous vous conseillons également d’indiquer les en-têtes suivants (qui sont facultatifs). Ils aident l’API Vérification orthographique Bing à renvoyer des résultats plus précis :  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#location)  

Pour obtenir la liste complète des en-têtes de requête et de réponse, consultez la page [En-têtes](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#headers).

Quand vous appelez l’API Vérification orthographique Bing en JavaScript, les fonctionnalités de sécurité intégrées de votre navigateur peuvent dans certains cas vous empêcher d’accéder aux valeurs de ces en-têtes.

Pour résoudre ce problème, vous pouvez envoyer la requête à l’API Vérification orthographique Bing par le biais d’un proxy CORS. La réponse émanant d’un proxy de ce type a un en-tête `Access-Control-Expose-Headers` qui met les en-têtes de réponse sur liste verte et les rend accessibles à du code JavaScript.

Il est facile d’installer un proxy CORS pour autoriser [l’application du tutoriel](../tutorials/spellcheck.md) à accéder aux en-têtes clients facultatifs. Tout d’abord, si ce n’est déjà fait, [installez Node.js](https://nodejs.org/en/download/). Entrez alors la commande suivante dans l’invite de commandes.

    npm install -g cors-proxy-server

Ensuite, remplacez le point de terminaison de l’API Vérification orthographique Bing dans le fichier HTML par :

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

Enfin, démarrez le proxy CORS avec la commande suivante :

    cors-proxy-server

Laissez la fenêtre de commande ouverte pendant que vous utilisez l’application du didacticiel ; la fermeture de la fenêtre entraîne l’arrêt du proxy. Dans la section des en-têtes HTTP (qui peut être développée) sous les résultats de la recherche, vous pouvez maintenant voir l’en-tête `X-MSEdge-ClientID` (entre autres) et vérifier qu’il est identique pour toutes les requêtes.

## <a name="example-api-request"></a>Exemple de demande API

Vous trouverez ci-dessous une requête qui inclut tous les paramètres de requête et les en-têtes suggérés. Si vous appelez l’une des API Bing pour la première fois, n’incluez pas l’en-tête ID client. Indiquez uniquement l’ID client si vous avez précédemment appelé une API Bing et si Bing a renvoyé un ID client pour la combinaison utilisateur/appareil. 
  
> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

Voici la réponse à la requête précédente. L’exemple montre également les en-têtes de réponse propres à Bing.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  

## <a name="next-steps"></a>Étapes suivantes

- [Qu’est-ce que l’API Vérification orthographique Bing ?](../overview.md)
- [Informations de référence sur l’API Vérification orthographique Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
