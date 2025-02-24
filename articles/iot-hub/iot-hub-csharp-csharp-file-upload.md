---
title: Charger des fichiers sur Azure IoT Hub à partir d’appareils avec .NET | Microsoft Docs
description: Comment charger des fichiers sur le cloud à partir d’un appareil avec Azure IoT device SDK pour .NET. Les fichiers téléchargés sont stockés dans un conteneur d’objets blob de stockage Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.openlocfilehash: 79288f2204030790b2308905d90ff8e035fe2dd9
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621863"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>Charger des fichiers sur le cloud à partir d’un appareil avec IoT Hub en utilisant .NET

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Ce tutoriel s’appuie sur le code du tutoriel [Envoyer des messages cloud-à -appareil avec IoT Hub](iot-hub-csharp-csharp-c2d.md) pour vous montrer comment utiliser les fonctions de chargement de fichier d’IoT Hub. Cette rubrique vous explique les procédures suivantes :

* Fournissez en toute sécurité à un appareil un URI d’objet blob Azure pour le chargement d’un fichier.

* Utilisez les notifications de chargement de fichier IoT Hub pour déclencher le traitement du fichier dans votre serveur principal d’application.

Le guide de démarrage rapide [Envoyer des données de télémétrie à partir d’un appareil à un hub IoT](quickstart-send-telemetry-dotnet.md) et le tutoriel [Envoyer des messages cloud-à-appareil avec IoT Hub](iot-hub-csharp-csharp-c2d.md) illustrent les fonctionnalités de messages appareil-à-cloud et cloud-à-appareil de base offertes par IoT Hub. Le tutoriel [Configurer le routage des messages avec IoT Hub](tutorial-routing.md) décrit un moyen de stocker en toute fiabilité les messages appareil-à-cloud dans le stockage d’objets blob Azure. Toutefois, dans certains scénarios, vous ne pouvez pas facilement mapper les données que vos appareils envoient dans des messages appareil-à-cloud relativement petits et acceptés par IoT Hub. Par exemple :

* Fichiers volumineux qui contiennent des images
* Videos
* Données de vibration échantillonnées à une fréquence élevée
* Un certain type de données prétraitées

Ces fichiers sont généralement traités par lot dans le cloud à l’aide d’outils tels que [Azure Data Factory](../data-factory/introduction.md) ou de la pile [Hadoop](../hdinsight/index.yml). Si vous avez besoin de charger des fichiers à partir d’un appareil, vous pouvez toujours exploiter la sécurité et la fiabilité d’IoT Hub.

À la fin de ce didacticiel, vous exécutez deux applications console .NET :

* **SimulatedDevice**, version modifiée de l’application créée dans le cadre du tutoriel [Envoyer des messages cloud-à-appareil avec IoT Hub](iot-hub-csharp-csharp-c2d.md). Cette application charge un fichier de stockage à l’aide d’un URI SAP fourni par votre IoT Hub.

* **ReadFileUploadNotification**, qui reçoit les notifications de téléchargement de fichier à partir de votre IoT Hub.

