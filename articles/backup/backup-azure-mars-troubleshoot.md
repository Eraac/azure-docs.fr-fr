---
title: Résoudre les problèmes de l’agent Sauvegarde Azure
description: Résoudre les problèmes liés à l’installation et l’inscription de l’agent Sauvegarde Azure
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: saurse
ms.openlocfilehash: 1c4c2ed6265bdb3c29986fb0b90c3d85d32aadca
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434007"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Résoudre les problèmes liés à l’agent Microsoft Azure Recovery Services (MARS)

Cet article explique comment résoudre les erreurs qui peuvent survenir lors de la configuration, de l’inscription, de la sauvegarde et de la restauration.

## <a name="basic-troubleshooting"></a>Dépannage de base

Nous vous recommandons de passer en revue les points suivants avant de commencer à résoudre les problèmes liés à l’agent MARS :

- [Vérifiez que l’agent MARS est à jour](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Vérifiez la connectivité réseau entre l’agent MARS et Azure](https://aka.ms/AB-A4dp50).
- Vérifiez que MARS est en cours d’exécution (dans la console Service). Si nécessaire, redémarrez le système et recommencez l’opération.
- [Vérifiez qu’il existe entre 5 et 10 % d’espace de volume disponible à l’emplacement du dossier de travail](https://aka.ms/AB-AA4dwtt).
- [Vérifiez si un autre processus ou logiciel antivirus interfère avec le service Sauvegarde Azure](https://aka.ms/AB-AA4dwtk).
- Si la sauvegarde planifiée échoue, mais que la sauvegarde manuelle fonctionne, consultez [Les sauvegardes ne s’exécutent pas comme prévu](https://aka.ms/ScheduledBackupFailManualWorks).
- Assurez-vous que votre système d’exploitation dispose des dernières mises à jour.
- [Vérifiez que les lecteurs non pris en charge et les fichiers avec des attributs non pris en charge sont exclus de la sauvegarde](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Vérifiez que l’horloge sur le système protégé est configurée sur le bon fuseau horaire.
- [Assurez-vous que .NET Framework 4.5.2 (ou une version ultérieure) est installé sur le serveur](https://www.microsoft.com/download/details.aspx?id=30653).
- Si vous essayez de réinscrire votre serveur dans un coffre :
  - Vérifiez si l’agent est désinstallé sur le serveur et s’il est supprimé du portail.
  - Utilisez la même phrase secrète que celle initialement utilisée pour l’inscription du serveur.
- Pour les sauvegardes hors connexion, vérifiez qu’Azure PowerShell 3.7.0 est installé sur l’ordinateur source et l’ordinateur de copie avant de commencer la sauvegarde.
- Si l’agent de sauvegarde est en cours d’exécution sur une machine virtuelle Azure, consultez [cet article](https://aka.ms/AB-AA4dwtr).

## <a name="invalid-vault-credentials-provided"></a>Informations d’identification du coffre fournies non valides

**Message d’erreur** : Informations d’identification du coffre fournies non valides. Cela signifie que le fichier est endommagé ou qu’il ne contient pas les dernières informations d’identification associées au service de récupération. (ID : 34513)

| Cause : | Actions recommandées |
| ---     | ---    |
| **Les informations d’identification du coffre ne sont pas valides** <br/> <br/> Les fichiers d’informations d’identification du coffre peuvent avoir été endommagés ou être arrivés à expiration. (Par exemple, ils peuvent avoir été téléchargés plus de 48 heures avant l’heure de l’inscription.)| Téléchargez les nouvelles informations d’identification du coffre Recovery Services sur le portail Microsoft Azure. (Voir l’étape 6 de la section [Download the MARS agent](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) (Télécharger l’agent MARS).) Suivez ensuite ces étapes : <ul><li> Si vous avez déjà installé et inscrit MARS, ouvrez la console MMC de l’agent Sauvegarde Microsoft Azure, puis sélectionnez **Inscrire le serveur** dans le volet **Actions** pour terminer l’inscription avec les nouvelles informations d’identification. <br/> <li> Si la nouvelle installation échoue, réessayez avec les nouvelles informations d’identification.</ul> **Remarque**: Si plusieurs fichiers d’informations d’identification de coffre ont été téléchargés, seul le plus récent est valide pendant 48 heures. Nous vous recommandons de télécharger un nouveau fichier d’informations d’identification de coffre.
| **Le serveur proxy/pare-feu bloque l’inscription** <br/>or <br/>**Aucune connectivité Internet** <br/><br/> Si votre machine ou serveur proxy a une connectivité à Internet limitée et que vous ne garantissez pas l’accès pour les URL nécessaires, l’inscription échoue.| Suivez ces étapes :<br/> <ul><li> Travaillez avec votre équipe informatique pour vous assurer que le système dispose d’une connectivité Internet.<li> Si vous n’avez pas de serveur proxy, vérifiez que l’option de proxy n’est pas sélectionnée lorsque vous inscrivez l’agent. [Vérifiez vos paramètres de proxy](#verifying-proxy-settings-for-windows).<li> Si vous avez un pare-feu/serveur proxy, travaillez avec votre équipe de mise en réseau pour vous assurer que ces URL et adresses IP sont accessibles :<br/> <br> **URLs**<br> www.msftncsi.com <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**Adresses IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Essayez de procéder une nouvelle fois à l’inscription après avoir effectué les étapes précédentes de résolution des problèmes.
| **Un logiciel antivirus bloque l’inscription** | Si vous avez un logiciel antivirus installé sur le serveur, ajoutez les règles d’exclusion nécessaires à l’analyse antivirus pour ces fichiers et dossiers : <br/><ui> <li> CBengine.exe <li> CSC.exe<li> Le dossier de travail. Son emplacement par défaut est C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> Le dossier Bin à l’emplacement C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Recommandations supplémentaires
- Accédez à C:/Windows/Temp et vérifiez s’il y a plus de 60 000 ou 65 000 fichiers comportant l’extension .tmp. Si c’est le cas, supprimez ces fichiers.
- Vérifiez que la date et l’heure de la machine correspondent au fuseau horaire local.
- Vérifiez que [ces sites](backup-configure-vault.md#verify-internet-access) sont ajoutés à vos sites de confiance dans Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Vérification des paramètres de proxy pour Windows

1. Téléchargez PsExec à partir de la page [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec).
1. À partir d’une invite de commandes avec élévation de privilèges, exécutez `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"`.

   Cette commande ouvre Internet Explorer.
1. Accédez à **Outils** > **Options Internet** > **Connexions** > **Paramètres réseau**.
1. Vérifiez les paramètres de proxy pour le compte système.
1. Si aucun proxy n’est configuré et que les détails du proxy sont fournis, supprimez-les.
1. Si un proxy est configuré et que les détails du proxy sont incorrects, vérifiez que les informations **d’adresse IP de proxy** et de **port** sont correctes.
1. Fermez Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Impossible de télécharger le fichier d’informations d’identification de coffre

| Error   | Actions recommandées |
| ---     | ---    |
|Échec de téléchargement du fichier d'informations d'identification du coffre. (ID : 403) | <ul><li> Essayez de télécharger les informations d’identification de coffre à l’aide d’un autre navigateur, ou procédez comme suit : <ul><li> Démarrez Internet Explorer. Sélectionnez F12. </li><li> Accédez à l’onglet **Réseau** et effacez le cache et les cookies. </li> <li> Actualisez la page.<br></li></ul> <li> Vérifiez si l’abonnement est désactivé ou a expiré.<br></li> <li> Vérifiez si une règle de pare-feu bloque le téléchargement. <br></li> <li> Assurez-vous de ne pas avoir atteint la limite relative au coffre (50 machines par coffre).<br></li>  <li> Vérifiez que l’utilisateur a les autorisations Sauvegarde Azure nécessaires pour télécharger les informations d’identification du coffre et inscrire un serveur dans le coffre. Consultez [Use Role-Based Access Control to manage Azure Backup recovery points](backup-rbac-rs-vault.md) (Utiliser le contrôle d’accès en fonction du rôle pour gérer les points de récupération Sauvegarde Azure).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>L’agent Microsoft Azure Recovery Service n’a pas pu se connecter à la Sauvegarde Microsoft Azure

| Error  | Cause probable | Actions recommandées |
| ---     | ---     | ---    |
| <br /><ul><li>L’agent MARS n’a pas pu se connecter à Sauvegarde Microsoft Azure. (ID : 100050) Vérifiez vos paramètres réseau et assurez-vous que vous pouvez vous connecter à Internet.<li>(407) Authentification proxy requise. |Un proxy bloque la connexion. |  <ul><li>Dans Internet Explorer, accédez à **Outils** > **Options Internet** > **Sécurité** > **Internet**. Sélectionnez **Personnaliser le niveau** et faites défiler l’écran jusqu’à la section **Télécharger le fichier**. Sélectionnez **Activer**.<p>En outre, vous devez peut-être ajouter des [URL et adresses IP](backup-configure-vault.md#verify-internet-access) aux sites de confiance dans Internet Explorer.<li>Modifiez les paramètres pour utiliser un serveur proxy. Fournissez ensuite les détails du serveur proxy.<li> Si votre machine a un accès à Internet limité, assurez-vous que les paramètres du pare-feu sur la machine ou le proxy autorisent ces [URL et adresses IP](backup-configure-vault.md#verify-internet-access). <li>Si un logiciel antivirus est installé sur le serveur, excluez ces fichiers de l’analyse antivirus : <ul><li>CBEngine.exe (au lieu de dpmra.exe).<li>CSC.exe (lié à .NET Framework). Il existe un fichier CSC.exe pour chaque version de .NET Framework installée sur le serveur. Excluez les fichiers CSC.exe de toutes les versions de .NET Framework sur le serveur concerné. <li>L’emplacement du dossier de travail ou du cache. <br>L’emplacement par défaut du dossier de travail ou le chemin d’accès à l’emplacement du cache est C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>Le dossier Bin à l’emplacement C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Échec de définition de la clé de chiffrement pour les sauvegardes sécurisées

| Error | Causes possibles | Actions recommandées |
| ---     | ---     | ---    |
| <br />Échec de définition de la clé de chiffrement pour les sauvegardes sécurisées. L’activation n’a pas entièrement abouti, mais la phrase secrète de chiffrement a été enregistrée dans le fichier suivant. |<li>Le serveur est déjà inscrit auprès d’un autre coffre.<li>Lors de la configuration, la phrase secrète a été endommagée.| Désinscrivez le serveur du coffre et réinscrivez-le avec une nouvelle phrase secrète.

## <a name="the-activation-did-not-complete-successfully"></a>L’activation n’a pas abouti

| Error  | Causes possibles | Actions recommandées |
|---------|---------|---------|
|<br />L’activation n’a pas réussi. Échec de l’opération en cours en raison d’une erreur de service interne [0x1FC07]. Réessayez l’opération après un certain temps. Si le problème persiste, contactez le support technique Microsoft.     | <li> Le dossier de travail se situe sur un volume dont l’espace est insuffisant. <li> Le dossier de travail a été déplacé de façon incorrecte. <li> Le fichier OnlineBackup.KEK est manquant.         | <li>Effectuez une mise à niveau vers la [dernière version](https://aka.ms/azurebackup_agent) de l’agent MARS.<li>Déplacez l’emplacement du dossier de travail ou du cache vers un volume avec un espace disponible compris entre 5 et 10 % de la taille totale des données de sauvegarde. Pour déplacer correctement l’emplacement du cache, reportez-vous aux étapes contenues dans [Questions courantes sur la sauvegarde de fichiers et de dossiers](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Assurez-vous que le fichier OnlineBackup.KEK est présent. <br>*L’emplacement par défaut du dossier de travail ou le chemin d’accès à l’emplacement du cache est C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>La phrase secrète de chiffrement n’est pas correctement configurée

| Error  | Causes possibles | Actions recommandées |
|---------|---------|---------|
| <br />Erreur 34506. La phrase secrète de chiffrement stockée sur cet ordinateur n’est pas configurée correctement.    | <li> Le dossier de travail se situe sur un volume dont l’espace est insuffisant. <li> Le dossier de travail a été déplacé de façon incorrecte. <li> Le fichier OnlineBackup.KEK est manquant.        | <li>Effectuez une mise à niveau vers la [dernière version](https://aka.ms/azurebackup_agent) de l’Agent MARS.<li>Déplacez l’emplacement du dossier de travail ou du cache vers un volume avec un espace disponible compris entre 5 et 10 % de la taille totale des données de sauvegarde. Pour déplacer correctement l’emplacement du cache, reportez-vous aux étapes contenues dans [Questions courantes sur la sauvegarde de fichiers et de dossiers](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Assurez-vous que le fichier OnlineBackup.KEK est présent. <br>*L’emplacement par défaut du dossier de travail ou le chemin d’accès à l’emplacement du cache est C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-schedule"></a>Les sauvegardes ne s’exécutent pas comme prévu
Si les sauvegardes planifiées ne se déclenchent pas automatiquement, alors que les sauvegardes manuelles fonctionnent correctement, essayez ceci :

- Vérifiez que la planification de la sauvegarde de Windows Server n’est pas en conflit avec la planification de la sauvegarde des fichiers et dossiers Azure.

- Vérifiez que l’état de la sauvegarde en ligne est défini sur **Activer**. Pour vérifier l’état, procédez comme suit :

  1. Dans le Planificateur de tâches, développez **Microsoft** et sélectionnez **Sauvegarde en ligne**.
  1. Double-cliquez sur **Microsoft-OnlineBackup** et accédez à l’onglet **Déclencheurs**.
  1. Assurez-vous que l’état est défini sur **Activé**. Si ce n’est pas le cas, sélectionnez **Modifier**, **Activé**, puis **OK**.

- Vérifiez que le compte d’utilisateur sélectionné pour l’exécution de la tâche est **SYSTÈME** ou le **groupe Administrateurs locaux** du serveur. Pour vérifier le compte d’utilisateur, accédez à l’onglet **Général** et vérifiez les options **Sécurité**.

- Vérifiez que PowerShell 3.0 ou version ultérieure est installé sur le serveur. Pour vérifier la version de PowerShell, exécutez cette commande et vérifiez que le numéro de version `Major` est 3 ou plus :

  `$PSVersionTable.PSVersion`

- Vérifiez que ce chemin d’accès fait partie de la variable d’environnement `PSMODULEPATH` :

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Si la stratégie d’exécution de PowerShell pour `LocalMachine` est définie sur « restreinte », la cmdlet PowerShell qui déclenche la tâche de sauvegarde peut échouer. Exécutez ces commandes en mode avec élévation de privilèges pour vérifier la stratégie d’exécution et la définir sur `Unrestricted` ou `RemoteSigned` :

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Vérifiez qu’il n’y a aucun fichier MSOnlineBackup de module PowerShell endommagé ou manquant. En cas de fichiers manquants ou endommagés, procédez comme suit :

  1. Sur n’importe quelle machine disposant d’un agent MARS opérationnel, copiez le dossier MSOnlineBackup depuis C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. Sur la machine qui pose problème, collez les fichiers copiés au même emplacement de dossier (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     S’il existe déjà un dossier MSOnlineBackup sur la machine, collez les fichiers dessus ou remplacez les fichiers existants.


> [!TIP]
> Pour veiller à ce que les modifications soient appliquées de manière cohérente, redémarrez le serveur après avoir suivi les étapes ci-dessus.


## <a name="troubleshoot-restore-problems"></a>Résoudre les problèmes de restauration

Sauvegarde Azure peut ne pas correctement monter le volume de récupération, même au bout de plusieurs minutes. Vous pouvez également recevoir des messages d’erreur pendant le processus. Pour entamer normalement la récupération, procédez comme suit :

1.  Si le processus de montage s’exécute depuis plusieurs minutes, annulez-le.

2.  Vérifiez que vous disposez de la dernière version de l’agent Sauvegarde. Pour vérifier la version, dans le volet **Actions** de la console MARS, sélectionnez **À propos de l’agent Microsoft Azure Recovery Services**. Vérifiez que le numéro de la **version** est supérieur ou égal à la celui mentionné dans [cet article](https://go.microsoft.com/fwlink/?linkid=229525). Sélectionnez ce lien pour [télécharger la dernière version](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Cliquez sur **Gestionnaire de périphériques** > **Contrôleurs de stockage** et localisez le service **Initiateur Microsoft iSCSI**. Si vous le localisez, passez directement à l’étape 7.

4.  Si vous ne parvenez pas à localiser le service Initiateur Microsoft iSCSI, essayez de trouver sous **Gestionnaire de périphériques** > **Contrôleurs de stockage** l’entrée **Appareil inconnu** associée à l’ID matériel **ROOT\ISCSIPRT**.

5.  Cliquez avec le bouton droit sur l’entrée **Appareil inconnu** et sélectionnez **Mettre à jour le pilote**.

6.  Mettez à jour le pilote, en sélectionnant l’option **Rechercher automatiquement un pilote logiciel mis à jour**. Cette mise à jour doit remplacer l’entrée **Appareil inconnu** par **Initiateur Microsoft iSCSI** :

    ![Capture d’écran du Gestionnaire de périphériques de Sauvegarde Azure, avec Contrôleurs de stockage en surbrillance](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Cliquez sur **Gestionnaire des tâches** > **Services (local)**  > **Service Initiateur iSCSI de Microsoft** :

    ![Capture d’écran du Gestionnaire des tâches de Sauvegarde Azure, avec Services (local) en surbrillance](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Redémarrez le service Initiateur Microsoft iSCSI. Pour ce faire, cliquez avec le bouton droit sur le service, puis sélectionnez **Arrêter**. Cliquez de nouveau avec le bouton droit et sélectionnez **Démarrer**.

9.  Retentez la récupération à l’aide de la [Restauration instantanée](backup-instant-restore-capability.md).

Si celle-ci échoue encore, redémarrez votre serveur ou client. Si vous ne souhaitez pas redémarrer, ou si la récupération échoue encore après le redémarrage du serveur, essayez de [l’exécuter depuis une autre machine](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique
Si vous avez encore besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour résoudre rapidement votre problème.

## <a name="next-steps"></a>Étapes suivantes
* Obtenez plus de détails sur [comment sauvegarder votre serveur Windows avec l’agent Sauvegarde Azure](tutorial-backup-windows-server-to-azure.md).
* Si vous avez besoin de restaurer une sauvegarde, consultez l’article dédié à la [restauration de fichiers sur une machine Windows](backup-azure-restore-windows-server.md).
