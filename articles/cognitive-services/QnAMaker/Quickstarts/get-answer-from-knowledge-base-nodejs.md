---
title: 'Démarrage rapide : Obtenir des réponses d’une base de connaissances - REST, Node.js - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide basé sur REST Node.js vous aide à obtenir programmatiquement une réponse à partir d’une base de connaissances.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 389d6ed369de513125a2bcd0d0de881f524dcd82
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791542"
---
# <a name="get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>Obtenir des réponses à une question à partir d’une base de connaissance avec Node.js

Ce guide de démarrage rapide vous aide à obtenir programmatiquement une réponse à partir d’une base de connaissances QnA Maker publiée. La base de connaissances contient des questions et réponses de [sources de données](../Concepts/data-sources-supported.md) telles que des FAQ. La [question](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) est envoyée au service QnA Maker. La [réponse](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclut la réponse la plus prévisible. 

## <a name="prerequisites"></a>Prérequis

* [Node.JS](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Vous devez disposer d’un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pour récupérer votre clé, sélectionnez **Clés** sous **Gestion des ressources** dans votre tableau de bord Azure pour votre ressource QnA Maker. 
* Paramètres de la page **Publier**. Si vous ne disposez pas d’une base de connaissances publiée, créez une base de connaissances vide, importez une base de connaissances sur la page **Paramètres**, puis publiez. Vous pouvez télécharger et utiliser [cette base de connaissances](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv). 

    Les paramètres de la page de publication incluent la valeur de route POST, la valeur d’hôte et la valeur EndpointKey. 

    ![Paramètres de publication](../media/qnamaker-quickstart-get-answer/publish-settings.png)

Le code associé à ce guide de démarrage rapide est disponible dans le dépôt [https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/get-answer). 

## <a name="create-a-nodejs-file"></a>Créer un fichier Node.js

Ouvrez VSCode et créez un fichier nommé `get-answer.js`. 

## <a name="add-the-required-dependencies"></a>Ajouter les dépendances nécessaires

En haut du fichier `get-answer.js`, ajoutez les dépendances nécessaires au projet :

[!code-nodejs[Add the required dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=1-4 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Ajouter les constantes nécessaires

Ajoutez ensuite les constantes nécessaires pour accéder à QnA Maker. Ces valeurs se trouvent dans la page **Publier** une fois la base de connaissances publiée. 

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=6-22 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>Ajouter une requête POST pour envoyer une question et obtenir une réponse

Le code suivant adresse une requête HTTPS à l’API QnA Maker afin d’envoyer la question à la base de connaissances et reçoit la réponse :

[!code-nodejs[Add a POST request to send question to knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=24-49 "Add a POST request to send question to knowledge base")]

La valeur de l’en-tête `Authorization` inclut la chaîne `EndpointKey`. 

## <a name="install-the-dependencies"></a>Installer les dépendances

Installez les dépendances à partir de la ligne de commande :

```bash
npm install request request-promise
```

## <a name="run-the-program"></a>Exécuter le programme

Exécutez le programme à partir de la ligne de commande. Il envoie automatiquement la requête à l’API QnA Maker, puis affiche la réponse dans la fenêtre de console.

Exécutez le fichier :

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

En savoir plus sur la [requête](../how-to/metadata-generateanswer-usage.md#generateanswer-request) et la [réponse](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
