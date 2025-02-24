---
title: 'Définir le flux de travail de modération avec la console d’API REST : Content Moderator'
titlesuffix: Azure Cognitive Services
description: Vous pouvez utiliser les API de révision Azure Content Moderator pour définir des flux de travail personnalisés et des seuils en fonction de vos stratégies de contenu.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: e150b1321f2fbd348e737222c752203281503643
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60605901"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Définir et utiliser des flux de travail de modération (REST)

Les flux de travail sont des filtres personnalisés basés sur le cloud qui vous permettent de gérer plus efficacement le contenu. Flux de travail peut se connecter à une variété de services pour filtrer le contenu de différentes façons et puis exécutez l’action appropriée. Ce guide vous montre comment utiliser les API REST de flux de travail via la console d’API pour créer et utiliser des flux de travail. Une fois que vous comprenez la structure des API, vous pouvez facilement déplacer ces appels vers n’importe quelle plateforme compatible avec REST.

## <a name="prerequisites"></a>Prérequis

- Connectez-vous ou créez un compte sur le site de l’[outil de révision](https://contentmoderator.cognitive.microsoft.com/) de Content Moderator.

## <a name="create-a-workflow"></a>Créer un workflow

Pour créer ou mettre à jour un flux de travail, accédez à la page de référence de l’API **[Flux de travail : créer ou mettre à jour](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** et sélectionnez le bouton de votre région clé (que vous pouvez trouver dans l’URL de point de terminaison à la page **Informations d’identification** de l’[Outil de révision](https://contentmoderator.cognitive.microsoft.com/)). Ceci fait démarrer la console d’API, où vous pouvez facilement construire et exécuter des appels d’API REST.

![Sélection de la région sur la page Flux de travail - Créer ou mettre à jour](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Entrez les paramètres d’appel REST

Entrez les valeurs de **team**, **workflowname** et **Ocp-Apim-Subscription-Key** :

- **team** : ID d’équipe que vous avez créé lors de la configuration de votre compte [Outil de révision](https://contentmoderator.cognitive.microsoft.com/) compte (il se trouve dans le champ **Id** sur l’écran des informations d’identification de votre outil de révision).
- **workflowname** : nom d’un nouveau flux de travail à ajouter (ou un nom existant si vous souhaitez mettre à jour un flux de travail existant).
- **Ocp-Apim-Subscription-Key** : votre clé Content Moderator Vous pouvez la trouver sous l’onglet **Paramètres** de l’[Outil de révision](https://contentmoderator.cognitive.microsoft.com).

![Paramètres de requête et en-têtes de la console Flux de travail - Créer ou mettre à jour](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Saisir une définition du flux de travail

1. Modifiez la zone **Corps de la requête** pour entrer la demande en JSON avec des détails pour la **description** et le **type** (`Image` ou `Text`).
2. Pour **Expression**, copiez l’expression JSON du flux de travail par défaut. La chaîne JSON finale devrait ressembler à ceci :

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Vous pouvez définir des expressions simples, complexes et même imbriquées pour vos flux de travail à l’aide de cette API. La documentation [Flux de travail - Créer ou mettre à jour](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) contient des exemples d’une logique plus complexe.

### <a name="submit-your-request"></a>Soumettre votre requête
  
Sélectionnez **Envoyer**. Si l’opération réussit, l’**État de la réponse** est `200 OK` et la zone **Contenu de la réponse** affiche `true`.

### <a name="examine-the-new-workflow"></a>Examiner le nouveau flux de travail

Dans l’[outil de révision](https://contentmoderator.cognitive.microsoft.com/), sélectionnez **Paramètres** > **Flux de travail**. Votre nouveau flux de travail doit apparaître dans la liste.

![Liste des flux de travail de l’outil de révision](images/workflow-console-new-workflow.PNG)

Sélectionnez l’option **Modifier** pour votre flux de travail et accédez à l’onglet **JSON**. Ici, vous pouvez voir une représentation intuitive de la logique JSON.

![Onglet Concepteur pour un flux de travail sélectionné](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Obtenir les détails des flux de travail

Pour récupérer des informations sur un flux de travail existant, accédez à la page de référence d’API **[Flux de travail - Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** et sélectionnez le bouton de votre région (la région dans laquelle votre clé est administrée).

![Flux de travail - Sélection de région](images/test-drive-region.png)

Entrez les paramètres d’appel REST, comme dans la section ci-dessus. Assurez-vous que cette fois, **workflowname** est le nom d’un flux de travail existant.

![Obtenir les paramètres de requête et les en-têtes](images/workflow-get-default.PNG)

Sélectionnez **Envoyer**. Si l’opération réussit, l’**État de la réponse** est `200 OK` et la zone **Contenu de la réponse** affiche la définition de flux de travail au format JSON, comme suit :

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment utiliser des flux de travail avec des [travaux de modération du contenu](try-review-api-job.md).