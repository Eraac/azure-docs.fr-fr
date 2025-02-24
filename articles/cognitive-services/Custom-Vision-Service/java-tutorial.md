---
title: 'Démarrage rapide : Créer un projet de classification d’images à l’aide du kit SDK Custom Vision pour Java'
titlesuffix: Azure Cognitive Services
description: Créez un projet, ajoutez des balises, chargez des images, effectuez l’apprentissage de votre projet ainsi qu’une prédiction avec le kit de développement logiciel (SDK) Java.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: areddish
ms.openlocfilehash: 62f60e0a143bee7e8392792719b30aca1d629880
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606852"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>Démarrage rapide : Créer un projet de classification d’images à l’aide du kit SDK Custom Vision pour Java

Cet article fournit des informations et un exemple de code pour vous aider à prendre en main le kit de développement logiciel (SDK) de Vision personnalisée avec Java, afin de générer un modèle de classification d’images. Après la création du projet, vous pouvez ajouter des mots clés, charger des images, entraîner le projet, obtenir l’URL du point de terminaison de prédiction par défaut du projet et utiliser ce point de terminaison pour tester par programmation une image. Utilisez cet exemple comme modèle pour générer votre propre application Java. Si vous voulez générer et utiliser un modèle de classification _sans_ code, consultez le [guide basé sur navigateur](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Prérequis

- Un IDE Java de votre choix
- [JDK 7 ou 8](https://aka.ms/azure-jdks) est installé.
- Maven est installé

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obtenir le Kit de développement logiciel (SDK) Custom Vision et un exemple de code

Pour écrire une application Java qui utilise Custom Vision, vous avez besoin des packages maven Custom Vision. Ces packages sont inclus dans l’exemple de projet que vous allez télécharger, mais vous pouvez y accéder individuellement ici.

Vous pouvez installer le SDK Vision personnalisée à partir du référentiel central Maven :

- [SDK d’entraînement](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [SDK de prédiction](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Clonez ou téléchargez le projet [Cognitive Services Java SDK Samples](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master) (Exemples de Kit de développement logiciel (SDK) Java Cognitive Services). Accédez au dossier **Vision/CustomVision/** .

Ce projet Java crée un projet de classification d’images Custom Vision nommé __Sample Java Project__ (Exemple de projet Java), accessible via le [site Custom Vision](https://customvision.ai/). Elle charge ensuite les images pour entraîner et tester un classifieur. Dans ce projet, le classifieur a pour but de déterminer si l’arbre est une __cigüe__ ou un __cerisier du Japon__.

[!INCLUDE [get-keys](includes/get-keys.md)]

Le programme est configuré de manière à stocker vos données de clé en tant que variables d’environnement. Définissez ces variables en accédant au dossier **Vision/CustomVision** dans PowerShell. Entrez ensuite les commandes :

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Comprendre le code

Chargez le projet `Vision/CustomVision` dans votre IDE Java, puis ouvrez le fichier _CustomVisionSamples.java_. Recherchez la méthode **runSample** et commentez l’appel de méthode **ObjectDetection_Sample**. Cette méthode exécute le scénario de détection d’objet, qui n’est pas abordé dans ce guide. La méthode **ImageClassification_Sample** implémente la fonctionnalité principale de cet exemple ; accédez à sa définition et examinez le code.

### <a name="create-a-custom-vision-service-project"></a>Créer un projet Service Vision personnalisée

Ce premier bit de code crée un projet de classification d’images. Le projet créé apparaît sur le [site web Custom Vision](https://customvision.ai/) sur lequel vous êtes allé plus tôt. 

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=57-63)]

### <a name="create-tags-in-the-project"></a>Créer des balises dans un projet

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=65-74)]

### <a name="upload-and-tag-images"></a>Charger et étiqueter des images

Les exemples d’images sont inclus dans le dossier **src/main/resources** du projet. Ils y sont lus puis sont chargés dans le service avec les balises appropriées.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=76-87)]

L’extrait de code précédent utilise deux fonctions d’assistance qui récupèrent les images comme flux de ressources et les charge dans le service.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=277-314)]

### <a name="train-the-classifier-and-publish"></a>Entraîner le classifieur et publier

Ce code crée la première itération dans le projet, puis la publie sur le point de terminaison de prédiction. Le nom donné à l’itération publiée peut être utilisé pour envoyer des requêtes de prédiction. Les itérations ne sont pas disponibles sur le point de terminaison de prédiction tant qu’elles n’ont pas été publiées.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=89-99)]

### <a name="use-the-prediction-endpoint"></a>Utiliser le point de terminaison de prédiction

Le point de terminaison de prédiction, représenté par l’objet `predictor` ici, est la référence que vous pouvez utiliser pour soumettre une image au modèle actuel et obtenir une prédiction de classification. Dans cet exemple, `predictor` est défini ailleurs à l’aide de la variable d’environnement de la clé de prédiction.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=108-120)]

## <a name="run-the-application"></a>Exécution de l'application

Pour compiler et exécuter la solution à l’aide de maven, exécutez la commande suivante dans le répertoire du projet dans PowerShell :

```powershell
mvn compile exec:java
```

Le résultat de la console de l’application ressemble au texte suivant :

```console
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Vous pouvez alors vérifier que la prédiction d’image test (les dernières lignes du résultat) est correcte.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous savez maintenant comment effectuer la classification d’images dans le code. Cet exemple exécute une itération d’apprentissage unique, mais vous aurez souvent besoin d’entraîner et de tester votre modèle à plusieurs reprises pour plus de précision.

> [!div class="nextstepaction"]
> [Tester et réentraîner un modèle](test-your-model.md)
