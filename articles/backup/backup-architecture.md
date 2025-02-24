---
title: Architecture de Sauvegarde Azure
description: Fournit une vue d’ensemble de l’architecture, des composants et des processus utilisés par le service Sauvegarde Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: dacurwin
ms.openlocfilehash: 44bf85eafe3f5cfa801b6c845a51e3dcd5e1262a
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466870"
---
# <a name="azure-backup-architecture"></a>Architecture de Sauvegarde Azure

Le [service Sauvegarde Azure](backup-overview.md) permet de sauvegarder des données sur la plateforme cloud Microsoft Azure. Cet article résume l’architecture, les composants et les processus de Sauvegarde Azure. 

## <a name="what-does-azure-backup-do"></a>Que fait Sauvegarde Azure ?

Le service Sauvegarde Azure sauvegarde les données, l’état de la machine et les charges de travail en cours d’exécution sur des ordinateurs locaux et des instances de machines virtuelles Azure. Il existe plusieurs scénarios de Sauvegarde Azure.

## <a name="how-does-azure-backup-work"></a>Comment fonctionne Sauvegarde Azure ?

Vous pouvez sauvegarder des machines et des données à l’aide de plusieurs méthodes :

- **Sauvegarder des ordinateurs locaux** :
    - Vous pouvez sauvegarder des machines Windows locales directement sur Azure à l’aide de l’agent Microsoft Azure Recovery Services (MARS) de Sauvegarde Azure. Les machines Linux ne sont pas prises en charge.
    - Vous pouvez sauvegarder des machines locales sur un serveur de sauvegarde (System Center Data Protection Manager ou Serveur de sauvegarde Microsoft Azure). Vous pouvez ensuite sauvegarder le serveur de sauvegarde dans un coffre Recovery Services dans Azure.

- **Sauvegarder des machines virtuelles Azure** :
    - vous pouvez directement sauvegarder des machines virtuelles Azure. Le service Sauvegarde Azure installe une extension de sauvegarde de l’agent de machine virtuelle Azure qui s’exécute sur la machine virtuelle. Cette extension sauvegarde la totalité de la machine virtuelle.
    - Vous pouvez sauvegarder des fichiers et dossiers spécifiques sur la machine virtuelle Azure en exécutant l’agent MARS.
    - Vous pouvez sauvegarder des machines virtuelles Azure sur le Serveur Sauvegarde Microsoft Azure qui s’exécute dans Azure, puis sauvegarder ce serveur dans un coffre Recovery Services.

