---
title: Utilisation du stockage de files d’attente (C++) - Stockage Azure
description: Découvrez comment utiliser le service de stockage de files d’attente dans Azure. Les exemples sont écrits en C++.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: cpp
ms.topic: article
ms.date: 05/11/2017
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 440033233bbd60421cc3245a04544cd04caec6f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153119"
---
# <a name="how-to-use-queue-storage-from-c"></a>Utilisation du stockage de files d'attente à partir de C++
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d'ensemble
Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de stockage des files d'attente Azure. Les exemples ont été écrits en C++ et utilisent la [bibliothèque cliente Azure Storage pour C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Les scénarios traités incluent **l’insertion**, la **lecture furtive**, la **récupération** et la **suppression** des messages de file d’attente, ainsi que la **création et suppression des files d’attente**.

> [!NOTE]
> Ce guide cible la bibliothèque cliente Azure Storage pour C++ version 1.0.0 et les versions ultérieures. La version recommandée est la bibliothèque cliente de stockage version 2.2.0, disponible par le biais de [NuGet](https://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp/).
> 
> 

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Création d’une application C++
Dans ce guide, vous allez utiliser des fonctionnalités de stockage qui peuvent être exécutées dans une application C++.

Pour ce faire, vous devez installer la bibliothèque cliente Azure Storage pour C++ et créer un compte Azure Storage dans votre abonnement Azure.

Pour installer la bibliothèque cliente Azure Storage pour C++, vous pouvez procéder comme suit :

* **Linux :** suivez les instructions disponibles dans la page [Azure Storage Client Library for C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .
* **Windows :** Dans Visual Studio, cliquez sur **Outils > Gestionnaire de package NuGet > Console du gestionnaire de package**. Entrez la commande suivante dans la [console du gestionnaire du package NuGet](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) et appuyez sur **ENTRÉE**.

```powershell
Install-Package wastorage
```

## <a name="configure-your-application-to-access-queue-storage"></a>Configuration de votre application pour accéder au stockage de files d'attente
Ajoutez les instructions import suivantes au début du fichier Java dans lequel vous voulez utiliser des API de stockage Azure pour accéder aux files d'attente :  

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configuration d’une chaîne de connexion au stockage Azure
Un client de stockage Azure utilise une chaîne de connexion de stockage pour stocker des points de terminaison et des informations d’identification permettant d’accéder aux services de gestion des données. Lors de l’exécution d’une application cliente, vous devez spécifier la chaîne de connexion au stockage au format suivant, en indiquant le nom de votre compte de stockage et sa clé d’accès de stockage, correspondant aux valeurs *AccountName* et *AccountKey*, sur le [portail Azure](https://portal.azure.com). Pour plus d'informations sur les comptes et les clés d'accès de stockage, consultez la page [À propos des comptes Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Cet exemple vous montre comment déclarer un champ statique pour qu’il contienne une chaîne de connexion :  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Pour tester votre application sur votre ordinateur Windows local, vous pouvez utiliser [l’émulateur de stockage Microsoft Azure](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) installé avec le [Kit de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads/). L'émulateur de stockage est un utilitaire qui simule sur votre ordinateur de développement local les objets blob, les files d'attente et les services de Table disponibles dans Azure. L’exemple suivant vous montre comment déclarer un champ statique pour qu’il contienne une chaîne de connexion vers votre émulateur de stockage local :  

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Pour démarrer l’émulateur de stockage Azure, sélectionnez le bouton **Démarrer** ou appuyez sur la touche **Windows**. Commencez à taper **Émulateur de stockage Azure**, puis sélectionnez **Émulateur de stockage Microsoft Azure** dans la liste des applications.

Les exemples ci-dessous partent du principe que vous avez utilisé l’une de ces deux méthodes pour obtenir la chaîne de connexion de stockage.

## <a name="retrieve-your-connection-string"></a>Récupération de votre chaîne de connexion
Vous pouvez utiliser la classe **cloud_storage_account** pour représenter les informations de votre compte de stockage. Pour extraire les informations de votre compte de stockage de la chaîne de connexion de stockage, vous pouvez utiliser la méthode **parse** .

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Activation Créer une file d’attente
Un objet **cloud_queue_client** vous permet d’obtenir les objets de référence pour les files d’attente. Le code suivant crée un objet **cloud_queue_client**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Utilisez l’objet **cloud_queue_client** pour obtenir une référence pointant vers la file d’attente à utiliser. Si la file d'attente n'existe pas, vous pouvez la créer :

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Activation Insertion d'un message dans une file d'attente
Pour insérer un message dans une file d’attente existante, commencez par créer un **cloud_queue_message**. Appelez ensuite la méthode **add_message**. Un **cloud_queue_message** peut être créé à partir d’une chaîne ou d’un tableau **d’octets**. Voici le code qui crée une file d'attente (si elle n'existe pas) et insère le message « Hello, World » :

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();

// Create a message and add it to the queue.
azure::storage::cloud_queue_message message1(U("Hello, World"));
queue.add_message(message1);  
```

## <a name="how-to-peek-at-the-next-message"></a>Activation Lecture furtive du message suivant
Vous pouvez lire furtivement le message au début de la file d’attente sans le supprimer de la file d’attente en appelant la méthode **peek_message**.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Peek at the next message.
azure::storage::cloud_queue_message peeked_message = queue.peek_message();

// Output the message content.
std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Activation Modification du contenu d'un message en file d'attente
Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l'état de la tâche. Le code suivant met à jour le message de la file d'attente avec un nouveau contenu et ajoute 60 secondes au délai d'expiration de la visibilité. Cette opération enregistre l'état de la tâche associée au message et accorde une minute supplémentaire au client pour traiter le message. Vous pouvez utiliser cette technique pour suivre des flux de travail à plusieurs étapes sur les messages de file d'attente, sans devoir reprendre du début si une étape du traitement échoue à cause d'une défaillance matérielle ou logicielle. Normalement, vous conservez aussi un nombre de nouvelles tentatives et si le message est retenté plus de n fois, vous le supprimez. Cela protège du déclenchement d'une erreur d'application par un message chaque fois qu'il est traité.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the message from the queue and update the message contents.
// The visibility timeout "0" means make it visible immediately.
// The visibility timeout "60" means the client can get another minute to continue
// working on the message.
azure::storage::cloud_queue_message changed_message = queue.get_message();

changed_message.set_content(U("Changed message"));
queue.update_message(changed_message, std::chrono::seconds(60), true);

// Output the message content.
std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  
```

## <a name="how-to-de-queue-the-next-message"></a>Activation Enlèvement du message suivant de la file d'attente
Votre code enlève un message d'une file d'attente en deux étapes. Lorsque vous appelez **get_message**, vous obtenez le message suivant dans une file d’attente. Un message renvoyé par **get_message** devient invisible de tout autre code lisant les messages de cette file d’attente. Pour finaliser la suppression du message de la file d’attente, vous devez aussi appeler **delete_message**. Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle **delete_message** juste après le traitement du message.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the next message.
azure::storage::cloud_queue_message dequeued_message = queue.get_message();
std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

// Delete the message.
queue.delete_message(dequeued_message);
```

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Activation Utilisation d’options supplémentaires pour l’enlèvement des messages
Il existe deux façons de personnaliser la récupération des messages à partir d'une file d'attente. Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L’exemple de code suivant utilise la méthode **get_messages** pour obtenir 20 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle **for** . Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message. Notez que le délai de 5 minutes démarre en même temps pour tous les messages, donc une fois les 5 minutes écoulées après l’appel de **get_messages**, tous les messages n’ayant pas été supprimés redeviennent visibles.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
// 5 minutes (300 seconds).
azure::storage::queue_request_options options;
azure::storage::operation_context context;

// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

for (auto it = messages.cbegin(); it != messages.cend(); ++it)
{
    // Display the contents of the message.
    std::wcout << U("Get: ") << it->content_as_string() << std::endl;
}
```

## <a name="how-to-get-the-queue-length"></a>Activation Obtention de la longueur de la file d'attente
Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode **download_attributes** demande au service de files d’attente d’extraire les attributs de la file d’attente, y compris le nombre de messages. La méthode **approximate_message_count** obtient le nombre approximatif de messages dans la file d’attente.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Fetch the queue attributes.
queue.download_attributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.approximate_message_count();

// Display number of messages.
std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  
```

## <a name="how-to-delete-a-queue"></a>Activation Suppression d'une file d'attente
Pour supprimer une file d’attente et tous les messages qu’elle contient, appelez la méthode **delete_queue_if_exists** sur l’objet file d’attente.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// If the queue exists and delete it.
queue.delete_queue_if_exists();  
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous connaissez les bases du stockage de files d'attente, consultez les liens suivants pour en savoir plus sur Azure Storage.

* [Utilisation du stockage d'objets blob à partir de C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Utilisation du stockage de tables à partir de C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Listage des ressources Azure Storage en C++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Référence de la bibliothèque cliente de stockage pour C++](https://azure.github.io/azure-storage-cpp)
* [Documentation d'Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
