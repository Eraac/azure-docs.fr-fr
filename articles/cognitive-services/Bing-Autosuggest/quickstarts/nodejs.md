---
title: 'Démarrage rapide : Suggérer des requêtes de recherche avec l’API REST Suggestion automatique Bing et Node.js'
titleSuffix: Azure Cognitive Services
description: Découvrez comment démarrer rapidement en suggérant des termes de recherche dans en temps réel avec l’API Suggestion automatique Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 47c91b1e089c491eb94cd37c0ee9710deaa0e59a
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405309"
---
# <a name="quickstart-suggest-search-queries-with-the-bing-autosuggest-rest-api-and-nodejs"></a>Démarrage rapide : Suggérer des requêtes de recherche avec l’API REST Suggestion automatique Bing et Node.js

Utilisez ce démarrage rapide pour commencer des appels à l’API Suggestion automatique Bing et obtenir la réponse JSON. Cette application Node.js simple envoie une requête de recherche partielle à l’API et renvoie des suggestions pour les recherches. Alors que cette application est écrite en JavaScript, l’API est un service web RESTful compatible avec la plupart des langages de programmation. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingAutosuggestv7.js)

## <a name="prerequisites"></a>Prérequis

* [Node.js 6](https://nodejs.org/en/download/) ou version ultérieure

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-a-new-application"></a>Créer une application

1. Créez un fichier JavaScript dans votre éditeur ou IDE favori, puis définissez la sévérité et les exigences HTTPS.
    
    ```javascript
    'use strict';
    
    let https = require ('https');
    ```

2. Créez des variables pour l’hôte et le chemin d’accès du point de terminaison d’API, votre clé d’abonnement, le [code marché](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#market-codes) et un terme de recherche.

    ```javascript
    // Replace the subscriptionKey string value with your valid subscription key.
    let subscriptionKey = 'enter key here';
    
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/Suggestions';
    
    let mkt = 'en-US';
    let query = 'sail';
    ```

## <a name="construct-the-search-request-and-query"></a>Construisez la requête de recherche et la demande.

1. Créez une chaîne de paramètres pour votre requête en ajoutant votre code marché au paramètre `mkt=` et en ajoutant votre requête au paramètre `q=`.

    ```javascript 
    let params = '?mkt=' + mkt + '&q=' + query;
    ```

2. Créez une fonction nommée `get_suggestions()`. Utilisez les variables des dernières étapes pour mettre en forme une URL de recherche pour la requête d’API. Notez que le terme de recherche doit être encodé sous forme d’URL avant d’être envoyé à l’API.

    ```javascript
    let get_suggestions = function () {
      let request_params = {
        method : 'GET',
        hostname : host,
        path : path + params,
        headers : {
          'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
      };
    //...
    }
    ```

    1. Dans la même fonction, utilisez la bibliothèque de requêtes pour envoyer votre requête à l’API. `response_handler` sera défini dans la section suivante.
    
        ```javascript
        //...
        let req = https.request(request_params, response_handler);
        req.end();
        ```

## <a name="create-a-search-handler"></a>Créer un gestionnaire de recherche

1. Définissez une fonction nommée `response_handler` qui prend un appel HTTP, `response`, comme paramètre. Au sein de cette fonction, effectuez les étapes suivantes :
    
    1. Définissez une variable pour contenir le corps de la réponse JSON.  

        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Stockez le corps de la réponse quand l’indicateur **data** est appelé.
        
        ```javascript
        response.on ('data', function (d) {
        body += d;
        });
        ```

    3. Quand un indicateur **fin** est signalé, l’utilisateur `JSON.parse()` et `JSON.stringify()` pour imprimer la réponse.
    
        ```javascript
        response.on ('end', function () {
        let body_ = JSON.parse (body);
        let body__ = JSON.stringify (body_, null, '  ');
            console.log (body__);
        });
        response.on ('error', function (e) {
            console.log ('Error: ' + e.message);
        });
        ```

2. Appelez `get_suggestions()` pour envoyer la requête à l’API Suggestion automatique Bing.

## <a name="example-json-response"></a>Exemple de réponse JSON

Une réponse correcte est retournée au format JSON, comme dans l’exemple suivant : 

```json
{
  "_type": "Suggestions",
  "queryContext": {
    "originalQuery": "sail"
  },
  "suggestionGroups": [
    {
      "name": "Web",
      "searchSuggestions": [
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dgvtP9TS9NwhajSapY2Se6y1eCbP2fq_GiP2n-cxi6OY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailrite%26FORM%3dUSBAPI\u0026p\u003dDevEx,5003.1",
          "displayText": "sailrite",
          "query": "sailrite",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dBTS0G6AakxntIl9rmbDXtk1n6rQpsZZ99aQ7ClE7dTY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsail%2bsand%2bpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5004.1",
          "displayText": "sail sand point",
          "query": "sail sand point",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dc0QOA_j6swCZJy9FxqOwke2KslJE7ZRmMooGClAuCpY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboats%2bfor%2bsale%26FORM%3dUSBAPI\u0026p\u003dDevEx,5005.1",
          "displayText": "sailboats for sale",
          "query": "sailboats for sale",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dmnMdREUH20SepmHQH1zlh9Hy_w7jpOlZFm3KG2R_BoA\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailing%2banarchy%26FORM%3dUSBAPI\u0026p\u003dDevEx,5006.1",
          "displayText": "sailing anarchy",
          "query": "sailing anarchy",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dWLFO-B1GG5qtBGnoU1Bizz02YKkg5fgAQtHwhXn4z8I\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5007.1",
          "displayText": "sailpoint",
          "query": "sailpoint",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dquBMwmKlGwqC5wAU0K7n416plhWcR8zQCi7r-Fw9Y0w\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailflow%26FORM%3dUSBAPI\u0026p\u003dDevEx,5008.1",
          "displayText": "sailflow",
          "query": "sailflow",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003d0udadFl0gCTKCp0QmzQTXS3_y08iO8FpwsoKPHPS6kw\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboatdata%26FORM%3dUSBAPI\u0026p\u003dDevEx,5009.1",
          "displayText": "sailboatdata",
          "query": "sailboatdata",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003deSSt0MRSbl2V0RFPSuVd-gC7fGOT4717pz55EBUgPec\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailor%2b2025%26FORM%3dUSBAPI\u0026p\u003dDevEx,5010.1",
          "displayText": "sailor 2025",
          "query": "sailor 2025",
          "searchKind": "WebSearch"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](../tutorials/autosuggest.md)

- [Qu’est-ce que la Suggestion automatique Bing ?](../get-suggested-search-terms.md)
- [Informations de référence sur l’API Suggestion automatique Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
