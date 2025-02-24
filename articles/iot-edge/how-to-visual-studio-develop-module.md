---
title: Développer et déboguer des modules dans Visual Studio – Azure IoT Edge | Microsoft Docs
description: Utiliser Visual Studio 2019 pour développer et déboguer des modules pour Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/01/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 953a549a44d224f9d196c6d6842bdcae04cca975
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485913"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Utiliser Visual Studio 2019 pour développer et déboguer des modules pour Azure IoT Edge

Vous pouvez transformer votre logique métier en modules pour Azure IoT Edge. Cet article vous explique comment utiliser Visual Studio 2019 comme outil principal pour développer et déboguer des modules.

Azure IoT Edge Tools pour Visual Studio offre les avantages suivants :

- Créez, modifiez, générez, exécutez et déboguez des solutions et des modules Azure IoT Edge sur votre ordinateur de développement local.
- Déployez votre solution Azure IoT Edge sur l’appareil Azure IoT Edge via Azure IoT Hub.
- Codez vos modules Azure IoT en C ou C# tout en bénéficiant de tous les avantages du développement Visual Studio.
- Gérez les appareils et les modules Azure IoT Edge avec une interface utilisateur.

Cet article vous explique comment utiliser Azure IoT Edge Tools pour Visual Studio 2019 pour développer vos modules IoT Edge. Vous y découvrez aussi comment déployer votre projet sur votre appareil Azure IoT Edge.

> [!TIP]
> La structure de projet IoT Edge créée par Visual Studio n’est pas la même que dans Visual Studio Code.
  
## <a name="prerequisites"></a>Prérequis

Cet article part du principe que vous utilisez un ordinateur ou une machine virtuelle Windows comme machine de développement. Vous pouvez utiliser un autre appareil physique comme appareil IoT Edge.

Comme cet article utilise Visual Studio 2019 comme outil de développement principal, installez Visual Studio. Veillez à inclure les charges de travail **Développement Azure** et de **Développement Desktop en C++** lorsque vous installez Visual Studio 2019. Vous pouvez [modifier Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) pour ajouter les charges de travail requises.

Une fois votre Visual Studio 2019 prêt, vous avez également besoin des outils et composants suivants :

- Téléchargez et installez les [outils Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) à partir de la place de marché Visual Studio pour créer un projet IoT Edge dans Visual Studio 2019.

> [!TIP]
> Si vous utilisez Visual Studio 2017, téléchargez et installez [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) pour VS 2017 à partir de la place de marché Visual Studio

