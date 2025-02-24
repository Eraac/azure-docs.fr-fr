---
title: Résoudre les problèmes de sauvegarde de l’état du système avec le service Sauvegarde Azure
description: Résolvez les problèmes de sauvegarde de l’état du système.
author: srinathvasireddy
manager: sivan
keywords: mode de sauvegarde ; sauvegarder l’état du système
ms.service: backup
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: srinathv
ms.openlocfilehash: 8996270acb1525697f29b4251bf4e11d2db62fdf
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465354"
---
# <a name="troubleshoot-system-state-backup"></a>Résoudre les problèmes de sauvegarde de l’état du système

Cet article décrit les solutions aux problèmes que vous pouvez rencontrer lors de l’utilisation de la sauvegarde de l’état du système.

## <a name="basic-troubleshooting"></a>Dépannage de base
Nous vous recommandons d’effectuer les validations ci-dessous avant de résoudre les problèmes de sauvegarde de l’état du système :

- [Vérifiez que Microsoft Azure Recovery Services (MARS) Agent est à jour](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Vérifiez la connectivité réseau entre l’agent MARS et Azure](https://aka.ms/AB-A4dp50)
- Assurez-vous que Microsoft Azure Recovery Services est en cours d’exécution (dans la console de service). Si nécessaire, redémarrez le système et recommencez l’opération
- [Vérifiez qu’il existe entre 5 et 10 % d’espace de volume disponible à l’emplacement du dossier de travail](https://aka.ms/AB-AA4dwtt)
- [Vérifiez si un autre processus ou logiciel antivirus interfère avec le service Sauvegarde Azure](https://aka.ms/AB-AA4dwtk)
- [La sauvegarde planifiée échoue, mais la sauvegarde manuelle fonctionne](https://aka.ms/ScheduledBackupFailManualWorks)
- Assurez-vous que votre système d’exploitation dispose des dernières mises à jour
- [Vérifiez que les lecteurs non pris en charge et les fichiers avec des attributs non pris en charge sont exclus de la sauvegarde](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Vérifiez que l’**horloge système** sur le système protégé est configurée sur le bon fuseau horaire <br>
- [Vérifiez que le serveur dispose au minimum de .NET Framework version 4.5.2 et versions ultérieures](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Si vous essayez de **réinscrire votre serveur** à un coffre : <br>
  - Vérifiez si l’agent est désinstallé sur le serveur et s’il est supprimé du portail <br>
  - Utilisez la même phrase secrète que celle initialement utilisée pour l’inscription du serveur <br>
- En cas de sauvegarde hors connexion, assurez-vous qu’Azure PowerShell version 3.7.0 est installé sur l’ordinateur source et de copie avant de lancer l’opération de sauvegarde.
- [Éléments à prendre en compte lorsque l’agent de sauvegarde est en cours d’exécution sur une machine virtuelle Azure](https://aka.ms/AB-AA4dwtr)

### <a name="limitation"></a>Limitation
- La récupération vers un autre composant matériel à l’aide de la fonction de récupération de l’état du système n’est pas recommandée par Microsoft
- La fonctionnalité de sauvegarde de l’état du système prend actuellement en charge les serveurs Windows « en local ». Elle n’est pas disponible pour les machines virtuelles Azure.

## <a name="pre-requisite"></a>Conditions préalables

Avant de résoudre les problèmes de sauvegarde de l’état du système avec le service Sauvegarde Azure, veillez à effectuer la vérification des conditions préalables indiquées ci-dessous.  

### <a name="verify-windows-server-backup-is-installed"></a>Vérifier que la fonctionnalité Sauvegarde Windows Server est installée

Assurez-vous que la fonctionnalité Sauvegarde Windows Server est installée et activée sur le serveur. Pour vérifier l’état de l’installation, exécutez la commande PowerShell ci-dessous :

 ```
 PS C:\> Get-WindowsFeature Windows-Server-Backup
 ```
Si la sortie affiche **l’état d’installation** **disponible**, cela signifie que la fonctionnalité Sauvegarde Windows Server est disponible pour l’installation, mais qu’elle n’est pas installée sur le serveur. Toutefois, si elle n’est pas installée, utilisez l’une des méthodes ci-dessous pour l’installer.

**Méthode 1 : installer la fonctionnalité Sauvegarde Windows Server à l’aide de PowerShell**

Pour installer la fonctionnalité Sauvegarde Windows Server à l’aide de PowerShell, exécutez la commande ci-dessous :

  ```
  PS C:\> Install-WindowsFeature -Name Windows-Server-Backup
  ```

**Méthode 2 : installer la fonctionnalité Sauvegarde Windows Server à l’aide du Gestionnaire de serveur**

Pour installer la fonctionnalité Sauvegarde Windows Server à l’aide de PowerShell, procédez comme suit :

1. Dans le **Gestionnaire de serveur**, cliquez sur **Ajouter des rôles et des fonctionnalités**. **L’Assistant Ajout de rôles et de fonctionnalités** s’affiche.

    ![tableau de bord](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Sélectionnez **Type d’installation**, puis cliquez sur **Suivant**.

    ![Type d’installation](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Sélectionnez un serveur dans le pool de serveurs, puis cliquez sur **Suivant**. Dans l’onglet Rôles de serveurs, laissez la sélection par défaut, puis cliquez sur **Suivant**.
4. Sélectionnez **Sauvegarde Windows Server** dans l’onglet **Fonctionnalités**, puis cliquez sur **Suivant**.

    ![features](./media/backup-azure-system-state-troubleshoot/features.png)

5. Dans l’onglet **Confirmation**, cliquez sur **Installer** pour démarrer le processus d’installation.
6. L’onglet **Résultats** affiche la fonctionnalité Sauvegarde Windows Server qui a été installée sur Windows Server.

    ![result](./media/backup-azure-system-state-troubleshoot/results.jpg)


### <a name="system-volume-information-permission"></a>Autorisation sur le dossier System Volume Information

Assurez-vous que le système local dispose d’un contrôle total sur le dossier **System Volume Information** situé dans le volume où Windows est installé. Il s’agit généralement de **C:\System Volume Information**. La fonctionnalité Sauvegarde Windows Server peut échouer si les autorisations indiquées ci-dessus ne sont pas définies correctement.

### <a name="dependent-services"></a>Services dépendants

Vérifiez que les services indiqués ci-dessous sont en cours d’exécution :

**Nom du service** : | **Type de démarrage**
--- | ---
Appel de procédure distante (RPC) | Automatique
Système d’événement COM+ (EventSystem) | Automatique
Service de notification d’événements système (SENS) | Automatique
Cliché instantané des volumes (VSS) | Manuel
Fournisseur de cliché instantané de logiciel Microsoft (SWPRV) | Manuel

### <a name="validate-windows-server-backup-status"></a>Valider l’état de la fonctionnalité Sauvegarde Windows Server

Pour valider l’état de la fonctionnalité Sauvegarde Windows Server, procédez comme suit :

  * Vérifiez que WSB PowerShell s’exécute.

    -   Exécutez la commande `Get-WBJob` à partir d’une invite PowerShell avec élévation de privilèges et vérifiez qu’elle ne retourne pas l’erreur suivante :

    > [!WARNING]
    > Get-WBJob : Le terme Get-WBJob n’est pas reconnu comme nom de cmdlet, fonction, fichier de script ou programme exécutable. Vérifiez l’orthographe du nom ou, si un chemin d’accès a été inclus, vérifiez que le chemin d’accès est correct et réessayez.

    -   Si la commande échoue avec cette erreur, réinstallez la fonctionnalité Sauvegarde Windows Server sur l’ordinateur serveur, comme indiqué dans les conditions préalables de l’étape 1.

  * Assurez-vous que la fonctionnalité Sauvegarde Windows Server fonctionne correctement en exécutant la commande ci-dessous à partir d’une invite de commandes avec élévation de privilèges :

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Remplacez X par la lettre du lecteur du volume où vous voulez stocker l’image de sauvegarde de l’état du système.

    - Vérifiez régulièrement l’état du travail en exécutant la commande `Get-WBJob` à partir d’une invite PowerShell avec élévation de privilèges.        
    - Une fois le travail de sauvegarde terminé, vérifiez son état final en exécutant la commande `Get-WBJob -Previous 1`.

Si le travail échoue, cela indique un problème de Sauvegarde Windows Server, qui entraînerait l’échec des sauvegardes de l’état du système de Microsoft Azure Recovery Services (MARS) Agent.

## <a name="common-errors"></a>Erreurs courantes

### <a name="vss-writer-timeout-error"></a>Erreur de délai d’expiration de l’enregistreur VSS

| Symptôme | Cause : | Résolution :
| -- | -- | --
| - MARS Agent échoue avec le message d’erreur suivant : « La tâche Sauvegarde Windows Server a échoué avec des erreurs VSS. Consultez les journaux d’événements VSS pour résoudre l’échec ».<br/><br/> - Le journal des erreurs suivant est présent dans les journaux d’événements d’application VSS : « Un enregistreur VSS a rejeté un événement avec l’erreur 0x800423f2. Le délai de l’enregistreur a expiré entre les événements Freeze et Thaw. »| L’enregistreur VSS ne peut pas terminer la tâche à temps en raison d’un manque de ressources d’UC et de mémoire sur la machine. <br/><br/> Comme un autre logiciel de sauvegarde utilise déjà l’enregistreur VSS, l’opération de capture instantanée n’a pas pu se terminer pour cette sauvegarde. | Attendez que des ressources d’UC/de mémoire se libèrent sur le système ou abandonnez les processus qui en consomment trop, puis réessayez l’opération. <br/><br/>  Attendez que la sauvegarde en cours se termine, puis recommencez l’opération ultérieurement lorsque aucune sauvegarde n’est en cours d’exécution sur la machine.


### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Espace disque insuffisant pour augmenter le volume de clichés instantanés

| Symptôme | Résolution :
| -- | --
| - MARS Agent échoue avec le message d’erreur suivant : La sauvegarde a échoué, car le volume de clichés instantanés ne peut pas augmenter en raison d’un espace disque insuffisant sur les volumes contenant les fichiers système. <br/><br/> - Le journal des erreurs/avertissements suivant est présent dans les journaux d’événements système volsnap : « Espace disque insuffisant sur le volume C: pour agrandir l’espace de stockage des clichés instantanés de C:. Suite à cette défaillance, tous les clichés instantanés du volume C: risquent d’être supprimés. » | - Libérez de l’espace dans le volume mis en surbrillance dans le journal des événements afin que l’espace disque soit suffisant pour augmenter le volume des clichés instantanés pendant la sauvegarde en cours. <br/><br/> - Nous pouvons limiter la quantité d’espace utilisé pour les clichés instantanés lors de la configuration de cet espace. Pour plus d’informations, consultez cet [article](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax).


### <a name="efi-partition-locked"></a>Partition EFI verrouillée

| Symptôme | Résolution :
| -- | --
| \- MARS Agent échoue avec le message d’erreur suivant : « La sauvegarde de l’état système a échoué, car la partition système EFI est verrouillée. Cela peut être causé par un logiciel de sécurité ou de sauvegarde tiers qui accède à la partition système. » | - Si le problème est dû à un logiciel de sécurité tiers, vous devez contacter le fournisseur de l’antivirus afin qu’il autorise MARS Agent. <br/><br/> - Si un logiciel de sauvegarde tiers est en cours d’exécution, attendez qu’elle se termine, puis recommencez la sauvegarde.


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur l’état du système Windows dans un déploiement Resource Manager, consultez [Sauvegarder l’état du système Windows dans un déploiement Resource Manager](backup-azure-system-state.md).
