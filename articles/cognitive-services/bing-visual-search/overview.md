---
title: Qu’est-ce que l’API Recherche visuelle Bing ?
titleSuffix: Azure Cognitive Services
description: La Recherche visuelle Bing fournit des détails ou des informations sur une image, comme des images similaires ou des sources d’achat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 03/27/2019
ms.author: scottwhi
ms.openlocfilehash: 4da4340176259265fd36c55d2b81ecfc3f4ed5f8
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657698"
---
# <a name="what-is-the-bing-visual-search-api"></a>Qu’est-ce que l’API Recherche visuelle Bing ?

L’API Recherche visuelle Bing retourne des insights pour une image. Vous pouvez charger une image ou fournir une URL vers une image. Les insights sont des images visuellement similaires, des sources d’achat, des pages web qui incluent l’image, etc. Les insights retournés par l’API Recherche visuelle Bing sont similaires à ceux qui apparaissent sur Bing.com/images.

Si vous utilisez l’[API Recherche d’images Bing](../bing-image-search/overview.md), vous pouvez utiliser des jetons d’insight provenant des résultats de recherche de l’API pour votre recherche d’images Bing au lieu de charger une image.

> [!IMPORTANT]
> Si vous obtenez des informations sur les images à l’aide de l’API Recherche d’images Bing, songez à passer à l’API Recherche visuelle Bing pour accéder à des informations plus complètes.

## <a name="insights"></a>Insights

Vous pouvez découvrir les insights suivants avec Recherche visuelle Bing :

| Insight                              | Description |
|--------------------------------------|-------------|
| Images visuellement similaires              | Liste d’images qui sont visuellement semblables à l’image d’entrée. |
| Produits visuellement similaires            | Produits visuellement similaires au produit présenté.            |
| Sources d’achat                     | Lieux où vous pouvez acheter l’article affiché dans l’image d’entrée.            |
| Recherches associées                     | Recherches associées effectuées par d’autres personnes ou basées sur le contenu de l’image.            |
| Pages web incluant l’image     | Pages Web contenant l’image d’entrée.            |
| Recettes                              | Pages web comportant des recettes pour préparer le plat montré dans l’image d’entrée.            |

En plus de ces insights, Recherche visuelle Bing retourne également différents termes (c’est-à-dire des étiquettes) dérivés de l’image d’entrée. Ces étiquettes permettent aux utilisateurs d’explorer les concepts trouvés dans l’image. Par exemple, si l’image d’entrée représente un sportif célèbre, une des étiquettes peut être le nom du sportif et une autre peut être « Sport ». Si l’image d’entrée représente une tarte aux pommes, les étiquettes peuvent être Tarte aux pommes, Tarte et Desserts.

Les résultats de Recherche visuelle Bing incluent également des rectangles englobants pour les zones intéressantes de l’image. Par exemple, si l’image représente plusieurs célébrités, les résultats peuvent inclure des rectangles englobants pour chacune des célébrités reconnues. Si Bing reconnaît un produit ou un vêtement dans l’image, le résultat peut inclure un rectangle englobant pour l’élément reconnu.

## <a name="workflow"></a>Workflow

L’API Recherche visuelle Bing est un service web RESTful et peut donc être facilement appelé à partir de n’importe quel langage de programmation permettant d’exécuter des requêtes HTTP et d’analyser des réponses JSON. Vous pouvez utiliser l’API REST ou le SDK pour le service.

1. Créez un [compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour accéder aux API Recherche Bing. Si vous n’avez pas d’abonnement Azure, vous pouvez [créer un compte gratuitement](https://azure.microsoft.com/free/). Vous pouvez obtenir votre clé d’abonnement auprès du [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#get-the-keys-for-your-subscription) après avoir créé votre compte, ou du [site web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis) après l’activation d’un essai gratuit.
2. Envoyez une demande à l’API avec une requête de recherche valide.
3. Traitez la réponse de l’API en analysant le message JSON renvoyé.

## <a name="next-steps"></a>Étapes suivantes

Commencez par essayer la [démonstration interactive](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/) de l’API Recherche visuelle Bing.
La démonstration vous explique comment personnaliser rapidement une requête de recherche et parcourir le web à la recherche d’images.

Pour configurer rapidement votre première demande, consultez ces guides de démarrage rapide : [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).

## <a name="see-also"></a>Voir aussi

* Les informations de référence [Images - Recherche visuelle](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) donnent les définitions et des informations sur les points de terminaison, les en-têtes de demande, les réponses et les paramètres de requête que vous pouvez utiliser pour demander des résultats de recherche basée sur des images.

* L’article [Conditions d’utilisation et d’affichage de l’API Recherche Bing](../bing-web-search/use-display-requirements.md) spécifie les utilisations acceptables du contenu et des informations obtenus via les API Recherche Bing.