- Téléchargez et installez [Docker Community Edition](https://docs.docker.com/install/) sur votre machine de développement pour générer et exécuter vos images de module. Vous devez configurer Docker CE pour qu’il s’exécute en mode conteneur Linux ou conteneur Windows.

- Configurez votre environnement de développement local pour le débogage, l’exécution et le test de votre solution IoT Edge en installant l’[outil de développement Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Installez [Python (2.7/3.6) et Pip ](https://www.python.org/), puis installez le package **iotedgehubdev** en exécutant la commande suivante sur votre terminal. Vérifiez que votre version de l’outil de développement Azure IoT pour EdgeHub est supérieure à 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Clonez le référentiel et installez le Gestionnaire de bibliothèque Vcpkg, puis installez le **package azure-iot-sdk-c** pour Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) ou [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Vous pouvez utiliser un registre Docker local pour le prototype et à des fins de test, au lieu d’un registre cloud.

- Pour tester votre module sur un appareil, vous devez disposer d’un hub IoT actif avec au moins un appareil IoT Edge. Pour utiliser votre ordinateur comme appareil IoT Edge, suivez les étapes décrites dans le guide de démarrage rapide pour [Linux](quickstart-linux.md) ou [Windows](quickstart.md). Si vous exécutez le démon IoT Edge sur votre machine de développement, il peut être nécessaire d’arrêter EdgeHub et EdgeAgent avant de commencer à développer dans Visual Studio.

### <a name="check-your-tools-version"></a>Vérifier la version des outils

1. Dans le menu **Outils**, sélectionnez **Extensions et mises à jour**. Développez **Installé > Outils** pour trouver **Outils Azure IoT Edge** et **Cloud Explorer pour Visual Studio**.

1. Notez la version installée. Vous pouvez comparer cette version avec la version la plus récente sur Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. Si votre version est antérieure à ce qui est disponible sur Visual Studio Marketplace, mettez à jour vos outils dans Visual Studio, comme indiqué dans la section suivante.

### <a name="update-your-tools"></a>Mettre à jour les outils

1. Dans la boîte de dialogue **Extensions et mises à jour**, développez **Mises à jour > Visual Studio Marketplace**, sélectionnez **Outils Azure IoT Edge** ou **Cloud Explorer pour Visual Studio**, puis choisissez **Mettre à jour**.

1. Une fois la mise à jour des outils téléchargée, fermez Visual Studio pour déclencher la mise à jour des outils à l’aide du programme d’installation VSIX.

1. Dans le programme d’installation, sélectionnez **OK** pour démarrer, puis **Modifier** pour mettre à jour les outils.

1. Une fois la mise à jour terminée, choisissez **Fermer**, puis redémarrez Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Créer un projet Azure IoT Edge

Le modèle de projet Azure IoT Edge dans Visual Studio crée un projet qui peut être déployé sur des appareils Azure IoT Edge dans Azure IoT Hub. Vous commencez par créer une solution Azure IoT Edge, puis vous y générez le premier module. Chaque solution IoT Edge peut contenir plusieurs modules.

1. Dans la nouvelle boîte de dialogue de projet Visual Studio, recherchez et sélectionnez le projet **Azure IoT Edge**, puis cliquez sur **Suivant**. Dans la fenêtre de configuration du projet, entrez le nom et spécifiez l’emplacement du projet, puis sélectionnez **Créer**. Le nom du projet par défaut est **AzureIoTEdgeApp1**.

   ![Créer un projet](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. Dans la fenêtre **Add IoT Edge Application and Module** (Ajouter une application et un module IoT Edge), sélectionnez **Module C#** ou **Module C**, puis spécifiez le nom et le référentiel d’image de votre module. Par défaut, Visual Studio nomme le module **localhost:5000/<nom de votre module\>** . Remplacez-le par vos propres informations de registre. Si vous utilisez un registre Docker local à des fins de test, **localhost** convient. Si vous utilisez Azure Container Registry, utilisez le serveur de connexion à partir des paramètres de votre registre. Le serveur de connexion se présente comme suit : * **\<nom du registre\>*.azurecr.io**. Remplacez uniquement la partie **localhost:5000** de la chaîne afin d’obtenir le résultat final suivant : * *\<* nom du registre *\>.azurecr.io/* \<nom de votre module\>***. Le nom par défaut du module est **IoTEdgeModule1**

   ![Ajouter un application et un module](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Sélectionnez **OK** pour créer la solution Azure IoT Edge avec un module utilisant C# ou C.

Vous disposez maintenant d’un projet **AzureIoTEdgeApp1.Linux.Amd64** ou d’un projet **AzureIoTEdgeApp1.Windows.Amd64**, ainsi que d’un projet **IoTEdgeModule1** dans votre solution. Chaque projet **AzureIoTEdgeApp1** comprend un fichier `deployment.template.json` qui définit les modules que vous voulez générer et déployer pour votre solution IoT Edge, ainsi que les routes entre les modules. La solution par défaut a un module **tempSensor** et un module **IoTEdgeModule1**. Le module **tempSensor** génère des données simulées à destination du module **IoTEdgeModule1**, tandis que le code par défaut dans le module **IoTEdgeModule1** canalise directement les messages reçus vers Azure IoT Hub.

Le projet **IoTEdgeModule1** est une application console .NET Core 2.1 s’il s’agit d’un module en C#. Il contient les fichiers Docker requis dont vous avez besoin pour votre appareil IoT Edge qui s’exécute avec un conteneur Windows ou Linux. Le fichier `module.json` décrit les métadonnées d’un module. Le code du module réel, qui prend comme dépendance le Kit de développement logiciel (SDK) Azure IoT Device, se trouve dans le fichier `Program.cs` ou `main.c`.

## <a name="develop-your-module"></a>Développer votre module

Le code du module par défaut qui est fourni avec la solution se trouve dans **IoTEdgeModule1** > **Program.cs** (pour C#) ou **main.c** (pour C). Le module et le fichier `deployment.template.json` sont définis de manière à vous permettre de générer la solution, de l’envoyer vers votre registre de conteneurs et de la déployer sur un appareil pour commencer les tests, sans avoir à utiliser de code. Le module est conçu pour récupérer les entrées d’une source (dans ce cas, le module **tempSensor** qui simule des données) et les acheminer vers Azure IoT Hub.

Lorsque vous êtes prêt à personnaliser le modèle de module avec votre propre code, utilisez les kits [SDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) pour générer des modules répondant aux besoins des solutions IoT, tels que la sécurité, la gestion des appareils et la fiabilité.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Initialiser iotegehubdev avec une chaîne de connexion d’appareil IoT Edge

1. Copiez la chaîne de connexion d’un appareil IoT Edge à partir de la **Chaîne de connexion principale** dans Visual Studio Cloud Explorer. Veillez ne pas à copier la chaîne de connexion d’un appareil non Edge, car l’icône d’un appareil IoT Edge est différente de celle d’un appareil non Edge.

   ![Copier la chaîne de connexion d’un appareil IoT Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Accédez à **Outils** > **Outils Azure IoT Edge** > **Setup IoT Edge Simulator**, collez la chaîne de connexion, puis cliquez sur **OK**.

   ![Ouvrir la fenêtre Définir la chaîne de connexion Edge](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Entrez la chaîne de connexion de la première étape, puis sélectionnez **OK**.

> [!NOTE]
> Vous ne devez suivre ces étapes qu’une seule fois sur votre ordinateur de développement, car les résultats sont automatiquement appliqués à toutes les solutions Azure IoT Edge suivantes. Vous pouvez suivre cette procédure à nouveau si vous devez changer de chaîne de connexion.

## <a name="build-and-debug-single-module"></a>Générer et déboguer un seul module

En général, vous voulez tester et déboguer chaque module avant de l’exécuter au sein d’une solution entière avec plusieurs modules.

1. Cliquez avec le bouton droit sur **IoTEdgeModule1**, puis, dans le menu contextuel, sélectionnez **Définir en tant que projet de démarrage**.

   ![Définir le projet de démarrage](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Appuyez sur **F5** ou cliquez sur le bouton qui se trouve en dessous pour exécuter le module. La première fois, cela peut prendre 10&ndash;20 secondes.

   ![Exécuter le module](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Si le module a été initialisé avec succès, vous devriez voir démarrer une application console .NET Core.

   ![Module en cours d’exécution](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Si vous développez en C#, définissez un point d’arrêt dans la fonction `PipeMessage()` dans **Program.cs**. Si vous utilisez C, définissez un point d’arrêt dans la fonction `InputQueue1Callback()` dans **main.c**. Vous pouvez ensuite le tester en envoyant un message à l’aide de la commande suivante dans un interpréteur de commandes **Git Bash** ou **WSL Bash**. (Vous ne pouvez pas exécuter la commande `curl` à partir de PowerShell ou d’une invite de commandes.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Déboguer un seul module](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Le point d’arrêt doit être déclenché. Vous pouvez regarder les variables dans la fenêtre **Variables locales** de Visual Studio.

   > [!TIP]
   > Vous pouvez aussi utiliser [PostMan](https://www.getpostman.com/) ou d’autres outils d’API pour envoyer des messages à la place de `curl`.

1. Appuyez sur **Ctrl+F5** ou cliquez sur le bouton Arrêter pour arrêter le débogage.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Générer et déboguer une solution IoT Edge avec plusieurs modules

Une fois que nous avons fini de développer un module, nous pouvons exécuter et déboguer une solution entière avec plusieurs modules.

1. Ajoutez un deuxième module à la solution en cliquant avec le bouton droit sur **AzureIoTETEgege1**, puis en sélectionnant **Ajouter** > **Nouveau module IoT Edge**. Le nom par défaut du deuxième module est **IoTEdgeModule2** et il agit comme un autre module de canal.

1. Ouvrez le fichier `deployment.template.json`. Vous verrez que **IoTEdgeModule2** a été ajouté dans la section **modules**. Remplacez la section **routes** par ce qui suit. Si vous avez personnalisé vos noms de modules, veillez à les mettre à jour pour qu’ils correspondent.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

1. Cliquez avec le bouton droit sur **AzureIoTEdgeApp1**, puis, dans le menu contextuel, sélectionnez **Définir en tant que projet de démarrage**.

1. Créez vos points d’arrêt, puis appuyez sur **F5** pour exécuter et déboguer plusieurs modules simultanément. Vous devriez voir plusieurs fenêtres d’applications console .NET Core, chacune d’elles représentant un module différent.

   ![Déboguer plusieurs modules](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Appuyez sur **Ctrl+F5** ou sélectionnez le bouton Arrêter pour arrêter le débogage.

## <a name="build-and-push-images"></a>Générer et envoyer (push) des images

1. Vérifiez que **AzureIoTEdgeApp1** est le projet de démarrage. Sélectionnez **Déboguer** ou **Publier** comme configuration pour générer vos images de module.

    > [!NOTE]
    > Lorsque vous choisissez **Déboguer**, Visual Studio utilise `Dockerfile.(amd64|windows-amd64).debug` pour générer des images Docker. Ceci inclut le débogueur en ligne de commande .NET Core (VSDBG) dans votre image conteneur lors de sa génération. Pour obtenir des modules IoT Edge prêts pour la production, nous vous recommandons d’utiliser la configuration **Publier** qui utilise `Dockerfile.(amd64|windows-amd64)` sans VSDBG.

1. Si vous utilisez un registre privé tel qu’Azure Container Registry, servez-vous de la commande Docker suivante pour vous y connecter. Si vous utilisez un registre local, vous pouvez [exécuter un registre local](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Si vous utilisez un registre privé tel qu’Azure Container Registry, vous devez ajouter vos informations de connexion au registre aux paramètres de runtime figurant dans le fichier `deployment.template.json`. Remplacez les espaces réservés par le nom d’utilisateur, le mot de passe et le nom de registre de votre administrateur ACR réel.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. Cliquez avec le bouton droit sur **AzureIoTEdgeApp1**, puis sélectionnez **Build and Push Edge Solution** (Générer et envoyer la solution Edge) pour générer et envoyer l’image Docker de chaque module.

   ![Générer et envoyer (push) des images](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>Déployer la solution

Dans l’article de démarrage rapide que vous avez utilisé pour configurer votre appareil IoT Edge, vous avez déployé un module à l’aide du Portail Azure. Vous pouvez également déployer des modules avec Cloud Explorer pour Visual Studio. Vous avez déjà un manifeste de déploiement préparé pour votre scénario, le fichier `deployment.json`. Il vous suffit de sélectionner un appareil pour recevoir le déploiement.

1. Ouvrez **Cloud Explorer** en cliquant sur **Afficher** > **Cloud Explorer**. Assurez-vous que vous êtes connecté à Visual Studio 2019.

1. Dans **Cloud Explorer**, développez votre abonnement, recherchez votre hub Azure IoT et l’appareil Azure IoT Edge que vous voulez déployer.

1. Cliquez avec le bouton droit sur l’appareil IoT Edge afin de créer un déploiement pour celui-ci. Vous devez choisir le fichier manifeste de déploiement sous `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

   > [!NOTE]
   > Vous ne devez pas sélectionner `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`.

1. Cliquez sur le bouton Actualiser pour voir les nouveaux modules en cours d’exécution, ainsi que le module **TempSensor**, et **$edgeAgent** et **$edgeHub**.

## <a name="view-generated-data"></a>Afficher les données générées

1. Pour surveiller le message D2C pour un appareil spécifique, sélectionnez l’appareil dans la liste, puis cliquez sur **Start Monitoring Built-in Event Endpoint** (Démarrer la surveillance du point de terminaison d’événement intégré) dans la fenêtre **Action**.

1. Pour arrêter la surveillance des données, sélectionnez l’appareil dans la liste, puis choisissez **Stop Monitoring Built-in Event Endpoint** (Arrêter la surveillance du point de terminaison d’événement intégré) dans la fenêtre **Action**.

## <a name="next-steps"></a>Étapes suivantes

Pour développer des modules personnalisés pour vos appareils IoT Edge, voir [Comprendre et utiliser les kits Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md).
