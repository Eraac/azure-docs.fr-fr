---
title: Préparer et personnaliser une image de disque dur virtuel principale - Azure
description: Comment préparer, personnaliser et charger une image principale Windows Virtual Desktop en préversion dans Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: helohr
ms.openlocfilehash: 2413a380adf32755452482d2b68d2055f7db666d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620434"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Préparer et personnaliser une image de disque dur virtuel principale

Cet article vous indique comment préparer une image de disque dur virtuel (VHD) principal pour la télécharger dans Azure, y compris comment créer des machines virtuelles (VM) et installer des logiciels dessus. Ces instructions concernent une configuration spécifique de la préversion de Windows Virtual Desktop qui peut être utilisée avec des processus existants de votre organisation.

## <a name="create-a-vm"></a>Créer une machine virtuelle

Windows 10 Entreprise multisession est disponible dans la galerie d’images Azure. Deux options permettent de personnaliser cette image.

La première option consiste à configurer une machine virtuelle (VM) dans Azure en suivant les instructions de l’article [Créer une machine virtuelle à partir d’une image managée](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed), puis de passer à [Préparation et installation de logiciels](set-up-customize-master-image.md#software-preparation-and-installation).

La deuxième option consiste à créer l’image localement en la téléchargeant, en approvisionnant une machine virtuelle Hyper-V et en la personnalisant selon vos besoins, ce que nous aborderons dans la section suivante.

### <a name="local-image-creation"></a>Création d’une image locale

Après avoir téléchargé l’image vers un emplacement local, ouvrez **Gestionnaire Hyper-V** pour créer une machine virtuelle avec le disque dur virtuel que vous avez copié. Les instructions suivantes constituent une version simple, mais vous trouverez des instructions plus détaillées dans l’article [Créer une machine virtuelle dans Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v).

Pour créer une machine virtuelle avec le disque dur virtuel copié :

1. Ouvrez l’**assistant Créer une machine virtuelle**.

2. Dans la page Spécifier la génération, sélectionnez **Génération 1**.

    ![Une capture d’écran de la page Spécifier la génération. L’option « Génération 1 » est sélectionnée.](media/a41174fd41302a181e46385e1e701975.png)

3. Sous Type de point de contrôle, désactivez les points de contrôle en décochant la case.

    ![Une capture d’écran de la section Type de point de contrôle de la page Points de contrôle.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Vous pouvez également exécuter l’applet de commande suivante dans PowerShell pour désactiver les points de contrôle.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Disque fixe

Si vous créez une machine virtuelle à partir d’un disque dur virtuel existant, il crée un disque dynamique par défaut. Il peut être remplacé par un disque fixe en choisissant **Modifier le disque...**  comme indiqué dans l’image suivante. Pour plus d’informations, consultez [Préparer un VHD ou VHDX Windows à charger sur Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

![Une capture d’écran de l’option Modifier le disque.](media/35772414b5a0f81f06f54065561d1414.png)

Vous pouvez également exécuter l’applet de commande PowerShell suivante pour en faire un disque fixe.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Installation et préparation des logiciels

Cette section explique comment préparer et installer FSLogix, Windows Defender et d’autres applications courantes. 

Si vous installez Office 365 ProPlus et OneDrive sur votre machine virtuelle, consultez [Installer Office sur une image VHD principale](install-office-on-wvd-master-image.md). Suivez le lien dans les étapes suivantes de cet article pour revenir à cet article et terminer le processus du disque dur virtuel principal.

Si vos utilisateurs ont besoin d’accéder à certaines applications métier, nous vous recommandons de les installer à la fin des instructions de cette section.

### <a name="disable-automatic-updates"></a>Désactiver les mises à jour automatiques

Pour désactiver les mises à jour automatiques via une stratégie de groupe locale :

1. Ouvrez **Éditeur de stratégie de groupe locale\\Modèles d’administration\\Composants Windows\\Windows Update**.
2. Faites un clic droit sur **Configurer la mise à jour automatique** et réglez-le sur **Désactivé**.

Vous pouvez également exécuter la commande suivante dans une invite de commande pour désactiver les mises à jour automatiques.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Spécifier la mise en page de démarrage pour les PC Windows 10 (facultatif)

Exécutez cette commande pour spécifier une mise en page de démarrage pour les PC sous Windows 10.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-user-profile-container-fslogix"></a>Configurer le conteneur du profil utilisateur (FSLogix)

Pour inclure le conteneur FSLogix en tant que partie de l’image, suivez les instructions de l’article [Configurer un partage de profil utilisateur pour un pool d’hôtes](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Vous pouvez tester la fonctionnalité du conteneur FSLogix avec [ce démarrage rapide](https://docs.fslogix.com/display/20170529/Profile+Containers+-+Quick+Start).

### <a name="configure-windows-defender"></a>Configurer Windows Defender

Si Windows Defender est configuré dans la machine virtuelle, assurez-vous qu’il l’est de façon à ne pas analyser tout le contenu des fichiers VHD et VHDX durant le rattachement.

Cette configuration supprime uniquement l’analyse des fichiers VHD et VHDX au cours du rattachement, mais n’affecte pas l’analyse en temps réel.

Pour obtenir des instructions plus complètes sur la façon de configurer Windows Defender sur Windows Server, consultez [Configurer les exclusions de l’antivirus Windows Defender sur Windows Server](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus).

Pour en savoir plus sur la façon de configurer Windows Defender pour exclure l’analyse de certains fichiers, consultez [Configurer et valider des exclusions basées sur l’emplacement ou l’extension des fichiers](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus).

### <a name="configure-session-timeout-policies"></a>Configurer des stratégies de délai d’expiration de session

Des stratégies de session à distance peuvent être appliquées au niveau d’une stratégie de groupe dans la mesure où toutes les machines virtuelles au sein d’un pool d’hôtes font partie du même groupe de sécurité.

Pour configurer des stratégies de session à distance :

1. Accédez à **Modèles d’administration** > **Composants Windows** > **Services Bureau à distance** > **Hôte de session Bureau à distance** > **Limites de durée de session**.
2. Dans le volet situé à droite, sélectionnez la stratégie **Définir la limite de temps pour les sessions de services Bureau à distance actives mais dormantes**.
3. Une fois que la fenêtre modale s’affiche, modifiez l’option de stratégie de **Non configuré** à **Activé** pour activer la stratégie.
4. Dans le menu déroulant sous l’option de stratégie, définissez la durée sur **4 heures**.

Vous pouvez également configurer manuellement des stratégies de session à distance en exécutant les commandes suivantes :

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fResetBroken /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxConnectionTime /t REG_DWORD /d 10800000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxDisconnectionTime /t REG_DWORD /d 5000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxIdleTime /t REG_DWORD /d 7200000 /f
```

### <a name="set-up-time-zone-redirection"></a>Configurer la redirection de fuseau horaire

La redirection de fuseau horaire peuvent être appliquées au niveau d’une stratégie de groupe dans la mesure où toutes les machines virtuelles au sein d’un pool d’hôtes font partie du même groupe de sécurité.

Pour rediriger les fuseaux horaires :

1. Sur le serveur Active Directory, ouvrez la **console de gestion des stratégies de groupe**.
2. Développez votre domaine et les objets de stratégie de groupe.
3. Cliquez avec le bouton droit sur l’**objet de stratégie de groupe** que vous avez créé pour les paramètres de stratégie de groupe, puis sélectionnez **Modifier**.
4. Dans l **’éditeur de gestion des stratégies de groupe**, accédez à **Configuration de l’ordinateur** > **Stratégies** > **Modèles administratifs** > **Composants Windows** > **Services Bureau à distance** > **Hôte de Session Bureau à distance**  > **Redirection d’appareils et de ressources**.
5. Activez le paramètre **Autoriser la redirection de fuseau horaire**.

Vous pouvez également exécuter cette commande sur l’image principale pour rediriger les fuseaux horaires :

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Désactiver l’assistant Stockage

Pour l’hôte de session de Windows Virtual Desktop qui utilise Windows 10 Entreprise ou Windows 10 Entreprise multisession, nous vous recommandons de désactiver l’assistant Stockage. Vous pouvez désactiver l’assistant Stockage dans le menu Paramètres sous **Stockage**, comme illustré dans la capture d’écran suivante :

![Une capture d’écran du menu Stockage sous Paramètres. L’option « Assistant Stockage » est désactivée.](media/storagesense.png)

Vous pouvez également modifier le paramètre avec le registre en exécutant la commande suivante :

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Comprend une prise en charge de langages supplémentaires

Cet article n’aborde pas la configuration de langue et du support régional. Pour plus d’informations, consultez les articles suivants :

- [Ajouter des langues aux images Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/add-language-packs-to-windows)
- [Fonctionnalités à la demande](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities)
- [Fonctionnalités de langue et de région à la demande (FOD)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-language-fod)

### <a name="other-applications-and-registry-configuration"></a>Autre configuration des applications et du registre

Cette section décrit la configuration des applications et du système d’exploitation. Toutes les configurations dans cette section sont effectuées par le biais d’entrées de registre qui peuvent être exécutées par lignes de commande et avec les outils de regedit.

>[!NOTE]
>Vous pouvez implémenter les meilleures pratiques de configuration avec des objets de stratégie de groupe (GPO) ou des importations de registre. L’administrateur peut choisir une option selon les besoins de leur organisation.

Pour une collecte du hub de commentaires des données dans Windows 10 Entreprise multisession, exécutez cette commande :

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Exécutez la commande suivante pour résoudre des incidents Watson :

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Entrez les commandes suivantes dans l’éditeur du registre pour corriger la prise en charge de la résolution 5K. Vous devez exécuter les commandes avant de pouvoir activer la pile côte à côte.

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Préparation de l’image pour le téléchargement vers Azure

Une fois que vous avez terminé la configuration et installé toutes les applications, suivez les instructions de l’article [Préparer un disque dur virtuel Windows à charger sur Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) pour préparer l’image.

Après avoir préparé l’image pour le téléchargement, assurez-vous que la machine virtuelle reste dans un état désactivé ou désalloué.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Télécharger l’image principale vers un compte de stockage dans Azure

Cette section s’applique uniquement lorsque l’image principale a été créée localement.

Les instructions suivantes vous indiqueront comment charger votre image principale dans un compte de stockage Azure. Si vous ne disposez pas d’un compte de stockage Azure, suivez les instructions de [cet article](https://code.visualstudio.com/tutorials/static-website/create-storage) pour en créer un.

1. Convertissez l’image de machine virtuelle (VHD) sur Fixed si vous ne l’avez pas déjà fait. Si vous ne convertissez l’image sur Fixed, vous ne l’avez pas correctement créé.

2. Chargez le disque dur virtuel vers un conteneur d’objets blob dans votre compte de stockage. Vous pouvez le télécharger rapidement avec l’[outil Explorateur de stockage](https://azure.microsoft.com/features/storage-explorer/). Pour en savoir plus sur l’outil Explorateur de stockage, consultez [cet article](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

    ![Capture d’écran de la fenêtre de recherche de l’outil Explorateur de stockage Microsoft Azure. La case à cocher « Télécharger les fichiers .vhd ou vhdx en tant qu’objets blob de pages (recommandé) » est sélectionnée.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Ensuite, accédez au portail Azure dans votre navigateur et recherchez des « images ». Votre recherche doit vous conduire à la page **Créer une image**, comme représenté dans la capture d’écran suivante :

    ![Une capture d’écran de la page Créer une image du portail Azure, remplie avec des exemples de valeurs pour l’image.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Une fois l’image créée, vous devez voir une notification telle que celle celui illustrée dans la capture d’écran suivante :

    ![Une capture d’écran de la notification « image créée avec succès ».](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une image, vous pouvez créer ou mettre à jour des pools d’hôtes. Pour en savoir plus sur la création et la mise à jour des pools d’hôtes, consultez les articles suivants :

- [Créer un pool d’hôtes avec le modèle Azure Resource Manager](create-host-pools-arm-template.md)
- [Tutoriel : Créer un pool d’hôtes avec la Place de marché Azure](create-host-pools-azure-marketplace.md)
- [Créer un pool d’hôtes avec PowerShell](create-host-pools-powershell.md)
- [Configurer un partage de profil utilisateur pour un pool d’hôtes](create-host-pools-user-profile.md)
- [Configurer la méthode d’équilibrage de charge de Windows Virtual Desktop](configure-host-pool-load-balancing.md)
