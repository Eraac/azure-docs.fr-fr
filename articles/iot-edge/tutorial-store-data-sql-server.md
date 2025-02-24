---
title: 'Didacticiel : Stocker des données avec le module SQL - Azure IoT Edge | Microsoft Docs'
description: Découvrez comment stocker des données localement sur votre appareil IoT Edge avec un module SQL Server
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: b77b44856e9623235051470bc087885765ee12c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080427"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Didacticiel : Stocker des données en périphérie avec les bases de données SQL Server

Déployez un module SQL Server pour stocker des données sur un appareil Linux exécutant Azure IoT Edge.

Utilisez Azure IoT Edge et SQL Server pour stocker et interroger des données en périphérie. Azure IoT Edge comprend des fonctionnalités de stockage de base qui mettent en cache les messages quand un appareil est mis hors connexion, puis qui les transmettent une fois la connexion rétablie. Toutefois, vous souhaitez peut-être des capacités de stockage plus avancées, telles que la possibilité d’interroger des données localement. Vos appareils IoT Edge peuvent utiliser des bases de données locales pour effectuer des calculs plus complexes sans avoir à maintenir une connexion à IoT Hub. 

Cet article fournit des instructions pour le déploiement d’une base de données SQL Server sur un appareil IoT Edge. Azure Functions, exécuté sur l’appareil IoT Edge, structure les données entrantes, puis les envoie vers la base de données. Les étapes décrites dans cet article peuvent également être appliquées aux autres bases de données qui fonctionnent dans des conteneurs, telles que MySQL ou PostgreSQL.

Ce tutoriel vous montre comment effectuer les opérations suivantes : 

> [!div class="checklist"]
> * Utiliser Visual Studio Code pour créer une fonction Azure
> * Déployer une base de données SQL sur votre appareil IoT Edge
> * Utiliser Visual Studio Code pour générer des modules et les déployer sur votre appareil IoT Edge
> * Afficher les données générées

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel, vous devez avoir effectué celui qui précède pour configurer votre environnement de développement pour le développement de conteneur Linux : [Développer des modules IoT Edge pour les appareils Linux](tutorial-develop-for-linux.md). En suivant ce tutoriel, les conditions préalables suivantes doivent être remplies : 

