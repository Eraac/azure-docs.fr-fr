---
title: Résoudre les problèmes de sauvegarde des bases de données SQL Server avec Sauvegarde Azure | Microsoft Docs
description: Informations de résolution des problèmes de sauvegarde de bases de données SQL Server exécutées sur des machines virtuelles Azure avec Sauvegarde Azure.
author: anuragm
manager: sivan
ms.service: backup
ms.topic: article
ms.date: 06/18/2019
ms.author: anuragm
ms.openlocfilehash: c601ecb6997834aa216de094e2809670833dd9cb
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464918"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Résoudre les problèmes de sauvegarde des bases de données SQL Server avec Sauvegarde Azure

Cet article fournit des informations de dépannage pour les bases de données SQL Server s’exécutant sur des machines virtuelles Azure.

Pour plus d’informations sur le processus et les limitations de la sauvegarde, consultez [À propos de la sauvegarde SQL Server sur des machines virtuelles Azure](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>Autorisations SQL Server

Pour configurer la protection d’une base de données SQL Server sur une machine virtuelle, vous devez installer l’extension **AzureBackupWindowsWorkload** sur cette machine virtuelle. Si vous recevez l’erreur **UserErrorSQLNoSysadminMembership**, cela signifie que votre instance SQL Server n’a pas les autorisations de sauvegarde nécessaires. Pour corriger cette erreur, suivez les étapes décrites dans [Définir des autorisations de machine virtuelle](backup-azure-sql-database.md#set-vm-permissions).

## <a name="error-messages"></a>messages d'erreur

### <a name="backup-type-unsupported"></a>Type de sauvegarde non pris en charge

| severity | Description | Causes possibles | Action recommandée |
|---|---|---|---|
| Avertissement | Les paramètres actuels de cette base de données ne prennent pas en charge certains types de sauvegarde présents dans la stratégie associée. | <li>Seule une opération de sauvegarde complète de base de données peut être effectuée sur la base de données master. Ni une sauvegarde différentielle ni une sauvegarde du journal des transactions n’est possible. </li> <li>Aucune base de données en mode de récupération simple n’autorise la sauvegarde des journaux des transactions.</li> | Modifiez les paramètres de la base de données de manière à ce que tous les types de sauvegarde de la stratégie soient pris en charge. Vous pouvez aussi changer la stratégie actuelle pour y inclure seulement les types de sauvegarde pris en charge. Sinon, les types de sauvegarde non pris en charge seront ignorés lors de la sauvegarde planifiée ou le travail de sauvegarde échouera pour une sauvegarde ad hoc.


### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| This SQL database does not support the requested backup type. (Cette base de données SQL ne prend pas en charge le type de sauvegarde demandé.) | Se produit lorsque le mode de récupération de la base de données n’autorise pas le type de sauvegarde demandé. L’erreur peut se produire dans les situations suivantes : <br/><ul><li>Une base de données utilisant un mode de récupération simple n’autorise pas la sauvegarde de fichier journal.</li><li>Les sauvegardes différentielles et de fichier journal ne sont pas autorisées pour une base de données master.</li></ul>Pour plus d’informations, consultez la documentation [Modes de récupération de SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server). | Si la sauvegarde de fichier journal échoue pour la base de données en mode de récupération simple, essayez une des options suivantes :<ul><li>Si la base de données est en mode de récupération simple, désactivez les sauvegardes de fichier journal.</li><li>Utilisez la [documentation de SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) pour changer le mode de récupération de la base de données en Complet ou Journalisation en bloc. </li><li> Si vous ne souhaitez pas modifier le mode de récupération et si vous disposez d’une stratégie standard pour sauvegarder plusieurs bases de données ne pouvant être changée, ignorez l’erreur. Vos sauvegardes complètes et différentielles fonctionneront par planification. Les sauvegardes de fichier journal seront ignorées, ce qui est attendu dans ce cas.</li></ul>S’il s’agit d’une base de données master et si vous avez configuré la sauvegarde différentielle ou de fichier journal, utilisez une des étapes suivantes :<ul><li>Utilisez le portail pour changer la planification de la stratégie de sauvegarde pour la base de données master en Complète.</li><li>Si vous disposez d’une stratégie standard pour sauvegarder plusieurs bases de données ne pouvant être changée, ignorez l’erreur. Votre sauvegarde complète fonctionnera par planification. Les sauvegardes différentielles ou de fichier journal n’auront pas lieu, ce qui est attendu dans ce cas.</li></ul> |
| Operation canceled as a conflicting operation was already running on the same database. (Opération annulée car une opération conflictuelle était déjà en cours d’exécution sur la même base de données.) | Consultez le [billet de blog sur les limitations relatives à la sauvegarde et à la restauration](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) qui s’exécutent simultanément.| [Utilisez SQL Server Management Studio (SSMS) pour surveiller les travaux de sauvegarde](manage-monitor-sql-database-backup.md). Après l’échec de l’opération en conflit, recommencez l’opération.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| SQL database does not exist. (La base de données SQL n’existe pas.) | La base de données a été supprimée ou renommée. | Vérifiez si la base de données a été supprimée ou renommée par inadvertance.<br/><br/> Si la base de données a été supprimée par inadvertance, restaurez la base de données à l’emplacement d’origine pour poursuivre les sauvegardes.<br/><br/> Si vous avez supprimé la base de données et si vous n’avez pas besoin de sauvegardes ultérieures, dans le coffre Recovery Services, sélectionnez **Arrêter la sauvegarde** avec **Conserver les données de sauvegarde** ou **Supprimer les données de sauvegarde**. Pour plus d’informations, consultez [Gérer et surveiller des bases de données SQL Server sauvegardées](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| Log chain is broken. (La séquence de journaux de transactions consécutifs est altérée.) | La base de données ou la machine virtuelle est sauvegardée via une autre solution de sauvegarde, ce qui tronque la séquence de journaux de transactions consécutifs.|<ul><li>Vérifiez si un autre script ou une autre solution de sauvegarde est en cours d’utilisation. Si c’est le cas, arrêtez l’autre solution de sauvegarde. </li><li>Si la sauvegarde était de type sauvegarde de fichier journal ad hoc, déclenchez une sauvegarde complète pour commencer une nouvelle séquence de journaux de transactions consécutifs. Pour les sauvegardes de fichier journal planifiées, aucune action n’est nécessaire, car le service Sauvegarde Azure va déclencher automatiquement une sauvegarde complète pour corriger ce problème.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| Azure Backup is not able to connect to the SQL instance. (Sauvegarde Azure n’est pas en mesure de se connecter à l’instance SQL.) | Sauvegarde Azure ne peut pas se connecter à l’instance SQL Server. | Utilisez les informations supplémentaires disponibles sur le menu des erreurs du portail Azure pour déterminer les causes racines. Consultez [Résoudre les problèmes de connexion au moteur de base de données SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) pour corriger l’erreur.<br/><ul><li>Si les paramètres SQL par défaut n’autorisent pas les connexions à distance, modifiez les paramètres. Pour plus d’informations sur le changement des paramètres, consultez les articles suivants :<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>En cas de problème de connexion, utilisez les liens suivants pour les corriger :<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| First full backup is missing for this data source. (La première sauvegarde complète est manquante pour cette source de données.) | La sauvegarde complète est manquante pour la base de données. Les sauvegardes différentielles et de fichier journal sont apparentées à une sauvegarde complète : veillez donc à effectuer des sauvegardes complètes avant de déclencher des sauvegardes différentielles ou de fichier journal. | Déclenchez une sauvegarde complète ad hoc.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| Cannot take backup as transaction log for the data source is full. (Impossible de prendre la sauvegarde car le journal des transactions de la source de données est plein.) | L’espace dédié au journal des transactions de la base de données est plein. | Pour résoudre ce problème, reportez-vous à la [documentation SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| Database with same name already exists at the target location (Une base de données portant le même nom existe déjà dans l’emplacement cible) | La destination de restauration cible a déjà une base de données portant le même nom.  | <ul><li>Changez le nom de la base de données cible.</li><li>Vous pouvez aussi utiliser l’option de remplacement disponible sur la page de restauration.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| Restore failed as the database could not be brought offline. (Échec de la restauration car la base de données n’a pas pu être mise hors connexion.) | Pendant que vous effectuez une restauration, la base de données cible doit être mise hors connexion. Sauvegarde Azure ne peut pas mettre ces données hors connexion. | Utilisez les informations supplémentaires disponibles sur le menu des erreurs du portail Azure pour déterminer les causes racines. Pour plus d’informations, consultez la [documentation SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| Cannot find the server certificate with thumbprint on the target. (Impossible de trouver le certificat de serveur avec l’empreinte sur la cible.) | La base de données master sur l’instance de destination n’a pas une empreinte de chiffrement valide. | Importez l’empreinte de certificat valide utilisée sur l’instance source vers l’instance cible. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| La sauvegarde de fichier journal utilisée pour la récupération contient des modifications journalisées en bloc. Elle n’est pas utilisable pour s’arrêter à un point arbitraire dans le temps conformément aux directives SQL. | Quand une base de données est en mode de récupération avec journalisation en bloc, les données entre une transaction journalisée en bloc et la transaction de journal suivante ne peuvent pas être récupérées. | Choisissez un autre point de récupération dans le temps. [Plus d’informations](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105))


### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| Backup preference for SQL Always On Availability Group cannot be met as some nodes of the Availability Group are not registered. (Les préférences de sauvegarde pour le groupe de disponibilité Always On ne peuvent pas être respectées car certains nœuds du groupe de disponibilité ne sont pas inscrits.) | Les nœuds requis pour effectuer des sauvegardes ne sont pas inscrits ou sont inaccessibles. | <ul><li>Vérifiez que tous les nœuds nécessaires pour effectuer des sauvegardes de cette base de données sont inscrits et sains, puis réessayez l’opération.</li><li>Changez la préférence de sauvegarde pour le groupe de disponibilité SQL Server Always On.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| SQL server VM is either shutdown and not accessible to Azure Backup service. (La machine virtuelle SQL Server est arrêtée ou indisponible pour le service Sauvegarde Azure.) | La machine virtuelle est arrêtée. | Vérifiez que l’instance SQL Server est en cours d’exécution. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| Azure Backup service uses Azure VM guest agent for doing backup but guest agent is not available on the target server. (Le service Sauvegarde Azure utilise l’agent invité de machine virtuelle Azure pour effectuer la sauvegarde, mais l’agent invité n’est pas disponible sur le serveur cible.) | L’agent invité n’est pas activé ou n’est pas sain. | [Installez l’agent invité de machine virtuelle](../virtual-machines/extensions/agent-windows.md) manuellement. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Message d’erreur | Causes possibles | Action recommandée |
|---|---|---|
| L’intention de protection automatique a été supprimée ou n’est pas plus valide. | Quand vous activez la protection automatique sur une instance SQL Server, les travaux **Configurer la sauvegarde** s’exécutent pour toutes les bases de données de cette instance. Si vous désactivez la protection automatique pendant l’exécution des tâches, les tâches **En cours** sont annulées avec ce code d’erreur. | Réactivez la protection automatique pour protéger toutes les bases de données restantes. |

## <a name="re-registration-failures"></a>Échecs de réinscription

Vérifiez la présence d’un ou plusieurs des symptômes suivants avant de déclencher l’opération de réinscription :

* Toutes les opérations (comme la sauvegarde, la restauration et la configuration de la sauvegarde) échouent sur la machine virtuelle avec un des codes d’erreur suivants : **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
* La zone **État de la sauvegarde** pour l’élément de sauvegarde indique **Non accessible**. Éliminez toutes les autres causes susceptibles d’entraîner le même état :

  * Absence d’autorisation pour effectuer les opérations liées à la sauvegarde sur la machine virtuelle  
  * Arrêt de la machine virtuelle, de sorte que les sauvegardes ne peuvent pas avoir lieu
  * Problèmes de réseau  

  ![État « Inaccessible » dans la réinscription d’une machine virtuelle](./media/backup-azure-sql-database/re-register-vm.png)

* Avec un groupe de disponibilité Always On, les sauvegardes échouent après la modification d’une préférence de sauvegarde ou après un basculement.

Ces symptômes peuvent survenir pour une ou plusieurs des raisons suivantes :

* Une extension a été supprimée ou désinstallée sur le portail. 
* Une extension a été désinstallée depuis le **Panneau de configuration** de la machine virtuelle sous **Désinstaller ou modifier un programme**.
* La machine virtuelle a été restaurée à un point dans le temps via une restauration de disques sur place.
* La machine virtuelle a été arrêtée pendant une période prolongée, entraînant l’expiration de la configuration d’extension qui s’y trouvait.
* La machine virtuelle a été supprimée et une autre machine virtuelle a été créée avec le même nom et le même groupe de ressources que la machine virtuelle supprimée.
* Un des nœuds du groupe de disponibilité n’a pas reçu la configuration de sauvegarde complète. Ceci peut se produire quand le groupe de disponibilité est inscrit dans le coffre ou quand un nouveau nœud est ajouté.

Dans les scénarios précédents, nous vous recommandons de déclencher une opération de réinscription sur la machine virtuelle. Pour le moment, cette option est disponible seulement via PowerShell.

## <a name="size-limit-for-files"></a>Limite de taille pour les fichiers

La taille totale de la chaîne de fichiers dépend non seulement du nombre de fichiers, mais aussi de leurs noms et de leurs chemins. Obtenez le nom de fichier logique et le chemin physique de chacun des fichiers de base de données. Vous pouvez utiliser cette requête SQL :

```
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Réorganisez-les à présent selon le format suivant :

```
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Voici un exemple :

```
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Si la taille de la chaîne du contenu dépasse 20 000 octets, les fichiers de base de données sont stockés différemment. Pendant la récupération, vous ne pouvez pas définir le chemin du fichier cible pour la restauration. Les fichiers sont restaurés dans le chemin SQL par défaut fourni par SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Remplacer le chemin de fichier de restauration cible par défaut

Vous pouvez remplacer le chemin du fichier de restauration cible pendant l’opération de restauration en plaçant un fichier JSON contenant le mappage du fichier de base de données au chemin de restauration cible. Créez un fichier `database_name.json` et placez-le à l’emplacement *C:\Program Files\Azure Workload Backup\bin\plugins\SQL*.

Le contenu du fichier doit être à ce format :
```
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

Voici un exemple :

```
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

Dans le contenu précédent, vous pouvez obtenir le nom logique du fichier de base de données avec la requête SQL suivante :

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


Ce fichier doit être placé avant de déclencher l’opération de restauration.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Sauvegarde Azure pour les machines virtuelles SQL Server (préversion publique), consultez [Sauvegarde Azure pour machines virtuelles SQL](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
