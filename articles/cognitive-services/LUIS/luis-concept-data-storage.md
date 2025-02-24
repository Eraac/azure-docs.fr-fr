---
title: Stockage des données
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS stocke les données chiffrées dans un magasin de données Azure correspondant à la région spécifiée par la clé.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: diberry
ms.openlocfilehash: a1093c2a6303b453a17a52058303913de5ecfa8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60812937"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Stockage et suppression des données dans Language Understanding (LUIS) Cognitive Services
LUIS stocke les données chiffrées dans un magasin de données Azure correspondant à la région spécifiée par la clé. Ces données sont stockées pendant 30 jours. 

## <a name="export-and-delete-app"></a>Exporter et supprimer l’application
Les utilisateurs ont le contrôle total sur l’[exportation](luis-how-to-start-new-app.md#export-app) et la [suppression](luis-how-to-start-new-app.md#delete-app) de l’application. 

## <a name="utterances"></a>Énoncés

Les énoncés peuvent être stockées à deux emplacements différents. 

* Au cours du **processus de création**, les énoncés sont créés et stockés dans l’intention. Les énoncés dans les intentions sont requis pour un bon fonctionnement de l’application LUIS. Une fois que l’application est publiée et reçoit des requêtes au point de terminaison, chaîne de requête de la demande de point de terminaison, `log=false`, détermine si l’énoncé du point de terminaison est stocké. Si le point de terminaison est stocké, il fait alors partie des énoncés d’apprentissage actif trouvés dans la section **Build** du portail, dans la section **Passer en revue les énoncés des points de terminaison**. 
* Lorsque vous **examinez les énoncés des points de terminaison** et ajoutez un énoncé à une intention, l’énoncé n’est plus stocké dans le cadre des énoncés de point de terminaison à passer en revue. Il est ajouté aux intentions de l’application. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Supprimer des exemples d’énoncés d’une intention
Supprimer des exemples d’énoncés utilisés pour l’apprentissage de [LUIS](luis-reference-regions.md). Si vous supprimez un exemple d’énoncé de votre application LUIS, il est supprimé du service web LUIS et n’est plus disponible pour l’exportation.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Supprimer des énoncés de la révision de l’apprentissage actif

Vous pouvez supprimer des énoncés de la liste des énoncés d’utilisateurs que LUIS suggère dans la page **[Réviser les énoncés de point de terminaison](luis-how-to-review-endpoint-utterances.md)** . La suppression d’énoncés de cette liste a pour effet d’empêcher leur suggestion, mais pas de les supprimer des journaux d’activité.

Si vous ne souhaitez pas avoir d’énoncés d’apprentissage actif, vous pouvez [désactiver l’apprentissage actif](luis-how-to-review-endpoint-utterances.md#disable-active-learning). La désactivation de l’apprentissage actif désactive également la journalisation.

### <a name="disable-logging-utterances"></a>Désactiver la journalisation des énoncés
[La désactivation de l’apprentissage actif](luis-how-to-review-endpoint-utterances.md#disable-active-learning) désactive la journalisation.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Supprimer un compte
Si vous supprimez un compte, toutes les applications sont supprimées, ainsi que leurs exemples d’énoncés et journaux d’activité. Les données sont conservées pendant 60 jours avant que le compte et les données ne soient définitivement supprimés.

La suppression d’un compte est possible à partir de la page **Paramètres**. Sélectionnez le nom de votre compte en haut à droite dans la barre de navigation supérieure pour accéder à la page **Paramètres**.

## <a name="data-inactivity-as-an-expired-subscription"></a>Inactivité de données considérée comme un abonnement arrivé à expiration
Dans le cadre de la conservation et de la suppression des données, une application LUIS inactive peut, à la _discrétion de Microsoft_ être traitée comme un abonnement arrivé à expiration. Une application est considérée comme inactive si elle répond aux critères suivants pendant les 90 derniers jours : 

* Elle n’a reçu **aucun** appel.
* Elle n’a pas été modifiée.
* Aucune clé actuelle ne lui est assignée.
* Aucun utilisateur ne s’y est connecté.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’exportation et la suppression d’une application](luis-how-to-start-new-app.md)
