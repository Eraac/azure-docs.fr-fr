---
title: Qu’est l’API Recherche d’entreprises locales Bing ?
titleSuffix: Azure Cognitive Services
description: L’API Recherche d’entreprises locales Bing est un service RESTful permettant à vos applications de rechercher des informations sur des lieux et des entreprises à proximité à l’aide de requêtes.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 2078e4842f6b8af11fcf56760579ce1ec77dd23f
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423362"
---
# <a name="what-is-bing-local-business-search"></a>Présentation de Recherche d’entreprises locales Bing
L’API Recherche d’entreprises locales Bing est un service RESTful permettant à vos applications de rechercher des informations sur des entreprises locales à l’aide de requêtes. Par exemple, `q=<business-name> in Redmond, Washington` ou `q=Italian restaurants near me`. 

## <a name="features"></a>Caractéristiques
| Fonctionnalité | Description |  
| -- | -- | 
| [Rechercher des entreprises et des emplacements locaux](quickstarts/local-quickstart.md) | L’API Recherche d’entreprises locales Bing obtient les résultats localisés d’une requête. Les résultats incluent une URL pour le site web de l’entreprise et présentent un texte, un numéro de téléphone et un emplacement géographique, notamment : les coordonnées GPS, la ville et l’adresse postale. |  
| [Filtrer les résultats locaux avec des limites géographiques](specify-geographic-search.md) | Ajoutez des coordonnées comme paramètres de recherche pour limiter les résultats à une zone géographique spécifique, selon une zone circulaire ou un carré. | 
| [Filtrer les résultats des entreprises locales par catégorie](local-categories.md) | Recherchez les résultats des entreprises locales par catégorie. Cette option utilise l’emplacement IP inverse ou les coordonnées GPS de l’appelant pour retourner des résultats localisés dans différentes catégories d’entreprise.|

## <a name="workflow"></a>Workflow
Appelez l’API Recherche d’entreprises locales Bing à partir de n’importe quel langage de programmation permettant d’exécuter des requêtes HTTP et d’analyser des réponses JSON. Ce service est accessible à l’aide de l’API REST.
 
1. Créez un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) disposant d’un accès aux API Recherche Bing. Si vous n’avez pas d’abonnement Azure, vous pouvez [créer un compte gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. Encodez par URL vos termes de recherche pour le paramètre de requête `q=""`. Par exemple, `q=nearby+restaurant` ou `q=nearby%20restaurant`. Définissez également la pagination, si nécessaire. 
3. Envoyer une [API Recherche d’entreprises locales Bing](quickstarts/local-quickstart.md) 
4. Analyser la réponse JSON 

> [!NOTE]
> Actuellement, la recherche d’entreprises locales prend uniquement en charge le marché `en-US`. 
> [!NOTE]
> Actuellement, la recherche d’entreprises locales ne prend pas en charge la fonction Suggestion automatique. 

## <a name="next-steps"></a>Étapes suivantes
- [Requête et réponse](local-search-query-response.md)
- [Démarrage rapide de la Recherche d’entreprises locales](quickstarts/local-quickstart.md)
- [Informations de référence sur l’API Recherche d’entreprises locales](local-search-reference.md)
- [Conditions d’utilisation et d’affichage](use-display-requirements.md)
