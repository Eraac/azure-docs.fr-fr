---
title: Points de terminaison Recherche d’actualités Bing
titlesuffix: Azure Cognitive Services
description: Synthèse sur le point de terminaison de l’API Recherche d’actualités.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 6ea218da23d65696c76008cb15e183fcc4bbda10
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383227"
---
# <a name="bing-news-search-api-endpoints"></a>Points de terminaison de l'API Recherche d'actualités Bing

**L’API Recherche d’actualités** renvoie des articles, des pages web, des images, des vidéos et des [entités](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) en lien avec des actualités. Les entités contiennent un résumé d’informations sur une personne, un lieu ou un sujet.

## <a name="endpoints"></a>Points de terminaison

Pour obtenir des résultats de recherche d'actualités à l'aide de l'API Recherche d'actualités Bing, envoyez une requête `GET` à l'un des points de terminaison suivants. Les en-têtes et les paramètres d’URL définissent d’autres spécifications.

### <a name="news-items-by-search-query"></a>Articles en lien avec des actualités par requête de recherche

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Renvoie des articles en lien avec des actualités basés sur une requête de recherche. Si la requête de recherche est vide, l'API renvoie les meilleurs articles issus de différentes catégories. Envoyez une requête en encodant votre terme de recherche sous forme d'URL et en l'ajoutant au paramètre `q=""`. Pour la disponibilité, consultez [pays/régions et les marchés pris en charge](language-support.md#supported-markets-for-news-search-endpoint).

### <a name="top-news-items-by-category"></a>Meilleurs articles en lien avec des actualités par catégorie

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Renvoie les meilleurs articles en lien avec des actualités par catégorie. Vous pouvez demander spécifiquement les meilleurs articles en lien avec l’économie, le sport ou le divertissement à l’aide de `category=business`, `category=sports` ou `category=entertainment`.  Le paramètre `category` peut uniquement être utilisé avec l’URL `/news`. Certaines conditions s’appliquent pour spécifier des catégories ; reportez-vous à `category` dans la documentation sur le [paramètre de requête](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters). Envoyez une requête en encodant votre terme de recherche sous forme d'URL et en l'ajoutant au paramètre `q=""`. Pour la disponibilité, consultez [pays/régions et les marchés pris en charge](language-support.md#supported-markets-for-news-endpoint).

### <a name="trending-news-topics"></a>Sujets d'actualité tendance 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Renvoie des sujets en lien avec des actualités qui sont actuellement populaires sur les réseaux sociaux. Lorsque l’option `/trendingtopics` est incluse, la recherche Bing ignore plusieurs autres paramètres, tels que `freshness` et `?q=""`. Pour la disponibilité, consultez [pays/régions et les marchés pris en charge](language-support.md#supported-markets-for-news-trending-endpoint).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les en-têtes, les paramètres, les codes de marché, les objets de réponse, les erreurs, etc., consultez [News Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) (Référence sur l’API Recherche d’actualités Bing v7).

Pour plus d’informations sur les paramètres pris en charge par chaque point de terminaison, consultez les pages de référence pour chaque type.
Pour obtenir des exemples de requêtes de base à l’aide de l’API Recherche d’actualités, consultez les [démarrages rapides sur l’API Recherche d'actualités Bing](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
