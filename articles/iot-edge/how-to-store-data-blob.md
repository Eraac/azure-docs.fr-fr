---
title: Stocker des objets blob de blocs sur des appareils - Azure IoT Edge | Microsoft Docs
description: Comprendre les fonctionnalités de hiérarchisation et durée de vie, consulter les opérations de stockage blob prises en charge et se connecter à votre compte de stockage d’objets blob.
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: dabaa06e224c6498c0080c4546c04f40e3919bb6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448536"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Stocker des données à la périphérie avec le Stockage Blob Azure sur IoT Edge (préversion)

Le Stockage Blob Azure sur IoT Edge fournit une solution de stockage des [objets blob de blocs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) en périphérie. Un module de stockage d’objets blob sur votre appareil IoT Edge se comporte comme un service d’objets blob de blocs Azure, à ceci près que ces derniers sont stockés localement sur votre appareil IoT Edge. Vous pouvez accéder à vos objets blob avec les mêmes méthodes que celles du kit SDK de stockage Azure, ou aux appels d’API objets blob de blocs auxquels vous êtes déjà habitué.

Ce module est fourni avec les fonctionnalités **deviceToCloudUpload** et **deviceAutoDelete**.
> [!NOTE]
> Le Stockage Blob Azure sur IoT Edge est disponible en [préversion publique](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Regardez la vidéo de présentation rapide
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

**deviceToCloudUpload** est une fonctionnalité configurable, ce qui vous permet de télécharger automatiquement les données à partir de votre stockage blob local vers Azure avec prise en charge de connectivité internet intermittente. Vous pouvez ainsi :

- Activer/désactiver la fonctionnalité deviceToCloudUpload.
- Choisissez l’ordre dans lequel les données sont copiées vers Azure comme NewestFirst ou OldestFirst.
- Spécifiez le compte de stockage Azure vers lequel vous souhaitez que vos données soient téléchargées.
- Spécifiez les conteneurs que vous voulez télécharger dans Azure. Ce module vous permet de spécifier des noms de conteneur source et cible.
- Choisissez la possibilité de supprimer les objets blob immédiatement, après le téléchargement dans le stockage cloud.
- Effectuez le téléchargement d’objets blob complet (à l’aide de l’opération `Put Blob`) et le téléchargement de niveau bloc (à l’aide des opérations `Put Block` et `Put Block List`).

Ce module utilise le téléchargement de niveau bloc, lorsque votre objet blob est constitué de blocs. Voici quelques scénarios courants :

- Votre application met à jour des blocs d’un objet blob précédemment téléchargé, ce module télécharge uniquement les blocs mis à jour et pas l’objet blob entier.
- Si le module télécharge un objet blob lorsque vous perdez la connexion Internet, il ne téléchargera que les blocs restants et pas l’objet blob entier lorsque vous serez de nouveau connecté.

Si un arrêt inattendu du processus (par exemple, une panne de courant) se produit pendant le téléchargement d’un objet blob, tous les blocs qui devaient être téléchargés seront téléchargés à nouveau, lorsque le module repassera en ligne.

**deviceAutoDelete** est une fonctionnalité configurable où le module supprime automatiquement vos objets blob du stockage local lorsque la durée spécifiée (exprimée en minutes) expire. Vous pouvez ainsi :

- Activer/désactiver la fonctionnalité deviceAutoDelete.
- Spécifiez la durée en minutes (deleteAfterMinutes) après laquelle les objets blob sont supprimés automatiquement.
- Choisissez la possibilité de conserver l’objet blob pendant son téléchargement en cas d’expiration de la valeur deleteAfterMinutes.

Voici de bons exemples de scénarios dans lesquels utiliser ce module : lorsque des données comme des vidéos, des images, des données financières, des données hospitalières ou toutes les données devant être stockées localement, doivent pouvoir être traitées ultérieurement en local ou transférées vers le cloud.

Cet article explique les concepts relatifs au Stockage Blob Azure sur le conteneur IoT Edge qui exécute un service d’objets blob sur votre appareil IoT Edge.

## <a name="prerequisites"></a>Prérequis

Un appareil Azure IoT Edge :

- Vous pouvez utiliser votre ordinateur de développement ou une machine virtuelle comme un appareil IoT Edge, en suivant les étapes décrites dans le Guide de démarrage rapide pour [Linux](quickstart-linux.md) ou pour les [Appareils Windows](quickstart.md).

- Le module de stockage Blob Azure sur IoT Edge prend en charge les configurations d’appareil suivantes :

  | Système d’exploitation | Architecture |
  | ---------------- | ----- | ----- |
  | Ubuntu Server 16.04 | AMD64 |
  | Ubuntu Server 18.04 | AMD64 |
  | Windows 10 IoT Entreprise | AMD64 |
  | Windows Server 2019 | AMD64 |
  | Raspbian-stretch | ARM32 |

Ressources cloud :

Un niveau standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) dans Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>Propriétés deviceToCloudUpload et deviceAutoDelete

