---
title: Exigences concernant le service Azure Import/Export | Microsoft Docs
description: L’objectif est ici de comprendre la configuration matérielle et logicielle requise pour le service Azure Import/Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 4d002a82e3968e0462e5f6e775ec147669c01b0d
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147948"
---
# <a name="azure-importexport-system-requirements"></a>Configuration système requise du service Azure Import/Export

Cet article décrit les principales exigences de votre service Azure Import/Export. Nous vous recommandons de lire attentivement les informations suivantes avant d’utiliser le service Import/Export, puis d’y revenir par la suite si nécessaire.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Pour préparer les disques durs à l’aide de l’outil WAImportExport, un certain nombre de **systèmes d’exploitation 64 bits autorisant le chiffrement de lecteur BitLocker** sont pris en charge.


|Plateforme |Version |
|---------|---------|
|Windows     | Windows 7 Entreprise, Windows 7 Édition intégrale <br> Windows 8 Pro, Windows 8 Entreprise, Windows 8.1 Professionnel, Windows 8.1 Entreprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012 et Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Autres logiciels nécessaires pour le client Windows

|Plateforme |Version |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Comptes de stockage pris en charge

Le service Azure Import/Export prend en charge les types de comptes de stockage suivants :

- Comptes de stockage v2 à usage général standard (recommandés pour la plupart des scénarios)
- Comptes de stockage d’objets blob
- Comptes de stockage v1 à usage général (déploiements Classic ou Azure Resource Manager) 

Pour plus d’informations sur les comptes de stockage, consultez [Vue d’ensemble des comptes de stockage Azure](storage-account-overview.md).

Chaque tâche peut servir à transférer des données vers ou à partir d'un seul compte de stockage. Autrement dit, une même tâche d’importation/exportation ne peut pas englober plusieurs comptes de stockage. Pour plus d'informations sur la création d'un compte de stockage, consultez la page [Création d'un compte de stockage](storage-quickstart-create-account.md).

> [!IMPORTANT] 
> Le service Azure Import/Export ne prend pas en charge les comptes de stockage où la fonctionnalité [Points de terminaison de service de réseaux virtuels](../../virtual-network/virtual-network-service-endpoints-overview.md)  a été activée. 

## <a name="supported-storage-types"></a>Types de stockage pris en charge

Les types de stockage suivants sont pris en charge avec le service Azure Import/Export.


|Travail  |Service de stockage |Pris en charge  |Non pris en charge  |
|---------|---------|---------|---------|
|Importer     |  Stockage d'objets blob Azure <br><br> Présentation du stockage de fichiers       | Objets blob de blocs et de pages pris en charge <br><br> Fichiers pris en charge          |
|Exportation     |   Stockage d'objets blob Azure       | Objets blob de blocs, de pages et d’ajout pris en charge         | Fichiers Azure non pris en charge


## <a name="supported-hardware"></a>Matériel pris en charge 

Dans le cadre du service Azure Import/Export, vous avez besoin de disques pris en charge pour copier les données.

### <a name="supported-disks"></a>Disques pris en charge

Les types de disques suivants sont pris en charge avec le service Azure Import/Export.


|Type de disque  |Size  |Pris en charge |Non pris en charge  |
|---------|---------|---------|---------|
|SSD    |   2,5"      |SATA III          |  USB       |
|HDD     |  2,5"<br>3,5"       |SATA II, SATA III         |Disque dur externe avec adaptateur USB intégré <br> Disque situé à l’intérieur du boîtier d’un disque dur externe         |


Un travail d’importation/exportation peut avoir à lui seul :
- Un maximum de 10 disques HHD/SSD
- Un mélange de disques HDD/SSD de n’importe quelle taille

Il est possible de répartir un grand nombre de disques entre plusieurs tâches, et il n’existe aucune limite quant au nombre de tâches pouvant être créées. Dans le cas des tâches d’importation, seul le premier volume de données du lecteur est traité. Il doit être formaté avec NTFS.

Au moment de préparer les disques durs et de copier les données avec l’outil WAImportExport, vous pouvez utiliser les adaptateurs USB externes. La plupart des adaptateurs USB 3.0 ou version ultérieure du commerce doivent fonctionner. 


## <a name="next-steps"></a>Étapes suivantes

* [Configurer l’outil WAImportExport](storage-import-export-tool-how-to.md)
* [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
* [Exemple d’API REST Azure Import Export](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

