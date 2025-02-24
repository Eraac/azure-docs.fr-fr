---
title: Envoyer et recevoir des événements à l’aide d’une application .NET Core - Azure Event Hubs | Microsoft Docs
description: Cet article décrit la procédure à suivre pour créer une application .NET Core qui envoie des événements à Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 001abd15c88ae717fa0fb91605b2f0822a38973d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65603542"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-net-core"></a>Envoyer ou recevoir des événements d’Azure Event Hubs avec NET Core
Event Hubs constitue un service qui traite de grandes quantités de données d'événement (télémétrie) à partir de périphériques et d'applications connectés. Après avoir collecté des données dans les concentrateurs d’événements, vous pouvez les stocker à l’aide d’un cluster de stockage ou les transformer à l’aide d’un fournisseur d’analyses en temps réel. Cette fonctionnalité de collecte et de traitement d’événements à grande échelle représente un élément clé des architectures d’applications modernes, notamment l’Internet des objets (IoT). Pour une présentation détaillée d’Event Hubs, consultez [Vue d’ensemble d’Event Hubs](event-hubs-about.md) et [Fonctionnalités d’Event Hubs](event-hubs-features.md).

Ce tutoriel montre comment créer des applications .NET Core dans C# pour recevoir des événements à partir d’un hub d’événements ou lui en envoyer. 

> [!NOTE]
> Vous pouvez télécharger ce guide de démarrage rapide sous forme d’exemple depuis [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender), remplacer les chaînes `EventHubConnectionString` et `EventHubName` par les valeurs de votre hub d’événements, puis l’exécuter. Ou, vous pouvez suivre les étapes de ce didacticiel pour créer le vôtre.

## <a name="prerequisites"></a>Prérequis

