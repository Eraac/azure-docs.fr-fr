---
title: Installer Office sur une image VHD principale – Azure
description: Guide pratique pour installer et personnaliser Office sur une image principale Windows Virtual Desktop en préversion dans Azure.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: v-chjenk
ms.openlocfilehash: 0e89d37011ccdfc3acdace5b45faa8e9a64e4d3e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620462"
---
# <a name="install-office-on-a-master-vhd-image"></a>Installer Office sur une image VHD principale

Cet article explique comment installer Office 365 ProPlus, OneDrive et d’autres applications courantes sur une image principale de disque dur virtuel (VHD) en vue d’un chargement sur Azure. Si vos utilisateurs ont besoin d’accéder à certaines applications métier, nous vous recommandons de les installer à la fin des instructions de cet article.

Cet article suppose d’avoir déjà créé une machine virtuelle. Sinon, voir [Préparer et personnaliser une image VHD principale](set-up-customize-master-image.md#create-a-vm).

Cet article suppose également d’avoir élevé l’accès à la machine virtuelle, qu’elle soit approvisionnée dans Azure ou avec Hyper-V Manager. Sinon, voir [Élever l’accès pour gérer tous les groupes d’abonnements et d’administration Azure](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin).

>[!NOTE]
>Ces instructions concernent une configuration propre à la préversion de Windows Virtual Desktop, que vous pouvez utiliser avec les processus actuels de votre organisation.

## <a name="install-office-in-shared-computer-activation-mode"></a>Installer Office en mode activation d’ordinateurs partagés

L’activation d’ordinateurs partagés permet de déployer Office 365 ProPlus sur un ordinateur professionnel accessible par plusieurs utilisateurs. Pour plus d’informations sur l’activation d’ordinateurs partagés, voir [Vue d’ensemble de l’activation d’ordinateurs partagés pour Office 365 ProPlus](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus).

Utilisez [l’outil Déploiement d’Office](https://www.microsoft.com/download/details.aspx?id=49117) pour installer Office. Windows 10 Entreprise multisession prend uniquement en charge les versions suivantes d’Office :
- Office 365 ProPlus
- Office 365 Business fourni avec un abonnement Microsoft 365 Business

L’outil Déploiement d’Office exige un fichier XML de configuration. Pour personnaliser l’exemple suivant, voir [Options de configuration de l’outil Déploiement d’Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

L’exemple de fichier XML de configuration fourni effectue les opérations suivantes :

- installer Office à partir d’Insiders Channel et délivrer des mises à jour issues d’Insiders Channel lorsqu’elles sont exécutées ;
- utiliser l’architecture x64 ;
- désactiver les mises à jour automatiques ;
- installer Visio et Project ;
- supprimer toutes les installations existantes d’Office et migrer leurs paramètres ;
- activer l’activation d’ordinateurs partagés.

>[!NOTE]
>La fonctionnalité de recherche de gabarit de Visio ne fonctionne pas dans Windows Virtual Desktop lors de la configuration en préversion.

Cet exemple de fichier XML de configuration n’effectue pas les opérations suivantes :

- installer Skype Entreprise ;
- installer OneDrive en mode utilisateur par utilisateur. Pour plus d’informations, voir [Installer OneDrive en mode ordinateur par ordinateur](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>L’activation d’ordinateurs partagés peut être configurée par le biais d’objets de stratégie de groupe (GPO) ou des paramètres du Registre. Le GPO se trouve dans **Configuration d’ordinateur\\Stratégies\\Modèles d’administration\\Microsoft Office 2016 (ordinateur)\\Paramètres de gestion des licences**.

L’outil Déploiement d’Office contient setup.exe. Pour installer Office, exécutez la commande suivante en ligne de commande :

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Exemple de fichier configuration.xml

L’exemple de fichier XML suivant installe la version Insiders.

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="https://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="https://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>L’équipe Office recommande d’utiliser l’installation 64 bits pour le paramètre **OfficeClientEdition**.

Après avoir installé Office, vous pouvez mettre à jour le comportement d’Office par défaut. Exécutez les commandes suivantes une par une ou dans un fichier de commandes pour mettre à jour le comportement.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>Installer OneDrive en mode ordinateur par ordinateur

OneDrive s’installe normalement utilisateur par utilisateur. Dans cet environnement, il doit être installé ordinateur par ordinateur.

Voici la marche à suivre pour installer OneDrive en mode ordinateur par ordinateur :

1. Commencez par créer un emplacement pour le programme d’installation de OneDrive. Un dossier de disque local ou [\\\\unc](file://unc) conviendra.

2. Téléchargez OneDriveSetup.exe dans votre emplacement intermédiaire avec ce lien : <https://aka.ms/OneDriveWVD-Installer>.

3. Si vous avez installé Office avec OneDrive en omettant **\<ExcludeApp ID="OneDrive" /\>** , désinstallez les installations utilisateur par utilisateur existantes de OneDrive dans une invite de commandes avec élévation de privilèges en exécutant la commande suivante :
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Exécutez cette commande dans une invite de commandes avec élévation de privilèges pour définir la valeur de Registre **AllUsersInstall** :

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Exécutez cette commande pour installer OneDrive en mode ordinateur par ordinateur :

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Exécutez cette commande pour configurer OneDrive de sorte qu’il se lance à la connexion pour tous les utilisateurs :

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Activez **Configurer le compte d’utilisateur en mode silencieux** en exécutant la commande suivante.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Redirigez et déplacez les dossiers Windows connus dans OneDrive en exécutant la commande suivante.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Teams et Skype

Windows Virtual Desktop ne prend pas en charge Skype Entreprise et Teams.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez ajouté à Office à l’image, vous pouvez continuer à personnaliser votre image VHD principale. Voir [Préparer et personnaliser une image de disque dur virtuel principale](set-up-customize-master-image.md).
