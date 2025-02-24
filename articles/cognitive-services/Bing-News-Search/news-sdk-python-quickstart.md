---
title: "Démarrage rapide : Effectuer une recherche d'actualités avec le kit de développement logiciel (SDK) Recherche d'actualités Bing pour Python"
titleSuffix: Azure Cognitive Services
description: Utilisez ce démarrage rapide pour rechercher des actualités à l’aide du Kit de développement logiciel (SDK) Recherche d’actualités Bing pour Python et traiter la réponse.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2f6185ca6c2ddd44be2c12a81cedf1adcc2e30cd
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67339022"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-python"></a>Démarrage rapide : Effectuer une recherche d’actualités avec le Kit de développement logiciel (SDK) Recherche d’actualités Bing pour Python

Utilisez ce guide de démarrage rapide pour démarrer une recherche d’actualités avec le SDK Recherche d’actualités Bing pour Python. Si l’outil Recherche d’actualités Bing a une API REST compatible avec la plupart des langages de programmation, le SDK offre quant à lui un moyen facile d’intégrer le service à vos applications. Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Prérequis

* [Python](https://www.python.org/) 2.x ou 3.x

Il est recommandé d’utiliser un [environnement virtuel](https://docs.python.org/3/tutorial/venv.html) pour votre développement Python. Vous pouvez installer et initialiser l’environnement virtuel avec le [module venv](https://pypi.python.org/pypi/virtualenv). Vous devez installer un environnement virtuel pour Python 2.7. Vous pouvez créer un environnement virtuel avec :

```console
python -m venv mytestenv
```

Vous pouvez installer les dépendances du SDK Recherche d’actualités Bing à l’aide de cette commande :
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un fichier Python dans votre éditeur ou IDE favori, puis importez les bibliothèques suivantes. Créez une variable pour votre clé d’abonnement ainsi qu’un terme de recherche.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Initialiser le client et envoyer une requête

1. Créez une instance de `CognitiveServicesCredentials`. Instanciez le client :
    
    ```python
    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```

2. Envoyez une requête de recherche à l’API Recherche d’actualités et stockez la réponse.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>Analyser la réponse

Si des résultats sont retournés, affichez le premier résultat de la page web :

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(
        news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer une application web monopage](tutorial-bing-news-search-single-page-app.md)
