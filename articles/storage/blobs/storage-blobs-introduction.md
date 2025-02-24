---
title: Présentation du stockage Blob - Stockage d’objets dans Azure
description: Le stockage Blob Azure stocke de grandes quantités de données d’objet non structurées, telles que des données texte ou binaires. Le stockage Blob Azure est hautement évolutif et disponible. Les clients peuvent accéder aux objets de données dans le stockage Blob avec PowerShell ou Azure CLI, par programmation via des bibliothèques clientes de stockage Azure, ou par REST.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 05/24/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e216503cac2db55115bd4c1b5bf0e2f6e50355fc
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190853"
---
# <a name="introduction-to-azure-blob-storage"></a>Présentation du Stockage Blob Azure

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

## <a name="blob-storage-resources"></a>Ressources du stockage Blob

Le stockage Blob offre trois types de ressources :

- Le **compte de stockage**. 
- Un **conteneur** dans le compte de stockage.
- Un **objet blob** dans un conteneur. 

Le diagramme suivant montre la relation entre ces ressources.

![relation entre l’objet blob de compte et la ressource de conteneur](./media/storage-blob-introduction/blob1.png)

### <a name="storage-accounts"></a>Comptes de stockage

Un compte de stockage fournit un espace de noms unique dans Azure pour vos données. Chaque objet que vous stockez dans le stockage Azure a une adresse qui comprend votre nom de compte unique. La combinaison du nom du compte et du point de terminaison du Stockage Blob Azure forme l’adresse de base des objets dans votre compte de stockage.

Par exemple, si le nom de votre compte de stockage est *mystorageaccount*, le point de terminaison par défaut pour Stockage Blob est :

```
http://mystorageaccount.blob.core.windows.net 
```

Pour créer un compte de stockage, consultez [Créez un compte de stockage](../common/storage-quickstart-create-account.md). Pour plus d’informations sur les comptes de stockage, consultez [Vue d’ensemble des comptes de stockage Azure](../common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="containers"></a>Containers

Un conteneur regroupe un ensemble d’objets blob, à la manière d’un répertoire dans un système de fichiers. Un compte de stockage peut contenir un nombre illimité de conteneurs, et un conteneur peut stocker un nombre illimité d’objets blob. 

  > [!NOTE]
  > Le nom du conteneur doit être en minuscules. Pour plus d’informations sur le nommage des conteneurs, consultez [Nommage et référencement des conteneurs, des objets blob et des métadonnées](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

### <a name="blobs"></a>Objets blob
 
Le service Stockage Azure prend en charge trois types d’objets blob :

* Les **objets blob de blocs** stockent du texte et des données binaires, jusqu’à environ 4,7 To. Ils sont composés de blocs de données qui peuvent être gérés individuellement.
* Les **objets blob d’ajout** se composent de blocs, comme les objets blob de blocs, mais sont optimisés pour les opérations d’ajout. Les objets blob d’ajout sont parfaits pour les scénarios tels que la consignation des données issues des machines virtuelles.
* Les **objets blob de pages** stockent des fichiers à accès aléatoire d’une taille maximale de 8 To. Les objets blob de pages stockent les fichiers de disque dur virtuel et servent de disques pour les machines virtuelles Azure. Pour plus d’informations sur les objets blob de pages, consultez [Vue d’ensemble des objets blob de pages Azure](storage-blob-pageblob-overview.md)

Pour plus d’informations sur les différents types d’objets blob, consultez [Présentation des objets blob de blocs, des objets blob d’ajout et des objets blob de pages](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

## <a name="move-data-to-blob-storage"></a>Déplacer des données vers le stockage Blob

Plusieurs solutions existent pour migrer des données existantes vers le stockage Blob :

- **AzCopy** est un outil en ligne de commande facile à utiliser pour Windows et Linux, qui copie les données vers et depuis le stockage Blob, entre des conteneurs ou entre des comptes de stockage. Pour plus d’informations sur AzCopy, consultez [Transfert de données avec AzCopy v10 (préversion)](../common/storage-use-azcopy-v10.md) . 
- La **bibliothèque de déplacement des données de Stockage Azure** est une bibliothèque .NET pour déplacer des données entre les services Stockage Azure. L’utilitaire AzCopy est créé avec la bibliothèque de déplacement des données. Pour plus d’informations, consultez la [documentation de référence](/dotnet/api/microsoft.azure.storage.datamovement) de la bibliothèque de déplacement des données. 
- **Azure Data Factory** prend en charge la copie de données vers et depuis le stockage Blob avec une clé de compte, une signature d’accès partagé, un principal du service ou des identités managées pour les authentifications des ressources Azure. Pour plus d’informations, consultez [Copier des données depuis/vers Azure Data Lake Store à l’aide d’Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 
- **Blobfuse** est un pilote de système de fichiers virtuel pour Stockage Blob Azure. Vous pouvez utiliser blobfuse pour accéder à vos données d’objets blob de blocs existantes dans votre compte de stockage via le système de fichiers Linux. Pour plus d’informations, consultez [Guide pratique pour monter le stockage Blob comme système de fichiers avec blobfuse](storage-how-to-mount-container-linux.md).
- Le service **Azure Data Box** permet de transférer des données locales vers le stockage Blob quand des gros jeux de données ou des contraintes réseau rendent infaisable le chargement de données via le réseau. Selon la taille de vos données, vous pouvez demander des appareils [Azure Data Box Disk](../../databox/data-box-disk-overview.md), [Azure Data Box](../../databox/data-box-overview.md) ou [Azure Data Box Heavy](../../databox/data-box-heavy-overview.md) à Microsoft. Vous pouvez ensuite copier vos données sur ces appareils et les expédier à Microsoft qui les chargera dans le stockage Blob.
- Le **service Azure Import/Export** offre un moyen d’importer et d’exporter de grandes quantités de données depuis et vers votre compte de stockage à l’aide de disques durs que vous fournissez. Pour plus d’informations, consultez [Utiliser le service Microsoft Azure Import/Export pour transférer des données vers le stockage Blob](../common/storage-import-export-service.md).

## <a name="next-steps"></a>Étapes suivantes

* [Créez un compte de stockage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Objectifs de performance et d’extensibilité du Stockage Azure](../common/storage-scalability-targets.md)