Utilisez les propriétés souhaitées pour définir les propriétés deviceToCloudUpload et deviceAutoDelete. Elles peuvent être définies lors du déploiement ou modifiées ultérieurement en modifiant le jumeau de module sans avoir besoin de le redéployer. Nous vous recommandons de vérifier le « jumeau de module » et ses valeurs `reported configuration` et `configurationValidation` pour vous assurer qu’elles ont été correctement propagées.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Le nom de ce paramètre est `deviceToCloudUploadProperties`

| Champ | Valeurs possibles | Explication | Variable d’environnement |
| ----- | ----- | ---- | ---- |
| uploadOn | true, false | Par défaut, elle est définie sur `false`, si vous souhaitez l’activer, donnez-lui la valeur `true`| `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Vous permet de choisir l’ordre dans lequel les données sont copiées vers Azure. Par défaut, elle est définie sur `OldestFirst`. L’ordre est déterminé par l’heure de dernière modification de l’objet blob | `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` est une chaîne de connexion qui vous permet de spécifier le compte de stockage Azure vers lequel vous souhaitez que vos données soient téléchargées. Spécifiez `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Ajoutez la valeur EndpointSuffix appropriée d’Azure où les données sont chargées, elle varie entre Azure global, Azure Government et Microsoft Azure Stack. | `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}` <br><br> `"<source container name1>": {"target": "%d-%c"}` | Vous permet de spécifier les noms des conteneurs que vous voulez télécharger dans Azure. Ce module vous permet de spécifier des noms de conteneur source et cible. Si vous ne spécifiez pas le nom du conteneur cible, le nom `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>` lui sera automatiquement attribué. Vous pouvez créer des chaînes de modèle pour le nom du conteneur cible. Consultez la colonne des valeurs possibles. <br>* %h -> nom de l’IoT Hub (entre 3 et 50 caractères). <br>* %d -> ID d’appareil IoT Edge (entre 1 et 129 caractères). <br>* %m -> nom du module (entre 1 et 64 caractères). <br>* %c -> nom du conteneur source (entre 3 et 63 caractères). <br><br>La taille maximale du nom du conteneur est 63 caractères. En affectant automatiquement le nom du conteneur cible si la taille du conteneur dépasse 63 caractères, cela réduira chaque section (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) à 15 caractères. | `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target: <targetName>` |
| deleteAfterUpload | true, false | Par défaut, elle est définie sur `false`. Si elle est définie sur `true`, cela supprimera automatiquement les données après le chargement des données dans un stockage cloud. | `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Le nom de ce paramètre est `deviceAutoDeleteProperties`

| Champ | Valeurs possibles | Explication | Variable d’environnement |
| ----- | ----- | ---- | ---- |
| deleteOn | true, false | Par défaut, elle est définie sur `false`, si vous souhaitez l’activer, donnez-lui la valeur `true`| `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Indiquez le temps en minutes. Le module supprime automatiquement vos objets blob du stockage local à l’expiration de cette valeur | `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | Par défaut, elle est définie sur `true` et conservera l’objet blob pendant son chargement dans le stockage cloud si deleteAfterMinutes expire. Vous pouvez la définir sur `false` pour que les données soient supprimées dès que deleteAfterMinutes expire. Remarque : Pour que cette propriété fonctionne, uploadOn doit être définie sur true| `deviceAutoDeleteProperties__retainWhileUploading={false,true}` |

## <a name="configure-log-files"></a>Configurer des fichiers journaux

Pour plus d’informations sur la configuration des fichiers journaux de votre module, consultez ces [meilleures pratiques de production](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Se connecter au module de stockage d’objets blob

Vous pouvez utiliser le nom du compte et la clé de compte que vous avez configurés pour que votre module accède au stockage d’objets blob sur votre appareil IoT Edge.

Spécifiez votre appareil IoT Edge en tant que point de terminaison d’objets blob pour toutes les demandes de stockage que vous lui adressez. Vous pouvez [Créer une chaîne de connexion pour un point de terminaison de stockage explicite](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) en utilisant les informations de l’appareil IoT Edge et le nom du compte que vous avez configuré.

- Pour les modules qui sont déployés sur le même appareil, où le module de « Stockage Blob Azure sur IoT Edge » est en cours d’exécution, le point de terminaison d’objet blob est : `http://<module name>:11002/<account name>`.
- Pour des modules externes ou les applications qui s’exécutent sur un appareil différent de celui où le module de « Stockage Blob Azure sur IoT Edge » s’exécute, le trafic de données peut, en fonction de votre configuration réseau, atteindre l’appareil qui exécute le module de « Stockage Blob Azure sur IoT Edge » depuis votre module externe ou application. Le point de terminaison de l’objet blob est :
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Exemples de démarrage rapide de Stockage Blob Azure