- [Microsoft Visual Studio 2019](https://www.visualstudio.com).
- [Outils Visual Studio 2015 ou 2017 .NET Core](https://www.microsoft.com/net/core). 
- **Créez un espace de noms Event Hubs et un Event Hub**. La première étape consiste à utiliser le [portail Azure](https://portal.azure.com) pour créer un espace de noms de type Event Hubs et obtenir les informations de gestion nécessaires à votre application pour communiquer avec le concentrateur d’événements. Pour créer un espace de noms et un hub d’événements, suivez la procédure décrite dans [cet article](event-hubs-create.md). Ensuite, obtenez la **chaîne de connexion de l’espace de noms Event Hub** en suivant les instructions de l’article : [Obtenir la chaîne de connexion](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Vous utiliserez la chaîne de connexion plus loin dans ce tutoriel.

## <a name="send-events"></a>Envoyer des événements 
Cette section montre comment créer une application console .NET Core pour envoyer des événements à un hub d’événements. 

### <a name="create-a-console-application"></a>Création d’une application console

Démarrez Visual Studio. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**. Créez une application console .NET Core.

![Nouveau projet](./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png)

### <a name="add-the-event-hubs-nuget-package"></a>Ajout du package NuGet Event Hubs

Ajoutez le package NuGet de bibliothèque .NET Core [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) à votre projet en procédant comme suit : 

1. Cliquez avec le bouton droit sur le projet créé et sélectionnez **Gérer les packages NuGet**.
2. Cliquez sur l’onglet **Parcourir**, puis recherchez « Microsoft.Azure.EventHubs » et sélectionnez le package **Microsoft.Azure.EventHubs**. Cliquez sur **Installer** pour terminer l’installation, puis fermez cette boîte de dialogue.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Écriture de code pour envoyer des messages à un hub d’événements

1. Ajoutez les instructions `using` ci-après en haut du fichier Program.cs :

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Ajoutez des constantes à la classe `Program` pour le chemin de l’entité et la chaîne de connexion Event Hubs (nom du concentrateur d’événements individuel). Remplacez les espaces réservés entre crochets par les valeurs appropriées obtenues lors de la création du concentrateur d’événements. Vérifiez que `{Event Hubs connection string}` est la chaîne de connexion au niveau de l’espace de noms, et pas la chaîne de concentrateur d’événements. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. Ajoutez une nouvelle méthode nommée `MainAsync` à la classe `Program`, comme suit :

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Ajoutez une nouvelle méthode nommée `SendMessagesToEventHub` à la classe `Program`, comme suit :

    ```csharp
    // Creates an event hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Ajoutez le code suivant à la méthode `Main` dans la classe `Program` :

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Voici à quoi doit ressembler votre fichier Program.cs.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Creates an event hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Exécutez le programme et assurez-vous qu’il n’y a aucune erreur.

## <a name="receive-events"></a>Recevoir des événements
Cette section explique comment écrire une application console .NET Core qui reçoit des messages d’un hub d’événements à l’aide de [l’Hôte du processeur d’événements](event-hubs-event-processor-host.md). [L’hôte du processeur d’événements](event-hubs-event-processor-host.md) est une classe .NET qui simplifie la réception d’événements provenant de concentrateurs d’événements grâce à la gestion des points de contrôle permanents et des réceptions en parallèle de ces concentrateurs d’événements. L’hôte du processeur d’événements vous permet de répartir des événements sur plusieurs récepteurs, même quand ils sont hébergés dans des nœuds différents. Cet exemple illustre l’utilisation de l’hôte du processeur d’événements pour un récepteur unique.
> [!NOTE]
> Vous pouvez télécharger ce démarrage rapide sous forme d’exemple depuis [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver), remplacer `EventHubConnectionString` et `EventHubName`, `StorageAccountName`, `StorageAccountKey`, ainsi que les chaînes `StorageContainerName` par vos valeurs d’Event Hub, puis l’exécuter. Ou, vous pouvez suivre les étapes de ce didacticiel pour créer le vôtre.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Création d’une application console

Démarrez Visual Studio. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**. Créez une application console .NET Core.

![Nouveau projet](./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png)

### <a name="add-the-event-hubs-nuget-package"></a>Ajout du package NuGet Event Hubs

Ajoutez les packages NuGet de la bibliothèque .NET Standard [ **Microsoft.Azure.EventHubs** ](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) et [ **Microsoft.Azure.EventHubs.Processor** ](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) à votre projet en effectuant les étapes suivantes : 

1. Cliquez avec le bouton droit sur le projet créé et sélectionnez **Gérer les packages NuGet**.
2. Cliquez sur l’onglet **Parcourir**, recherchez **Microsoft.Azure.EventHubs**, puis sélectionnez le package **Microsoft.Azure.EventHubs**. Cliquez sur **Installer** pour terminer l’installation, puis fermez cette boîte de dialogue.
3. Répétez les étapes 1 et 2 et installez le package **Microsoft.Azure.EventHubs.Processor**.

### <a name="implement-the-ieventprocessor-interface"></a>Implémentation de l’interface IEventProcessor

1. Dans l’Explorateur de solutions, cliquez sur **Ajouter**, puis sur **Classe**. Nommez la nouvelle classe **SimpleEventProcessor**.

2. Ouvrez le fichier SimpleEventProcessor.cs et ajoutez les instructions `using` suivantes au début du fichier.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Implémentez l’interface `IEventProcessor`. Remplacez tout le contenu de la classe `SimpleEventProcessor` par le code suivant :

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Mettre à jour la méthode Main pour utiliser SimpleEventProcessor

1. Ajoutez les instructions `using` ci-après en haut du fichier Program.cs.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Ajoutez des constantes à la classe `Program` pour la chaîne de connexion du concentrateur d’événements, le nom du concentrateur d’événements, le nom du conteneur de compte de stockage, le nom du compte de stockage et la clé du compte de stockage. Ajoutez le code suivant, en remplaçant les espaces réservés par les valeurs correspondantes.

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Ajoutez une nouvelle méthode nommée `MainAsync` à la classe `Program`, comme suit :

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Ajoutez la ligne de code suivante à la méthode `Main` :

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Voici à quoi doit ressembler votre fichier Program.cs :

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Exécutez le programme et assurez-vous qu’il n’y a aucune erreur.


## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Fonctionnalités et terminologie dans Azure Event Hubs](event-hubs-features.md)
- [FAQ sur les hubs d’événements](event-hubs-faq.md)


