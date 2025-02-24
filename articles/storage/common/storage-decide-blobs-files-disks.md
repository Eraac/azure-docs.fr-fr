---
title: Quand utiliser des objets BLOB Azure, des fichiers Azure ou des disques Azure
description: Découvrez les différentes façons de stocker les données dans Azure et d’y accéder pour choisir la technologie la mieux adaptée.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 30c7c1c50e59162817d7cfab0d852d8e034457d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969410"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Quand utiliser des objets BLOB Azure, des fichiers Azure ou des disques Azure

Microsoft Azure propose plusieurs fonctionnalités dans le stockage Azure pour stocker vos données dans le cloud et y accéder. Cet article traite des fichiers, des objets BLOB et des disques Azure, afin de vous aider à choisir entre ces fonctionnalités.

## <a name="scenarios"></a>Scénarios

Le tableau suivant compare les fichiers, les objets blob et les disques, et il présente des exemples de scénarios appropriés pour chacun.

| Fonctionnalité | Description | Quand utiliser |
|--------------|-------------|-------------|
| **Azure Files** | Fournit une interface SMB, des bibliothèques clientes et une [interface REST](/rest/api/storageservices/file-service-rest-api) qui permet d’accéder en tout lieu aux fichiers stockés. | Vous souhaitez développer et transférer une application dans le cloud qui utilise déjà les API du système de fichiers natif pour partager des données avec d’autres applications s’exécutant dans Azure.<br/><br/>Vous souhaitez stocker les outils de développement et de débogage qui doivent être accessibles à partir de nombreuses machines virtuelles. |
| **Objets blob Azure** | Fournit des bibliothèques de client et une [interface REST](/rest/api/storageservices/blob-service-rest-api) qui permet de stocker des données non structurées et d’y accéder à grande échelle dans des objets blob de blocs.<br/><br/>Prend également en charge [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) pour les solutions d’analytique d’entreprise Big Data. | Vous souhaitez que votre application prenne en charge le streaming et l’accès aléatoire.<br/><br/>Vous souhaitez être en mesure d’accéder aux données d’application à partir de n’importe quel endroit.<br/><br/>Vous souhaitez générer un Data Lake d’entreprise sur Azure et effectuer une analytique de Big Data. |
| **Disques Azure** | Fournit des bibliothèques clientes et une [interface REST](/rest/api/compute/manageddisks/disks/disks-rest-api) qui permet de stocker de manière permanente les données et d’y accéder à partir d’un disque dur virtuel joint. | Vous souhaitez développer et transférer des applications qui utilisent les API du système de fichiers natif pour lire et écrire des données sur des disques persistants.<br/><br/>Vous souhaitez stocker des données dont l’accès n’est pas requis à l’extérieur de la machine virtuelle à laquelle le disque est joint. |

## <a name="comparison-files-and-blobs"></a>Comparaison : fichiers et objets blob

Le tableau suivant compare les fichiers Azure et les objets blob Azure.  
  
||||  
|-|-|-|  
|**Attribut**|**Objets blob Azure**|**Azure Files**|  
|Options de durabilité||LRS, ZRS, GRS, RA-GRS|LRS, ZRS, GRS|  
|Accessibilité|API REST|API REST<br /><br /> SMB 2.1 et SMB 3.0 (API du système de fichiers standard)|  
|Connectivité|API REST : monde entier|API REST : monde entier<br /><br /> SMB 2.1 : région<br /><br /> SMB 3.0 : monde entier|  
|Points de terminaison|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|Répertoires|Espace de noms plat|Vrais objets d’annuaire|  
|Sensibilité des noms à la casse|Respect de la casse|Non sensible à la casse, mais la casse est conservée|  
|Capacité|Limite du compte jusqu'à 2 Tio |Partages de fichiers de 5 Tio|  
|Débit|Jusqu’à 60 Mio/s par objet blob de blocs|Jusqu’à 60 Mio/s par partage|  
|Taille de l’objet|Jusqu'à environ 4,75 Tio par objet blob de blocs|Jusqu'à 1 Tio par fichier|  
|Capacité facturée|En fonction des octets écrits|En fonction de la taille de fichier|  
|Bibliothèques clientes|Plusieurs langages|Plusieurs langages|  
  
## <a name="comparison-files-and-disks"></a>Comparaison : fichiers et disques

Les fichiers Azure complètent les disques Azure. Un disque ne peut être joint qu’à une seule machine virtuelle Azure à la fois. Les disques sont des VHD de format fixe stockés en tant qu’objets blob de pages dans le stockage Azure, et ils sont utilisés par la machine virtuelle pour stocker des données durables. Les partages de fichiers dans les fichiers Azure sont accessibles de la même façon que le disque local (par le biais des API du système de fichiers natif) et peuvent être partagés entre plusieurs machines virtuelles.  

Le tableau suivant compare les fichiers Azure et les disques Azure.  

||||  
|-|-|-|  
|**Attribut**|**Disques Azure**|**Azure Files**|  
|Étendue|Exclusif à une seule machine virtuelle|Accès partagé entre plusieurs machines virtuelles|  
|Captures instantanées et copie|OUI|OUI|  
|Configuration|Connexion au démarrage de la machine virtuelle|Connexion après le démarrage de la machine virtuelle|  
|Authentication|Intégration|Configuration avec net use|  
|Accès à l’aide de REST|Les fichiers du disque dur virtuel ne sont pas accessibles|Les fichiers stockés dans un partage sont accessibles|  
|Taille maximale|Disque de 32 Tio|Partage de fichiers de 5 Tio et fichier de 1 Tio au sein du partage|  
|Nombre maximal d’IOPS|20 000 IOPS|1 000 E/S par seconde|  
|Débit|Jusqu’à 900 Mio/s par disque|La cible est de 60 Mio/s par partage de fichiers (pouvant être plus élevée pour des tailles d’e/s plus élevées)|  

## <a name="next-steps"></a>Étapes suivantes

Lors des décisions concernant le stockage et l’accès de vos données, vous devez également prendre en considération les coûts impliqués. Pour plus d’informations, consultez [Tarification du stockage Azure](https://azure.microsoft.com/pricing/details/storage/).
  
Certaines fonctionnalités SMB ne sont pas applicables au cloud. Pour plus d’informations, consultez [Features not supported by the Azure File service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service) (Fonctionnalités non prises en charge par le service Azure File).
  
Pour plus d’informations sur les disques, consultez notre [Introduction aux disques managés](../../virtual-machines/windows/managed-disks-overview.md) et l’article [Comment attacher un disque de données à une machine virtuelle Windows](../../virtual-machines/windows/attach-managed-disk-portal.md).