Apprenez-en davantage sur [ce que vous pouvez sauvegarder](backup-overview.md) et sur les [scénarios de sauvegarde pris en charge](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Où les données sont-elles sauvegardées ?

Sauvegarde Azure stocke les données sauvegardées dans un coffre Recovery Services. Un coffre est une entité de stockage en ligne dans Azure qui permet de conserver des données telles que des copies de sauvegarde, des points de récupération et des stratégies de sauvegarde.

Les coffres Recovery Services offrent les fonctionnalités suivantes :

- Les coffres facilitent l’organisation de vos données de sauvegarde, tout en réduisant le temps de gestion.
- Dans chaque abonnement Azure, vous pouvez créer jusqu’à 500 coffres.
- Vous pouvez superviser les éléments sauvegardés dans un coffre, notamment les machines virtuelles Azure et les ordinateurs locaux.
- Vous pouvez gérer l’accès au coffre avec le [contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) Azure.
- Vous spécifiez le mode de réplication des données dans le coffre pour la redondance :
    - **Stockage localement redondant (LRS)**  : Pour vous protéger contre des défaillances de centre de données, vous pouvez utiliser un stockage localement redondant. LRS réplique les données vers une unité d’échelle de stockage. [Plus d’informations](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
    - **Stockage géo-redondant (GRS)**  : Pour vous protéger contre des pannes régionales, vous pouvez utiliser un stockage géoredondant. Celui-ci réplique vos données dans une région secondaire. [Plus d’informations](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs) 
    - Par défaut, les coffres Recovery Services utilisent un stockage géoredondant. 

## <a name="backup-agents"></a>Agents de sauvegarde

Sauvegarde Azure fournit différents agents de sauvegarde, selon le type de machine sauvegardée :

**Agent** | **Détails** 
--- | --- 
**Agent MARS** | <ul><li>S’exécute sur des serveurs Windows locaux pour sauvegarder des fichiers, des dossiers et l’état du système.</li> <li>S’exécute sur des machines virtuelles Azure pour sauvegarder des fichiers, des dossiers et l’état du système.</li> <li>S’exécute sur des serveurs DPM/MABS pour sauvegarder le disque de stockage DPM/MABS local sur Azure.</li></ul> 
**Extension de machine virtuelle Azure** | S’exécute sur des machines virtuelles Azure pour les sauvegarder dans un coffre.

## <a name="backup-types"></a>Types de sauvegarde

Le tableau suivant présente les différents types de sauvegardes et quand ils sont utilisés :

**Type de sauvegarde** | **Détails** | **Utilisation**
--- | --- | ---
**Complète** | Une sauvegarde complète contient la source de données entière. Occupe davantage de bande passante réseau que les sauvegardes différentielles ou incrémentielles. | Utilisée pour la sauvegarde initiale.
**Différentielle** |  Un sauvegarde différentielle stocke les blocs ayant changé depuis la sauvegarde complète initiale. Utilise une plus petite quantité de stockage et de réseau, et ne conserve pas de copies redondantes des données inchangées.<br/><br/> Inefficace, car les blocs de données inchangés entre les sauvegardes successives sont transférés et stockés. | Non utilisée par Sauvegarde Azure.
**Incrémentielle** | Une sauvegarde incrémentielle stocke uniquement les blocs de données ayant changé depuis la sauvegarde précédente. Efficacité élevée du point de vue du stockage et du réseau. <br/><br/> Avec une sauvegarde incrémentielle, il est inutile d’effectuer des sauvegardes complètes régulières. | Utilisée par DPM/MABS pour les sauvegardes sur disque, et utilisée dans toutes les sauvegardes vers Azure.

## <a name="sql-server-backup-types"></a>Types de sauvegarde SQL Server

Le tableau suivant présente les différents types de sauvegardes utilisées pour les bases de données SQL Server et la fréquence à laquelle elles sont utilisées :

**Type de sauvegarde** | **Détails** | **Utilisation**
--- | --- | ---
**Sauvegarde complète** | une sauvegarde complète de base de données sauvegarde l’intégralité de la base de données. Elle contient toutes les données d’une base de données spécifique ou d’un ensemble de fichiers ou de groupes de fichiers. Elle contient également suffisamment de journaux pour récupérer ces données. | Vous pouvez déclencher au plus une sauvegarde complète par jour.<br/><br/> Vous pouvez choisir d’effectuer une sauvegarde complète à intervalle quotidien ou hebdomadaire.
**Sauvegarde différentielle** | Une sauvegarde différentielle est basée sur la sauvegarde de données complète précédente la plus récente.<br/><br/> Elle capture uniquement les données qui ont changé depuis la sauvegarde complète. |  Vous pouvez déclencher au plus une sauvegarde différentielle par jour.<br/><br/> Vous ne pouvez pas configurer une sauvegarde complète et une sauvegarde différentielle le même jour.
**Sauvegarde de fichier journal** | une sauvegarde de fichier journal permet d’effectuer une restauration ponctuelle à la seconde donnée. | Au plus, vous pouvez configurer des sauvegardes du journal des transactions toutes les 15 minutes.

### <a name="comparison-of-backup-types"></a>Comparaison des types de sauvegardes

La consommation du stockage, l’objectif de délai de récupération (RTO) et la consommation réseau varient pour chaque type de sauvegarde. L’illustration suivante compare les types de sauvegarde :

- La source de données A est composée de 10 blocs de stockage A1-A10, qui sont sauvegardés mensuellement.
- Les blocs A2, A3, A4 et A9 ont changé lors du premier mois et le bloc A5 a changé lors du mois suivant.
- Pour les sauvegardes différentielles, lors du deuxième mois, les blocs A2, A3, A4 et A9 qui ont changé sont sauvegardés. Lors du troisième mois, ces mêmes blocs sont à nouveau sauvegardés, ainsi que le bloc A5 qui a changé. Les blocs modifiés continuent d’être sauvegardés jusqu’à la prochaine sauvegarde complète.
- Pour les sauvegardes incrémentielles, le deuxième mois, les blocs A2, A3, A4 et A9 sont marqués comme modifiés et transférés. Lors du troisième mois, seul le bloc A5 qui a changé est marqué et transféré. 

![Illustration montrant des comparaisons entre méthodes de sauvegarde](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Fonctionnalités de sauvegarde

Le tableau suivant récapitule les fonctionnalités prises en charge pour les différents types de sauvegardes :

**Fonctionnalité** | **Ordinateurs Windows Server locaux (direct)** | **Machines virtuelles Azure** | **Ordinateurs ou applications avec DPM/MABS**
--- | --- | --- | ---
Sauvegarder vers le coffre | ![OUI][green] | ![OUI][green] | ![OUI][green] 
Sauvegarder sur disque DPM/MABS, puis sur Azure | | | ![OUI][green] 
Compresser les données envoyées pour la sauvegarde | ![OUI][green] | Aucune compression n’est effectuée lors du transfert des données. Le stockage croît légèrement, mais la restauration est plus rapide.  | ![OUI][green] 
Exécuter une sauvegarde incrémentielle |![OUI][green] |![OUI][green] |![OUI][green] 
Sauvegarder les disques dédupliqués | | | ![Partiellement][yellow]<br/><br/> Uniquement pour les serveurs DPM/MABS déployés localement. 

![Clé de table](./media/backup-architecture/table-key.png)

## <a name="architecture-direct-backup-of-azure-vms"></a>Architecture : sauvegarde directe de machines virtuelles Azure

1. Quand vous activez la sauvegarde pour une machine virtuelle Azure, une sauvegarde s’exécute conformément à la planification que vous spécifiez.
1. Lors de la première sauvegarde, une extension de sauvegarde est installée sur la machine virtuelle si celle-ci est en cours d’exécution.
    - Pour les machines virtuelles Windows, l’extension VMSnapshot est installée.
    - Pour les machines virtuelles Linux, l’extension Linux VMSnapshot est installée.
1. L’extension prend un instantané au niveau du stockage. 
    - Pour les machines virtuelles Windows en cours d’exécution, le service Sauvegarde se coordonne avec le service VSS (Volume Shadow Copy Service) Windows pour prendre un instantané de cohérence d’application de la machine virtuelle. Par défaut, Sauvegarde Azure effectue des sauvegardes VSS complètes. Si Sauvegarde Azure ne peut pas prendre d’instantané cohérent au niveau de l’application, il prend un instantané cohérent au niveau fichier.
    - Pour les machines virtuelles Linux, Sauvegarde Azure prend une capture instantanée cohérente au niveau fichier. Pour les instantanés de cohérence d’application, vous devez personnaliser manuellement le pré-script et le post-script.
    - La sauvegarde est optimisée grâce à la sauvegarde de chaque disque de machine virtuelle en parallèle. Pour chaque disque en cours de sauvegarde, Sauvegarde Azure lit les blocs sur le disque et stocke uniquement les données modifiées. 
1. Une fois l’instantané pris, les données sont transférées vers le coffre. 
    - Seuls les blocs de données qui ont changé depuis la dernière sauvegarde sont copiés.
    - Les données ne sont pas chiffrées. Sauvegarde Azure peut sauvegarder des machines virtuelles Azure chiffrées avec Azure Disk Encryption.
    - Les données d’instantanés peuvent ne pas être immédiatement copiées dans le coffre. Aux heures de pointe, la sauvegarde peut prendre quelques heures. La durée de sauvegarde totale d’une machine virtuelle est inférieure à 24 heures pour les stratégies de sauvegarde quotidienne.
1. Une fois les données envoyées au coffre, la capture instantanée est supprimée et un point de récupération est créé.

Notez que les machines virtuelles Azure ont besoin d’un accès Internet pour les commandes de contrôle. Si vous sauvegardez des charges de travail à l’intérieur de la machine virtuelle (par exemple, si vous effectuez une sauvegarde de base de données SQL Server), les données principales ont également besoin d’un accès Internet. 

![Sauvegarde des machines virtuelles Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Architecture : Sauvegarde directe de serveurs Windows locaux ou de fichiers ou dossiers de machines virtuelles Azure

1. Pour configurer le scénario, vous téléchargez et installez l’agent MARS sur l’ordinateur. Vous sélectionnez ensuite les éléments à sauvegarder, le moment d’exécution des sauvegardes et la durée pendant laquelle elles doivent être conservées dans Azure.
1. La sauvegarde initiale s’exécute conformément à vos paramètres de sauvegarde.
1. L’agent MARS utilise le service VSS pour prendre un instantané des volumes sélectionnés pour la sauvegarde.
    - L’agent MARS utilise uniquement l’opération d’écriture système Windows pour capturer l’instantané.
    - L’agent n’utilisant aucun enregistreur VSS d’application, il ne capture pas d’instantané de cohérence d’application.
1. Après avoir pris l’instantané avec VSS, l’agent MARS crée un disque dur virtuel dans le dossier de cache que vous avez spécifié quand vous avez configuré la sauvegarde. L’agent stocke également des sommes de contrôle pour chaque bloc de données.
1. Les sauvegardes incrémentielles s’exécutent conformément à la planification que vous spécifiez, sauf si vous exécutez une sauvegarde ad hoc.
1. Dans les sauvegardes incrémentielles, les fichiers modifiés sont identifiés et un nouveau disque dur virtuel est créé. Le disque dur virtuel est compressé et chiffré, puis envoyé au coffre.
1. Une fois la sauvegarde incrémentielle terminée, le nouveau disque dur virtuel est fusionné avec le disque dur virtuel créé après la réplication initiale. Ce disque dur virtuel fusionné fournit le dernier état à utiliser pour la comparaison dans le cadre de la sauvegarde en cours.

![Sauvegarde de serveurs Windows locaux avec l’agent MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Architecture : sauvegarder dans DPM/MABS

1. Vous installez l’agent de protection DPM ou MABS sur les ordinateurs que vous voulez protéger. Vous ajoutez ensuite ceux-ci à un groupe de protection DPM.
    - Pour protéger des ordinateurs locaux, le serveur DPM ou MABS doit être local.
    - Pour protéger des machines virtuelles Azure, le serveur MABS doit se trouver dans Azure et être exécuté en tant que machine virtuelle Azure.
    - À l’aide de DPM/MABS, vous pouvez protéger des volumes, des partages, des fichiers et des dossiers. Vous pouvez également protéger l’état du système d’un ordinateur, ainsi que des applications spécifiques avec des paramètres de sauvegarde prenant en compte les applications.
1. Quand vous configurez la protection d’une machine ou d’une application dans DPM/MABS, vous choisissez de sauvegarder sur le disque local MABS/DPM pour le stockage à court terme et sur Azure pour la protection en ligne. Vous spécifiez également quand doit s’exécuter la sauvegarde vers le stockage DPM/MABS local, et quand doit s’exécuter la sauvegarde en ligne sur Azure.
1. Le disque de la charge de travail protégée est sauvegardé sur les disques MABS/DPM locaux, conformément à la planification que vous avez spécifiée.
4. Les disques DPM/MABS sont sauvegardés dans le coffre par l’agent MARS en cours d’exécution sur le serveur DPM/MABS.

![Sauvegarde de machines et charges de travail protégées par DPM ou MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Stockage de machine virtuelle Azure

Les machines virtuelles Azure utilisent des disques pour stocker leur système d’exploitation, applications et données. Chaque machine virtuelle Azure a au moins deux disques : l’un pour le système d’exploitation et l’autre servant de disque temporaire. Les machines virtuelles Azure peuvent également avoir des disques de données pour des données d’application. Les disques sont stockés en tant que disques durs virtuels.

- Les disques durs virtuels sont stockés sous la forme d’objets blob de pages dans des comptes de stockage standard ou premium sur Azure :
    - **Stockage standard :** prise en charge de disque fiable et économique pour les machines virtuelles exécutant des charges de travail qui ne sont pas sensibles à la latence. Un stockage standard peut utiliser des disques SSD standard ou des disques HDD standard.
    - **Stockage premium :** prise en charge de disque à hautes performances. Utilise des disques SSD Premium.
- Il existe différents niveaux de performances pour les disques :
    - **Disque HDD standard :** repose sur des disques durs et est utilisé pour un stockage économique.
    - **Disque SSD standard :** combine des éléments de disques SSD premium et de disques HDD standard. Offre des performances et une fiabilité plus cohérentes que celles d’un disque HDD, mais toujours économiques.
    - **Disque SSD premium :** s’appuyant sur des disques SDD, fournit de hautes performances et une faible latence pour les machines virtuelles exécutant des charges de travail nécessitant de nombreuses opérations d’E/S.
- Les disques peuvent être managés ou non managés :
    - **Disques non managés :** types de disques classiques utilisés par les machines virtuelles. Pour ces disques, vous créez votre propre compte de stockage et spécifiez ce dernier lors de la création du disque. Vous devez ensuite déterminer comment optimiser les ressources de stockage pour vos machines virtuelles.
    - **Disques managés :** Azure crée et gère les comptes de stockage pour vous. Vous spécifiez le niveau de performances et la taille des disques, et Azure crée les disques managés pour vous. Lorsque vous ajoutez des disques et mettez à l’échelle les machines virtuelles, Azure gère les comptes de stockage.

Pour plus d’informations sur le stockage sur disque et les types de disques disponibles pour les machines virtuelles, voir les articles suivants :

- [Disques managés Azure pour machines virtuelles Windows](../virtual-machines/windows/managed-disks-overview.md)
- [Disques managés Azure pour machines virtuelles Linux](../virtual-machines/linux/managed-disks-overview.md)
- [Types de disques disponibles pour machines virtuelles](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Sauvegarder et restaurer des machines virtuelles Azure avec un stockage premium 

Vous pouvez sauvegarder des machines virtuelles Azure en utilisant un stockage premium avec Sauvegarde Azure :

- Lors du processus de sauvegarde de machines virtuelles avec un stockage premium, le service Sauvegarde crée un emplacement intermédiaire temporaire nommé *AzureBackup-* dans le compte de stockage. La taille de l’emplacement intermédiaire est égale à celle de la capture instantanée du point de récupération.
- Vérifiez que le compte de stockage Premium dispose d’un espace libre suffisant pour prendre en compte cet emplacement intermédiaire temporaire. [Plus d’informations](../storage/common/storage-scalability-targets.md#premium-performance-storage-account-scale-limits) Ne modifiez pas l’emplacement intermédiaire.
- Une fois la sauvegarde terminée, l’emplacement intermédiaire est supprimé.
- Le prix du stockage utilisé pour l’emplacement intermédiaire est conforme à la [tarification du stockage Premium](../virtual-machines/windows/disks-types.md#billing).

Quand vous restaurez des machines virtuelles Azure utilisant un stockage premium, vous pouvez les restaurer vers un stockage premium ou standard. En règle générale, vous les restaurerez vers un stockage premium. Mais si vous n’avez besoin que d’un sous-ensemble des fichiers de la machine virtuelle, il peut être économique de les restaurer vers un stockage standard.

### <a name="back-up-and-restore-managed-disks"></a>Sauvegarder et restaurer des disques managés

Vous pouvez sauvegarder des machines virtuelles Azure avec des disques managés :

- Pour sauvegarder des machines virtuelles avec disques managés, vous devez procéder de la même façon que pour toute autre machine virtuelle Azure. Vous pouvez sauvegarder la machine virtuelle directement à partir des paramètres de machine virtuelle, ou vous pouvez activer la sauvegarde pour des machines virtuelles dans le coffre Recovery Services.
- Vous pouvez sauvegarder des machines virtuelles sur des disques managés par le biais des collections RestorePoint basées sur des disques managés.
- Sauvegarde Azure prend également en charge la sauvegarde de machines virtuelles avec des disques managés qui ont été chiffrées à l’aide d’Azure Disk Encryption.

Quand vous restaurez des machines virtuelles avec des disques managés, vous pouvez effectuer la restauration sur une machine virtuelle complète avec des disques managés ou vers un compte de stockage :

- Pendant le processus de restauration, Azure gère les disques managés. Si vous utilisez l’option de compte de stockage, vous gérez le compte de stockage créé pendant le processus de restauration.
- Si vous restaurez une machine virtuelle managée qui est chiffrée, assurez-vous que les clés et secrets de la machine virtuelle existent dans le coffre de clés avant que de commencer le processus de restauration.

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue la matrice de prise en charge pour [découvrir les fonctionnalités prises en charge et les limitations pour les scénarios de sauvegarde](backup-support-matrix.md).
- Configurez la sauvegarde pour l’un des scénarios :
    - [Sauvegarder des machines virtuelles Azure](backup-azure-arm-vms-prepare.md).
    - [Sauvegarder des ordinateurs Windows directement](tutorial-backup-windows-server-to-azure.md), sans serveur de sauvegarde.
    - [Configurer MABS](backup-azure-microsoft-azure-backup.md) pour la sauvegarde dans Azure, puis sauvegarder des charges de travail dans MABS.
    - [Configurer DPM](backup-azure-dpm-introduction.md) pour la sauvegarde dans Azure, puis sauvegarder des charges de travail dans DPM.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

