---
title: 'Démarrage rapide : Exécuter le SDK Speech Devices sur Windows - Services Speech'
titleSuffix: Azure Cognitive Services
description: Prérequis et instructions pour bien démarrer avec le SDK Windows Speech Devices.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: erhopf
ms.openlocfilehash: ac0ed218aa27a36b3b8cd8ed8123e2baef6948c6
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723291"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-windows"></a>Démarrage rapide : Exécuter l’exemple d’application du SDK Speech Devices sur Windows

Dans ce guide de démarrage rapide, vous allez découvrir comment utiliser le SDK Speech Devices pour Windows pour créer un produit avec reconnaissance vocale ou pour l’utiliser comme appareil de [transcription de conversation](conversation-transcription-service.md). Actuellement, seul [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) est pris en charge.

L’application est créée avec le package Speech SDK et l’IDE Eclipse Java (v4) sur Windows 64 bits. Elle s’exécute sur un environnement d’exécution Java 8 (JRE) 64 bits.

Ce guide nécessite un compte [Azure Cognitive Services](get-started.md) avec une ressource des services Speech. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/) pour obtenir une clé d’abonnement.

Le code source de l’[exemple d’application](https://aka.ms/sdsdk-download-JRE) est inclus avec le SDK Speech Devices. Il est également [disponible sur GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* Système d’exploitation : Windows 64 bits
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [IDE Eclipse Java](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) uniquement.
* [Redistribuable Microsoft Visual C++](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Clé d’abonnement Azure pour le service Speech. [Vous pouvez en obtenir une gratuitement](get-started.md).
* Téléchargez la dernière version du [SDK Speech Devices](https://aka.ms/sdsdk-download-JRE) pour Java et décompressez le fichier .zip dans votre répertoire de travail.
   > [!NOTE]
   > Le fichier JRE-Sample-Release.zip inclut l’exemple d’application JRE ; ce guide de démarrage rapide part du principe que l’application est extraite dans C:\SDSDK\JRE-Sample-Release

Transcription de conversation est actuellement disponible seulement pour « en-US » et « zh-CN » dans les régions « USA Centre » et « Asie Est ». Vous devez disposer d’une clé de reconnaissance vocale dans une de ces régions pour utiliser Transcription de conversation.

Si vous prévoyez d’utiliser les intentions, vous aurez besoin d’un abonnement [Language Understanding Intelligent Service (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription). Pour plus d’informations sur LUIS et la reconnaissance des intentions, consultez [Reconnaître les intentions vocales avec LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). Un [exemple de modèle LUIS](https://aka.ms/sdsdk-luis) est disponible pour cette application.

## <a name="create-and-configure-the-project"></a>Créer et configurer le projet

1. Démarrez Eclipse.

1. Dans le champ **Workspace** (Espace de travail) de **Eclipse IDE Launcher**, entrez le nom d’un nouveau répertoire d’espace de travail. Sélectionnez ensuite **Launch** (Lancer).

   ![Capture d’écran d’Eclipse Launcher](media/speech-devices-sdk/eclipse-launcher.png)

1. La fenêtre principale de l’IDE Eclipse apparaît au bout d’un instant. Fermez l’écran d’accueil s’il en existe un.

1. Dans la barre de menus Eclipse, créez un projet en choisissant **File (Fichier)**  > **New (Nouveau)**  > **Java Project (Projet Java)** . Si ce n’est pas disponible, choisissez **Project** (Projet), puis **Java Project** (Projet Java).

1. L’Assistant **New Java Project** (Nouveau projet Java) démarre. **Accédez**  à l’emplacement de l’exemple de projet. Sélectionnez **Terminer**.

   ![Capture d’écran de l’Assistant New Java Project (Nouveau projet Java)](media/speech-devices-sdk/eclipse-new-java-project.png)

1. Dans **Package explorer** (l’Explorateur de package), cliquez avec le bouton droit sur votre projet. Choisissez **Configure (Configurer)**  > **Convert to Maven Project (Convertir en projet Maven)** dans le menu contextuel. Sélectionnez **Terminer**.

   ![Capture d’écran de l’explorateur de package](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Copiez `kws.table`, `participants.properties` et `Microsoft.CognitiveServices.Speech.extension.pma.dll` dans le dossier **target/classes** du projet

## <a name="configure-the-sample-application"></a>Configurer l’exemple d’application

1. Ajoutez votre clé d’abonnement Speech au code source. Si vous voulez essayer la reconnaissance de l’intention, ajoutez également votre clé d’abonnement au [service Language Understanding](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), ainsi que l’ID de l’application.

   Pour la reconnaissance vocale et LUIS, vos informations vont dans `FunctionsList.java` :

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Si vous utilisez la transcription de conversation, vos informations de clé et de région de reconnaissance vocale sont également nécessaires dans `Cts.java` :

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Le mot signifiant (mot clé) par défaut est « Computer ». Vous pouvez également essayer l’un des autres mots signifiants fournis, tels que « Machine » ou « Assistant ». Les fichiers de ressources pour ces mots alternatifs figurent dans le dossier keyword du Kit de développement logiciel (SDK) Speech Devices. Par exemple, `C:\SDSDK\JRE-Sample-Release\keyword\Computer` contient les fichiers utilisés pour le mot de sortie de veille « Computer ».

   > [!TIP]
   > Vous pouvez également [créer un mot signifiant personnalisé](speech-devices-sdk-create-kws.md).

    Pour utiliser un nouveau mot de sortie de veille, mettez à jour les deux lignes suivantes dans `FunctionsList.java` et copiez le package de mots de sortie de veille dans votre application. Par exemple, pour utiliser le mot de sortie de veille « Machine » du package de mots de sortie de veille `kws-machine.zip` :

   * Copiez le package de mots de sortie de veille dans le dossier **target/classes** du projet.

   * Mettez à jour le fichier `FunctionsList.java` avec le mot clé et le nom du package :

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Exécuter l’exemple d’application à partir d’Eclipse

1. Dans la barre de menu Eclipse, sélectionnez **Run (Exécuter)**  > **Run As (Exécuter en tant que)**  > **Java Application (Application Java)** . Sélectionnez ensuite **FunctionsList**, puis **OK**.

   ![Capture d’écran de la sélection de Application Java](media/speech-devices-sdk/eclipse-run-sample.png)

1. L’exemple d’application du Kit de développement logiciel (SDK) Speech Devices démarre et affiche les options suivantes :

   ![Exemple d’application et options du Kit de développement logiciel (SDK) Speech Devices](media/speech-devices-sdk/java-sample-app-windows.png)

1. Essayez la nouvelle démonstration de **Transcription de conversation**. Démarrez la transcription avec **Session** > **Démarrer**. Par défaut, tout le monde est un invité. Cependant, si vous avez des signatures vocales des participants, vous pouvez les placer dans un fichier `participants.properties` dans le dossier **target/classes** du projet. Pour générer la signature vocale, consultez [Transcrire des conversations (SDK)](how-to-use-conversation-transcription-service.md).

   ![Application de démonstration de Transcription de conversation](media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Créer et exécuter une application autonome

1. Dans **Package explorer** (l’Explorateur de package), cliquez avec le bouton droit sur votre projet. Choisissez **Export** (Exporter). 

1. La fenêtre **Export** (Exporter) apparaît. Développez **Java**, sélectionnez **Runnable JAR file** (Fichier JAR exécutable), puis sélectionnez **Suivant**.

   ![Capture d’écran de la fenêtre Export (Exporter)](media/speech-devices-sdk/eclipse-export-windows.png) 

1. La fenêtre **Runnable JAR File Export** (Fichier JAR exécutable) apparaît. Choisissez une **Export destination** (Destination de l’exportation) pour l’application, puis sélectionnez **Finish** (Terminer).
 
   ![Capture d’écran de l’exportation du fichier JAR exécutable](media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Placez `kws.table`, `participants.properties`, `unimic_runtime.dll`, `pma.dll` et `Microsoft.CognitiveServices.Speech.extension.pma.dll` dans le dossier de destination sélectionné ci-dessus, car ces fichiers sont nécessaires à l’application.

1. Pour exécuter l’application autonome

     ```powershell
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Examiner les notes de publication](devices-sdk-release-notes.md)
