---
title: "Démarrage rapide : Rechercher des images - API REST Recherche d'images Bing et Python"
titleSuffix: Azure Cognitive Services
description: Utilisez ce démarrage rapide pour envoyer des requêtes de recherche d'images à l'API REST Recherche d'images Bing à l'aide de Python et recevoir des réponses JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 02/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 59c7a85ae564981029163bf99a8e44e72237f709
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383655"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Démarrage rapide : Rechercher des images à l'aide de l'API REST Recherche d'images Bing et de Python

Utilisez ce guide de démarrage rapide pour commencer à envoyer des demandes de recherche à l’API Recherche d’images Bing. Cette application Python envoie une requête de recherche à l’API et affiche l’URL de la première image dans les résultats. Bien que cette application soit écrite en Python, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

Vous pouvez exécuter cet exemple comme un bloc-notes Jupyter sur [MyBinder](https://mybinder.org) en cliquant sur le badge de lancement de Binder :

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py) avec une gestion des erreurs supplémentaire et des annotations.


## <a name="prerequisites"></a>Prérequis

* [Python 2.x ou 3.x](https://www.python.org/)
* La bibliothèque [Python Imaging Library (PIL)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un fichier Python dans votre environnement de développement intégré (IDE) ou éditeur favori, et importez les modules suivants. Créez une variable pour votre clé d’abonnement, le point de terminaison de recherche et un terme de recherche.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. Ajoutez votre clé d’abonnement à l’en-tête `Ocp-Apim-Subscription-Key` en créant un dictionnaire et en ajoutant la clé comme valeur. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>Créer et envoyer la demande de recherche

1. Créez un dictionnaire pour les paramètres de la demande de recherche. Ajoutez votre terme de recherche au paramètre `q`. Utilisez « public » pour le paramètre `license` afin de rechercher des images dans le domaine public. Utilisez « photo » pour `imageType` afin de rechercher uniquement des photos.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. Utilisez la bibliothèque `requests` pour appeler l’API Recherche d’images Bing. Ajoutez à la demande votre en-tête et vos paramètres, et renvoyez la réponse sous forme d’objet JSON. 

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

## <a name="view-the-response"></a>Afficher la réponse

1. Créez une figure avec quatre colonnes et quatre lignes à l’aide de la bibliothèque matplotlib. 

2. Effectuez une itération dans les lignes et colonnes de la figure, et utilisez la méthode `Image.open()` de la bibliothèque PIL pour ajouter une vignette de l’image à chaque espace. 

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    ```

3. Utilisez `plt.show()` pour tracer la figure et afficher les images.

## <a name="example-json-response"></a>Exemple de réponse JSON

Les réponses de l’API Recherche d’images Bing sont retournées au format JSON. Cet exemple de réponse a été tronqué pour afficher un résultat unique.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel d’une application monopage Recherche d’images Bing](../tutorial-bing-image-search-single-page-app.md)

* [Qu’est-ce que l’API Recherche d’images Bing ?](../overview.md)  
* [Détail des prix](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) des API Recherche Bing. 
* [Obtenir une clé d’accès Cognitive Services gratuite](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Documentation Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Informations de référence sur l’API Recherche d’images Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
