---
title: Recommandations de performances pour SQL Server dans Azure | Microsoft Docs
description: Présente les recommandations pour optimiser les performances de SQL Server dans les machines virtuelles Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/26/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3fda34e46ddb7ea17c98795ad6632841b79764eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076908"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Recommandations de performances pour SQL Server dans les machines virtuelles Azure

## <a name="overview"></a>Vue d'ensemble

Cet article donne des conseils pour optimiser les performances de SQL Server dans la machine virtuelle Microsoft Azure. Lorsque vous exécutez SQL Server dans Microsoft Azure Virtual Machines, nous vous recommandons de continuer à utiliser les mêmes options de réglage des performances de base de données qui s’appliquent à SQL Server dans un environnement serveur local. Toutefois, les performances d’une base de données relationnelle dans un cloud public dépendent de nombreux facteurs, comme la taille de la machine virtuelle et la configuration des disques de données.

Les [images SQL Server provisionnées dans le portail Azure](quickstart-sql-vm-create-portal.md) suivent les bonnes pratiques générales en matière de configuration du stockage. Pour plus d’informations sur la configuration du stockage, consultez [Configuration du stockage pour les machines virtuelles SQL Server](virtual-machines-windows-sql-server-storage-configuration.md). Après l’approvisionnement, vous pouvez appliquer les autres optimisations présentées dans cet article. Faites votre choix en fonction de votre charge de travail et vérifiez en effectuant des tests.

> [!TIP]
> Il existe généralement un compromis entre l’optimisation des coûts et l’optimisation des performances. Cet article est axé sur l’obtention des *meilleures* performances pour SQL Server sur les machines virtuelles Azure. Si votre charge de travail est moindre, vous n’aurez peut-être pas besoin de toutes les optimisations suivantes. Tenez compte de vos besoins de performances, des coûts et des modèles de charges de travail lors de l’évaluation de ces recommandations.

## <a name="quick-check-list"></a>Liste de vérification rapide

Voici une liste de vérification rapide pour optimiser les performances de SQL Server sur Azure Virtual Machines :

