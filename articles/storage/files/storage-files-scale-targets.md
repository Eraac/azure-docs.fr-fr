---
title: Objectifs de performance et d’extensibilité d'Azure Files | Microsoft Docs
description: Obtenez plus d’informations sur les objectifs d’extensibilité et de performances pour Azure Files, y compris la capacité, le taux de demandes et les limites de bande passante entrante et sortante.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 5/5/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 91ec65e17b77ccb3864fce45e30729ff420a48b6
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542644"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Objectifs de performance et d’extensibilité d'Azure Files

[Azure Files](storage-files-introduction.md) offre des partages de fichiers entièrement gérés dans le cloud, accessibles via le protocole SMB standard. Cet article présente les objectifs de performance et d’extensibilité pour Azure Files et Azure File Sync.

Les objectifs de performance et d’extensibilité répertoriés ici sont des objectifs haut de gamme, mais ils peuvent être affectés par d'autres variables de votre déploiement. Par exemple, le débit d’un fichier peut également être limité par votre bande passante réseau disponible, et pas seulement par les serveurs qui hébergent le service Azure Files. Nous vous recommandons vivement de tester votre modèle d’utilisation pour déterminer si l’extensibilité et les performances d'Azure Files répondent à vos besoins. Nous sommes résolus à augmenter ces limites au fil du temps. N’hésitez pas à nous faire part de vos remarques, soit dans les commentaires ci-dessous ou sur [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), concernant les limites que vous aimeriez voir augmenter.

## <a name="azure-storage-account-scale-targets"></a>Objectifs de mise à l'échelle d'un compte de stockage Azure

La ressource parente d’un partage de fichiers Azure est un compte de stockage Azure. Un compte de stockage représente un pool de stockage dans Azure, qui peut être utilisé par plusieurs services de stockage, notamment Azure Files, pour stocker les données. Les autres services qui stockent les données dans des comptes de stockage Azure Blob, Azure Queue et Azure Table. Les objectifs suivants s’appliquent à tous les services de stockage qui stockent les données dans un compte de stockage :

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> L’utilisation d’un compte de stockage à usage général provenant d’autres services de stockage affecte les partages de fichiers Azure dans votre compte de stockage. Par exemple, si vous atteignez la capacité de compte de stockage maximale avec un stockage Blob Azure, vous ne pouvez plus créer de fichiers sur votre partage de fichiers Azure, même si sa taille est inférieure à la taille maximale de partage.

## <a name="azure-files-scale-targets"></a>Objectifs de mise à l’échelle Azure Files

Il existe trois catégories de limitations à prendre en compte pour Azure Files : les comptes de stockage, les partages et les fichiers.

Par exemple :  Avec des partages de fichiers premium, un partage unique peut atteindre 100 000 IOPS et un seul fichier peut scale up jusqu’à 5 000 IOPS. Par conséquent, si vous avez trois fichiers dans un partage, celui-ci peut vous permettre d’obtenir jusqu’à 15 000 IOPS.

### <a name="standard-storage-account-limits"></a>Limites d’un compte de stockage standard

