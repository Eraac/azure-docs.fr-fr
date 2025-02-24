---
title: Modèle de données des journaux Azure Monitor pour Sauvegarde Azure
description: Cet article présente des informations détaillées sur le modèle de données des journaux Azure Monitor pour les données de Sauvegarde Azure.
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: 6563eefffee0ed8d9ce94c3e0a1e24b0d32314f0
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466160"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modèle de données Log Analytics pour les données de sauvegarde Azure

Utilisez le modèle de données Log Analytics pour créer des alertes personnalisées à partir de Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>Utilisation du modèle de données de sauvegarde Azure

Vous pouvez utiliser les champs suivants fournis dans le cadre du modèle de données pour créer des éléments visuels, des requêtes personnalisées et des tableaux de bord, en fonction de vos besoins.

### <a name="alert"></a>Alerte

Ce tableau fournit plus d’informations sur les champs liés aux alertes.

| Champ | Type de données | Description |
| --- | --- | --- |
| AlertUniqueId_s |Texte |Identificateur unique de l’alerte générée |
| AlertType_s |Texte |Type d’alerte, par exemple, Sauvegarde |
| AlertStatus_s |Texte |État de l’alerte, par exemple, Active |
| AlertOccurrenceDateTime_s |Date/Heure |Date et heure de la création de l’alerte |
| AlertSeverity_s |Texte |Gravité de l’alerte, par exemple, Critique |
|AlertTimeToResolveInMinutes_s    | Number        |Temps nécessaire pour résoudre une alerte. Vide pour les alertes actives.         |
|AlertConsolidationStatus_s   |Texte         |Indique si l’alerte est une alerte consolidée ou non         |
|CountOfAlertsConsolidated_s     |Number         |Nombre d’alertes consolidées dans le cas d’une alerte consolidée          |
|AlertRaisedOn_s     |Texte         |Type d’entité sur lequel l’alerte est déclenchée         |
|AlertCode_s     |Texte         |Code qui identifie un type d’alerte         |
|RecommendedAction_s   |Texte         |Action recommandée pour résoudre l’alerte         |
| EventName_s |Texte |Nom de l’événement. Toujours AzureBackupCentralReport |
| BackupItemUniqueId_s |Texte |Identificateur unique de l’élément de sauvegarde associé à l’alerte |
| SchemaVersion_s |Texte |Version actuelle du schéma, par exemple **V2** |
| State_s |Texte |État actuel de l’objet alerte, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur exécutant la sauvegarde (par exemple, Machine virtuelle IaaS, Dossier de fichiers) auquel appartient cette alerte |
| OperationName |Texte |Nom de l’opération actuelle, par exemple, Alerte |
| Catégorie |Texte |Catégorie de données de diagnostic envoyées (push) aux journaux Azure Monitor. Toujours AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| ProtectedContainerUniqueId_s |Texte |Identificateur unique du serveur protégé associé à l’alerte (il s’agissait de ProtectedServerUniqueId_s dans V1)|
| VaultUniqueId_s |Texte |Identificateur unique du coffre protégé associé à l’alerte |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur unique de la ressource sur laquelle les données sont collectées. Par exemple, un id de ressource de coffre Recovery Services |
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

### <a name="backupitem"></a>BackupItem

