---
title: Utilisation du stockage d’objets (blob) à partir de C++ - Azure | Microsoft Docs
description: Stockez des données non structurées dans le cloud avec le stockage d’objet blob Azure.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mhopkins
ms.reviewer: seguler
ms.subservice: blobs
ms.openlocfilehash: 32601fc15e6b6a25a1005b6d95499d4aae4fb0d2
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461179"
---
# <a name="how-to-use-blob-storage-from-c"></a>Utilisation du stockage d’objets blob à partir de C++

Ce guide explique le déroulement des scénarios courants dans le cadre de l’utilisation du Stockage Blob Azure. Les exemples illustrent comment charger, lister, télécharger et supprimer des objets blob. Les exemples ont été écrits en C++ et utilisent la [bibliothèque cliente Azure Storage pour C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).   

Pour en savoir plus sur le Stockage Blob, consultez [Présentation du Stockage Blob Azure](storage-blobs-introduction.md).

> [!NOTE]
> Ce guide cible la bibliothèque cliente Azure Storage pour C++ version 1.0.0 et les versions ultérieures. Microsoft recommande d’utiliser la dernière version de la bibliothèque cliente pour C++, disponible via [NuGet](https://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Création d’une application C++
Dans ce guide, vous allez utiliser des fonctionnalités de stockage qui peuvent être exécutées dans une application C++.  

Pour ce faire, vous devez installer la bibliothèque cliente Azure Storage pour C++ et créer un compte Azure Storage dans votre abonnement Azure.   

Pour installer la bibliothèque cliente Azure Storage pour C++, vous pouvez procéder comme suit :

* **Linux :** suivez les instructions disponibles dans la page [Azure Storage Client Library for C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .  
* **Windows :** Dans Visual Studio, cliquez sur **Outils > Gestionnaire de package NuGet > Console du gestionnaire de package**. Entrez la commande suivante dans la [console du gestionnaire du package NuGet](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) et appuyez sur **ENTRÉE**.  
  
     Install-Package wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Configurer votre application pour accéder au stockage d’objets blob
Ajoutez l'instruction include suivante au début du fichier C++ dans lequel vous voulez utiliser des API de stockage Azure pour accéder aux objets blob :  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Configuration d’une chaîne de connexion de stockage Azure
Un client de stockage Azure utilise une chaîne de connexion de stockage pour stocker des points de terminaison et des informations d’identification permettant d’accéder aux services de gestion des données. Lors de l’exécution d’une application cliente, vous devez spécifier la chaîne de connexion au stockage au format suivant, en indiquant le nom de votre compte de stockage et sa clé d’accès de stockage, correspondant aux valeurs *AccountName* et *AccountKey*, sur le [portail Azure](https://portal.azure.com). Pour plus d'informations sur les comptes et les clés d'accès de stockage, consultez la page [À propos des comptes Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Cet exemple vous montre comment déclarer un champ statique pour qu’il contienne une chaîne de connexion :  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Pour tester votre application sur votre ordinateur Windows local, vous pouvez utiliser [l’émulateur de stockage Microsoft Azure](../storage-use-emulator.md) installé avec le [Kit de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads/). L'émulateur de stockage est un utilitaire qui simule sur votre ordinateur de développement local les objets blob, les files d'attente et les services de Table disponibles dans Azure. L’exemple suivant vous montre comment déclarer un champ statique pour qu’il contienne une chaîne de connexion vers votre émulateur de stockage local :

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Pour démarrer l’émulateur de stockage Azure, sélectionnez le bouton **Démarrer** ou appuyez sur la touche **Windows**. Commencez à taper **Émulateur de stockage Azure**, puis sélectionnez **Émulateur de stockage Microsoft Azure** dans la liste des applications.  

Les exemples ci-dessous partent du principe que vous avez utilisé l’une de ces deux méthodes pour obtenir la chaîne de connexion de stockage.  

## <a name="retrieve-your-storage-account"></a>Récupérer votre compte de stockage
Vous pouvez utiliser la classe **cloud_storage_account** pour représenter les informations de votre compte de stockage. Pour extraire les informations de votre compte de stockage de la chaîne de connexion de stockage, vous pouvez utiliser la méthode **parse** .  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Ensuite, récupérez une référence pointant vers une classe **cloud_blob_client**, car elle permet de récupérer des objets représentant des conteneurs et des objets blob stockés dans le stockage d’objets blob. Le code suivant crée un objet **cloud_blob_client** en utilisant l’objet de compte de stockage récupéré ci-dessus :  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Activation Créez un conteneur.
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Cet exemple montre comment créer un conteneur, si celui-ci n’existe pas encore :  

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

Le nouveau conteneur est privé par défaut et vous devez indiquer votre clé d'accès de stockage pour télécharger des objets blob depuis ce conteneur. Si vous voulez que les fichiers (objets blob) du conteneur soient publics, vous pouvez configurer le conteneur en utilisant le code suivant :  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Tous les utilisateurs d’Internet peuvent afficher les objets blob d’un conteneur public, mais seuls ceux possédant la clé d’accès adéquate peuvent les modifier ou les supprimer.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Activation Charger un objet blob dans un conteneur
Le service de stockage d’objets blob Azure prend en charge les objets blob de blocs et de pages. Dans la plupart des cas, il est recommandé d’utiliser le type d’objet blob de blocs.  

Pour télécharger un fichier vers un objet blob de blocs, obtenez une référence de conteneur et utilisez-la pour obtenir une référence d’objet blob de blocs. Lorsque vous disposez d’une référence d’objet blob, vous pouvez télécharger un flux de données vers cet objet en appelant la méthode **upload_from_stream**. Si l’objet blob n’existe pas, cette opération entraîne sa création. S’il existe, il est remplacé. L’exemple suivant illustre le chargement d’un objet blob dans un conteneur en partant du principe que le conteneur existe déjà.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

Vous pouvez également utiliser la méthode **upload_from_file** pour télécharger un fichier vers un objet blob de blocs.

## <a name="how-to-list-the-blobs-in-a-container"></a>Activation Créer la liste des objets blob d’un conteneur
Pour créer une liste d’objets blob dans un conteneur, commencez par obtenir une référence pointant vers un conteneur. Vous pouvez ensuite utiliser la méthode **list_blobs** du conteneur pour récupérer les objets blob et/ou les répertoires qu’il contient. Pour accéder à l’ensemble complet des propriétés et méthodes d’un **list_blob_item** renvoyé, vous devez appeler la méthode **list_blob_item.as_blob** afin d’obtenir un objet **cloud_blob** ou la méthode **list_blob.as_directory** afin d’obtenir un objet cloud_blob_directory. Le code suivant illustre la récupération et la génération de l'URI de chaque élément du conteneur **my-sample-container** :

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

Pour plus d’informations sur les opérations de listage, consultez [Listage des ressources Azure Storage en C++](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Activation Télécharger des objets blob
Pour télécharger des objets blob, commencez par récupérer une référence d’objet blob, puis appelez la méthode **download_to_stream**. L’exemple suivant utilise la méthode **download_to_stream** pour transférer les contenus d’objets blob vers un objet de flux pouvant être rendu persistant dans un fichier local.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

Vous pouvez également utiliser la méthode **download_to_file** pour télécharger le contenu d’un objet blob dans un fichier.
De plus, vous pouvez aussi utiliser la méthode **download_text** pour télécharger le contenu d’un objet blob en tant que chaîne de texte.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>Activation Suppression d’objets blob
Pour supprimer un objet blob, commencez par obtenir une référence d’objet blob, puis appelez la méthode **delete_blob** associée.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous connaissez les bases du stockage d'objets blob, consultez les liens suivants pour en savoir plus sur Azure Storage.  

* [Utilisation du service de stockage de files d'attente à partir de C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Utilisation du stockage de tables à partir de C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Listage des ressources Azure Storage en C++](../storage-c-plus-plus-enumeration.md)
* [Référence de la bibliothèque cliente de stockage pour C++](https://azure.github.io/azure-storage-cpp)
* [Documentation d’Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Transfert de données avec l’utilitaire de ligne de commande AzCopy](../storage-use-azcopy.md)

