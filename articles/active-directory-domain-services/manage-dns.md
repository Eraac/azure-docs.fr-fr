---
title: Gérer DNS pour Azure AD Domain Services | Microsoft Docs
description: Gérer DNS pour Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: iainfou
ms.openlocfilehash: 6753c26a99bb38e92613a6bad753e7dd101ba68e
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473139"
---
# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Gérer le serveur DNS sur un domaine géré par les services de domaine Azure Active Directory
Les services de domaine Azure Active Directory incluent un serveur DNS (Domain Name Resolution) qui gère la résolution DNS du domaine géré. Il se peut que vous deviez configurer parfois DNS dans le domaine géré. Vous devrez peut-être créer des enregistrements DNS pour les machines qui ne sont pas jointes au domaine, ou configurer les adresses IP virtuelles des équilibreurs de charge ou des redirecteurs DNS externes. Pour cette raison, les utilisateurs qui appartiennent au groupe « AAD DC Administrators » bénéficient de privilèges d’administration DNS sur le domaine géré.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Avant de commencer
Pour effectuer les tâches décrites dans cet article, vous avez besoin de ce qui suit :

1. Un **abonnement Azure**valide.
2. Un **répertoire Azure AD** , synchronisé avec un répertoire local ou un répertoire cloud uniquement.
3. **services de domaine Azure AD** , qui doivent être activés pour le répertoire Azure AD. Si ce n’est déjà fait, suivez l’ensemble des tâches décrites dans le [Guide de mise en route](create-instance.md).
4. Une **machine virtuelle jointe au domaine** , qui vous permet d’administrer le domaine géré par les services de domaine Azure AD. Si vous ne disposez pas de cette machine, suivez toutes les tâches décrites dans l’article intitulé [Joindre une machine virtuelle Windows Server à un domaine géré](active-directory-ds-admin-guide-join-windows-vm.md).
5. Vous devez vous procurer les informations d’identification d’un **compte d’utilisateur appartenant au groupe « AAD DC Administrators »** dans votre répertoire, afin d’administrer DNS pour votre domaine géré.

<br>

## <a name="task-1---create-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Tâche 1 : créer une machine virtuelle jointe au domaine afin d’administrer le nom DNS du domaine managé à distance
Vous pouvez administrer à distance les domaines gérés par les services de domaine Azure Active Directory (AD) par l’intermédiaire des outils d’administration familiers d’Active Directory, par exemple le Centre d’administration Active Directory (ADAC, Active Directory Administrative Center) ou AD PowerShell. De même, le serveur DNS du domaine géré peut être administré à distance via les outils d’administration du serveur DNS.

Les administrateurs du répertoire Azure AD ne disposent pas des privilèges permettant la connexion aux contrôleurs de domaine sur le domaine géré, via le Bureau à distance. Les membres du groupe « AAD DC Administrators » peuvent administrer DNS à distance pour les domaines gérés, à l’aide des outils du serveur DNS d’un ordinateur client/Windows Server joint au domaine géré. Les outils du serveur DNS font partie de la fonctionnalité facultative Outils d'administration de serveur distant.

