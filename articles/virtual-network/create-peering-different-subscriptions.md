---
title: Créer un appairage de réseaux virtuels Azure - Resource Manager - Abonnements différents
titlesuffix: Azure Virtual Network
description: Découvrez comment créer une homologation de réseaux virtuels entre des réseaux virtuels créés via Resource Manager dans des abonnements Azure différents.
services: virtual-network
documentationcenter: ''
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: anavin
ms.openlocfilehash: cf414cf08771090990775d124e27222e51f786e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122016"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Créer une homologation de réseaux virtuels - Resource Manager - Abonnements différents

Dans ce didacticiel, vous allez découvrir comment créer une homologation de réseaux virtuels entre des réseaux virtuels créés par le biais de Resource Manager. Les réseaux virtuels existent dans des abonnements différents. Homologuer deux réseaux virtuels permet aux ressources de différents réseaux virtuels de communiquer entre elles avec la même bande passante et latence, comme si les ressources se trouvaient sur le même réseau virtuel. En savoir plus sur l’[homologation de réseaux virtuels](virtual-network-peering-overview.md).

Les étapes de création d’une homologation de réseaux virtuels sont différentes, selon que les réseaux virtuels sont dans le même abonnement ou dans des abonnements différents, et selon le [modèle de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) utilisé pour les créer. Découvrez comment créer une homologation de réseaux virtuels dans d’autres scénarios en sélectionnant le scénario souhaité dans le tableau suivant :

|Modèle de déploiement Azure  | Abonnement Azure  |
|--------- |---------|
|[Tous deux Resource Manager](tutorial-connect-virtual-networks-portal.md) |Identique|
|[Un modèle Resource Manager, un modèle classique](create-peering-different-deployment-models.md) |Identique|
|[Un modèle Resource Manager, un modèle classique](create-peering-different-deployment-models-subscriptions.md) |Différent|

