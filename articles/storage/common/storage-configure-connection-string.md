---
title: Configurer une chaîne de connexion pour le stockage Azure
description: Configurez une chaîne de connexion pour un compte de stockage Azure. Une chaîne de connexion inclut les informations nécessaires pour autoriser l’accès à un compte de stockage, depuis votre application et pendant l’exécution, au moyen de l’autorisation Clé partagée.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7c83b382f8aca3d8fda1c0de4785c51f3f3b1fc5
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67302524"
---
# <a name="configure-azure-storage-connection-strings"></a>Configuration des chaînes de connexion Stockage Azure

Une chaîne de connexion inclut les informations d’authentification nécessaires pour que l’application accède aux données dans un compte de stockage Azure, à l’aide de l’autorisation Clé partagée pendant l’exécution. Vous pouvez configurer les chaînes de connexion pour effectuer les opérations suivantes :

* Connexion à l’émulateur de stockage Azure
* Accès à un compte de stockage dans Azure
* Accès aux ressources spécifiées dans Azure via une signature d’accès partagé (SAS).

[!INCLUDE [storage-recommend-azure-ad-include](../../../includes/storage-recommend-azure-ad-include.md)]

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-and-copy-a-connection-string"></a>Afficher et copier une chaîne de connexion

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="store-a-connection-string"></a>Stocker une chaîne de connexion

Votre application doit accéder à la chaîne de connexion pendant l’exécution pour autoriser les requêtes transmises au stockage Azure. Plusieurs options vous permettant de stocker votre chaîne de connexion s’offrent à vous :

* Vous pouvez stocker votre chaîne de connexion dans une variable d’environnement.
* Une application s’exécutant sur le bureau ou sur un appareil peut stocker la chaîne de connexion dans un fichier **app.config** ou **web.config**. Ajoutez la chaîne de connexion dans la section **AppSettings** de ces fichiers.
* Une application s’exécutant dans un service cloud Azure peut stocker la chaîne de connexion dans le [schéma de configuration du service Azure (fichier .cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Ajoutez la chaîne de connexion à la section **ConfigurationSettings** du fichier de configuration du service.

Le stockage de votre chaîne de connexion dans un fichier de configuration facilite la mise à jour de la chaîne de connexion qui vous permet de basculer entre l’émulateur de stockage et un compte de stockage Azure dans le cloud. Il vous suffit de modifier la chaîne de connexion pour la faire pointer vers votre environnement cible.

Vous pouvez utiliser [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) pour accéder à votre chaîne de connexion lors de l’exécution, quel que soit l’environnement d’exécution de votre application.

## <a name="configure-a-connection-string-for-the-storage-emulator"></a>Configurer une chaîne de connexion pour l’émulateur de stockage

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Pour plus d’informations sur l’émulateur de stockage, consultez [Utilisation de l'émulateur de stockage Azure pour le développement et le test](storage-use-emulator.md).

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>Configurer une chaîne de connexion pour un compte de stockage Azure

Pour créer une chaîne de connexion pour votre compte de stockage Azure, utilisez le format suivant. Indiquez si vous souhaitez vous connecter au compte de stockage via HTTPS (recommandé) ou HTTP, remplacez `myAccountName` par le nom de votre compte de stockage et remplacez `myAccountKey` par la touche d’accès rapide à votre compte :

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Par exemple, votre chaîne de connexion peut ressembler à ceci :

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Même si le stockage Azure prend en charge HTTP et HTTPS au sein d’une chaîne de connexion, nous vous *conseillons vivement d’utiliser HTTPS*.

> [!TIP]
> Vous trouverez les chaînes de connexion de votre compte de stockage dans le [portail Azure](https://portal.azure.com). Accédez à **PARAMÈTRES** > **Clés d’accès** dans le panneau de menu de votre compte de stockage pour afficher les chaînes de connexion pour les clés d’accès primaire et secondaire.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Création d’une chaîne de connexion à l’aide d’une signature d’accès partagé

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Création d’une chaîne de connexion pour un point de terminaison de stockage explicite

Vous pouvez spécifier les points de terminaison de service explicites dans votre chaîne de connexion au lieu d’utiliser les points de terminaison par défaut. Pour créer une chaîne de connexion spécifiant un point de terminaison explicite, indiquez le point de terminaison complet de chaque service, ainsi que le protocole (HTTPS (recommandé) ou HTTP) au format suivant :

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Si vous avez mappé votre point de terminaison Stockage Blob à un [domaine personnalisé](../blobs/storage-custom-domain-name.md), il vaut mieux spécifier un point de terminaison explicite. Dans ce cas, vous pouvez spécifier votre point de terminaison personnalisé pour le stockage Blob dans votre chaîne de connexion. Vous pouvez éventuellement spécifier les points de terminaison par défaut pour les autres services si votre application les utilise.

Voici un exemple de chaîne de connexion qui spécifie un point de terminaison explicite pour le service Blob :

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Cet exemple spécifie des points de terminaison explicites pour tous les services, notamment un domaine personnalisé pour le service Blob :

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

Les valeurs des points de terminaison dans une chaîne de connexion sont utilisées pour construire les URI de demande aux services de stockage. Elles indiquent la forme des URI renvoyés à votre code.

Si vous avez mappé un point de terminaison de stockage à un domaine personnalisé et omettez ce point de terminaison d’une chaîne de connexion, vous ne pourrez pas accéder aux données de ce service avec votre code à l’aide de cette chaîne de connexion.

> [!IMPORTANT]
> Les valeurs des points de terminaison de service dans vos chaînes de connexion doivent être des URI correctement formés, notamment `https://` (recommandé) ou `http://`. Étant donné que le stockage Azure ne prend pas encore en charge HTTPS pour les domaines personnalisés, vous *devez* spécifier `http://` pour n’importe quel URI de point de terminaison qui pointe vers un domaine personnalisé.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Création d’une chaîne de connexion avec un suffixe de point de terminaison

Pour créer une chaîne de connexion d’un service de stockage dans les régions ou les instances, avec des suffixes de point de terminaison différents, comme pour Azure China 21Vianet ou Azure Government, utilisez le format de chaîne de connexion suivant. Indiquez si vous souhaitez vous connecter au compte de stockage via HTTPS (recommandé) ou HTTP, remplacez `myAccountName` par le nom de votre compte de stockage, remplacez `myAccountKey` par votre clé d’accès au compte et remplacez `mySuffix` par le suffixe d’URI :

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Voici un exemple de chaîne de connexion pour des services de stockage dans Azure China 21Vianet :

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Analyse d’une chaîne de connexion

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation de l’émulateur de stockage Azure pour le développement et le test](storage-use-emulator.md)
* [Explorateurs du stockage Azure](storage-explorers.md)
* [Utilisation des signatures d’accès partagé (SAP)](storage-dotnet-shared-access-signature-part-1.md)