Ce tableau fournit plus d’informations sur les champs liés aux éléments de sauvegarde.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Nom de l’événement. Toujours AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texte |Identificateur unique de l’élément de sauvegarde |
| BackupItemId_s |Texte |Identificateur de l’élément de sauvegarde (ce champ ne concerne que le schéma v1) |
| BackupItemName_s |Texte |Nom de l’élément de sauvegarde |
| BackupItemFriendlyName_s |Texte |Nom convivial de l’élément de sauvegarde |
| BackupItemType_s |Texte |Type d’élément de sauvegarde, par exemple, Machine virtuelle, Dossier de fichiers |
| BackupItemProtectionState_s |Texte |État de protection de l’élément de sauvegarde |
| BackupItemAppVersion_s |Texte |Version de l’application de l’élément de sauvegarde |
| ProtectionState_s |Texte |État de protection actuel de l’élément de sauvegarde par exemple, Protégé, Protection arrêtée |
| ProtectionGroupName_s |Texte | Nom du groupe de protection dans lequel l’élément de sauvegarde est protégé, pour SC DPM et MABS, le cas échéant|
| SecondaryBackupProtectionState_s |Texte |Indique si la protection secondaire est activée pour l’élément de sauvegarde|
| SchemaVersion_s |Texte |Version du schéma, par exemple **V2** |
| State_s |Texte |État de l’objet élément de sauvegarde, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur exécutant la sauvegarde (par exemple, Machine virtuelle IaaS, Dossier de fichiers) auquel appartient cet élément de sauvegarde |
| OperationName |Texte |Nom de l’opération, par exemple, BackupItem |
| Catégorie |Texte |Catégorie de données de diagnostic envoyées (push) aux journaux Azure Monitor. Toujours AzureBackupReport |
| Ressource |Texte |Ressource pour laquelle les données sont collectées, par exemple le nom du coffre Recovery Services |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Id de ressource des données collectées, par exemple id de ressource du coffre Recovery Services |
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) des données collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) des données collectées |
| ResourceProvider |Texte |Fournisseur de ressources des données collectées, par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de la ressource des données collectées, par exemple, Coffres |

### <a name="backupitemassociation"></a>BackupItemAssociation

Ce tableau fournit des détails sur les associations d’éléments de sauvegarde avec différentes entités.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texte |ID unique de l’élément de sauvegarde |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V2** |
| State_s |Texte |État actuel de l’objet association d’éléments de sauvegarde, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| BackupItemSourceSize_s |Texte | Taille frontale de l’élément de sauvegarde |
| BackupManagementServerUniqueId_s |Texte | Champ qui identifie de manière unique le serveur de gestion des sauvegardes par le biais duquel l’élément de sauvegarde est protégé, le cas échéant |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic transférées à Log Analytics, c’est-à-dire AzureBackupReport |
| OperationName |Texte |Ce champ représente le nom de l’opération en cours - Association d’éléments de sauvegarde |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| ProtectedContainerUniqueId_s |Texte |Identificateur unique du serveur protégé associé à l’élément de sauvegarde (il s’agissait de ProtectedServerUniqueId_s dans V1) |
| VaultUniqueId_s |Texte |Identificateur unique du coffre contenant l’élément de sauvegarde |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services |
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources des données collectées, par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de la ressource pour laquelle les données sont collectées, par exemple, Coffres |

### <a name="backupmanagementserver"></a>BackupManagementServer

Ce tableau fournit des détails sur les associations d’éléments de sauvegarde avec différentes entités.

| Champ | Type de données | Description |
| --- | --- | --- |
|BackupManagementServerName_s     |Texte         |Nom du serveur de gestion des sauvegardes        |
|AzureBackupAgentVersion_s     |Texte         |Version de l’agent Sauvegarde Azure sur le serveur de gestion des sauvegardes          |
|BackupManagementServerVersion_s     |Texte         |Version du serveur de gestion des sauvegardes|
|BackupManagementServerOSVersion_s    |Texte            |Version du système d’exploitation du serveur de gestion des sauvegardes|
|BackupManagementServerType_s     |Texte         |Type du serveur de gestion des sauvegardes, par exemple as MABS et SC DPM|
|BackupManagementServerUniqueId_s     |Texte         |Champ qui identifie de façon unique le serveur de gestion des sauvegardes       |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services |
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources des données collectées, par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de la ressource pour laquelle les données sont collectées, par exemple, Coffres |

### <a name="job"></a>Travail