Vous ne pouvez pas créer d’homologation de réseaux virtuels entre deux réseaux virtuels déployés via le modèle de déploiement classique. Si vous avez besoin de connecter des réseaux virtuels tous deux créés par le biais du modèle de déploiement classique, vous pouvez utiliser une [passerelle VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure.

Ce didacticiel permet d’homologuer des réseaux virtuels situés dans la même région. Vous pouvez également homologuer des réseaux virtuels dans différentes [régions prise en charge](virtual-network-manage-peering.md#cross-region). Il est recommandé de vous familiariser avec les [exigences et contraintes de l’appairage](virtual-network-manage-peering.md#requirements-and-constraints) avant d’appairer des réseaux virtuels.

Vous pouvez utiliser le [portail Azure](#portal), [l’interface de ligne de commande](#cli) (CLI) Azure, Azure [PowerShell](#powershell) ou un [modèle Azure Resource Manager](#template) pour créer une homologation de réseaux virtuels. Sélectionnez l’un des liens d’outils précédents pour accéder directement à la procédure permettant de créer une homologation de réseaux virtuels à l’aide de l’outil de votre choix.

## <a name="portal"></a>Créer une homologation - portail Azure

Si les réseaux virtuels que vous voulez appairer se trouvent dans les abonnements qui sont associés à différents locataires Azure Active Directory, suivez les étapes décrites dans la section CLI et PowerShell de cet article. Le portail n’offre pas de prise en charge pour appairer des réseaux virtuels appartenant à des abonnements à partir de différents locataires Active Directory. 

Remarque : Cloud Shell dispose de limitations quant au changement d’abonnements et d’abonnés à cause desquelles VNet Peering ou Global VNet Peering entre les réseaux virtuels appartenant aux abonnements dans différents abonnés Azure Active Directory ne fonctionnent pas. Veuillez utiliser PowerShell ou CLI.

Les étapes suivantes utilisent des comptes différents pour chaque abonnement. Si vous utilisez un compte qui a des autorisations pour les deux abonnements, vous pouvez utiliser le même compte pour toutes les étapes, ignorer les étapes de déconnexion du portail et ignorer les étapes d’affectation d’autorisations d’accès aux réseaux virtuels à un autre utilisateur.

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant que *UserA*. Le compte auquel vous vous connectez doit avoir les autorisations nécessaires pour créer une homologation de réseaux virtuels. Pour obtenir une liste d’autorisations, consultez [Autorisations d’homologation de réseau virtuel](virtual-network-manage-peering.md#permissions).
2. Sélectionnez **+ Créer une ressource**, **Mise en réseau**, puis **Réseau virtuel**.
3. Sélectionnez ou entrez les exemples de valeurs suivants pour les paramètres correspondants, puis cliquez sur **Créer** :
    - **Nom** : *myVnetA*
    - **Espace d’adressage** : *10.0.0.0/16*
    - **Nom du sous-réseau** : *par défaut*
    - **Plage d’adresses de sous-réseau** : *10.0.0.0/24*
    - **Abonnement**: sélectionnez l’abonnement A.
    - **Groupe de ressources** : sélectionnez **Créer** et entrez *myResourceGroupA*.
    - **Emplacement** : *USA Est*
4. Dans le champ **Rechercher des ressources** située en haut du portail, tapez *myVnetA*. Quand **myVnetA** apparaît dans les résultats de la recherche, sélectionnez cette entrée. 
5. Dans la liste verticale d’options située à gauche, sélectionnez **Contrôle d’accès (IAM)** .
6. Sous **myVnetA - Contrôle d’accès (IAM)** , sélectionnez **+ Ajouter une attribution de rôle**.
7. Dans la zone **Rôle**, sélectionnez **Contributeur de réseaux**.
8. Dans la zone **Sélectionner**, sélectionnez *UserB* ou tapez l’adresse e-mail de UserB pour rechercher cet utilisateur.
9. Sélectionnez **Enregistrer**.
10. Sous **myVnetA - Contrôle d’accès (IAM)** , sélectionnez **Propriétés** dans la liste verticale d’options située à gauche. Copiez l’**ID de ressource**, qui vous servira lors d’une étape ultérieure. L’ID de la ressource ressemble à celui de l’exemple qui suit : `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA`.
11. Déconnectez-vous du portail en tant que UserA, puis connectez-vous en tant que UserB.
12. Effectuez les étapes 2 et 3, en entrant ou en sélectionnant les valeurs suivantes à l’étape 3 :

    - **Nom** : *myVnetB*
    - **Espace d’adressage** : *10.1.0.0/16*
    - **Nom du sous-réseau** : *par défaut*
    - **Plage d’adresses de sous-réseau** : *10.1.0.0/24*
    - **Abonnement**: sélectionnez l’abonnement B.
    - **Groupe de ressources** : sélectionnez **Créer** et entrez *myResourceGroupB*.
    - **Emplacement** : *USA Est*

13. Dans le champ **Rechercher des ressources** située en haut du portail, tapez *myVnetB*. Quand **myVnetB** apparaît dans les résultats de recherche, sélectionnez cette entrée.
14. Sous **myVnetB**, sélectionnez **Propriétés** dans la liste verticale d’options située à gauche. Copiez l’**ID de ressource**, qui vous servira lors d’une étape ultérieure. L’ID de la ressource ressemble à celui de l’exemple qui suit : `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`.
15. Sous **myVnetB**, sélectionnez **Contrôle d’accès (IAM)** , puis suivez les étapes 5 à 10 pour myVnetB, en entrant **UserA** à l’étape 8.
16. Déconnectez-vous du portail en tant que UserB, puis connectez-vous en tant que UserA.
17. Dans le champ **Rechercher des ressources** située en haut du portail, tapez *myVnetA*. Quand **myVnetA** apparaît dans les résultats de la recherche, sélectionnez cette entrée.
18. Sélectionnez **myVnetA**.
19. Sous **PARAMÈTRES**, sélectionnez **Homologations**.
20. Sous **myVnetA - Homologations**, sélectionnez **+ Ajouter**.
21. Sous **Ajouter l’homologation**, entrez ou sélectionnez les options suivantes, puis sélectionnez **OK** :
     - **Nom** : *myVnetAToMyVnetB*
     - **Modèle de déploiement de réseau virtuel** :  Sélectionnez **Gestionnaire des ressources**.
     - **Je connais mon ID de ressource** : cochez cette case.
     - **ID de ressource** : entrez l’ID de ressource noté à l’étape 14.
     - **Autoriser l’accès au réseau virtuel** : vérifiez que **Activé** est sélectionné.
    Il n’y aucun autre paramètre utilisé dans ce didacticiel. Pour en savoir plus sur tous les paramètres d’homologation, consultez [Create, change, or delete a virtual network peering](virtual-network-manage-peering.md#create-a-peering) (Créer, modifier ou supprimer une homologation de réseaux virtuels).
22. L’homologation créée s’affiche quelques instants après que vous avez sélectionné **OK** à l’étape précédente. **Lancé** est indiqué dans la colonne **ÉTAT D’APPAIRAGE** pour l’homologation **myVnetAToMyVnetB** que vous avez créée. Vous avez homologué myVnetA à myVnetB, mais vous devez maintenant homologuer myVnetB à myVnetA. L’homologation doit être créée dans les deux directions pour permettre aux ressources des réseaux virtuels de communiquer entre elles.
23. Déconnectez-vous du portail en tant que UserA, puis connectez-vous en tant que UserB.
24. Répétez les étapes 17 à 21 pour MyVnetB. À l’étape 21, nommez l’homologation *myVnetBToMyVnetA*, sélectionnez *myVnetA* pour **Réseau virtuel** et entrez l’ID noté à l’étape 10 dans la zone **ID de ressource**.
25. Quelques secondes après que vous avez sélectionné **OK** pour créer l’homologation pour myVnetB, l’homologation **myVnetBToMyVnetA** que vous venez de créer est répertoriée avec **Connecté** dans la colonne **ÉTAT D’APPAIRAGE**.
26. Déconnectez-vous du portail en tant que UserB, puis connectez-vous en tant que UserA.
27. Répétez les étapes 17-19. L’**ÉTAT D’APPAIRAGE** pour l’homologation **myVnetAToVNetB** est maintenant également défini sur **Connecté**. L’homologation est établie avec succès une fois que vous voyez **Connecté** dans la colonne **ÉTAT D’APPAIRAGE** pour les deux réseaux virtuels de l’homologation. Les ressources Azure que vous créez dans un réseau virtuel sont désormais en mesure de communiquer entre elles via leurs adresses IP. Si vous utilisez la résolution de noms Azure par défaut pour les réseaux virtuels, les ressources dans les réseaux virtuels ne sont pas en mesure de résoudre les noms dans les réseaux virtuels. Si vous souhaitez résoudre les noms dans les réseaux virtuels d’une homologation, vous devez créer votre propre serveur DNS. Apprenez à configurer la [résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce tutoriel, vous pouvez créer une machine virtuelle sur chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
29. **Facultatif** : pour supprimer les ressources créées dans ce tutoriel, effectuez les étapes décrites dans la section [Supprimer des ressources](#delete-portal) de cet article.

## <a name="cli"></a>Créer une homologation - interface de ligne de commande Azure

Ce didacticiel utilise des comptes différents pour chaque abonnement. Si vous utilisez un compte qui a des autorisations pour les deux abonnements, vous pouvez utiliser le même compte pour toutes les étapes, ignorer les étapes de déconnexion d’Azure et supprimer les lignes de script qui créent les affectations de rôle utilisateur. Remplacez UserA@azure.com et UserB@azure.com dans tous les scripts suivants par les noms d’utilisateurs que vous utilisez pour UserA et UserB. Si les réseaux virtuels se trouvent dans des abonnements différents et que les abonnements sont associés à différents locataires Azure Active Directory, effectuez les étapes suivantes avant de continuer :
 - Ajoutez l’utilisateur à partir de chaque locataire Active Directory en tant [qu’utilisateur invité](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) dans le locataire Azure Active Directory opposé.
 - Chaque utilisateur doit accepter l’invitation d’utilisateur invité du locataire Azure Active Directory opposé.

Les scripts suivants :

- Requiert l’interface de ligne de commande Azure version 2.0.4 ou ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez mettre à niveau, consultez [Installation d’Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Fonctionne dans un interpréteur de commandes Bash. Pour en savoir plus sur l’exécution de scripts Azure CLI sur un client Windows, consultez [Installer Azure CLI sur Windows](/cli/azure/install-azure-cli-windows).

Au lieu d’installer l’interface CLI et ses dépendances, vous pouvez utiliser Azure Cloud Shell. Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. L’interface Azure CLI est préinstallée et configurée pour être utilisée avec votre compte. Sélectionnez le bouton **Try it** dans le script qui suit afin d’appeler un Cloud Shell avec lequel vous pouvez vous connecter à votre compte Azure.

1. Ouvrez une session CLI et connectez-vous à Azure en tant que UserA à l’aide de la commande `azure login`. Le compte auquel vous vous connectez doit avoir les autorisations nécessaires pour créer une homologation de réseaux virtuels. Pour obtenir une liste d’autorisations, consultez [Autorisations d’homologation de réseau virtuel](virtual-network-manage-peering.md#permissions).
2. Copiez le script suivant dans un éditeur de texte sur votre ordinateur, remplacez `<SubscriptionA-Id>` par l’ID de l’abonnement A, puis copiez le script modifié, collez-le dans votre session CLI et appuyez sur `Enter`. Si vous ne connaissez pas votre ID d’abonnement, entrez la commande `az account show`. La valeur de **id** dans la sortie est votre ID d’abonnement.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

3. Déconnectez-vous d’Azure en tant que UserA à l’aide de la commande `az logout`, puis connectez-vous à Azure en tant que UserB. Le compte auquel vous vous connectez doit avoir les autorisations nécessaires pour créer une homologation de réseaux virtuels. Pour obtenir une liste d’autorisations, consultez [Autorisations d’homologation de réseau virtuel](virtual-network-manage-peering.md#permissions).
4. Créez myVnetB. Copiez le contenu du script à l’étape 2 dans un éditeur de texte sur votre PC. Remplacez `<SubscriptionA-Id>` par l’ID de l’abonnement B. Remplacez 10.0.0.0/16 par 10.1.0.0/16, remplacez tous les A par des B, et tous les B par des A. Copiez le script modifié, collez-le dans votre session CLI et appuyez sur `Enter`.
5. Déconnectez-vous d’Azure en tant que UserB et connectez-vous à Azure en tant que UserA.
6. Créez une homologation de réseaux virtuels de myVnetA à myVnetB. Copiez le contenu du script suivant dans un éditeur de texte sur votre PC. Remplacez `<SubscriptionB-Id>` par l’ID de l’abonnement B. Pour exécuter le script, copiez le script modifié, collez-le dans votre session CLI, puis appuyez sur Entrée.

    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)

        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. Affichez l’état d’homologation de myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    L’état est **Lancé**. Il devient **Connecté** une fois que vous avez créé l’homologation à myVnetA à partir de myVnetB.

8. Déconnectez UserA d’Azure et connectez-vous à Azure en tant que UserB.
9. Créez l’homologation de myVnetB à myVnetA. Copiez le contenu du script à l’étape 6 dans un éditeur de texte sur votre PC. Remplacez `<SubscriptionB-Id>` par l’ID de l’abonnement A, et remplacez tous les A par des B et tous les B par des A. Une fois les modifications effectuées, copiez le script modifié, collez-le dans votre session CLI, puis appuyez sur `Enter`.
10. Affichez l’état d’homologation de myVnetB. Copiez le contenu du script à l’étape 7 dans un éditeur de texte sur votre PC. Remplacez A par B dans les noms du groupe de ressources et du réseau virtuel, copiez le script, collez le script modifié dans votre session CLI, puis appuyez sur `Enter`. L’état d’homologation est **Connecté**. L’état d’homologation de myVnetA devient **Connecté** une fois que vous avez créé l’homologation de myVnetB à myVnetA. Vous pouvez reconnecter UserA dans Azure et réeffectuer l’étape 7 pour vérifier l’état d’homologation de myVnetA. 

    > [!NOTE]
    > L’homologation n’est pas établie tant que l’état d’homologation n’est pas **Connecté** pour les deux réseaux virtuels.

11. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce tutoriel, vous pouvez créer une machine virtuelle sur chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
12. **Facultatif** : pour supprimer les ressources créées dans ce tutoriel, effectuez les étapes décrites dans la section [Supprimer des ressources](#delete-cli) de cet article.

Les ressources Azure que vous créez dans un réseau virtuel sont désormais en mesure de communiquer entre elles via leurs adresses IP. Si vous utilisez la résolution de noms Azure par défaut pour les réseaux virtuels, les ressources dans les réseaux virtuels ne sont pas en mesure de résoudre les noms dans les réseaux virtuels. Si vous souhaitez résoudre les noms dans les réseaux virtuels d’une homologation, vous devez créer votre propre serveur DNS. Apprenez à configurer la [résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="powershell"></a>Créer une homologation - PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ce didacticiel utilise des comptes différents pour chaque abonnement. Si vous utilisez un compte qui a des autorisations pour les deux abonnements, vous pouvez utiliser le même compte pour toutes les étapes, ignorer les étapes de déconnexion d’Azure et supprimer les lignes de script qui créent les affectations de rôle utilisateur. Remplacez UserA@azure.com et UserB@azure.com dans tous les scripts suivants par les noms d’utilisateurs que vous utilisez pour UserA et UserB.
Si les réseaux virtuels se trouvent dans des abonnements différents et que les abonnements sont associés à différents locataires Azure Active Directory, effectuez les étapes suivantes avant de continuer :
 - Ajoutez l’utilisateur à partir de chaque locataire Active Directory en tant [qu’utilisateur invité](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) dans le locataire Azure Active Directory opposé.
 - Chaque utilisateur doit accepter l’invitation d’utilisateur invité du locataire Active Directory opposé.

1. Vérifiez que vous disposez de la version 1.0.0 ou d’une version ultérieure d’Azure PowerShell. Pour ce faire, exécutez la commande `Get-Module -Name Az`. Nous vous recommandons d’installer la dernière version du module [Az](/powershell/azure/install-az-ps) PowerShell. Si vous débutez dans l’utilisation d’Azure PowerShell, voir [Vue d’ensemble d’Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json). 
2. Démarrez une session PowerShell.
3. Dans PowerShell, connectez-vous à Azure en tant que UserA en entrant la commande `Connect-AzAccount`. Le compte auquel vous vous connectez doit avoir les autorisations nécessaires pour créer une homologation de réseaux virtuels. Pour obtenir une liste d’autorisations, consultez [Autorisations d’homologation de réseau virtuel](virtual-network-manage-peering.md#permissions).
4. Créez un groupe de ressources et le réseau virtuel A. Copiez le script suivant dans un éditeur de texte sur votre PC. Remplacez `<SubscriptionA-Id>` par l’ID de l’abonnement A. Si vous ne connaissez pas votre ID d’abonnement, entrez la commande `Get-AzSubscription` pour l’afficher. La valeur de **id** dans la sortie retournée est votre ID d’abonnement. Pour exécuter le script, copiez le script modifié, collez-le dans PowerShell, puis appuyez sur `Enter`.

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Déconnectez UserA d’Azure et connectez UserB. Le compte auquel vous vous connectez doit avoir les autorisations nécessaires pour créer une homologation de réseaux virtuels. Pour obtenir une liste d’autorisations, consultez [Autorisations d’homologation de réseau virtuel](virtual-network-manage-peering.md#permissions).
6. Copiez le contenu du script à l’étape 4 dans un éditeur de texte sur votre PC. Remplacez `<SubscriptionA-Id>` par l’ID de l’abonnement B. Remplacez 10.0.0.0/16 par 10.1.0.0/16. Remplacez tous les A par des B et tous les B par des A. Pour exécuter le script, copiez le script modifié, collez-le dans PowerShell, puis appuyez sur `Enter`.
7. Déconnectez UserB d’Azure et connectez UserA.
8. Créez l’homologation de myVnetA à myVnetB. Copiez le script suivant dans un éditeur de texte sur votre PC. Remplacez `<SubscriptionB-Id>` par l’ID de l’abonnement B. Pour exécuter le script, copiez le script modifié, collez-le dans PowerShell, puis appuyez sur `Enter`.

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. Affichez l’état d’homologation de myVnetA.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    L’état est **Lancé**. Il devient **Connecté** une fois que vous avez configuré l’homologation à myVnetA à partir de myVnetB.

10. Déconnectez UserA d’Azure et connectez UserB.
11. Créez l’homologation de myVnetB à myVnetA. Copiez le contenu du script à l’étape 8 dans un éditeur de texte sur votre PC. Remplacez `<SubscriptionB-Id>` par l’ID de l’abonnement A. Remplacez tous les A par des B et tous les B par des A. Pour exécuter le script, copiez le script modifié, collez-le dans PowerShell, puis appuyez sur `Enter`.
12. Affichez l’état d’homologation de myVnetB. Copiez le contenu du script à l’étape 9 dans un éditeur de texte sur votre PC. Remplacez A par B dans les noms du groupe de ressources et du réseau virtuel. Pour exécuter le script, collez le script modifié dans PowerShell, puis appuyez sur `Enter`. L’état est **Connecté**. L’état d’homologation de **myVnetA** devient **Connecté** une fois que vous avez créé l’homologation de **myVnetB** à **myVnetA**. Vous pouvez reconnecter UserA dans Azure et réeffectuer l’étape 9 pour vérifier l’état d’homologation de myVnetA.

    > [!NOTE]
    > L’homologation n’est pas établie tant que l’état d’homologation n’est pas **Connecté** pour les deux réseaux virtuels.

    Les ressources Azure que vous créez dans un réseau virtuel sont désormais en mesure de communiquer entre elles via leurs adresses IP. Si vous utilisez la résolution de noms Azure par défaut pour les réseaux virtuels, les ressources dans les réseaux virtuels ne sont pas en mesure de résoudre les noms dans les réseaux virtuels. Si vous souhaitez résoudre les noms dans les réseaux virtuels d’une homologation, vous devez créer votre propre serveur DNS. Apprenez à configurer la [résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce tutoriel, vous pouvez créer une machine virtuelle sur chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
14. **Facultatif** : pour supprimer les ressources créées dans ce tutoriel, effectuez les étapes décrites dans la section [Supprimer des ressources](#delete-powershell) de cet article.

## <a name="template"></a>Créer une homologation - modèle Resource Manager

Si les réseaux virtuels se trouvent dans des abonnements différents et que les abonnements sont associés à différents locataires Azure Active Directory, effectuez les étapes suivantes avant de continuer :
 - Ajoutez l’utilisateur à partir de chaque locataire Active Directory en tant [qu’utilisateur invité](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) dans le locataire Azure Active Directory opposé.
 - Chaque utilisateur doit accepter l’invitation d’utilisateur invité du locataire Active Directory opposé.

1. Pour créer un réseau virtuel et attribuer les [autorisations](virtual-network-manage-peering.md#permissions) appropriées, suivez les étapes des sections [Portail](#portal), [Azure CLI](#cli) ou [PowerShell](#powershell) de cet article.
2. Enregistrez le texte qui suit dans un fichier sur votre ordinateur local. Remplacez `<subscription ID>` par votre ID d’abonnement UtilisateurA. Vous pouvez enregistrez le fichier en tant que vnetpeeringA.json, par exemple.

   ```json
   {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
   }
   ```

3. Connectez-vous à Azure en tant qu’UtilisateurA et déployez le modèle à l’aide de [portail](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template) ou [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Spécifiez le nom de fichier dans lequel vous avez enregistré l’exemple de texte json à l’étape 2.
4. Copiez l’exemple json de l’étape 2 dans un fichier sur votre ordinateur et apporter des modifications pour les lignes qui commencent par :
   - **nom** : remplacez *myVnetA/myVnetAToMyVnetB* par *myVnetB/myVnetBToMyVnetA*.
   - **ID** : remplacez `<subscription ID>` par l’ID d’abonnement de UserB et remplacez *myVnetB* par *myVnetA*.
5. Effectuez l’étape 3 à nouveau, connecté à Azure en tant qu’UtilisateurB.
6. **Facultatif** : bien que la création de machines virtuelles ne soit pas abordée dans ce tutoriel, vous pouvez créer une machine virtuelle sur chaque réseau virtuel et vous connecter d’une machine virtuelle à l’autre pour valider la connectivité.
7. **Facultatif** : pour supprimer les ressources créées dans ce tutoriel, effectuez les étapes décrites dans la section [Supprimer des ressources](#delete) de cet article avec le portail Azure, PowerShell ou Azure CLI.

## <a name="delete"></a>Supprimer des ressources
Lorsque vous aurez terminé ce didacticiel, vous souhaiterez peut-être supprimer les ressources que vous avez créées, afin que leur utilisation ne soit pas facturée. La suppression d’un groupe de ressources supprime également toutes les ressources qu’il contient.

### <a name="delete-portal"></a>Portail Azure

1. Connectez-vous au portail Azure en tant que UserA.
2. Dans le champ de recherche du portail, entrez **myResourceGroupA**. Dans les résultats de la recherche, sélectionnez **myResourceGroupA**.
3. Sélectionnez **Supprimer**.
4. Pour confirmer la suppression, dans le champ **Tapez le nom du groupe de ressources**, entrez **myResourceGroupA**, puis sélectionnez **Supprimer**.
5. Déconnectez-vous du portail en tant que UserA, puis connectez-vous en tant que UserB.
6. Effectuez les étapes 2 à 4 pour myResourceGroupB.

### <a name="delete-cli"></a>Interface CLI Azure

1. Connectez-vous à Azure en tant que UserA et exécutez la commande suivante :

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Déconnectez-vous d’Azure en tant que UserA, puis connectez-vous en tant que UserB.
3. Exécutez la commande suivante :

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="delete-powershell"></a>PowerShell

1. Connectez-vous à Azure en tant que UserA et exécutez la commande suivante :

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Déconnectez-vous d’Azure en tant que UserA, puis connectez-vous en tant que UserB.
3. Exécutez la commande suivante :

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Étapes suivantes

- Familiarisez-vous bien avec les [comportements et contraintes importants de l’homologation de réseaux virtuels](virtual-network-manage-peering.md#requirements-and-constraints) avant d’en créer une pour une utilisation en production.
- Apprenez-en davantage sur tous les [paramètres d’homologation de réseaux virtuels](virtual-network-manage-peering.md#create-a-peering).
- Découvrez comment [créer une topologie de réseau de type hub et spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) avec l’homologation de réseaux virtuels.
