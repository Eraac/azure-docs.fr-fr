---
title: Démarrage rapide de la solution Azure VMware de CloudSimple - Créer un cloud privé
description: Découvrez comment créer et configurer un cloud privé avec la solution Azure VMware par CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 85a8840ccf6f6fe6390b5eeaccd715d87169f157
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476029"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Démarrage rapide - Configurer un environnement de cloud privé

Dans cet article, découvrez comment créer un cloud privé CloudSimple et configurer votre environnement de cloud privé.

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Créer un cloud privé

1. Sélectionnez **Tous les services**.
2. Recherchez **Services CloudSimple**.
3. Sélectionnez le service CloudSimple où vous voulez créer votre cloud privé.
4. Depuis la vue d’ensemble, cliquez sur **Créer un cloud privé** pour ouvrir un nouvel onglet de navigateur pour le portail CloudSimple.  Si vous y êtes invité, connectez-vous avec vos informations d’identification Azure.  

    ![Créer un cloud privé depuis Azure](media/create-private-cloud-from-azure.png)

5. Dans le portail CloudSimple, fournissez un nom pour votre cloud privé
6. Sélectionnez l’**emplacement** de votre cloud privé
7. Sélectionnez le **Type de nœud** que vous avez configuré sur Azure.  Vous pouvez choisir l’[option CS28 ou CS36](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku). Cette dernière option inclut la capacité de calcul et de mémoire maximale.
8. Spécifiez le **nombre de nœuds**.  Un minimum de trois nœuds est requis pour créer un cloud privé

    ![Créer un cloud privé - Informations de base](media/create-private-cloud-basic-info.png)

9. Cliquez sur **Suivant : Options avancées**.
10. Entrez la plage CIDR pour les sous-réseaux vSphere/vSAN. Assurez-vous que la plage CIDR ne chevauche pas votre réseau local ou d’autres sous-réseaux Azure.

    ![Créer un cloud privé - Options avancées](media/create-private-cloud-advanced-options.png)

11. Sélectionnez **Suivant : Examiner et créer**.
12. Passez en revue les paramètres. Si vous avez besoin modifier des paramètres, cliquez sur **Précédent**.
13. Cliquez sur **Créer**.

Le processus d’approvisionnement du cloud privé sera démarré.  L’approvisionnement du cloud privé peut prendre jusqu’à deux heures.

## <a name="launch-cloudsimple-portal"></a>Lancer le portail CloudSimple