Ce tableau fournit plus d’informations sur les champs liés aux travaux.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Nom de l’événement. Toujours AzureBackupCentralReport |
| BackupItemUniqueId_s |Texte |Identificateur unique de l’élément de sauvegarde |
| SchemaVersion_s |Texte |Version du schéma, par exemple **V2** |
| State_s |Texte |État actuel de l’objet travail, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| OperationName |Texte |Ce champ représente le nom de l’opération en cours - Travail |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic envoyées (push) aux journaux Azure Monitor, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| ProtectedServerUniqueId_s |Texte |Identificateur unique du serveur protégé associé au travail |
| ProtectedContainerUniqueId_s |Texte | ID unique identifiant le conteneur protégé sur lequel le travail est exécuté |
| VaultUniqueId_s |Texte |Identificateur unique du coffre protégé |
| JobOperation_s |Texte |Opération pour laquelle le travail est exécuté, par exemple, Sauvegarde, Restauration, Configuration de sauvegarde |
| JobStatus_s |Texte |État du travail terminé par exemple, Terminé, Échec |
| JobFailureCode_s |Texte |Chaîne de Code d’échec en raison du type d’échec de travail survenu |
| JobStartDateTime_s |Date/Heure |Date et heure de démarrage de l’exécution du travail |
| BackupStorageDestination_s |Texte |Destination du stockage de sauvegarde, par exemple, Cloud, Disque  |
| AdHocOrScheduledJob_s |Texte | Champ qui indique si le travail est de type ad hoc ou planifié |
| JobDurationInSecs_s | Number |Durée totale de la tâche en secondes |
| DataTransferredInMB_s | Number |Données transférées en Mo pour ce travail|
| JobUniqueId_g |Texte |ID unique d’identification du travail |
| RecoveryJobDestination_s |Texte | Destination d’un travail de récupération, où les données sont récupérées |
| RecoveryJobRPDateTime_s |DateTime | Date et heure de la création du point de récupération en cours de récupération |
| RecoveryJobRPLocation_s |Texte | Emplacement auquel a été stocké le point de récupération en cours de récupération|
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services|
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

### <a name="policy"></a>Stratégie

Ce tableau fournit plus d’informations sur les champs liés à la stratégie.

| Champ | Type de données | Versions applicables | Description |
| --- | --- | --- | --- |
| EventName_s |Texte ||Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte ||Ce champ indique la version actuelle du schéma, **V2** |
| State_s |Texte ||État actuel de l’objet stratégie, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte ||Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| OperationName |Texte ||Ce champ représente le nom de l’opération en cours - Stratégie |
| Catégorie |Texte ||Ce champ représente la catégorie des données de diagnostic envoyées (push) aux journaux Azure Monitor, c’est-à-dire AzureBackupReport |
| Ressource |Texte ||Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| PolicyUniqueId_g |Texte ||ID unique d’identification de la stratégie |
| PolicyName_s |Texte ||Nom de la stratégie définie |
| BackupFrequency_s |Texte ||Fréquence à laquelle les sauvegardes sont exécutées, par exemple, Quotidienne, Hebdomadaire |
| BackupTimes_s |Texte ||Date et heure auxquelles les sauvegardes sont planifiées |
| BackupDaysOfTheWeek_s |Texte ||Jours de la semaine auxquels les sauvegardes sont planifiées |
| RetentionDuration_s |Nombre entier ||Durée de rétention des sauvegardes configurées |
| DailyRetentionDuration_s |Nombre entier ||Durée totale de rétention en jours des sauvegardes configurées |
| DailyRetentionTimes_s |Texte ||Date et heure auxquelles la rétention quotidienne est configurée |
| WeeklyRetentionDuration_s |Nombre décimal ||Durée totale de rétention hebdomadaire en semaines des sauvegardes configurées |
| WeeklyRetentionTimes_s |Texte ||Date et heure auxquelles la rétention hebdomadaire est configurée |
| WeeklyRetentionDaysOfTheWeek_s |Texte ||Jours de la semaine sélectionnés pour la rétention hebdomadaire |
| MonthlyRetentionDuration_s |Nombre décimal ||Durée totale de rétention en mois des sauvegardes configurées |
| MonthlyRetentionTimes_s |Texte ||Date et heure auxquelles la rétention mensuelle est configurée |
| MonthlyRetentionFormat_s |Texte ||Type de configuration de la rétention mensuelle, par exemple, quotidienne pour une configuration quotidienne, hebdomadaire pour une configuration hebdomadaire |
| MonthlyRetentionDaysOfTheWeek_s |Texte ||Jours de la semaine sélectionnés pour la rétention mensuelle |
| MonthlyRetentionWeeksOfTheMonth_s |Texte ||Semaines du mois au cours desquelles la rétention mensuelle est configurée, par exemple, Première, Dernière, etc. |
| YearlyRetentionDuration_s |Nombre décimal ||Durée totale de rétention en années des sauvegardes configurées |
| YearlyRetentionTimes_s |Texte ||Date et heure auxquelles la rétention annuelle est configurée |
| YearlyRetentionMonthsOfTheYear_s |Texte ||Mois de l’année sélectionnés pour la rétention annuelle |
| YearlyRetentionFormat_s |Texte ||Type de configuration de la rétention annuelle, par exemple, quotidienne pour une configuration quotidienne, hebdomadaire pour une configuration hebdomadaire | |
| YearlyRetentionDaysOfTheMonth_s |Texte ||Dates du mois sélectionnées pour la rétention annuelle |
| SynchronisationFrequencyPerDay_s |Nombre entier |v2|Nombre de synchronisations quotidiennes d’une sauvegarde de fichiers pour SC DPM et MABS |
| DiffBackupFormat_s |Texte |v2|Format des sauvegardes différentielles pour SQL dans une sauvegarde de machine virtuelle Azure |
| DiffBackupTime_s |Temps |v2|Durée des sauvegardes différentielles pour SQL dans une sauvegarde de machine virtuelle Azure|
| DiffBackupRetentionDuration_s |Nombre décimal |v2|Durée de conservation des sauvegardes différentielles pour SQL dans une sauvegarde de machine virtuelle Azure|
| LogBackupFrequency_s |Nombre décimal |v2|Fréquence des sauvegardes de fichier journal pour SQL|
| LogBackupRetentionDuration_s |Nombre décimal |v2|Durée de conservation des sauvegardes de fichier journal pour SQL dans une sauvegarde de machine virtuelle Azure|
| DiffBackupDaysofTheWeek_s |Texte |v2|Jours de la semaine où sont effectuées les sauvegardes différentielles pour SQL dans une sauvegarde de machine virtuelle Azure|
| SourceSystem |Texte ||Système source des données actuelles - Azure |
| ResourceId |Texte ||Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services |
| SubscriptionId |Texte ||Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte ||Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte ||Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte ||Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

