---
title: Forum aux questions sur la sauvegarde de bases de données SQL Server sur des machines virtuelles Azure avec Sauvegarde Azure
description: Trouvez des réponses aux questions courantes sur la sauvegarde de bases de données SQL Server sur des machines virtuelles Azure avec Sauvegarde Azure.
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vijayts
ms.openlocfilehash: 082817899adc6ab9b4d57f7e0d4bc4e7c2f0a2ab
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464822"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Forum aux questions sur les bases de données SQL Server qui s'exécutent sur une sauvegarde de machines virtuelles Azure

Cet article fournit des réponses à des questions courantes sur la sauvegarde de bases de données SQL Server s’exécutant sur des machines virtuelles Azure et utilisant le service [Sauvegarde Azure](backup-overview.md).

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Puis-je utiliser Sauvegarde Azure pour une machine virtuelle IaaS et SQL Server sur la même machine ?
Oui, vous pouvez disposer de la sauvegarde de machine virtuelle et de la sauvegarde SQL sur la même machine virtuelle. Dans ce cas, nous déclenchons en interne une sauvegarde de copie uniquement sur la machine virtuelle afin de ne pas tronquer les journaux.


## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>La solution retente-t-elle ou corrige-t-elle automatiquement les sauvegardes ?

Dans certaines circonstances, le service Sauvegarde Azure déclenche des sauvegardes correctives. La correction automatique peut intervenir pour l'une des six conditions mentionnées ci-dessous :

  - Si une sauvegarde de fichier journal ou différentielle échoue en raison d’une erreur de validation de LSN, la sauvegarde de fichier journal ou différentielle suivante se transforme en sauvegarde complète.
  - Si aucune sauvegarde complète n’a eu lieu avant une sauvegarde de fichier journal ou différentielle, cette sauvegarde de fichier journal ou différentielle se transforme en sauvegarde complète.
  - Si la dernière sauvegarde complète jusqu'à une date et une heure est antérieure à 15 jours, la sauvegarde de fichier journal ou différentielle suivante se transforme en sauvegarde complète.
  - Tous les travaux de sauvegarde annulés en raison d’une mise à niveau d’extension sont re-déclenchés une fois la mise à niveau terminée et l’extension démarrée.
  - Si vous choisissez de remplacer la base de données lors de la restauration, la sauvegarde de fichier journal ou différentielle suivante échoue et une sauvegarde complète est déclenchée.
  - Si une sauvegarde complète est requise pour redéfinir les chaînes de fichier journal en raison d'une modification du modèle de récupération de base de données, cette sauvegarde est déclenchée lors de la planification suivante.

Par défaut, la correction automatique est activée pour tous les utilisateurs, mais si vous décidez de la désactiver, procédez comme suit :

  * Sur l’instance SQL Server, dans le dossier *C:\Program Files\Azure Workload Backup\bin*, créez ou modifiez le fichier **ExtensionSettingsOverrides.json**.
  * Dans le fichier  **ExtensionSettingsOverrides.json**, définissez *{« EnableAutoHealer » : false}* .
  * Enregistrer vos modifications et fermez le fichier.
  * Sur l’instance SQL Server, ouvrez **Gérer la tâche** , puis redémarrez le service **AzureWLBackupCoordinatorSvc**.  

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>Puis-je contrôler le nombre de sauvegardes pouvant être exécutées simultanément sur le serveur SQL ?