* Un niveau gratuit ou standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure.
* Un [appareil Linux exécutant Azure IoT Edge](quickstart-linux.md).
* Un registre de conteneurs tel qu’[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [Visual Studio Code](https://code.visualstudio.com/) configuré avec [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) configuré pour exécuter des conteneurs Linux.

Ce tutoriel utilise un module d’Azure Functions pour envoyer des données à SQL Server. Pour développer un module IoT Edge avec Azure Functions, installez les conditions préalables supplémentaires suivantes sur votre machine de développement : 

* [Extension C# pour Visual Studio Code (technologie OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [SDK .NET Core 2.1](https://www.microsoft.com/net/download). 

## <a name="create-a-function-project"></a>Créer un projet Function

Pour envoyer des données dans une base de données, vous avez besoin d’un module qui peut structurer les données correctement, puis les stocke dans une table. 

### <a name="create-a-new-project"></a>Création d'un projet

Les étapes suivantes vous montrent comment créer une fonction IoT Edge à l’aide de Visual Studio Code et des outils Azure IoT.

1. Ouvrez Visual Studio Code.

2. Ouvrez la palette de commandes VS Code en sélectionnant **Affichage** > **Palette de commandes**.

3. Dans la palette de commandes, tapez et exécutez la commande **Azure IoT Edge: New IoT Edge solution**. Dans la palette de commandes, spécifiez les informations suivantes pour créer votre solution : 

   | Champ | Valeur |
   | ----- | ----- |
   | Sélectionner le dossier | Choisissez l’emplacement sur votre machine de développement pour que VS Code crée les fichiers de la solution. |
   | Provide a solution name (Nommer la solution) | Entrez un nom descriptif pour votre solution, par exemple **SqlSolution**, ou acceptez le nom par défaut. |
   | Select module template (Sélectionner un modèle de module) | Choisissez **Azure Functions - C#** . |
   | Provide a module name (Nommer le module) | Nommez votre module **sqlFunction**. |
   | Provide Docker image repository for the module (Indiquer le référentiel d’images Docker pour le module) | Un référentiel d’images comprend le nom de votre registre de conteneurs et celui de votre image conteneur. Votre image conteneur est préremplie à partir de la dernière étape. Remplacez **localhost:5000** par la valeur de serveur de connexion de votre registre de conteneurs Azure. Vous pouvez récupérer le serveur de connexion à partir de la page Vue d’ensemble de votre registre de conteneurs dans le Portail Azure. <br><br>La chaîne finale ressemble à ceci : \<nom de registre\>.azurecr.io/sqlfunction. |

   La fenêtre VS Code charge l’espace de travail de votre solution IoT Edge. 
   
### <a name="add-your-registry-credentials"></a>Ajouter les informations d’identification de votre registre

Le fichier d’environnement stocke les informations d’identification de votre registre de conteneurs et les partage avec le runtime IoT Edge. Le runtime a besoin de ces informations d’identification pour extraire vos images privées sur l’appareil IoT Edge.

1. Dans l’Explorateur VS Code, ouvrez le fichier .env.
2. Mettre à jour les champs avec les valeurs de **nom d’utilisateur** et de **mot de passe** que vous avez copiées à partir de votre registre de conteneurs Azure.
3. Enregistrez ce fichier.

### <a name="select-your-target-architecture"></a>Sélectionner votre architecture cible

Visual Studio Code peut développer des modules C pour les appareils Linux AMD64 et Linux ARM32v7. Vous devez sélectionner l’architecture que vous ciblez avec chaque solution, car le conteneur est généré et s’exécute différemment pour chaque type d’architecture. Linux AMD64 est la valeur par défaut. 

1. Ouvrez la palette de commandes et recherchez **Azure IoT Edge: Définir la plateforme cible par défaut pour la solution Edge**, ou sélectionnez l’icône de raccourci dans la barre latérale en bas de la fenêtre. 

2. Dans la palette de commandes, sélectionnez l’architecture cible dans la liste des options. Pour ce tutoriel, comme nous utilisons une machine virtuelle Ubuntu en tant qu’appareil IoT Edge, ce dernier conservera la valeur par défaut **amd64**. 

### <a name="update-the-module-with-custom-code"></a>Mettre à jour le module avec du code personnalisé

1. Dans l’Explorateur Visual Studio Code, ouvrez **modules** > **sqlFunction** > **sqlFunction.cs**.

2. Remplacez tout le contenu du fichier par le code suivant :

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.       
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
           public Ambient ambient {get; set;}
           public string timeCreated {get; set;}
       }
       class Machine
       {
           public double temperature {get; set;}
           public double pressure {get; set;}         
       }
       class Ambient
       {
           public double temperature {get; set;}
           public int humidity {get; set;}         
       }
   }
   ```

3. À la ligne 35, remplacez la chaîne **\<sql connection string\>** par la chaîne suivante. La propriété **Data Source** fait référence au conteneur SQL Server, qui n’existe pas encore mais que vous allez créer avec le nom **SQL** dans la section suivante. 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

4. Enregistrez le fichier **sqlFunction.cs**. 

5. Ouvrez le fichier **sqlFunction.csproj**.

6. Recherchez le groupe de références de package, puis ajoutez-en une nouvelle pour inclure SqlClient. 

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

7. Enregistrez le fichier **sqlFunction.csproj**.

## <a name="add-the-sql-server-container"></a>Ajouter le conteneur SQL Server

Un [manifeste de déploiement](module-composition.md) déclare les modules que le runtime IoT Edge va installer sur votre appareil IoT Edge. Vous avez fourni du code pour créer un module Function personnalisé dans la section précédente, mais le module SQL Server est déjà généré et disponible dans la Place de marché Azure. Vous devez simplement indiquer au runtime IoT Edge de l’inclure, puis de le configurer sur votre appareil. 

1. Dans Visual Studio Code, ouvrez la palette de commandes en sélectionnant **Affichage** > **Palette de commandes**.

2. Dans la palette de commandes, saisissez et exécutez la commande **Azure IoT Edge: Ajoutez un module IoT Edge**. Dans la palette de commandes, fournissez les informations suivantes pour ajouter un nouveau module : 

   | Champ | Valeur | 
   | ----- | ----- |
   | Select deployment template file (Sélectionner le fichier de modèle de déploiement) | La palette de commandes met en évidence le fichier deployment.template.json dans votre dossier de solution en cours. Sélectionnez ce fichier.  |
   | Select module template (Sélectionner un modèle de module) | Sélectionnez **Module from Azure Marketplace** (Module de la Place de marché Azure). |

3. Dans la place de marché des modules Azure IoT Edge, recherchez et sélectionnez **Module SQL Server**. 

4. Remplacez le nom du module par **sql**, entièrement en minuscules. Ce nom correspond au nom de conteneur déclaré dans la chaîne de connexion dans le fichier sqlFunction.cs. 

5. Sélectionnez **Importer** pour ajouter le module à votre solution. 

6. Dans votre dossier de solution, ouvrez le fichier **deployment.template.json**. 

7. Recherchez la section **modules**. Vous devez voir trois modules. Le module *tempSensor* est inclus par défaut dans les nouvelles solutions et fournit des données de test à utiliser avec vos autres modules. Le module *sqlFunction* est le module que vous avez initialement créé et mis à jour avec le nouveau code. Enfin, le module *sql* a été importé à partir de la Place de marché Azure. 

   >[!Tip]
   >Le module SQL Server est fourni avec un mot de passe par défaut défini dans les variables d’environnement du manifeste de déploiement. Chaque fois que vous créez un conteneur SQL Server dans un environnement de production, vous devez [modifier le mot de passe administrateur par défaut du système](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker).

8. Fermez le fichier **deployment.template.json**.

## <a name="build-your-iot-edge-solution"></a>Générer votre solution IoT Edge

Dans les sections précédentes, vous avez créé une solution avec un module, puis en avez ajouté un autre au modèle du manifeste de déploiement. Le module SQL Server est hébergé publiquement par Microsoft, mais vous devez conteneuriser le code dans le module Functions. Dans cette section, vous allez générer la solution, créer des images de conteneur pour le module sqlFunction et envoyer (push) l’image vers votre registre de conteneurs. 

1. Dans Visual Studio Code, ouvrez le terminal intégré en sélectionnant **Affichage** > **Terminal**.  

1. Connectez-vous à votre registre de conteneurs à Visual Studio Code afin de pouvoir envoyer les images au registre. Utilisez les informations d’identification ACR (Azure Container Registry)que vous avez ajoutées au fichier .env. Entrez la commande suivante dans le terminal intégré :

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```
    
    Vous pouvez voir un avertissement de sécurité vous recommandant l’utilisation du paramètre --password-stdin. Même si son utilisation n’est pas le sujet de cet article, nous vous recommandons de suivre cette meilleure pratique. Pour en savoir plus, consultez les informations de référence sur la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin). 