### <a name="policyassociation"></a>PolicyAssociation

Ce tableau fournit des détails sur les associations de stratégies avec différentes entités.

| Champ | Type de données | Versions applicables | Description |
| --- | --- | --- | --- |
| EventName_s |Texte ||Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte ||Ce champ indique la version actuelle du schéma, **V2** |
| State_s |Texte ||État actuel de l’objet stratégie, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte ||Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| OperationName |Texte ||Ce champ représente le nom de l’opération en cours - Association de stratégies |
| Catégorie |Texte ||Ce champ représente la catégorie des données de diagnostic envoyées (push) aux journaux Azure Monitor, c’est-à-dire AzureBackupReport |
| Ressource |Texte ||Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| PolicyUniqueId_g |Texte ||ID unique d’identification de la stratégie |
| VaultUniqueId_s |Texte ||ID unique du coffre auquel cette stratégie appartient |
| BackupManagementServerUniqueId_s |Texte |v2 |Champ qui identifie de manière unique le serveur de gestion des sauvegardes par le biais duquel l’élément de sauvegarde est protégé, le cas échéant        |
| SourceSystem |Texte ||Système source des données actuelles - Azure |
| ResourceId |Texte ||Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services |
| SubscriptionId |Texte ||Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte ||Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte ||Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte ||Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

### <a name="protected-container"></a>Conteneur protégé

Ce tableau indique les champs de base relatifs aux conteneurs protégés. (ProtectedServer dans la version 1)

