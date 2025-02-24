---
title: Créer un pool d’hôtes Windows Virtual Desktop Preview avec PowerShell - Azure
description: Découvrez comment créer un pool d’hôtes dans Windows Virtual Desktop Preview avec des cmdlets PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: helohr
ms.openlocfilehash: 374d5a8f51e28b8a10595842cfc301db503b6bed
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613326"
---
# <a name="create-a-host-pool-with-powershell"></a>Créer un pool d’hôtes avec PowerShell

Les pools d’hôtes sont une collection d’une ou de plusieurs machines virtuelles identiques dans des environnements de locataires Windows Virtual Desktop Preview. Chaque pool d’hôtes peut contenir un groupe d’applications avec lequel les utilisateurs peuvent interagir comme ils le feraient sur un ordinateur de bureau physique.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Utilisez votre client PowerShell pour créer un pool d’hôtes

Tout d’abord, si vous ne l’avez pas déjà fait, [téléchargez et importez le module PowerShell Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell.

Exécutez la cmdlet suivante pour vous connecter à l’environnement Windows Virtual Desktop

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Exécutez ensuite cette cmdlet pour créer un nouveau pool d’hôtes dans votre locataire Windows Virtual Desktop :

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Exécutez la cmdlet suivante pour créer un jeton d’inscription et permettre à un hôte de session de rejoindre le pool d'hôtes et de le sauvegarder dans un nouveau fichier sur votre ordinateur local. Vous pouvez spécifier la durée de validité du jeton d’inscription à l'aide du paramètre -ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

Exécutez la commande suivante pour ajouter des utilisateurs Azure Active Directory au groupe d’applications de bureau par défaut du pool d’hôtes.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

La cmdlet **Add-RdsAppGroupUser** ne prend pas en charge l'ajout de groupes de sécurité et n'ajoute qu'un seul utilisateur à la fois au groupe d’applications. Pour ajouter plusieurs utilisateurs au groupe d'applications, réexécutez la cmdlet avec les noms des utilisateurs principaux.

Exécutez la cmdlet suivante pour exporter le jeton d’inscription vers une variable, que vous utiliserez ultérieurement dans [Inscrire les machines virtuelles dans le pool d'hôtes Windows Virtual Desktop](#register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Créer les machines virtuelles pour le pool d'hôtes

Vous pouvez maintenant créer une machine virtuelle Azure à joindre à votre pool d’hôtes Windows Virtual Desktop.

Vous pouvez créer une machine virtuelle de plusieurs façons :

- [Créer une machine virtuelle à partir d’une image Azure Gallery](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Créer une machine virtuelle à partir d’une image managée](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Créer une machine virtuelle à partir d’une image non managée](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-preview-agent-installations"></a>Préparer les machines virtuelles pour l'installation des agents Windows Virtual Desktop (préversion)

Avant d'installer les agents Windows Virtual Desktop et d'inscrire les machines virtuelles dans votre pool d’hôtes Windows Virtual Desktop, les opérations suivantes sont requises :

- Vous devez joindre le domaine à la machine. Ainsi, le compte Azure Active Directory des utilisateurs Windows Virtual Desktop entrants est mappé à leur compte Active Directory pour leur permettre d'accéder à la machine virtuelle.
- Vous devez installer le rôle Hôte de session Bureau à distance (RDSH) si la machine virtuelle exécute un système d’exploitation Windows Server. Le rôle RDSH permet la bonne installation des agents Windows Virtual Desktop.

À des fins de jonction de domaine, procédez comme suit sur chaque machine virtuelle :

1. [Connectez-vous à la machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) avec les informations d’identification que vous avez indiquées lors de la création de la machine virtuelle.
2. Sur la machine virtuelle, lancez le **Panneau de configuration** et sélectionnez **Système**.
3. Sélectionnez successivement **Nom de l’ordinateur**, **Modifier les paramètres** et **Modifier…**
4. Sélectionnez **Domaine**, puis entrez le domaine Active Directory sur le réseau virtuel.
5. Authentifiez-vous avec un compte de domaine qui dispose de privilèges d’accès sur les machines de jonction de domaine.

    >[!NOTE]
    > Si vous joignez vos machines virtuelles à un environnement Azure AD Domain Services, vérifiez que votre utilisateur de jonction de domaine est également membre du [groupe Administrateurs AAD DC](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-admingroup#task-3-configure-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool"></a>Inscrire les machines virtuelles dans le pool d'hôtes Windows Virtual Desktop (préversion)

L'inscription des machines virtuelles dans un pool d’hôtes Windows Virtual Desktop est aussi simple que l'installation des agents Windows Virtual Desktop.

Pour inscrire les agents Windows Virtual Desktop, procédez comme suit sur chaque machine virtuelle :

1. [Connectez-vous à la machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) avec les informations d’identification que vous avez indiquées lors de la création de la machine virtuelle.
2. Téléchargez et installez l'agent Windows Virtual Desktop.
   - Téléchargez l'[agent Windows Virtual Desktop](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Cliquez sur le programme d’installation téléchargé, sélectionnez **Propriétés**, **Débloquer**, puis **OK**. Votre système sera ainsi en mesure d’approuver le programme d’installation.
   - Exécutez le programme d’installation. Lorsque le programme d’installation vous demande le jeton d’inscription, entrez la valeur obtenue à partir de la cmdlet **Export-RdsRegistrationInfo**.
3. Téléchargez et installez le chargeur de démarrage de l'agent Windows Virtual Desktop.
   - Téléchargez le [chargeur de démarrage de l'agent Windows Virtual Desktop](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Cliquez sur le programme d’installation téléchargé, sélectionnez **Propriétés**, **Débloquer**, puis **OK**. Votre système sera ainsi en mesure d’approuver le programme d’installation.
   - Exécutez le programme d’installation.

>[!IMPORTANT]
>Pour contribuer à sécuriser votre environnement Windows Virtual Desktop dans Azure, nous vous recommandons de ne pas ouvrir le port entrant 3389 sur vos machines virtuelles. Windows Virtual Desktop ne nécessite pas l’ouverture du port entrant 3389 pour permettre aux utilisateurs d’accéder aux machines virtuelles du pool hôte. Si vous devez ouvrir le port 3389 pour résoudre des problèmes, nous vous recommandons d’utiliser un [accès à la machine virtuelle juste-à-temps](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé un pool d'hôtes, vous pouvez le remplir avec RemoteApps. Pour en savoir plus sur la façon de gérer des applications dans Windows Virtual Desktop, consultez le tutoriel Gérer les groupes d’applications.

> [!div class="nextstepaction"]
> [Gérer les groupes d’applications](./manage-app-groups.md)
