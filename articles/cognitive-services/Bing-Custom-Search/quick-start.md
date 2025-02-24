---
title: 'Démarrage rapide : Créer une première instance Recherche personnalisée Bing | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Utilisez cet article pour créer une instance Bing personnalisée qui peut effectuer des recherches dans les domaines et pages web que vous définissez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: 2806ca6f7079ffac3d2222363cd5b3839ef8f97d
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405020"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Démarrage rapide : Créer votre première instance de Recherche personnalisée Bing

Pour utiliser la Recherche personnalisée Bing, vous devez créer une instance de recherche personnalisée qui définit votre vue, ou section du web. Cette instance contient les domaines, sites web et pages web publics dans lesquels vous voulez effectuer des recherches et procéder aux ajustements de classement que vous souhaitez. 

Pour créer l’instance, utilisez le [portail Recherche personnalisée Bing](https://customsearch.ai). 

![Image du portail Recherche personnalisée Bing](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Création d’une instance de recherche personnalisée

Pour créer une instance Recherche personnalisée Bing :

1. Cliquez sur **Bien démarrer** dans la page web [Portail Recherche personnalisée Bing](https://customsearch.ai) et connectez-vous avec votre compte Microsoft.

2. Cliquez sur **Nouvelle Instance**, puis entrez un nom descriptif. Vous pouvez changer le nom de votre instance à tout moment.
 
3. Sous l’onglet **Active** (Éléments actifs) sous **Search Experience** (Expérience de recherche), entrez l’URL d’un ou de plusieurs sites web à inclure dans votre recherche. 

    > [!NOTE]
    > Les instances Recherche personnalisée Bing retournent uniquement des résultats pour les domaines et pages web qui sont publics et ont été indexés par Bing.

4. Vous pouvez utiliser le côté droit du portail Recherche personnalisée Bing pour entrer une requête et examiner les résultats de recherche retournés par votre instance de recherche. Si aucun résultat n’est retourné, faites un nouvel essai en entrant une autre URL.  

5. Cliquez sur **Publier** pour publier vos modifications dans l’environnement de production et mettre à jour les points de terminaison de l’instance.

6.  Cliquez sur l’onglet **Production**. Sous **Points de terminaison**, copiez votre **ID de configuration personnalisée**. Vous avez besoin de cet identificateur pour appeler l’API Recherche personnalisée en l’ajoutant au paramètre de requête `customconfig=` dans vos appels.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Démarrage rapide : Appeler votre point de terminaison Recherche personnalisée Bing](./call-endpoint-csharp.md)