Vous pouvez accéder au portail CloudSimple à partir du portail Azure.  Le portail CloudSimple sera lancé avec vos informations d’identification Azure à l’aide de l’authentification unique (SSO).  L’accès au portail CloudSimple vous demande d’autoriser l’application **Autorisation du Service CloudSimple**.  Pour plus d’informations sur l’octroi d’autorisations, consultez [Consentement pour l’application Autorisation du Service CloudSimple](https://docs.azure.cloudsimple.com/access-cloudsimple-portal/#consent-to-cloudsimple-service-authorization-application)

1. Sélectionnez **Tous les services**.
2. Recherchez **Services CloudSimple**.
3. Sélectionnez le service CloudSimple où vous voulez créer votre cloud privé.
4. Depuis la vue d’ensemble, cliquez sur **Accéder au portail CloudSimple** pour ouvrir un nouvel onglet de navigateur pour le portail CloudSimple.  Si vous y êtes invité, connectez-vous avec vos informations d’identification Azure.  

    ![Lancer le portail CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Créer un VPN de point à site

Une connexion VPN de point à site est la façon la plus simple pour vous connecter à votre cloud privé depuis votre ordinateur. Utilisez la connexion VPN de point à site si vous vous connectez au cloud privé à distance.  Pour accéder rapidement à votre cloud privé, suivez les étapes ci-dessous.  L’accès à la région CloudSimple à partir de votre réseau local peut être effectué à l’aide de [VPN de site à site](https://docs.azure.cloudsimple.com/vpn-gateway/) ou [Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/).

### <a name="create-gateway"></a>Créer une passerelle

1. Lancez le portail CloudSimple et sélectionnez **Réseau**.
2. Sélectionnez **Passerelle VPN**.
3. Cliquez sur **Nouvelle passerelle VPN**.

    ![Créer la passerelle VPN](media/create-vpn-gateway.png)

4. Pour **Configuration de la passerelle**, spécifiez les paramètres suivants et cliquez sur **Suivant**.

    * Sélectionnez **VPN de point à site** comme type de passerelle.
    * Entrez un nom pour identifier la passerelle.
    * Sélectionnez l’emplacement Azure où votre service CloudSimple est déployé.
    * Spécifiez le sous-réseau client pour la passerelle de point à site.  Les adresses DHCP seront accordées à partir de ce sous-réseau lorsque vous vous connectez.

5. Pour **Connexions/Utilisateur**, spécifiez les paramètres suivants et cliquez sur **Suivant**.

    * Pour autoriser automatiquement tous les utilisateurs actuels et futurs à accéder au cloud privé via cette passerelle point à site, sélectionnez **Ajouter automatiquement tous les utilisateurs**. Lorsque vous sélectionnez cette option, tous les utilisateurs dans la liste des utilisateurs sont automatiquement sélectionnés. Vous pouvez remplacer l’option automatique en désélectionnant des utilisateurs individuels dans la liste.
    * Pour sélectionner uniquement les utilisateurs individuels, cliquez sur les cases dans la liste des utilisateurs.

6. La section Réseaux locaux virtuels/Sous-réseaux vous permet de spécifier la gestion et les utilisateur des réseaux locaux virtuels/sous-réseaux pour la passerelle et les connexions.

    * Les options **Ajouter automatiquement** définissent la stratégie globale pour cette passerelle. Les paramètres s’appliquent à la passerelle en cours. Les paramètres peuvent être remplacés dans la zone **Sélectionner**.
    * Sélectionnez **Ajouter la gestion des réseaux locaux virtuels/sous-réseaux de clouds privés**. 
    * Pour ajouter tous les réseaux locaux virtuels/sous-réseaux définis par l’utilisateur, cliquez sur **Ajouter des réseaux locaux virtuels/sous-réseaux définis par l’utilisateur**. 
    * Les paramètres **Sélectionner** remplacent les paramètres généraux sous **Ajouter automatiquement**. 

7. Cliquez sur **Suivant** pour passer en revue les paramètres. Cliquez sur les icônes Modifier pour apporter des modifications.
8. Cliquez sur **Créer** pour créer la passerelle VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Se connecter à CloudSimple à l’aide du VPN de point à site

Un client VPN est nécessaire pour la connexion à CloudSimple depuis votre ordinateur.  Téléchargez le [client OpenVPN](https://openvpn.net/community-downloads/) pour Windows ou [Viscosity](https://www.sparklabs.com/viscosity/download/) pour Mac OS et OS X.

1. Lancez le portail CloudSimple et sélectionnez **Réseau**.
2. Sélectionnez **Passerelle VPN**.
3. Dans la liste des passerelles VPN, cliquez sur la passerelle VPN de point à site.
4. Sélectionnez **Utilisateurs**.
5. Cliquez sur **Télécharger la configuration de mon VPN**

    ![Télécharger une configuration VPN](media/download-p2s-vpn-configuration.png)

6. Importer la configuration sur votre client VPN

    * Instructions pour [l’importation de la configuration sur un client Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instructions pour [l’importation de la configuration sur un client macOS ou OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Se connecter à CloudSimple

## <a name="create-a-vlan-for-your-workload-vms"></a>Créer un réseau local virtuel pour vos machines virtuelles de charge de travail

Après avoir créé un cloud privé, créez un réseau local virtuel où vous déploierez vos machines virtuelles de charge de travail/d’applications.

1. Dans le portail CloudSimple, sélectionnez **Réseau**.
2. Cliquez sur **Réseau local virtuel/sous-réseaux**.
3. Cliquez sur **Créer un réseau local virtuel/sous-réseau**

    ![Créer un réseau local virtuel/sous-réseau](media/create-new-vlan-subnet.png)

4. Sélectionnez le **cloud privé** pour le nouveau réseau local virtuel/sous-réseau.
5. Sélectionnez un ID de réseau local virtuel dans la liste.  
6. Entrez un nom de sous-réseau pour l’identifier.
7. Spécifiez le masque et la plage CIDR du sous-réseau.  Cette plage ne doit pas chevaucher les sous-réseaux existants.
8. Cliquez sur **Envoyer**.

    ![Détails de la création d’un réseau local virtuel/sous-réseau](media/create-new-vlan-subnet-details.png)

Le réseau local virtuel/sous-réseau sera créé.  Vous pouvez désormais utiliser cet ID de réseau local virtuel pour créer un groupe de ports distribués sur votre cloud privé vCenter. 

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Connecter votre environnement à un réseau virtuel Azure

CloudSimple vous offre un circuit ExpressRoute pour votre cloud privé. Vous pouvez connecter votre réseau virtuel sur Azure au circuit ExpressRoute. Pour plus d’informations sur la configuration de la connexion, suivez les étapes dans [Connexion au réseau virtuel Azure à l’aide d’ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/)

## <a name="sign-in-to-vcenter"></a>Se connecter à vCenter

Vous pouvez maintenant vous connecter à vCenter pour configurer des stratégies et des machines virtuelles.

1. Pour accéder à vCenter, démarrez depuis le portail CloudSimple. Sur la page d’accueil, sous **Tâches courantes**, cliquez sur **Lancer le client vSphere**.  Sélectionnez le cloud privé, puis cliquez sur **Lancer le client vSphere** sur le cloud privé.

    ![Lancer le client vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

2. Sélectionnez votre client vSphere préféré pour accéder à vCenter et connectez-vous avec votre nom d’utilisateur et votre mot de passe.  Les valeurs par défaut sont :
    * Nom d’utilisateur : **CloudOwner@cloudsimple.local**
    * Mot de passe : **CloudSimple123!**  

Les écrans de vCenter dans les procédures suivantes sont tirés du client vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Modifier votre mot de passe vCenter

CloudSimple vous recommande de modifier votre mot de passe la première fois que vous vous connectez à vCenter.  
Le mot de passe doit répondre aux exigences suivantes :

* Durée de vie maximale : Le mot de passe doit être modifié tous les 365 jours
* Limiter la réutilisation : Les utilisateurs ne peuvent pas réutiliser l’un des cinq derniers mots de passe utilisés
* Longueur : Entre 8 et 20 caractères
* Caractères spéciaux : Au moins un caractère spécial
* Caractères alphabétiques : Au moins un caractère en majuscule, A-Z et au moins un caractère minuscule, de a à z
* Nombres : Au moins un caractère numérique, 0-9
* Nombre maximal de caractères identiques adjacents : Trois

    Exemple : CC ou CCC est acceptable dans le cadre du mot de passe, mais CCCC ne l’est pas.

Si vous définissez un mot de passe ne répondant pas aux exigences :

* Si vous utilisez le client Flash vSphere, il signale une erreur
* Si vous utilisez le client HTML5, il ne signale pas d’erreur. Le client n’accepte pas la modification et l’ancien mot de passe continue de fonctionner.

## <a name="change-nsx-administrator-password"></a>Modifier le mot de passe d’administrateur NSX

Le gestionnaire NSX est déployé avec un mot de passe par défaut.  Nous vous recommandons de le modifier après avoir créé votre cloud privé.

   * Nom d’utilisateur : **admin**
   * Mot de passe : **CloudSimple123!**

Vous pouvez trouver le nom de domaine complet (FQDN) et l’adresse IP de NSX Manager sur le portail CloudSimple.

1. Lancez le portail CloudSimple et sélectionnez **Ressources**.
2. Cliquez sur le cloud privé que vous souhaitez utiliser.
3. Sélectionnez **Réseau de gestion vSphere**
4. Utiliser le nom de domaine complet ou l’adresse IP de **NSX Manager** et connectez-vous en utilisant un navigateur web. 

    ![Rechercher le nom de domaine complet de NSX Manager](media/private-cloud-nsx-manager-fqdn.png)

Pour modifier le mot de passe, suivez les instructions de l’article [Gérer le mot de passe d’un utilisateur](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.4/administration/GUID-DB31B304-66A5-4516-9E55-2712D12B4F27.html).

> [!WARNING]
> Par défaut, le mot de passe administrateur NSX expire au bout de 90 jours.

## <a name="create-a-port-group"></a>Créer un groupe de ports

Pour créer un groupe de ports distribués dans vSphere :

1. Suivez les instructions de l’article « Ajouter un groupe de ports distribués » dans le [guide de mise en réseau vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Lorsque vous configurez le groupe de ports distribués, fournissez l’ID de réseau local virtuel créé dans [Créer un réseau local virtuel pour vos machines virtuelles de charge de travail](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser des machines virtuelles VMware sur Azure](https://docs.azure.cloudsimple.com/quickstart-create-vmware-virtual-machine)
* [Utiliser des machines virtuelles VMware sur Azure](quickstart-create-vmware-virtual-machine.md)
* [Se connecter à un réseau local à l’aide d’Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Configurer un VPN de site à site en local](https://docs.azure.cloudsimple.com/vpn-gateway/)