2. Dans l’Explorateur VS Code, cliquez avec le bouton droit sur le fichier **deployment.template.json** et sélectionnez **Build and Push IoT Edge solution** (Générer et envoyer (push) la solution IoT Edge). 

Quand vous indiquez à Visual Studio Code de générer votre solution, il extrait d’abord les informations contenues dans le modèle de déploiement et génère un fichier deployment.json dans un nouveau dossier nommé **config**. Il exécute ensuite deux commandes dans le terminal intégré : `docker build` et `docker push`. Ces deux commandes génèrent votre code, conteneurisent le module, puis envoient (push) le code au registre de conteneurs que vous avez spécifié lors de l’initialisation de la solution. 

Vous pouvez vérifier que le module sqlFunction a été correctement envoyé (push) à votre registre de conteneurs. Dans le portail Azure, accédez à votre registre de conteneurs. Sélectionnez **référentiels** et recherchez **sqlFunction**. Les deux autres modules, tempSensor et sql, ne sont pas envoyés à votre registre de conteneurs, car vous pointez déjà vers leurs référentiels dans les registres Microsoft.

## <a name="deploy-the-solution-to-a-device"></a>Déployer la solution sur un appareil

Vous pouvez définir des modules sur un appareil via le hub IoT, mais vous pouvez également accéder à vos hub IoT et appareils via Visual Studio Code. Dans cette section, vous configurez l’accès à votre hub IoT, puis utilisez VS Code pour déployer la solution sur votre appareil IoT Edge. 

