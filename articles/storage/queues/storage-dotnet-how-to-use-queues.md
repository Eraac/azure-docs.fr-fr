---
title: Prise en main du Stockage File d’attente Azure à l’aide de .NET - Stockage Azure
description: Les files d’attente Azure fournissent une messagerie asynchrone fiable entre les composants d’application. La messagerie cloud permet de mettre à l’échelle vos composants d’application indépendamment.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 59995715ab42b4682befa7d1512b14427740dea2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446855"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Prise en main du stockage de files d’attente Azure à l’aide de .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="overview"></a>Vue d'ensemble

Le stockage de files d’attente Azure fournit une messagerie cloud entre les composants d’application. Lors de la conception d'applications pour la mise à l'échelle, des composants d'application sont souvent découplés, de sorte qu'ils peuvent être mis à l'échelle indépendamment. Le stockage de files d’attente offre une messagerie asynchrone pour la communication entre les composants d’application, qu’ils soient exécutés dans le cloud, sur le bureau, sur un serveur local ou sur un appareil mobile. Le stockage de files d’attente prend également en charge la gestion des tâches asynchrones et la création des flux de travail de processus.

### <a name="about-this-tutorial"></a>À propos de ce didacticiel

Ce didacticiel montre comment écrire du code .NET pour des scénarios courants d’utilisation du stockage de files d’attente Azure. Les scénarios traités sont les suivants : création et suppression de files d’attente, et ajout, lecture et suppression des messages de file d’attente.

**Durée estimée :** 45 minutes

