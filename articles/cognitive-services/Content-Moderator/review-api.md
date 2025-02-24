---
title: Concepts des révisions, flux de travail et travaux - Content Moderator
titlesuffix: Azure Cognitive Services
description: En savoir plus sur les révisions, flux de travail et travaux
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: c1d4ef640e2ae072dacba7a665b6689e3224c55c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60607294"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Révisions, flux de travail et travaux avec modération du contenu

Content Moderator combine la modération assistée par ordinateur et les capacités d’intervention humaine pour créer un processus de modération optimal adapté à des scénarios concrets. Il utilise pour cela l’[outil de révision](https://contentmoderator.cognitive.microsoft.com) dans le cloud. Dans ce guide, vous découvrirez les concepts fondamentaux de l’outil de révision : révisions, flux de travail et travaux.

## <a name="reviews"></a>Révisions

Dans une révision, le contenu est chargé dans l’outil de révision et apparaît sous l’onglet **Révision**. De là, les utilisateurs peuvent modifier les balises appliquées et appliquer leurs propres balises personnalisées selon leurs besoins. Lorsqu’un utilisateur soumet une révision, les résultats sont envoyés à un point de terminaison de rappel spécifié, puis le contenu est retiré du site.

![Site web avec outil de révision ouvert dans un navigateur, sous l’onglet Révision](./Review-Tool-user-Guide/images/image-workflow-review.png)

Consultez le [guide de l’outil de révision](./review-tool-user-guide/review-moderated-images.md) pour commencer à créer des révisions, ou le [guide de l’API REST](./try-review-api-review.md) pour apprendre à le faire par programmation.

## <a name="workflows"></a>Flux de travail

Un flux de travail est un filtre personnalisé basé sur le cloud pour le contenu. Flux de travail peut se connecter à une variété de services pour filtrer le contenu de différentes façons et puis exécutez l’action appropriée. Avec le connecteur Content Moderator, un flux de travail peut automatiquement appliquer des balises de modération et créer révisions avec le contenu soumis.

### <a name="view-workflows"></a>Afficher des flux de travail

Pour afficher vos flux de travail existants, accédez à l’[outil de révision](https://contentmoderator.cognitive.microsoft.com/), puis sélectionnez **Paramètres** > **Flux de travail**.

![Flux de travail par défaut](images/default-workflow-listed.PNG)

Les flux de travail peuvent être décrits complètement en tant que chaînes JSON, ce qui les rend accessibles par programmation. Si vous choisissez l’option **Modifier** pour votre flux de travail, puis choisissez sélectionnez l’onglet **JSON**, une expression JSON similaire à ce qui suit s’affiche :

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

Consultez le [guide de l’outil de révision](./review-tool-user-guide/workflows.md) pour commencer à créer et à utiliser des flux de travail, ou le [guide de l’API REST](./try-review-api-workflow.md) pour apprendre à le faire par programmation.

## <a name="jobs"></a>Tâches

Un travail de modération est un type de wrapper permettant d’obtenir une modération du contenu, des flux de travail et des révisions. Le travail analyse votre contenu à l’aide de l’API Modération des images ou de l’API Modération du texte de Content Moderator, puis compare le résultat obtenu au flux de travail désigné. Selon les résultats de flux de travail, il crée ou non une révision pour le contenu dans l’[outil de révision](./review-tool-user-guide/human-in-the-loop.md). Même s’il est possible de créer et de configurer des révisions et des flux de travail avec leurs API respectives, l'API de travail vous permet d'obtenir un rapport détaillé de l'ensemble du processus (qui peut être envoyé à un point de terminaison de rappel spécifié).

Consultez le [guide de l’API REST](./try-review-api-job.md) pour commencer à utiliser des travaux.

## <a name="next-steps"></a>Étapes suivantes

* Testez la [console d’API Job](try-review-api-job.md) et utilisez les exemples de code API REST. Si vous connaissez déjà Visual Studio et C#, consultez aussi le [guide de démarrage des tâches .NET](moderation-jobs-quickstart-dotnet.md). 
* Pour les révisions, prenez en main la [console d’API Révision](try-review-api-review.md), et utilisez les exemples de code de l’API REST. Consultez ensuite le [guide de démarrage rapide des révisions .NET](moderation-reviews-quickstart-dotnet.md).
* Pour des révisions vidéo, utilisez le [guide de démarrage rapide pour révision vidéo](video-reviews-quickstart-dotnet.md), et apprenez à [ajouter des transcriptions à la révision vidéo](video-transcript-reviews-quickstart-dotnet.md).