1. Dans l’Explorateur VS Code, développez la section **Azure IoT Hub Devices** (Appareils Azure IoT Hub). 

2. Cliquez avec le bouton droit sur l’appareil que vous souhaitez cibler avec votre déploiement et sélectionnez **Créer un déploiement pour un seul appareil**. 

3. Dans l’Explorateur de fichiers, accédez au dossier **config** à l’intérieur de votre solution, puis choisissez **deployment.amd64**. Cliquez sur **Select Edge Deployment Manifest** (Sélectionner un manifeste de déploiement Edge). 

   N’utilisez pas le fichier deployment.template.json en tant que manifeste de déploiement.

Si le déploiement réussit, un message de confirmation est affiché dans la sortie VS Code. 

Actualisez l’état de votre appareil dans la section Appareils Azure IoT Hub de VS Code. Les nouveaux modules sont listés et commencent à générer des rapports à mesure qu’ils s’exécutent au cours des minutes suivantes pendant l’installation et le démarrage des conteneurs. Vous pouvez également vérifier que tous les modules sont opérationnels sur votre appareil. Sur votre appareil IoT Edge, exécutez la commande suivante pour afficher l’état des modules. 

   ```cmd/sh
   iotedge list
   ```

## <a name="create-the-sql-database"></a>Créer la base de données SQL

Quand vous appliquez le manifeste de déploiement à votre appareil, trois modules sont en cours d’exécution. Le module tempSensor génère des données d’environnement simulées. Le module sqlFunction récupère les données et les met en forme pour une base de données. Cette section vous guide tout au long de la configuration de la base de données SQL pour stocker les données de température. 

Exécutez les commandes suivantes sur votre appareil IoT Edge. Ces commandes se connectent au module **sql** en cours d’exécution sur votre appareil, et créent une base de données et une table pour contenir les données de température qui lui sont envoyées. 

1. Dans un outil en ligne de commande sur votre appareil IoT Edge, connectez-vous à votre base de données. 
      ```bash
      sudo docker exec -it sql bash
      ```

2. Ouvrez l’outil de commande SQL.
      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Créez votre base de données : 
      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Définissez votre table.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Vous pouvez personnaliser votre fichier Docker SQL Server pour configurer automatiquement le déploiement de votre serveur SQL Server sur plusieurs appareils IoT Edge. Pour plus d’informations, consultez le [projet de démonstration du conteneur Microsoft SQL Server](https://github.com/twright-msft/mssql-node-docker-demo-app). 

## <a name="view-the-local-data"></a>Consulter les données locales

Une fois que votre table est créée, le module sqlFunction démarre le stockage des données dans une base de données SQL Server 2017 locale sur votre appareil IoT Edge. 

À partir de l’outil de commande SQL, exécutez la commande suivante pour afficher vos données de table mises en forme : 

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![Afficher le contenu de la base de données locale](./media/tutorial-store-data-sql-server/view-data.png)



## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous envisagez de passer à l’article recommandé suivant, vous pouvez conserver les ressources et configurations que vous avez créées afin de les réutiliser. Vous pouvez également continuer à utiliser le même appareil IoT Edge comme appareil de test. 

Sinon, vous pouvez supprimer les ressources Azure et les configurations locales que vous avez créées dans cet article pour éviter les frais. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un module Azure Functions qui contient le code pour filtrer les données brutes générées par votre appareil IoT Edge. Quand vous êtes prêt à créer vos propres modules, vous pouvez découvrir comment [développer Azure Functions avec Azure IoT Edge pour Visual Studio Code](how-to-develop-csharp-function.md). 

Si vous souhaitez essayer une autre méthode de stockage en périphérie, découvrez comment utiliser le Stockage Blob Azure sur IoT Edge. 

> [!div class="nextstepaction"]
> [Stocker des données en périphérie avec le Stockage Blob Azure sur IoT Edge](how-to-store-data-blob.md)