| Domaine | Optimisations |
| --- | --- |
| [Taille de la machine virtuelle](#vm-size-guidance) | - [DS3 version 2](../sizes-general.md) ou supérieure pour l’édition SQL Server Entreprise.<br/><br/> - [DS2 version 2](../sizes-general.md) ou supérieure pour les éditions SQL Server Standard ou Web. |
| [Stockage](#storage-guidance) | - Utilisez des [disques SSD premium](../disks-types.md). Le stockage standard n’est recommandé que pour le développement et le test.<br/><br/> - Conservez le [compte de stockage](../../../storage/common/storage-create-storage-account.md) et la machine virtuelle SQL Server dans la même région.<br/><br/> * Désactivez le [stockage géo-redondant](../../../storage/common/storage-redundancy.md) (géo-réplication) d’Azure sur le compte de stockage. |
| [Disques](#disks-guidance) | - Utilisez au moins 2 [disques P30](../disks-types.md#premium-ssd) (1 pour les fichiers journaux et 1 pour les fichiers de données notamment TempDB). Pour les charges de travail nécessitant ~50 000 E/S par seconde, envisagez d’utiliser un disque SSD Ultra. <br/><br/> - Évitez d’utiliser des disques de système d’exploitation ou temporaires pour le stockage ou la journalisation des bases de données.<br/><br/> - Activez la mise en cache de lecture sur le ou les disques hébergeant les fichiers de données et TempDB.<br/><br/> - N’activez pas la mise en cache sur le ou les disques hébergeant le fichier journal.  **Important !** Arrêtez le service SQL Server lorsque vous modifiez le paramètre de cache d’un disque de machine virtuelle Azure.<br/><br/> - Entrelacez plusieurs disques de données Azure pour obtenir un débit d’E/S plus élevé.<br/><br/> - Formatez avec des tailles d’allocation documentées. <br/><br/> - Placez TempDB sur le disque SSD local pour les charges de travail SQL Server stratégiques (après avoir choisi la taille de machine virtuelle appropriée). |
| [E/S](#io-guidance) |- Activez la compression des pages de base de données.<br/><br/> - Activez l’initialisation de fichiers instantanée pour les fichiers de données.<br/><br/> - Limitez la croissance automatique sur la base de données.<br/><br/> - Désactivez la réduction automatique sur la base de données.<br/><br/> - Déplacez toutes les bases de données vers des disques de données, y compris les bases de données système.<br/><br/> - Déplacez les répertoires des journaux d’erreurs et des fichiers de trace SQL Server vers des disques de données.<br/><br/> - Configurez les emplacements par défaut du fichier de sauvegarde et du fichier de base de données.<br/><br/> - Activez les pages verrouillées.<br/><br/> - Appliquez les correctifs de performances de SQL Server. |
| [Spécifique aux fonctionnalités](#feature-specific-guidance) | - Sauvegardez directement dans le stockage d’objets blob. |

Pour plus d’informations sur *comment* et *pourquoi* effectuer ces optimisations, passez en revue les détails et les instructions fournies dans les sections suivantes.

## <a name="vm-size-guidance"></a>Conseils liés à la taille des machines virtuelles

Pour les applications sensibles aux performances, il est recommandé d’utiliser les [tailles de machines virtuelles](../sizes.md)suivantes :

* **SQL Server Édition Entreprise** : DS3_v2 ou ultérieur
* **SQL Server Éditions Standard et Web** : DS2_v2 ou ultérieur

Les machines virtuelles de la [série DSv2](../sizes-general.md#dsv2-series) prennent en charge le stockage premium, recommandé pour obtenir des performances optimales. Les tailles recommandées ici sont des bases de référence, mais la taille de machine que vous sélectionnez vraiment dépend des exigences de votre charge de travail. Les machines virtuelles de la série DSv2 sont des machines virtuelles universelles appropriées pour un large éventail de charges de travail, tandis que les tailles des autres machines sont optimisées pour des types de charge de travail spécifiques. Par exemple, la [série M](../sizes-memory.md#m-series) offre le nombre de processeurs virtuels et la quantité de mémoire les plus élevés pour les plus grandes charges de travail SQL Server. La [série GS](../sizes-previous-gen.md#gs-series) et la [série DSv2-11-15](../sizes-memory.md#dsv2-series-11-15) conviennent si la mémoire requise est volumineuse. Ces deux séries sont également disponibles dans des [tailles de cœur restreintes](../../windows/constrained-vcpu.md), réduisant le coût des charges de travail ayant des exigences de calcul moindres. Les machines de la [série Ls](../sizes-storage.md) sont optimisées pour un débit et des E/S de disque élevés. Il est important d’évaluer votre charge de travail SQL Server et d’en tenir compte au moment de choisir une taille et une série de machines virtuelles.

## <a name="storage-guidance"></a>Conseils liés au stockage

Les machines virtuelles de série DS (ainsi que des séries DSv2 et GS) prennent en charge les [disques SSD premium](../disks-types.md). Les disques SSD Premium sont recommandés pour toutes les charges de travail de production.

> [!WARNING]
> Les disques durs standard et les disques SSD possèdent différents temps de latence et une bande passante variable. Ils sont recommandés uniquement pour les charges de travail de développement/de test. Les charges de production doivent utiliser des disques SSD premium.

Par ailleurs, nous vous recommandons de créer votre compte de stockage Azure dans le même centre de données que vos machines virtuelles SQL Server afin de réduire les délais de transfert. Lors de la création d’un compte de stockage, désactivez la géo-réplication, étant donné que la cohérence de l’ordre d’écriture sur différents disques n’est pas garantie. Envisagez plutôt de configurer une technologie de récupération d’urgence de SQL Server entre deux centres de données Azure. Pour plus d’informations, consultez [Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Conseils liés aux disques

Il existe trois types de disques principaux sur une machine virtuelle Azure :

* **Disque de système d’exploitation** : Quand vous créez une machine virtuelle Azure, la plateforme attache au moins un disque (désigné par la lettre **C**) à la machine virtuelle en tant que disque de système d’exploitation. Ce disque est un disque dur virtuel (VHD) stocké en tant qu’objet blob de pages dans le stockage.
* **Disque temporaire** : Les machines virtuelles Azure contiennent un autre disque, appelé disque temporaire (portant le nom de lecteur **D**:). C’est un disque du nœud qui peut être utilisé comme un espace de travail temporaire.
* **Disques de données** : Vous pouvez également attacher des disques supplémentaires à votre machine virtuelle en tant que disques de données ; ces disques sont également stockés en tant qu’objets blob de pages.

Les sections suivantes décrivent des recommandations pour l’utilisation de ces disques différents.

### <a name="operating-system-disk"></a>Disque de système d’exploitation

Un disque de système d’exploitation est un disque dur virtuel (VHD) que vous pouvez amorcer et monter comme version d’exécution d’un système d’exploitation. Il est désigné par la lettre de lecteur **C**.

La stratégie de mise en cache par défaut sur le disque du système d’exploitation est **Lecture/Écriture**. Pour les applications sensibles aux performances, nous vous recommandons d’utiliser le disque de données au lieu du disque du système d’exploitation. Reportez-vous à la section sur les disques de données ci-dessous.

### <a name="temporary-disk"></a>Disque temporaire

Le disque de stockage temporaire, désigné par la lettre de **D**:, n’est pas persistant dans Azure Blob Storage. Ne stockez pas vos fichiers de base de données utilisateur ou vos fichiers journaux des transactions utilisateur sur le lecteur **D**:.

Sur les machines virtuelles de série D, Dv2 et G, le lecteur temporaire réside sur un disque SSD. Si votre charge de travail exploite intensivement TempDB (par exemple, pour les objets temporaires ou des jointures complexes), le stockage de TempDB sur le lecteur **D** peut entraîner un débit plus élevé et réduire la latence de TempDB. Pour obtenir un exemple de scénario, consultez la discussion sur TempDB dans le billet de blog suivant : [Instructions pour la configuration du stockage pour SQL Server sur une machine virtuelle Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm).

Pour les machines virtuelles qui prennent en charge les disques SSD premium (de série DS, DSv2 et GS), nous vous recommandons de stocker TempDB sur un disque qui prend en charge les disques SSD premium avec la mise en cache en lecture activée.

Il existe une exception à cette recommandation : _si votre utilisation de TempDB est intensive en écriture, vous pouvez obtenir des performances supérieures en stockant TempDB sur le lecteur **D** local, qui est également un disque SSD sur ces tailles de machines._

### <a name="data-disks"></a>Disques de données

* **Utilisez des disques de données pour les fichiers journaux et de données** : Si vous n’utilisez pas l’entrelacement de disques, utilisez au moins deux disques P30 SSD premium, l’un pour contenir le ou les fichiers journaux et l’autre pour contenir le ou les fichiers TempDB et les données. Chaque disque SSD premium fournit un nombre d’opérations d’E/S par seconde et une bande passante (Mo/s) en fonction de sa taille, comme décrit dans l’article, [Sélectionner un type de disque](../disks-types.md). Si vous utilisez une technique d’entrelacement de disques, comme des espaces de stockage, vous obtenez des performances optimales en ayant deux pools, un pour les fichiers journaux et l’autre pour les fichiers de données. Toutefois, si vous envisagez d’utiliser des instances de cluster de basculement SQL Server, vous devez configurer un pool.

   > [!TIP]
   > - Pour obtenir les résultats des tests sur différentes configurations de disque et de charge de travail, consultez le billet de blog suivant : [Instructions pour la configuration du stockage pour SQL Server sur une machine virtuelle Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - Pour les performances stratégiques des serveurs SQL Server nécessitant ~50 000 E/S par seconde, envisagez de remplacer 10 disques -P30 par un disque SSD Ultra. Pour plus d’informations, consultez le billet de blog suivant : [Performances stratégiques avec un disque SSD Ultra](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Lorsque vous configurez une machine virtuelle SQL Server dans le portail, vous avez la possibilité de modifier votre configuration de stockage. Selon votre configuration, Azure configure un ou plusieurs disques. Plusieurs disques sont combinés en un pool de stockage unique par entrelacement. Les fichiers journaux et de données se trouvent dans cette configuration. Pour plus d’informations, voir [Configuration du stockage pour les machines virtuelles SQL Server](virtual-machines-windows-sql-server-storage-configuration.md).

* **Entrelacement de disques** : Pour augmenter le débit, vous pouvez ajouter des disques de données et utiliser l’entrelacement de disques. Pour déterminer le nombre de disques de données, vous devez analyser le nombre d’opérations d’E/S par seconde et de bande passante disponibles pour vos fichiers journaux, vos données et vos fichiers TempDB. Notez que les différentes tailles de machine virtuelle sont associées à des limites spécifiques concernant la bande passante et le nombre d’opérations d’E/S par seconde pris en charge. Pour en savoir plus, consultez les tableaux relatifs aux nombre d’opérations d’E/S par seconde en fonction des [tailles de machine virtuelle](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Respectez les recommandations suivantes :

  * Pour Windows 8/Windows Server 2012 ou version ultérieure, utilisez des [espaces de stockage](https://technet.microsoft.com/library/hh831739.aspx) en respectant les consignes suivantes :

      1. Définissez l’intervalle (taille de bande) sur 64 Ko (65 536 octets) pour les charges de travail OLTP et sur 256 Ko (262 144 octets) pour les charges de travail d’entrepôt de données, afin d’éviter qu’un alignement incorrect de la partition n’affecte les performances. Ces paramètres doivent être définis avec PowerShell.
      2. Nombre de colonnes définies = nombre de disques physiques. Utilisez PowerShell lorsque vous configurez plus de 8 disques (et non l’interface utilisateur du gestionnaire de serveur). 

    Par exemple, le PowerShell suivant crée un nouveau pool de stockage avec un entrelacement de 64 Ko et 2 colonnes :

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Pour Windows 2008 R2 ou version antérieure, vous pouvez utiliser des disques dynamiques (volumes entrelacés de système d’exploitation). La taille de l’entrelacement restera 64 Ko. Remarque : cette option est déconseillée à partir des versions Windows 8/Windows Server 2012. Pour plus d’informations, consultez les instructions du support dans [Transition du service de disque virtuel vers l’API de gestion de stockage Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Si vous utilisez les [espaces de stockage direct (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) avec des [instances de cluster de basculement SQL Server](virtual-machines-windows-portal-sql-create-failover-cluster.md), vous devez configurer un pool unique. Notez que même si vous créez différents volumes sur ce pool unique, tous partageront les mêmes caractéristiques, par exemple la même stratégie de mise en cache.

  * Déterminez le nombre de disques associés à votre pool de stockage en fonction de vos attentes en matière de charge. N’oubliez pas que les différentes tailles de machines virtuelles autorisent différents nombres de disques de données attachés. Pour plus d’informations, consultez [Tailles des machines virtuelles](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Si vous n’utilisez pas les SSD premium (scénarios de développement/de test), nous vous recommandons d’ajouter le nombre maximal de disques de données pris en charge par la [taille de votre machine virtuelle](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) et d’utiliser l’entrelacement de disques.

* **Stratégie de mise en cache** : Notez les recommandations suivantes pour la stratégie de mise en cache en fonction de la configuration de votre stockage.

  * Si vous utilisez des disques différents pour les fichiers journaux et de données, activez la mise en cache de lecture sur les disques de données hébergeant vos fichiers de données et les fichiers de données TempDB. Cela devrait vous permettre d’obtenir un gain de performances significatif. N’activez pas la mise en cache sur le disque contenant le fichier journal, car cela entraîne une légère diminution des performances.

  * Si vous utilisez l’entrelacement de disques dans un pool de stockage unique, la plupart des charges de travail peuvent tirer parti de la mise en cache en lecture. Si vous avez des pools de stockage distincts pour les fichiers journaux et de données, activez la mise en cache en lecture uniquement sur le pool de stockage pour les fichiers de données. Avec certaines charges de travail nécessitant beaucoup d’écritures, il est possible d’améliorer les performances sans mise en cache. Cela ne peut être déterminé que par le biais de test.

  * Les suggestions précédentes s’appliquent aux disques SSD premium. Si vous n’utilisez pas des disques SSD premium, n’activez aucune mise en cache sur les disques de données.

  * Pour obtenir des instructions sur la configuration de la mise en cache des disques, consultez les articles suivants. Pour plus d’informations sur le modèle de déploiement classique (ASM), consultez : [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) et [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Pour plus d’informations sur le modèle de déploiement Azure Resource Manager, consultez : [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) et [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Arrêtez le service SQL Server lorsque vous modifiez le paramètre de cache de disques de machines virtuelles Azure pour éviter toute altération de la base de données.

* **Taille d’unité d’allocation NTFS** : Lors du formatage du disque de données, il est recommandé d’utiliser une taille d’unité d’allocation de 64 Ko pour les fichiers de données et les fichiers journaux, ainsi que pour TempDB.

* **Bonnes pratiques de gestion des disques** : Lors de la suppression d’un disque de données ou de la modification de son type de cache, arrêtez le service SQL Server. Lorsque les paramètres de mise en cache sont modifiés sur le disque du système d’exploitation, Azure arrête la machine virtuelle, modifie le type de cache et redémarre la machine virtuelle. Lorsque les paramètres du cache d’un disque de données sont modifiés, la machine virtuelle n’est pas arrêtée, mais le disque de données est détaché de la machine virtuelle lors de la modification, puis rattaché.

  > [!WARNING]
  > Si le service SQL Server n’est pas arrêté au cours de ces opérations, cela peut provoquer une altération de la base de données.


## <a name="io-guidance"></a>Recommandations liées aux E/S

* Pour obtenir les meilleurs résultats avec les disques SSD premium, vous devez paralléliser vos applications et vos demandes. Les disques SSD Premium sont conçus pour les scénarios où la profondeur de file d’attente est supérieure à 1. Les améliorations seront donc minimes ou inexistantes pour les demandes de série à thread unique (même si elles sont gourmandes en stockage). Par exemple, cela peut affecter les résultats des tests à thread unique des outils d’analyses de performances, par exemple SQLIO.

* Envisagez d’utiliser un [compression des pages de base de données](https://msdn.microsoft.com/library/cc280449.aspx) , car elle peut améliorer les performances de charges de travail gourmandes en E/S. Toutefois, la compression de données peut augmenter la consommation d’UC sur le serveur de base de données.

* Envisagez d’activer l’initialisation instantanée des fichiers pour réduire le temps requis pour l’allocation initiale des fichiers. Pour tirer parti de l’initialisation instantanée des fichiers, vous devez accorder l’autorisation SE_MANAGE_VOLUME_NAME au compte de service SQL Server (MSSQLSERVER) et l’ajouter à la stratégie de sécurité **Effectuer des tâches de maintenance sur les volumes**. Si vous utilisez une image de plateforme SQL Server pour Azure, le compte de service par défaut (NT Service\MSSQLSERVER) n’est pas ajouté à la stratégie de sécurité **Effectuer des tâches de maintenance sur les volumes**. En d’autres termes, l’initialisation instantanée des fichiers n’est pas activée dans une image de plateforme SQL Server pour Azure. Après avoir ajouté le compte de service SQL Server à la stratégie de sécurité **Effectuer les tâches de maintenance de volume** , redémarrez le service SQL Server. Des considérations de sécurité liées à l’utilisation de cette fonctionnalité peuvent exister. Pour plus d’informations, consultez [Initialisation des fichiers de base de données](https://msdn.microsoft.com/library/ms175935.aspx).

* **autogrow** ) est considérée comme un simple plan d’urgence en cas de croissance plus rapide que prévu. Ne gérez pas la croissance de vos données et journaux quotidiennement avec la croissance automatique. En cas d’utilisation de la croissance automatique, augmentez préalablement le fichier à l’aide du commutateur Taille.

* Vérifiez que la fonctionnalité de réduction automatique ( **autoshrink** ) est désactivée afin d’éviter une surcharge inutile susceptible d’affecter négativement les performances.

* Déplacer toutes les bases de données vers des disques de données, y compris les bases de données système. Pour plus d’informations, consultez [Déplacer des bases de données système](https://msdn.microsoft.com/library/ms345408.aspx).

* Déplacer les répertoires des journaux d’erreurs et des fichiers de trace SQL Server vers des disques de données. Cette opération peut être effectuée dans le Gestionnaire de configuration SQL Server en double-cliquant sur l’instance de SQL Server et en sélectionnant des propriétés. Il est possible de modifier les paramètres du fichier journal et du fichier de trace sous l’onglet **Paramètres de démarrage** . Le répertoire de vidage est spécifié sous l’onglet **Avancé** . La capture d’écran suivante indique où rechercher le paramètre de démarrage du journal des erreurs.

    ![Capture d’écran du journal des erreurs SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Configurez les emplacements par défaut du fichier de sauvegarde et du fichier de base de données. Utilisez les recommandations de cet article, puis apportez les modifications souhaitées dans la fenêtre Propriétés du serveur. Pour obtenir des instructions, consultez [Afficher ou modifier les emplacements par défaut des fichiers de données et des fichiers journaux (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). La capture d’écran suivante montre comment effectuer ces modifications.

    ![Journal des données et fichiers de sauvegarde SQL](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Activez les pages verrouillées pour réduire les activités d’E/S et de pagination. Pour plus d’informations, consultez [Activer l’option Lock Pages in Memory (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Si vous exécutez SQL Server 2012, installez la mise à jour cumulative 10 Service Pack 1. Elle contient le correctif qui permet de résoudre les problèmes de performances d’E/S lorsque vous exécutez une sélection dans l’instruction de table temporaire dans SQL Server 2012. Pour plus d’informations, consultez cet [article de la Base de connaissances](https://support.microsoft.com/kb/2958012).

* Envisagez de compresser tous les fichiers de données lors des transferts vers et depuis Azure.

## <a name="feature-specific-guidance"></a>Conseils spécifiques aux fonctionnalités

Certains déploiements peuvent bénéficier de plus grands avantages en termes de performances à l’aide de techniques de configuration avancées. La liste suivante présente certaines fonctionnalités SQL Server qui peuvent vous aider à améliorer les performances :

### <a name="backup-to-azure-storage"></a>Sauvegarde sur Stockage Azure
Durant la création de sauvegardes pour un serveur SQL Server s’exécutant sur des machines virtuelles Azure, vous pouvez vous référer à [Sauvegarde de SQL Server vers une URL](https://msdn.microsoft.com/library/dn435916.aspx). Cette fonctionnalité est disponible à partir de SQL Server 2012 SP1 CU2, et recommandée pour la sauvegarde vers les disques de données attachés. Lors de la sauvegarde ou la restauration vers/depuis Azure Storage, suivez les recommandations indiquées dans [Meilleures pratiques et résolution des problèmes pour la sauvegarde de SQL Server vers une URL et Restauration à partir de sauvegardes stockées dans Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). Vous pouvez également automatiser ces sauvegardes en utilisant la [Sauvegarde automatisée pour SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-automated-backup.md).

Pour les versions antérieures à SQL Server 2012, vous pouvez utiliser l’ [outil de sauvegarde SQL Server vers Azure](https://www.microsoft.com/download/details.aspx?id=40740). Cet outil peut augmenter le débit de sauvegarde à l’aide de plusieurs cibles d’entrelacement de sauvegarde.

### <a name="sql-server-data-files-in-azure"></a>Fichiers de données SQL Server dans Azure

Cette nouvelle fonctionnalité, [Fichiers de données SQL Server dans Azure](https://msdn.microsoft.com/library/dn385720.aspx), est disponible à partir de SQL Server 2014. L’exécution de SQL Server avec des fichiers de données dans Azure offre des caractéristiques de performances comparables à l’utilisation de disques de données Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Instance de cluster de basculement et espaces de stockage

Si vous utilisez des espaces de stockage, lors de l’ajout de nœuds dans le cluster sur la page **Confirmation**, désélectionnez la case à cocher **Ajouter la totalité du stockage disponible au cluster**. 

![Décochez la case du stockage éligible](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

Si vous utilisez des espaces de stockage et que vous ne désactivez pas la case à cocher **Ajouter la totalité du stockage disponible au cluster**, Windows détache les disques virtuels pendant le processus de mise en cluster. Par conséquent, ils n’apparaissent pas dans le Gestionnaire de disque ou dans l’Explorateur, jusqu’à ce que les espaces de stockage soient supprimés du cluster et rattachés à l’aide de PowerShell. Les espaces de stockage regroupent plusieurs disques dans des pools de stockage. Pour plus d’informations, consultez la page [Espaces de stockage](/windows-server/storage/storage-spaces/overview).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le stockage et les performances, consultez [Storage Configuration Guidelines for SQL Server on Azure VM](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

Pour les meilleures pratiques de sécurité, consultez [Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-security.md).

Consultez d’autres articles relatifs aux machines virtuelles avec SQL Server à la page [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md). Si vous avez des questions sur les machines virtuelles SQL Server, consultez le [Forum aux Questions](virtual-machines-windows-sql-server-iaas-faq.md).
