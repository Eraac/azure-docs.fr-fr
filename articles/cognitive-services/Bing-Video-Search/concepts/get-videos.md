---
title: Envoi de requêtes de recherche à l’API Recherche de vidéos Bing
titleSuffix: Azure Cognitive Services
description: Découvrez l’envoi de requêtes de recherche à l’API Recherche de vidéos Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: f1d433dd7d919aa5d11f2f59b74a5e3f2dca0b59
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500288"
---
# <a name="search-for-videos-with-the-bing-video-search-api"></a>Rechercher des vidéos avec l’API Recherche de vidéos Bing

L’API Recherche de vidéos Bing facilite l’intégration des capacités de recherche de vidéos cognitive de Bing dans vos applications. Alors que l’API trouve et retourne surtout des vidéos pertinentes provenant du web, elle propose plusieurs fonctionnalités pour les récupérer de manière intelligente et pertinente.

## <a name="getting-videos"></a>Obtention de vidéos

Pour obtenir des vidéos web en rapport avec le terme de recherche de l’utilisateur, envoyez la requête GET suivante :

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Toutes les demandes doivent provenir d’un serveur.

Si vous appelez l’une des API Bing pour la première fois, n’incluez pas l’en-tête ID client. Indiquez uniquement l’ID client si vous avez précédemment appelé une API Bing et si Bing a renvoyé un ID client pour la combinaison utilisateur/appareil.

Pour obtenir des vidéos à partir d’un domaine spécifique, utilisez l’opérateur de requête [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

La réponse contient un objet [Videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) comprenant une liste de vidéos qui, selon Bing, ont un rapport avec la requête. Chaque objet [Video](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) de la liste inclut, entre autres attributs, l’URL de la vidéo, sa durée, ses dimensions et son format d’encodage. L’objet vidéo comprend également l’URL et les dimensions d’une miniature de la vidéo.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

## <a name="video-thumbnails"></a>Vidéos miniatures

Vous pouvez afficher la totalité ou une partie des vidéos miniatures retournées par l’API Recherche de vidéos Bing. Si vous affichez une partie, donnez à l’utilisateur la possibilité d’afficher les vidéos restantes. Conformément aux [exigences liées à l’affichage et à l’utilisation](../UseAndDisplayRequirements.md) de l’API Bing, vous devez afficher les vidéos dans l’ordre indiqué dans la réponse. Pour plus d’informations sur le redimensionnement de la miniature, consultez [Redimensionnement et rognage de miniatures](../../bing-web-search/resize-and-crop-thumbnails.md). 

Vous pouvez utiliser [motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-motionthumbnailurl) pour lire une version miniature de la vidéo lorsque l’utilisateur pointe sur la miniature. Veillez à attribuer la vidéo miniature lorsque vous l’affichez.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Quand vous cliquez sur une miniature, trois options d’affichage de la vidéo sont proposées :

- Utilisez [hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-hostpageurl) pour afficher la vidéo sur le site web hôte (par exemple, YouTube)
- Utilisez [webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-websearchurl) pour afficher la vidéo dans le navigateur vidéo Bing
- Utilisez [embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-embedhtml) pour incorporer la vidéo à votre propre expérience 

Veillez à utiliser l’éditeur et le créateur pour attribuer la vidéo lors de sa lecture.

Pour en savoir plus sur l’utilisation de [videoId](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-videoid) et obtenir des informations sur la vidéo, consultez [Informations sur les vidéos](../video-insights.md).

## <a name="filtering-videos"></a>Filtrage de vidéos

Par défaut, l’API Recherche de vidéos retourne toutes les vidéos ayant un rapport avec la requête. Si vous souhaitez uniquement obtenir des vidéos gratuites ou d’une durée inférieure à cinq minutes, préférez l’utilisation des paramètres de requête de filtre suivants :

- [pricing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#pricing)&mdash;Filtre les vidéos par prix (par exemple, celles qui sont gratuites ou celles pour lesquelles vous devez payer)
- [resolution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#resolution)&mdash;Filtre les vidéos par résolution (par exemple, celles qui ont une résolution de 720p ou supérieure)
- [videoLength](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videolength)&mdash;Filtre les vidéos par durée de vidéo (par exemple, celles dont la durée est inférieure à cinq minutes)
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#freshness)&mdash;Filtre les vidéos selon leur ancienneté (par exemple, celles découvertes par Bing la semaine dernière)

Pour obtenir des vidéos à partir d’un domaine spécifique, ajoutez l’opérateur de requête [site:](https://msdn.microsoft.com/library/ff795613.aspx) dans la chaîne de requête.

> [!NOTE]
> En fonction de la requête, si vous utilisez l’opérateur de requête `site:`, il est possible que la réponse présente du contenu pour adultes, et ce quel que soit le paramètre [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#safesearch) défini. Utilisez `site:` uniquement si vous connaissez le contenu du site et si votre scénario prend en charge le contenu pour adultes.

L’exemple suivant montre comment obtenir des vidéos gratuites, à partir de ContosoSailing.com, qui affichent une résolution de 720p minimum et que Bing a découvert le mois dernier.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>Développement de la requête

Si Bing peut développer la requête pour affiner la recherche d’origine, l’objet [Videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) contient le champ `queryExpansions`. Par exemple, si la requête est *Cleaning Gutters* (Nettoyage de gouttières), les requêtes développées peuvent être : Gutter Cleaning **Tools**, Cleaning Gutters **From the Ground**, Gutter Cleaning **Machine** et **Easy** Gutter Cleaning (outils pour le nettoyage de gouttières, nettoyage de gouttières à partir du sol, appareil pour le nettoyage de gouttières et nettoyage de gouttières facile).

L’exemple suivant montre les requêtes développées pour le *nettoyage de gouttières* (Cleaning Gutters).

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

Le champ `queryExpansions` contient une liste d’objets [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj). Le champ `text` contient la requête développée, et `displayText` le terme de développement. Vous pouvez utiliser les champs text et thumbnail pour montrer les chaînes des requêtes développées à l’utilisateur si c’est vraiment ce qu’il recherche. Pour rendre la miniature et le texte interactifs, utilisez l’URL `webSearchUrl` ou l’URL `searchLink`. Utilisez `webSearchUrl` pour envoyer à l’utilisateur les résultats de la recherche Bing, ou `searchLink` si vous fournissez votre propre page de résultats.

## <a name="pivoting-the-query"></a>Création d’un tableau croisé dynamique à partir de la requête

Si Bing peut segmenter la requête de recherche d’origine, l’objet [Videos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) contient le champ `pivotSuggestions`. Par exemple, Bing peut segmenter la requête d’origine *Nettoyage de gouttières* en *Nettoyage* et *Gouttières*.

L’exemple suivant montre les suggestions de tableau croisé dynamique pour *Nettoyage de gouttières*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

Pour chaque tableau croisé dynamique, la réponse contient une liste d’objets [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj) contenant les requêtes suggérées. Le champ `text` contient la requête suggérée, et `displayText` le terme qui remplace le tableau croisé dynamique dans la requête d’origine. Par exemple, le nettoyage de fenêtres.

Vous pouvez utiliser les champs `text` et `thumbnail` pour présenter les chaînes des requêtes développées à l’utilisateur si c’est vraiment ce qu’il recherche. Pour rendre la miniature et le texte interactifs, utilisez l’URL `webSearchUrl` ou l’URL `searchLink`. Utilisez `webSearchUrl` pour envoyer à l’utilisateur les résultats de la recherche Bing, ou `searchLink` si vous fournissez votre propre page de résultats.

## <a name="throttling-requests"></a>Demandes de limitation

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]
