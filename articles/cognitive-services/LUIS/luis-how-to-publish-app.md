---
title: Publier une application
titleSuffix: Azure Cognitive Services
description: Quand vous avez terminé la création et les tests de votre application LUIS active, mettez-la à disposition de votre application cliente en la publiant sur le point de terminaison.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: diberry
ms.openlocfilehash: 22bed877d853c7023f8efe6bfb3dd21b4aa4c8df
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60194425"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publier votre application active, formée sur un point de terminaison intermédiaire ou de production

Quand vous avez terminé la création et les tests de votre application LUIS active, mettez-la à disposition de votre application cliente en la publiant sur le point de terminaison. 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Publication

Pour publier sur le point de terminaison, sélectionnez **Publish** (Publier) dans le volet en haut à droite. 

![Barre de navigation supérieure, à droite](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

Sélectionnez l’emplacement correct quand la fenêtre contextuelle s’affiche : intermédiaire ou production. L’utilisation de deux emplacements de publication vous permet d’avoir deux versions différentes avec des points de terminaison publiés, ou la même version sur deux points de terminaison différents. 

L’application est publiée dans toutes les régions associées aux ressources LUIS ajoutés dans le portail de LUIS. Par exemple, pour une application créée sur [www.luis.ai](https://www.luis.ai), si vous créez une ressource LUIS dans **westus** et que vous l’ajoutez à l’application en tant que ressource, l’application est publiée dans cette région. Pour plus d’informations sur les régions LUIS, consultez [Régions](luis-reference-regions.md).
 
![Fenêtre contextuelle de publication](./media/luis-how-to-publish-app/publish-pop-up.png)

Une fois votre application correctement publiée, une notification de réussite de couleur verte s’affiche en haut du navigateur. La barre de notification verte comprend également un lien vers les points de terminaison. 

![Fenêtre contextuelle de publication avec un lien au point de terminaison](./media/luis-how-to-publish-app/publish-success.png)

Si vous avez besoin de l’URL de point de terminaison, sélectionnez le lien. Vous pouvez également accéder aux URL de point de terminaison en sélectionnant **Manage** (Gérer) dans le menu supérieur, puis **Keys and Endpoints** (Clés et points de terminaison) dans le menu gauche. 

## <a name="configuring-publish-settings"></a>Configuration des paramètres de publication

Configurez les paramètres de publication en sélectionnant **Manage** (Gérer) dans le menu de navigation en haut à droite, puis **Publish Settings** (Paramètres de publication). 

![Paramètres de publication](./media/luis-how-to-publish-app/publish-settings.png)

### <a name="publish-after-enabling-sentiment-analysis"></a>Publier après l’activation de l’analyse des sentiments

<a name="enable-sentiment-analysis"></a>

L’analyse des sentiments permet à LUIS de s’intégrer avec l’[Analyse de texte](https://azure.microsoft.com/services/cognitive-services/text-analytics/) pour fournir une analyse des sentiments et des expressions clés. 

Vous ne devez pas nécessairement fournir une clé d’Analyse de texte, et votre compte Azure ne sera pas facturé pour ce service. Lorsque vous activez ce paramètre, il est persistant. 

Les données de sentiment correspondent à un score compris entre 1 et 0 indiquant le sentiment, positif (plus proche de 1) ou négatif (plus proche de 0), des données. L’étiquette de sentiment de `positive`, `neutral` et `negative` est fonction de la culture prise en charge. Actuellement, seul l’anglais prend en charge les étiquettes de sentiment. 

Pour plus d’informations sur la réponse du point de terminaison JSON avec l’analyse des sentiments, voir [Analyse des sentiments](luis-concept-data-extraction.md#sentiment-analysis).

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Gérer les clés](./luis-how-to-azure-subscription.md) pour ajouter des clés à la clé d’abonnement Azure dans LUIS et pour savoir comment définir la clé de vérification orthographique Bing et inclure toutes les intentions dans les résultats.
* Voir [Former et tester votre application](luis-interactive-test.md) pour obtenir des instructions sur la façon de tester votre application publiée dans la console de test.