| Champ | Type de données | Description |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Texte | Champ qui identifie de façon unique un conteneur protégé |
| ProtectedContainerOSType_s |Texte |Type de système d’exploitation du conteneur protégé |
| ProtectedContainerOSVersion_s |Texte |Version du système d’exploitation du conteneur protégé |
| AgentVersion_s |Texte |Numéro de version de l’agent de sauvegarde ou de l’agent de protection (dans le cas de SC DPM et MABS) |
| BackupManagementType_s |Texte |Type de fournisseur pour effectuer la sauvegarde. Par exemple, IaaSVM, FileFolder |
| EntityState_s |Texte |État actuel de l’objet serveur protégé. Par exemple, Actif, Supprimé |
| ProtectedContainerFriendlyName_s |Texte |Nom convivial du serveur protégé |
| ProtectedContainerName_s |Texte |Nom du conteneur protégé |
| ProtectedContainerWorkloadType_s |Texte |Type du conteneur protégé sauvegardé. Par exemple, IaaSVMContainer |
| ProtectedContainerLocation_s |Texte |Indique si le conteneur protégé est situé en local ou dans Azure |
| ProtectedContainerType_s |Texte |Indique si le conteneur protégé est un serveur ou un conteneur |
| ProtectedContainerProtectionState_s  |Texte |État de protection du conteneur protégé |

### <a name="storage"></a>Stockage

Ce tableau fournit plus d’informations sur les champs liés au stockage.

| Champ | Type de données | Description |
| --- | --- | --- |
| CloudStorageInBytes_s |Nombre décimal |Stockage des sauvegardes dans le cloud utilisé par les sauvegardes, calculé en fonction de la dernière valeur (ce champ ne concerne que le schéma v1)|
| ProtectedInstances_s |Nombre décimal |Nombre d’instances protégées utilisées pour calculer le stockage frontal dans la facturation, calculé en fonction de la dernière valeur |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V2** |
| State_s |Texte |État actuel de l’objet stockage, par exemple, Actif, Supprimé |
| BackupManagementType_s |Texte |Type de fournisseur pour le serveur exécutant la sauvegarde, par exemple, Machine virtuelle IaaS, Dossier de fichiers |
| OperationName |Texte |Ce champ représente le nom de l’opération en cours - Stockage |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic envoyées (push) aux journaux Azure Monitor, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| ProtectedServerUniqueId_s |Texte |ID unique du serveur protégé pour lequel le stockage est calculé |
| VaultUniqueId_s |Texte |ID unique du coffre pour lequel le stockage est calculé |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services |
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |
| StorageUniqueId_s |Texte |ID unique utilisé pour identifier l’entité de stockage |
| StorageType_s |Texte |Type de stockage, par exemple Cloud, Volume, Disque |
| StorageName_s |Texte |Nom de l’entité de stockage, par exemple E:\ |
| StorageTotalSizeInGBs_s |Texte |Taille totale du volume de stockage, en Go, consommé par l’entité de stockage|

### <a name="storageassociation"></a>StorageAssociation

Ce tableau indique les champs de base liés au stockage utilisés pour la connexion du stockage à d’autres entités.

| Champ | Type de données | Description |
| --- | --- |  --- |
| StorageUniqueId_s |Texte |ID unique utilisé pour identifier l’entité de stockage |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V2** |
| BackupItemUniqueId_s |Texte |ID unique utilisé pour identifier l’élément de sauvegarde associé à l’entité de stockage |
| BackupManagementServerUniqueId_s |Texte |ID unique utilisé pour identifier le serveur de gestion des sauvegardes associé à l’entité de stockage|
| VaultUniqueId_s |Texte |ID unique utilisé pour identifier le coffre associé à l’entité de stockage|
| StorageConsumedInMBs_s |Number|Taille de stockage utilisée par l’élément de sauvegarde correspondant dans le stockage correspondant |
| StorageAllocatedInMBs_s |Number |Taille de stockage allouée par l’élément de sauvegarde correspondant dans le stockage correspondant de type disque|

### <a name="vault"></a>Coffre

Ce tableau fournit plus d’informations sur les champs liés aux coffres.