> [!NOTE]
> IoT Hub offre la prise en charge de plusieurs plateformes d’appareils et plusieurs langages (notamment C, Java et Javascript) par le biais des Kits de développement logiciel (SDK) d’appareils Azure IoT. Pour obtenir des instructions pas à pas expliquant comment connecter votre appareil à Azure IoT Hub, voir le [Centre de développement Azure IoT](https://azure.microsoft.com/develop/iot).

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Visual Studio

* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes seulement.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Charger un fichier à partir d’une application d’appareil

Dans cette section, vous allez modifier l’application d’appareil créée dans [Envoyer des messages cloud-à-appareil avec IoT Hub](iot-hub-csharp-csharp-c2d.md) pour recevoir des messages cloud-à-appareil en provenance du hub IoT.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **SimulatedDevice**, cliquez sur **Ajouter**, puis sur **Élément existant**. Accédez à un fichier image et ajoutez-le à votre projet. Ce didacticiel suppose que l’image a pour nom `image.jpg`.

1. Cliquez avec le bouton droit sur l’image, puis cliquez sur **Propriétés**. Assurez-vous que l’option **Copier dans le répertoire de sortie** est définie sur **Toujours copier**.

    ![Indiquer où mettre à jour la propriété de l’image pour Copier dans le répertoire de sortie](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. Au début du fichier **Program.cs** , ajoutez les instructions suivantes :

    ```csharp
    using System.IO;
    ```

1. Ajoutez la méthode suivante à la classe **Program** :

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    La méthode `UploadToBlobAsync` utilise le nom de fichier et la source de flux du fichier à télécharger et gère le téléchargement vers le stockage. L’application console affiche le temps nécessaire pour télécharger le fichier.

1. Ajoutez la méthode suivante à la méthode **Main** juste avant la ligne `Console.ReadLine()` :

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article [Gestion des erreurs temporaires](/azure/architecture/best-practices/transient-faults).

## <a name="receive-a-file-upload-notification"></a>Recevoir une notification de téléchargement de fichier

Dans cette section, vous allez écrire une application console .NET qui reçoit des messages de notification de téléchargement de fichier à partir d’IoT Hub.

1. Dans la solution Visual Studio actuelle, créez un projet Windows Visual C# à l’aide du modèle de projet **d’application de console** . Nommez le projet **ReadFileUploadNotification**.

    ![Nouveau projet dans Visual Studio](./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png)

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **ReadFileUploadNotification**, puis cliquez sur **Gérer les packages NuGet...** .

3. Dans la fenêtre **Gestionnaire de package NuGet**, recherchez **Microsoft.Azure.Devices**, cliquez sur **Installer** et acceptez les conditions d’utilisation.

    Cette action a pour effet de télécharger, d’installer et d’ajouter une référence au [Package NuGet du SDK service IoT Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/) dans le projet **ReadFileUploadNotification**.

4. Au début du fichier **Program.cs** , ajoutez les instructions suivantes :

    ```csharp
    using Microsoft.Azure.Devices;
    ```

5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur d’espace réservé par la chaîne de connexion du hub IoT issue de la procédure [Envoyer des données de télémétrie à partir d’un appareil à un hub IoT](quickstart-send-telemetry-dotnet.md) :

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

6. Ajoutez la méthode suivante à la classe **Program** :

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Notez que ce modèle de réception est le même que celui utilisé pour recevoir des messages cloud-à-appareil à partir de l’application de l’appareil.

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1. Dans Visual Studio, cliquez avec le bouton droit sur votre solution et sélectionnez **Définir les projets de démarrage**. Sélectionnez **Plusieurs projets de démarrage**, puis sélectionnez l’action **Démarrer** pour **ReadFileUploadNotification** et **SimulatedDevice**.

2. Appuyez sur **F5**. Les deux applications doivent démarrer. Vous devriez voir le téléchargement terminé dans une application console et le message de notification de téléchargement reçus par l’autre application console. Vous pouvez utiliser le [portail Azure](https://portal.azure.com/) ou l’Explorateur de serveurs Visual Studio pour vérifier la présence du fichier chargé dans votre compte Azure Storage.

    ![Capture d’écran montrant l’écran de sortie](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser les fonctionnalités de téléchargement de fichier d’IoT Hub pour simplifier les chargements de fichiers à partir d’appareils. Vous pouvez continuer à explorer les scénarios et fonctionnalités d’IoT Hub avec les articles suivants :

* [Créer un IoT Hub par programmation](iot-hub-rm-template-powershell.md)

* [Présentation du SDK C](iot-hub-device-sdk-c-intro.md)

* [Kits de développement logiciel (SDK) Azure IoT](iot-hub-devguide-sdks.md)

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Déploiement d’une IA sur des appareils de périmètre avec Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
