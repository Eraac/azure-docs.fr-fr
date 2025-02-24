---
title: 'Démarrage rapide : Générer, déployer et utiliser un modèle personnalisé - Custom Translator'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez être guidé pas à pas dans le processus de création d’un système de traduction avec Custom Translator.
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: quickstart
ms.openlocfilehash: 8f39abd41534ec470b5b4b6f232a556a8c8de5b9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449471"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Démarrage rapide : Générer, déployer et utiliser un modèle personnalisé pour la traduction

Cet article fournit des instructions détaillées permettant de créer un système de traduction avec Custom Translator.

## <a name="prerequisites"></a>Prérequis

1. Pour utiliser le portail [Custom Translator](https://portal.customtranslator.azure.ai), vous devez avoir un [compte Microsoft](https://signup.live.com) ou un [compte Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (compte d’organisation hébergé sur Azure) pour vous connecter.

2. Un abonnement à l’API de traduction de texte Translator Text via le portail Azure. Vous devez avoir la clé d’abonnement de l’API de traduction de texte Translator Text à associer à votre espace de travail dans Custom Translator. Consultez [Guide pratique pour s’inscrire à l’API de traduction de texte Translator Text](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup).

3. Une fois que vous avez les deux éléments ci-dessus, connectez-vous au portail [Custom Translator](https://portal.customtranslator.azure.ai). Dans le portail Custom Translator, accédez à la page des paramètres où vous pouvez associer votre clé d’abonnement de l’API de traduction de texte Translator Text avec votre espace de travail.

## <a name="create-a-project"></a>Création d’un projet

Dans la page d’accueil du portail Custom Translator, cliquez sur New project (Nouveau projet). Dans la boîte de dialogue, vous pouvez entrer le nom de votre choix pour votre projet, la paire de langues, la catégorie, ainsi que d’autres champs appropriés. Ensuite, enregistrez votre projet. Pour plus d’informations, visitez [Créer un projet](how-to-create-project.md).

![Créer un projet](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Chargement de documents

Ensuite, chargez les jeux de documents d’[entraînement](training-and-model.md#training-dataset-for-custom-translator), de [paramétrage](training-and-model.md#tuning-dataset-for-custom-translator) et de [test](training-and-model.md#testing-dataset-for-custom-translator). Vous pouvez charger à la fois des documents [parallèles](what-are-parallel-documents.md) et variés. Vous pouvez également charger un [dictionnaire](what-is-dictionary.md).

Vous pouvez charger des documents à partir de l’onglet des documents ou de la page propre à un projet.

![Chargement de documents](media/quickstart/ct-how-to-upload.png)

Lors du chargement des documents, choisissez le type de document (entraînement, paramétrage ou test) et la paire de langues. Lors du chargement des documents parallèles, vous devez également spécifier un nom de document. Pour plus d’informations, visitez [Charger un document](how-to-upload-document.md).

## <a name="create-a-model"></a>Création d’un modèle

Lorsque vous avez chargé tous les documents qu’il vous faut, l’étape suivante consiste à créer votre modèle.

Sélectionnez le projet que vous avez créé. Vous voyez tous les documents que vous avez chargés qui partagent une paire de langues avec ce projet. Sélectionnez les documents que vous souhaitez inclure dans votre modèle. Vous pouvez sélectionner des données d’[entraînement](training-and-model.md#training-dataset-for-custom-translator), de [paramétrage](training-and-model.md#tuning-dataset-for-custom-translator) et de [test](training-and-model.md#testing-dataset-for-custom-translator), ou sélectionner juste les données d’entraînement et laisser Custom Translator générer automatiquement les jeux de paramétrage et de test pour votre modèle.

![Création d’un modèle](media/quickstart/ct-how-to-train.png)

Quand vous avez fini de sélectionner les documents de votre choix, cliquez sur le bouton Créer un modèle pour créer votre modèle et commencer à l’entraîner. Vous pouvez voir l’état de votre entraînement et les détails de tous les modèles que vous avez entraînés sous l’onglet Modèles.

Pour plus d’informations, consultez [Créer un modèle](how-to-train-model.md).

## <a name="analyze-your-model"></a>Analyser votre modèle

Une fois que vous avez fini votre entraînement, examinez les résultats. Le score BLEU est une métrique qui indique la qualité de votre traduction. Vous pouvez aussi comparer manuellement les traductions effectuées à l’aide de votre modèle personnalisé avec les traductions fournies dans votre série de tests en accédant à l’onglet « Test » et en cliquant sur « System Results » (Résultats système). En inspectant manuellement quelques-unes de ces traductions, vous aurez une bonne idée de la qualité de traduction produite par votre système. Pour plus d’informations, consultez [Résultats des tests système](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>Déployer un modèle entraîné

Lorsque vous êtes prêt à déployer votre modèle entraîné, cliquez sur le bouton « Deploy » (Déployer). Vous pouvez avoir un modèle déployé par projet et voir l’état de votre déploiement dans la colonne Status (État). Pour plus d’informations, consultez [Modèle de déploiement](how-to-view-system-test-results.md#deploy-a-model)

![Déployer un modèle entraîné](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Utiliser un modèle déployé

Les modèles déployés sont accessibles via l’[API de traduction de texte Translator Text V3 de Microsoft en spécifiant le CategoryID](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl). Vous trouverez plus d’informations sur l’API de traduction de texte Translator Text dans la page web [Informations de référence sur les API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference).

## <a name="next-steps"></a>Étapes suivantes

- Apprenez à naviguer dans l’[espace de travail Custom Translator et gérer vos projets](workspace-and-project.md).