La documentation du Stockage Blob Azure comprend des guides de démarrage rapide avec des exemples de code dans différents langages. Vous pouvez exécuter ces exemples pour tester le Stockage Blob Azure sur IoT Edge en changeant le point de terminaison des objets blob pour le connecter à votre module de stockage d’objets blob local.

Les exemples de guides de démarrage rapide suivants utilisent des langages qui sont également pris en charge par IoT Edge, vous pouvez donc les déployer en tant que modules IoT Edge en même temps que le module de stockage d’objets blob :

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.JS](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Se connecter à votre stockage local avec l’Explorateur Stockage Microsoft Azure

Vous pouvez utiliser l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour vous connecter à votre compte de stockage local.

1. Télécharger et installer l’Explorateur Stockage Microsoft Azure

1. Se connecter à Stockage Azure à l’aide d’une chaîne de connexion

1. Fournissez la chaîne de connexion : `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Suivez les étapes pour vous connecter.

1. Créer le conteneur à l’intérieur de votre compte de stockage local

1. Démarrez le téléchargement de fichiers en tant qu’objets blob de blocs.
   > [!NOTE]
   > Ce module ne prend pas en charge les objets blob de pages.

1. Vous pouvez choisir de vous connecter à vos comptes de stockage Azure où vous chargez les données. Cela vous offre une vue unique sur votre compte de stockage local et votre compte de stockage Azure

## <a name="supported-storage-operations"></a>Opérations de stockage prises en charge

Les modules de stockage d’objets blob sur IoT Edge utilisent les mêmes kits SDK de stockage Azure et sont compatibles avec la version 17-04-2017 de l’API de stockage Azure pour les points de terminaison d’objets blob de blocs. Les versions ultérieures dépendent des besoins des clients.

Les opérations de Stockage Blob Azure ne sont pas toutes prises en charge par le stockage Blob Azure sur IoT Edge. Cette section liste le statut de chacune d’entre elles.

### <a name="account"></a>Compte

Prises en charge :

- Répertorier les conteneurs

Non prises en charge :

- Obtenir et définir les propriétés du service BLOB
- Demande d’objet blob préliminaire
- Obtenir les statistiques du service BLOB
- Obtenir les informations de compte

### <a name="containers"></a>Containers

Prises en charge :

- Créer et supprimer un conteneur
- Obtenir les métadonnées et les propriétés de conteneur
- Liste des objets blob
- Obtenir et définir une ACL de conteneur
- Définir les métadonnées de conteneur

Non prises en charge :

- Louer à bail un conteneur

### <a name="blobs"></a>Objets blob

Prises en charge :

- Placer, obtenir et supprimer un objet blob
- Obtenir et définir les propriétés d’un objet blob
- Obtenir et définir les métadonnées d’un objet blob

Non prises en charge :

- Louer à bail un objet blob
- Faire une capture instantanée d’un objet blob
- Copier et abandonner la copie d’un objet blob
- Annuler la suppression d’un objet blob
- Définir un niveau d’objet blob

### <a name="block-blobs"></a>Objets blob de blocs

Prises en charge :

- Placer un bloc
- Placer et obtenir une liste de blocs

Non prises en charge :

- Placer un bloc à partir d’une URL

## <a name="release-notes"></a>Notes de publication

Voici les [notes de publication dans docker hub](https://hub.docker.com/_/microsoft-azure-blob-storage) pour ce module

## <a name="feedback"></a>Commentaires

Vos commentaires sont importants et nous permettent de faire de ce module et de ses fonctionnalités utiles et faciles des outils faciles à utiliser et pratiques. Partagez vos commentaires et dites-nous comment nous pouvons les améliorer.

Vous pouvez nous contacter au absiotfeedback@microsoft.com

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [Déploiement du Stockage Blob Azure sur IoT Edge](how-to-deploy-blob.md)