La première tâche consiste à créer une machine virtuelle Windows Server jointe au domaine managé. Pour savoir comment procéder, consultez l’article [Joindre une machine virtuelle Windows Server à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Tâche 2 : installer les outils de serveur DNS sur la machine virtuelle
Suivez les étapes ci-dessous pour installer les outils d’administration DNS sur la machine virtuelle jointe au domaine. Pour en savoir plus sur [l’installation et l’utilisation des Outils d’administration de serveur distant](https://technet.microsoft.com/library/hh831501.aspx), voir TechNet.

1. Accédez au portail Azure. Cliquez sur **Toutes les ressources** dans le panneau gauche. Recherchez et cliquez sur la machine virtuelle que vous avez créée dans la tâche 1.
2. Cliquez sur le bouton **Connecter** sous l’onglet Vue d’ensemble. Un fichier de protocole Remote Desktop Protocol (.rdp) est créé et téléchargé.

    ![Se connecter à une machine virtuelle Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Pour vous connecter à votre machine virtuelle, ouvrez le fichier RDP téléchargé. À l’invite, cliquez sur **Se connecter**. Utilisez les informations d’identification d’un utilisateur appartenant au groupe « AAD DC Administrators ». Par exemple, « bob@domainservicespreview.onmicrosoft.com ». Un avertissement de certificat peut s’afficher pendant le processus de connexion. Cliquez sur Oui ou Continuer pour vous connecter.

4. Dans l’écran d’accueil, ouvrez **Gestionnaire de serveur**. Dans le volet central de la fenêtre Gestionnaire de serveur, cliquez sur l’option **Ajouter des rôles et fonctionnalités** .

    ![Lancer le gestionnaire de serveur sur la machine virtuelle](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Sur la page **Avant de commencer** de **l’Assistant Ajout de rôles et de fonctionnalités**, cliquez sur **Suivant**.

    ![Page Avant de commencer](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Sur la page **Type d’installation**, laissez l’option **Installation basée sur un rôle ou une fonctionnalité** sélectionnée et cliquez sur **Suivant**.

    ![Page Type d’installation](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Sur la page **Sélection du serveur**, choisissez la machine virtuelle actuelle dans le pool de serveurs, puis cliquez sur **Suivant**.

    ![Page Sélection du serveur](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Sur la page **Rôles de serveurs**, cliquez sur **Suivant**.
9. Sur la page **Fonctionnalités**, cliquez sur le nœud **Outils d’administration de serveur distant** pour le développer, puis sur le nœud **Outils d’administration de rôles**, pour le développer. Sélectionnez la fonctionnalité **Outils du serveur DNS** dans la liste Outils d’administration de rôles.

    ![Page Fonctionnalités](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)
10. Dans la page **Confirmation**, cliquez sur **Installer** pour installer la fonctionnalité Outils du serveur DNS sur la machine virtuelle. Une fois l’installation de la fonctionnalité terminée, cliquez sur **Fermer** afin de fermer l’Assistant **Ajout de rôles et de fonctionnalités**.

    ![Page Confirmation](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)

## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Tâche 3: lancer la console Gestion du service DNS pour administrer le serveur DNS
Vous pouvez à présent utiliser les outils DNS Windows Server pour administrer le nom DNS sur le domaine managé.

> [!NOTE]
> Vous devez être membre du groupe « AAD DC Administrators » pour pouvoir administrer le serveur DNS dans le domaine géré.
>
>

1. Dans l’écran d’accueil, cliquez sur **Outils d’administration**. Vous devez voir apparaître la console **DNS** installée sur la machine virtuelle.

    ![Outils d’administration - Console DNS](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)
2. Cliquez sur **DNS** pour lancer la console Gestion du service DNS.
3. Dans la boîte de dialogue **Se connecter au serveur DNS**, cliquez sur **L’ordinateur suivant** et entrez le nom DNS du domaine managé (par exemple, « contoso100.com »).

    ![Console DNS - Connexion au domaine](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)
4. La console DNS se connecte au domaine géré.

    ![Console DNS - Administration du domaine](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)
5. Vous pouvez désormais utiliser la console DNS pour ajouter des entrées DNS correspondant aux ordinateurs dans le réseau virtuel au sein duquel les services de domaine Azure AD sont activés.

> [!WARNING]
> Soyez prudent lorsque vous administrez DNS pour le domaine géré au moyen des outils d’administration DNS. Assurez-vous de ne pas **supprimer ou modifier les enregistrements DNS intégrés qu’utilisent les services de domaine au sein de domaine**. Les enregistrements DNS intégrés incluent les enregistrements DNS de domaine, les enregistrements de serveur de noms et d’autres enregistrements utilisés pour le lieu du contrôleur de domaine. Si vous modifiez ces enregistrements, les services de domaine sont interrompus sur le réseau virtuel.
>
>

Pour plus d’informations sur la gestion DNS, consultez l’article [Outils DNS](https://technet.microsoft.com/library/cc753579.aspx)sur Technet.

## <a name="related-content"></a>Contenu connexe
* [Services de domaine Azure AD : guide de mise en route](create-instance.md)
* [Joindre une machine virtuelle Windows Server à un domaine géré par les services de domaine Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Gérer un domaine Azure AD Domain Services](manage-domain.md)
* [Outils d’administration DNS](https://technet.microsoft.com/library/cc753579.aspx)