Consultez la section [Objectifs de mise à l’échelle d’un compte de stockage Azure](#azure-storage-account-scale-targets) pour en savoir plus sur ces limites.

### <a name="premium-filestorage-account-limits"></a>Limites d’un compte FileStorage premium

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Limites d’un compte de stockage s’appliquent à tous les partages. Vous pouvez scale up des comptes FileStorage au maximum uniquement si vous ne disposez que d’un seul partage par compte FileStorage.

### <a name="file-share-and-file-scale-targets"></a>Partage de fichiers et objectifs de mise à l’échelle des fichiers

> [!NOTE]
> Les partages de fichiers standard d’une taille supérieure à 5 Tio sont en préversion et présentent certaines limitations.
> Pour obtenir la liste des limitations et s’inscrire à la préversion de ces tailles de partage de fichiers plus importantes, consultez la section [Partages de fichiers standard](storage-files-planning.md#standard-file-shares) du guide de planification.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Objectifs de mise à l’échelle d’Azure File Sync

Azure File Sync a été conçu pour proposer un usage illimité, mais cela n’est pas toujours possible. Le tableau suivant indique les limites de tests réalisés par Microsoft, ainsi que les cibles constituant des limites matérielles :

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-file-sync-performance-metrics"></a>Métriques de performances Azure File Sync

Étant donné que l’agent Azure File Sync s’exécute sur un ordinateur Windows Server qui se connecte aux partages de fichiers Azure, les performances de synchronisation réelles dépendent de plusieurs facteurs dans votre infrastructure : Windows Server et la configuration de disque sous-jacente, la bande passante réseau entre le serveur et le stockage Azure, la taille des fichiers, la taille totale du jeu de données et l’activité sur le jeu de données. Comme Azure File Sync fonctionne au niveau du fichier, les caractéristiques de performances d’une solution Azure File Sync est exprimée de façon optimale en nombre d’objets (fichiers et répertoires) traités par seconde.

Pour Azure File Sync, les performances sont essentielles dans deux phases :

1. **Approvisionnement initial unique** : pour optimiser les performances au moment de l’approvisionnement initial, consultez [Intégrer Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) pour plus d’informations sur un déploiement optimal.
2. **Synchronisation continue** : une fois que les données sont initialement approvisionnées dans les partages de fichiers Azure, Azure File Sync synchronise plusieurs points de terminaison.

Pour vous aider à planifier votre déploiement pour chacune des phases, voici les résultats observés durant le test interne sur un système avec une configuration

| Configuration système |  |
|-|-|
| UC | 64 cœurs virtuels avec cache L3 64 MiB |
| Mémoire | 128 Go |
| Disque | Disques SAS avec RAID 10 et cache protégé par batterie |
| Réseau | Réseau 1 Gbit/s |
| Charge de travail | Serveur de fichiers à usage général|

| Provisionnement initial unique  |  |
|-|-|
| Nombre d’objets | 25 millions d’objets |
| Taille du jeu de données| ~4,7 Tio |
| Taille de fichier moyenne | ~200 Kio (plus gros fichier : 100 Gio) |
| Débit de chargement | 20 objets par seconde |
| Débit de téléchargement d’espace de noms* | 400 objets par seconde |

*Quand un point de terminaison de serveur est créé, l’agent Azure File Sync ne télécharge pas le contenu du fichier. Il synchronise d’abord l’espace de noms complet, puis déclenche un rappel en arrière-plan pour télécharger les fichiers dans leur intégralité ou, si la hiérarchisation cloud est activée, sur la stratégie de hiérarchisation de cloud définie sur le point de terminaison.

| Synchronisation continue  |   |
|-|--|
| Nombre d’objets synchronisés| 125 000 objets (variation ~1 %) |
| Taille du jeu de données| 50 GiB |
| Taille de fichier moyenne | ~ 500 Kio |
| Débit de chargement | 30 objets par seconde |
| Débit de téléchargement complet* | 60 objets par seconde |

* Si la hiérarchisation cloud est activée, vous devez avoir de meilleures performances, car seules certaines données de fichier sont téléchargées. Azure File Sync télécharge uniquement les données des fichiers mis en cache quand elles changent sur un point de terminaison. Pour les fichiers hiérarchisés ou nouvellement créés, l’agent ne télécharge pas les données de fichier et, à la place, synchronise uniquement l’espace de noms sur tous les points de terminaison de serveur. L’agent prend également en charge les téléchargements partiels de fichiers hiérarchisés à mesure qu’ils sont consultés par l’utilisateur. 

> [!Note]  
> Les nombres ci-dessus ne sont pas une indication des performances que vous allez rencontrer. Les performances réelles dépendent de plusieurs facteurs comme indiqué au début de cette section.

En règle générale pour votre déploiement, gardez ces quelques points à l’esprit :

- Le débit d’objets est proportionnel au nombre de groupes de synchronisation sur le serveur. Si vous fractionnez les données en plusieurs groupes de synchronisation sur un serveur, vous obtenez un meilleur débit qui est également limité par le serveur et le réseau.
- Le débit d’objets est inversement proportionnel au débit de MiB par seconde. Pour les plus petits fichiers, le débit est plus élevé en termes de nombre d’objets traités par seconde, mais inférieur en termes de MiB par seconde. À l’inverse, pour les plus gros fichiers, moins d’objets sont traités par seconde, mais le débit de MiB par seconde est supérieur. Le débit de MiB par seconde est limité par les objectifs d’échelle d’Azure Files.

## <a name="see-also"></a>Voir aussi

- [Planification d’un déploiement Azure Files](storage-files-planning.md)
- [Planification d’un déploiement de synchronisation de fichiers Azure](storage-sync-files-planning.md)
- [Objectifs d'extensibilité et de performance pour d'autres services de stockage](../common/storage-scalability-targets.md)