Oui. Vous pouvez limiter la fréquence à laquelle la stratégie de sauvegarde s’exécute pour minimiser l’impact sur une instance SQL Server. Pour modifier ce paramètre :
1. Sur l’instance SQL Server, dans le dossier *C:\Program Files\Azure Workload Backup\bin*, créez le fichier *ExtensionSettingsOverrides.json*.
2. Dans le fichier *ExtensionSettingsOverrides.json*, remplacez la valeur du paramètre **DefaultBackupTasksThreshold** par une valeur inférieure (par exemple, 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`

3. Enregistrer vos modifications et fermez le fichier.
4. Sur l’instance SQL Server, ouvrez le **Gestionnaire des tâches**. Redémarrez le service **AzureWLBackupCoordinatorSvc**.<br/> <br/>
 Si cette méthode est utile lorsque l'application de sauvegarde consomme beaucoup de ressources, SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) permet de spécifier des limites sur la quantité d’UC,d’E/S physiques et de mémoire utilisables par les requêtes d’application entrantes.

> [!NOTE]
> Dans l’expérience utilisateur, vous pouvez planifier autant de sauvegardes que vous le souhaitez à un moment donné, mais elles seront traitées dans une fenêtre glissante de 5, par exemple, comme ci-dessus.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Puis-je effectuer la sauvegarde complète d’un réplica secondaire ?
Conformément aux limites SQL, vous pouvez exécuter une sauvegarde de copie uniquement sur un réplica secondaire, mais la sauvegarde complète n'est pas autorisée.

## <a name="can-i-protect-availability-groups-on-premises"></a>Puis-je protéger des groupes de disponibilité en local ?
Non. Sauvegarde Azure protège les bases de données SQL Server s'exécutant dans Azure. Si un groupe de disponibilité (AG) est réparti entre les machines Azure et sur site, l’AG peut être protégé uniquement si le réplica principal est en cours d’exécution dans Azure. En outre, Sauvegarde Azure protège uniquement les nœuds s'exécutant dans la même région Azure que le coffre Recovery Services.

## <a name="can-i-protect-availability-groups-across-regions"></a>Puis-je protéger des groupes de disponibilité dans plusieurs régions ?
Le coffre Recovery Services de Sauvegarde Azure peut détecter et protéger tous les nœuds qui se trouvent dans la même région que le coffre. Si votre groupe de disponibilité SQL Server Always On s’étend sur plusieurs régions Azure, configurez la sauvegarde à partir de la région où se trouve le nœud principal. La Sauvegarde Azure peut découvrir et protéger toutes les bases de données du groupe de disponibilité en fonction de votre préférence de sauvegarde. Si votre préférence de sauvegarde n’est pas satisfaite, les sauvegardes échouent et vous recevez l’alerte d’échec.

## <a name="do-successful-backup-jobs-create-alerts"></a>La réussite des travaux de sauvegarde génère-t-elle des alertes ?
Non. Les travaux de sauvegarde réussis ne génèrent pas d’alertes. Les alertes ne sont envoyées qu’en cas d’échec de la sauvegarde. Le comportement détaillé des alertes de portail est détaillé [ici](backup-azure-monitoring-built-in-monitor.md). Cela étant, si vous souhaitez recevoir des alertes pour les travaux ayant abouti, vous pouvez utiliser [Surveillance à l'aide d'Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Les travaux de sauvegarde planifiés sont-ils affichés dans le menu Travaux de sauvegarde ?
Le menu **Travail de sauvegarde** affiche uniquement les travaux de sauvegarde ad hoc. Pour les travaux planifiés, utilisez [Surveillance à l'aide d'Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Les bases de données futures sont-elles automatiquement ajoutées pour la sauvegarde ?
Oui, la  [protection automatique](backup-sql-server-database-azure-vms.md#enable-auto-protection) le permet.  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Si je supprime une base de données d’une instance protégée automatiquement, qu'advient-il des sauvegardes ?
Si une base de données est supprimée d’une instance protégée automatiquement, les sauvegardes de cette base de données sont toujours essayées. Cela implique que la base de données supprimée devient défectueuse sous **Éléments de sauvegarde** mais reste protégée.

Pour ne plus protéger cette base de données, utilisez  **Arrêter la sauvegarde** avec **Supprimer des données** sur cette base de données.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Si j'arrête une opération de sauvegarde d'une base de données protégée automatiquement, comment se comportera-t-elle ?
Si vous choisissez d'**arrêter la sauvegarde en conservant les données**, aucune nouvelle sauvegarde n'interviendra et les points de récupération existants resteront intacts. La base de données sera toujours considérée comme protégée et s'affichera sous les **éléments de sauvegarde**.

Si vous choisissez d'**arrêter la sauvegarde avec suppression des données**, aucune nouvelle sauvegarde n'interviendra et les points de récupération existants seront également supprimés. La base de données sera considérée comme non protégée et s'affichera sous l'instance dans Configurer une sauvegarde. Cela étant, contrairement à d’autres bases de données non protégées pouvant être sélectionnées manuellement ou protégées automatiquement, cette base de données sera grisée et ne pourra pas être sélectionnée. Le seul moyen de re-protéger cette base de données consiste à désactiver la protection automatique sur l’instance. Vous pouvez maintenant sélectionner cette base de données et configurer la protection ou réactiver la protection automatique sur l'instance.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Si je modifie le nom de la base de données après qu'elle a été protégée, comment se comportera-t-elle ?
Une base de données renommée est traitée en tant que nouvelle base de données. Dès lors, le service gèrera cette situation comme si la base de données était introuvable et les sauvegardes échoueront.

Vous pouvez sélectionner la base de données renommée et configurer la protection. En cas d'activation de la protection automatique sur l'instance, la base de données renommée sera automatiquement détectée et protégée.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Pourquoi ne puis-je pas voir une base de données ajoutée pour une instance protégée automatiquement ?
Une base de données que vous [ajoutez à une instance protégée automatiquement](backup-sql-server-database-azure-vms.md#enable-auto-protection) n’apparaît pas immédiatement sous les éléments protégés. C’est parce que la découverte s’exécute généralement toutes les 8 heures. Cela étant, pour détecter et protéger les nouvelles bases de données immédiatement, vous pouvez exécuter manuellement une détection en sélectionnant **Récupérer les bases de données**, comme l'illustre l'image suivante.

  ![Détecter manuellement une base de données qui vient d'être ajoutée](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [sauvegarder une base de données SQL Server](backup-azure-sql-database.md) s'exécutant sur une machine virtuelle Azure.