| Champ | Type de données | Description |
| --- | --- | --- |
| EventName_s |Texte |Ce champ représente le nom de cet événement, qui est toujours AzureBackupCentralReport |
| SchemaVersion_s |Texte |Ce champ indique la version actuelle du schéma, **V2** |
| State_s |Texte |État actuel de l’objet coffre, par exemple, Actif, Supprimé |
| OperationName |Texte |Ce champ représente le nom de l’opération en cours - Coffre |
| Catégorie |Texte |Ce champ représente la catégorie des données de diagnostic envoyées (push) aux journaux Azure Monitor, c’est-à-dire AzureBackupReport |
| Ressource |Texte |Ceci est la ressource pour laquelle les données sont collectées ; affiche le nom du coffre Recovery Services |
| VaultUniqueId_s |Texte |ID unique du coffre |
| VaultName_s |Texte |Nom du coffre |
| AzureDataCenter_s |Texte |Centre de données dans lequel se trouve le coffre |
| StorageReplicationType_s |Texte |Type de réplication de stockage pour le coffre, par exemple, Géoredondant |
| SourceSystem |Texte |Système source des données actuelles - Azure |
| ResourceId |Texte |Identificateur de ressource des données collectées. Par exemple, id de ressource du coffre Recovery Services |
| SubscriptionId |Texte |Identificateur d’abonnement de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceGroup |Texte |Groupe de ressources de la ressource (par ex. coffre Recovery Services) pour laquelle les données sont collectées |
| ResourceProvider |Texte |Fournisseur de ressources pour lequel les données sont collectées. Par exemple, Microsoft.RecoveryServices |
| ResourceType |Texte |Type de ressource pour lequel les données sont collectées. Par exemple, Coffres |

### <a name="backup-management-server"></a>Serveur de gestion des sauvegardes

Ce tableau indique les champs de base relatifs aux serveurs de gestion des sauvegardes.

|Champ  |Type de données  | Description  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Texte         |Nom du serveur de gestion des sauvegardes        |
|AzureBackupAgentVersion_s     |Texte         |Version de l’agent Sauvegarde Azure sur le serveur de gestion des sauvegardes          |
|BackupManagmentServerVersion_s     |Texte         |Version du serveur de gestion des sauvegardes|
|BackupManagmentServerOSVersion_s     |Texte            |Version du système d’exploitation du serveur de gestion des sauvegardes|
|BackupManagementServerType_s     |Texte         |Type du serveur de gestion des sauvegardes, par exemple as MABS et SC DPM|
|BackupManagmentServerUniqueId_s     |Texte         |Champ qui identifie de façon unique le serveur de gestion des sauvegardes       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Ce tableau indique les charges de travail avec lesquelles un volume est associé.

| Champ | Type de données | Description |
| --- | --- | --- |
| StorageUniqueId_s |Texte |ID unique utilisé pour identifier l’entité de stockage |
| BackupItemType_s |Texte |Charges de travail pour lesquelles ce volume est le stockage par défaut|

### <a name="protectedinstance"></a>ProtectedInstance

Ce tableau indique les champs de base relatifs aux instances protégées.

| Champ | Type de données |Versions applicables | Description |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Texte |v2|ID unique qui identifie l’élément de sauvegarde pour les machines virtuelles sauvegardées à l’aide de DPM ou MABS|
| ProtectedContainerUniqueId_s |Texte |v2|ID unique qui identifie le conteneur protégé pour tout élément sauf les machines virtuelles sauvegardées à l’aide de DPM ou MABS|
| ProtectedInstanceCount_s |Texte |v2|Nombre d’instances protégées pour le conteneur protégé ou l’élément de sauvegarde associé à ces date et heure|

### <a name="recoverypoint"></a>RecoveryPoint

Ce tableau indique les champs de base relatifs aux points de récupération.

| Champ | Type de données | Description |
| --- | --- | --- |
| BackupItemUniqueId_s |Texte |ID unique qui identifie l’élément de sauvegarde pour les machines virtuelles sauvegardées à l’aide de DPM ou MABS|
| OldestRecoveryPointTime_s |Texte |Date et heure du point de récupération le plus ancien pour l’élément de sauvegarde|
| OldestRecoveryPointLocation_s |Texte |Emplacement du point de récupération le plus ancien pour l’élément de sauvegarde|
| LatestRecoveryPointTime_s |Texte |Date et heure du point de récupération le plus récent pour l’élément de sauvegarde|
| LatestRecoveryPointLocation_s |Texte |Emplacement du point de récupération le plus récent pour l’élément de sauvegarde|

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez passé en revue le modèle de données, vous pouvez commencer à [créer des requêtes personnalisées](../azure-monitor/learn/tutorial-logs-dashboards.md) dans les journaux Azure Monitor pour créer votre propre tableau de bord.
