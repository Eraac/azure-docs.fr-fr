---
title: 'Démarrage rapide : Rechercher des images - SDK Recherche d’images Bing pour Java'
titleSuffix: Azure Cognitive Services
description: Utilisez ce guide de démarrage rapide pour effectuer votre première recherche d’images à l’aide du SDK Recherche d’images Bing, qui est un wrapper de l’API et contient les mêmes fonctionnalités. Cette application Java simple envoie une requête de recherche d’image, analyse la réponse JSON et affiche l’URL de la première image retournée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 02/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 9b756d097f313179f4cc177a8cbe74419629317b
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423965"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-java"></a>Démarrage rapide : Rechercher des images à l’aide du SDK Recherche d’images Bing pour Java

Utilisez ce guide de démarrage rapide pour effectuer votre première recherche d’images à l’aide du SDK Recherche d’images Bing, qui est un wrapper de l’API et contient les mêmes fonctionnalités. Cette application Java simple envoie une requête de recherche d’image, analyse la réponse JSON et affiche l’URL de la première image retournée.

Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingImageSearch/Quickstart) avec une gestion des erreurs supplémentaire et des annotations.

## <a name="prerequisites"></a>Prérequis
Obtenez une [clé d’accès Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) sous **Recherche**.  Consultez également [Tarification Cognitive Services - API Recherche Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

La dernière version du [JDK (Java Development Kit)](https://aka.ms/azure-jdks)

Installez les dépendances du Kit de développement logiciel (SDK) pour Recherche d’images Bing en utilisant Maven, Gradle ou un autre système de gestion des dépendances. Le fichier POM Maven nécessite la déclaration suivante :

```xml
 <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-imagesearch</artifactId>
      <version>1.0.1</version>
    </dependency>
 </dependencies>
```

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Créer et initialiser l’application

1. Créez un projet Java dans votre éditeur ou IDE favori, et ajoutez les importations suivantes à votre implémentation de classe :

    ```java
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchAPI;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.BingImageSearchManager;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImageObject;
    import com.microsoft.azure.cognitiveservices.search.imagesearch.models.ImagesModel;
    ```

2. Dans votre méthode principale, créez des variables pour votre clé d’abonnement, ainsi que votre terme de recherche. Instanciez ensuite le client Recherche d’images Bing.

    ```java
    final String subscriptionKey = "COPY_YOUR_KEY_HERE";
    String searchTerm = "canadian rockies";
    //Image search client
    BingImageSearchAPI client = BingImageSearchManager.authenticate(subscriptionKey);
    ```

## <a name="send-a-search-request-to-the-api"></a>Envoyer une demande de recherche à l’API

1. À l’aide de `bingImages().search()`, envoyer la requête HTTP contenant la requête de recherche. Enregistrer la réponse sous la forme d’un `ImagesModel`.

   ```java
    ImagesModel imageResults = client.bingImages().search()
                .withQuery(searchTerm)
                .withMarket("en-us")
                .execute();
    ```

## <a name="parse-and-view-the-result"></a>Analyser et afficher le résultat

Analysez les résultats d’image retournés par la réponse.
Si la réponse contient des résultats de recherche, stockez le premier résultat et imprimez ses détails, comme l’URL d’une miniature, l’URL d’origine, ainsi que le nombre total d’images retournées.  

```java
if (imageResults != null && imageResults.value().size() > 0) {
    // Image results
    ImageObject firstImageResult = imageResults.value().get(0);

    System.out.println(String.format("Total number of images found: %d", imageResults.value().size()));
    System.out.println(String.format("First image thumbnail url: %s", firstImageResult.thumbnailUrl()));
    System.out.println(String.format("First image content url: %s", firstImageResult.contentUrl()));
}
else {
        System.out.println("Couldn't find image results!");
     }

```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel d’une application monopage Recherche d’images Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que la Recherche d’images Bing ?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Essayez une démonstration interactive en ligne](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Obtenir une clé d’accès Cognitive Services gratuite](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)
* [Exemples Java pour le SDK Azure Cognitive Services](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
* [Documentation Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [Informations de référence sur l’API Recherche d’images Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