### <a name="prerequisites"></a>Prérequis

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Bibliothèque de client commune du Stockage Azure pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
* [Bibliothèque de client du Stockage File d’attente Azure pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
* [Gestionnaire de configuration Azure pour .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)
* Un [compte de stockage Azure](../common/storage-quickstart-create-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Configuration de l'environnement de développement

Ensuite, configurez votre environnement de développement dans Visual Studio afin d’être prêt pour essayer les exemples de code fournis dans ce guide.

### <a name="create-a-windows-console-application-project"></a>Créer un projet d’application de console Windows

Dans Visual Studio, créez une application de console Windows. Les étapes suivantes vous montrent comment créer une application console dans Visual Studio 2019. Les étapes sont semblables pour d’autres versions de Visual Studio.

1. Sélectionnez **Fichier** > **Nouveau** > **Projet**
2. Sélectionnez **Plateforme** > **Windows**
3. Sélectionnez **Application console (.NET Framework)**
4. Sélectionnez **Suivant**.
5. Saisissez un nom pour votre application dans le champ **Nom du projet**.
6. Sélectionnez **Créer**

Tous les exemples de code figurant dans ce didacticiel peuvent être ajoutés à la méthode **Main()** dans le fichier **Program.cs** de votre application console.

Vous pouvez utiliser les bibliothèques de client du Stockage Azure dans n’importe quelle application .NET, y compris un service cloud Azure, une application web, ou une application de bureau ou mobile. Dans ce guide, nous utilisons une application console pour plus de simplicité.

### <a name="use-nuget-to-install-the-required-packages"></a>Utiliser NuGet pour installer les packages requis

Pour terminer ce didacticiel, vous devez référencer les trois packages suivants dans votre projet :

* [Bibliothèque de client commune du Stockage Microsoft Azure pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) : ce package fournit un accès programmatique aux ressources de données de votre compte de stockage.
* [Bibliothèque du Stockage File d’attente Azure pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/) : cette bibliothèque de client permet d’utiliser le service Stockage File d’attente Azure pour stocker les messages qui sont accessibles par un client.
* [Bibliothèque Microsoft Azure Configuration Manager pour .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) : ce package fournit une classe pour l’analyse d’une chaîne de connexion à partir d’un fichier config, quel que soit l’emplacement d’exécution de votre application.

Vous pouvez utiliser NuGet pour obtenir ces packages. Procédez comme suit :

1. Cliquez avec le bouton droit sur votre projet dans **l’Explorateur de solutions**, puis sélectionnez **Gérer les packages NuGet**.
2. Sélectionnez **Parcourir**.
3. Recherchez « Microsoft.Azure.Storage.Queue » en ligne, puis sélectionnez **Installer** pour installer la bibliothèque de client du Stockage Azure et ses dépendances. Cela installera également la bibliothèque Microsoft.Azure.Storage.Common, qui est une dépendance de la bibliothèque de file d’attente.
4. Recherchez « Microsoft.Azure.ConfigurationManager » en ligne, puis sélectionnez **Installer** pour installer Azure Configuration Manager.

> [!NOTE]
> Les packages de bibliothèques de client du Stockage Azure sont également disponibles dans le [Kit de développement logiciel (SDK) Azure pour .NET](https://azure.microsoft.com/downloads/). Toutefois, nous vous recommandons d’installer également les bibliothèques de client du Stockage Azure à partir de NuGet, pour vous assurer que vous avez toujours les dernières versions.
>
> Les dépendances ODataLib de la bibliothèque de client du Stockage Azure pour .NET sont résolues via les packages ODataLib disponibles sur NuGet, et non à partir des services de données WCF. Vous pouvez télécharger directement les bibliothèques ODataLib ou les référencer avec votre projet de code via NuGet. Les packages ODataLib utilisés par la bibliothèque de client du Stockage Azure sont [OData](https://nuget.org/packages/Microsoft.Data.OData/), [Edm](https://nuget.org/packages/Microsoft.Data.Edm/) et [Spatial](https://nuget.org/packages/System.Spatial/). Bien qu’elles soient utilisées par les classes de stockage de Table Azure, ces bibliothèques sont des dépendances requises pour la programmation avec la bibliothèque de client du Stockage Azure.

### <a name="determine-your-target-environment"></a>Déterminer votre environnement cible

Vous avez le choix entre deux environnements pour exécuter les exemples de ce guide :

* Vous pouvez exécuter votre code sur un compte Azure Storage dans le cloud.
* Vous pouvez exécuter votre code sur l’émulateur de stockage Azure. L’émulateur de stockage est un environnement local qui émule un compte Azure Storage dans le cloud. L’émulateur est une option gratuite permettant de tester et déboguer votre code lors du développement de votre application. L’émulateur utilise un compte et une clé connus. Pour plus d’informations, consultez [Utilisation de l’émulateur de stockage Azure pour le développement et le test](../common/storage-use-emulator.md).

Si vous ciblez un compte de stockage dans le cloud, copiez la clé d’accès primaire de votre compte de stockage à partir du portail Azure. Pour plus d’informations, consultez l’article [Clés d’accès](../common/storage-account-manage.md#access-keys).

> [!NOTE]
> Vous pouvez cibler l’émulateur de stockage pour éviter les frais liés à l’utilisation des services de stockage Azure. Toutefois, si vous choisissez de cibler un compte de stockage Azure situé dans le cloud, les frais associés à l’utilisation de ce didacticiel seront négligeables.

### <a name="configure-your-storage-connection-string"></a>Configurer votre chaîne de connexion de stockage

Les bibliothèques de client du Stockage Azure pour .NET prennent en charge l’utilisation d’une chaîne de connexion de stockage pour la configuration de points de terminaison et d’informations d’identification permettant d’accéder aux services de stockage. La meilleure façon de conserver votre chaîne de connexion de stockage est dans un fichier de configuration.

Pour plus d’informations sur les chaînes de connexion, voir [Configuration d’une chaîne de connexion dans Stockage Azure](../common/storage-configure-connection-string.md).

> [!NOTE]
> Votre clé de compte de stockage est similaire au mot de passe racine pour votre compte de stockage. Veillez toujours à protéger votre clé de compte de stockage. Évitez de la communiquer à d’autres utilisateurs, de la coder en dur ou de l’enregistrer dans un fichier texte brut accessible à d’autres personnes. Régénérez votre clé à l’aide du portail Azure si vous pensez que sa confidentialité est compromise.

Pour configurer votre chaîne de connexion, ouvrez le fichier **app.config** depuis l’Explorateur de solutions de Visual Studio. Ajoutez le contenu de l’élément **\<appSettings\>** indiqué ci-dessous. Remplacez *account name* par le nom de votre compte de stockage et *account-key*, par la clé d’accès de votre compte :

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Par exemple, votre paramètre de configuration est semblable à :

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Pour cibler l’émulateur de stockage, vous pouvez utiliser un raccourci qui correspond à la clé et au nom de compte connus. Dans ce cas, le paramètre de votre chaîne de connexion est :

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Ajouter des directives d’utilisation

Ajoutez les directives `using` suivantes au fichier `Program.cs` :

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Copier vos informations d’identification depuis le portail Azure

L’exemple de code a besoin d’autoriser l’accès à votre compte de stockage. Pour l’autorisation, vous devez fournir vos informations d’identification du compte de stockage à l’application sous la forme d’une chaîne de connexion. Pour afficher les informations d’identification de votre compte de stockage :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Recherchez votre compte de stockage.
3. Dans la section **Paramètres** de la présentation du compte de stockage, sélectionnez **Clés d’accès**. Vos clés d’accès au compte s’affichent, ainsi que la chaîne de connexion complète de chaque clé.
4. Recherchez la valeur de **Chaîne de connexion** sous **clé1**, puis cliquez sur le bouton **Copier** pour copier la chaîne de connexion. Vous allez ajouter la valeur de chaîne de connexion dans une variable d’environnement à l’étape suivante.

    ![Capture d’écran montrant comment copier une chaîne de connexion à partir du portail Azure](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

### <a name="parse-the-connection-string"></a>Analyse de la chaîne de connexion

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>Création du client du service Queue

La classe [CloudQueueClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet) vous permet de récupérer des files d’attente stockées dans Queue Storage. Voici un moyen de créer le client du service :

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

Vous êtes maintenant prêt à écrire du code qui lit et écrit des données dans le Queue Storage.

## <a name="create-a-queue"></a>Créer une file d’attente

Cet exemple montre comment créer une file d’attente, si elle n’existe pas encore :

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a container.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

## <a name="insert-a-message-into-a-queue"></a>Insertion d'un message dans une file d'attente

Pour insérer un message dans une file d'attente existante, commencez par créer un [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet). Appelez ensuite la méthode [AddMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet) . Un **CloudQueueMessage** peut être créé à partir d’une chaîne (au format UTF-8) ou d’un tableau **d’octets**. Voici le code qui crée une file d'attente (si elle n'existe pas) et insère le message « Hello, World » :

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist.
queue.CreateIfNotExists();

// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

## <a name="peek-at-the-next-message"></a>Lecture furtive du message suivant

Vous pouvez lire furtivement le message au début de la file d'attente sans l'enlever de la file d'attente en appelant la méthode [PeekMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet) .

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

## <a name="change-the-contents-of-a-queued-message"></a>Modification du contenu d'un message en file d'attente

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l'état de la tâche. Le code suivant met à jour le message de la file d'attente avec un nouveau contenu et ajoute 60 secondes au délai d'expiration de la visibilité. Cette opération enregistre l'état de la tâche associée au message et accorde une minute supplémentaire au client pour traiter le message. Vous pouvez utiliser cette technique pour suivre des flux de travail à plusieurs étapes sur les messages de file d'attente, sans devoir reprendre du début si une étape du traitement échoue à cause d'une défaillance matérielle ou logicielle. Normalement, vous conservez aussi un nombre de nouvelles tentatives et si le message est retenté plus de *n* fois, vous le supprimez. Cela protège du déclenchement d'une erreur d'application par un message chaque fois qu'il est traité.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

## <a name="de-queue-the-next-message"></a>Enlèvement du message suivant de la file d'attente

Votre code enlève un message d'une file d'attente en deux étapes. Lorsque vous appelez [GetMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet), vous obtenez le message suivant dans une file d'attente. Un message renvoyé par **GetMessage** devient invisible par les autres codes lisant les messages de cette file d'attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour finaliser la suppression du message de la file d'attente, vous devez aussi appeler [DeleteMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet). Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle **DeleteMessage** juste après le traitement du message.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Utiliser le modèle Async-Await avec les API de stockage de files d’attente communes

Cet exemple décrit comment utiliser le modèle Async-Await avec les API de stockage de files d’attente communes. L’exemple appelle la version asynchrone de chacune des méthodes spécifiées, comme l’indique le suffixe *Async* de chaque méthode. Quand une méthode asynchrone est utilisée, le modèle Async-Await suspend l’exécution locale jusqu’à la fin de l’appel. Ce comportement permet au thread actuel d’effectuer d’autres tâches afin d’éviter les goulots d’étranglement au niveau des performances et d’améliorer la réactivité globale de votre application. Pour plus d’informations sur l’utilisation du modèle Async-Await dans .NET, consultez l’article [Programmation asynchrone avec Async et Await (C# et Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Utilisation d’options supplémentaires pour l’enlèvement des messages

Il existe deux façons de personnaliser la récupération des messages à partir d'une file d'attente. Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L'exemple de code suivant utilise la méthode [GetMessages](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet) pour obtenir 20 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle **foreach** . Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message. Notez que le délai de 5 minutes démarre en même temps pour tous les messages, donc une fois les 5 minutes écoulées après l'appel de **GetMessages**, tous les messages n'ayant pas été supprimés redeviennent visibles.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Obtention de la longueur de la file d'attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode [FetchAttributes](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet) demande au service de files d'attente d'extraire les attributs de la file d'attente, y compris le nombre de messages. La propriété [ApproximateMessageCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet) renvoie la dernière valeur extraite par la méthode **FetchAttributes**, sans appeler le service de files d’attente.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="delete-a-queue"></a>Suppression d'une file d'attente

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode [Delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet) sur l'objet file d'attente.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les bases du stockage des files d'attente, consultez les liens suivants pour apprendre à exécuter les tâches de stockage plus complexes.

* Pour plus d'informations sur les API disponibles, consultez la documentation de référence des services de files d'attente :
  * [Référence de la bibliothèque cliente de stockage pour .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [Référence d’API REST](https://msdn.microsoft.com/library/azure/dd179355)
* Découvrez comment simplifier le code que vous écrivez avec Azure Storage, à l’aide du [Kit de développement logiciel (SDK) Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).
* Pour plus d’informations sur les autres options de stockage de données dans Azure, consultez d’autres guides de fonctionnalités.
  * [Prise en main du stockage de tables Azure à l’aide de .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) pour le stockage de données structurées.
  * [Prise en main d’Azure Blob Storage à l’aide de .NET](../blobs/storage-dotnet-how-to-use-blobs.md) pour le stockage de données non structurées.
  * [Connexion à SQL Database à l’aide de .NET (C#)](../../sql-database/sql-database-connect-query-dotnet-core.md) pour stocker des données relationnelles.

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating an Azure Project in Visual Studio]: https://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[OData]: https://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: https://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: https://nuget.org/packages/System.Spatial/5.0.2
