---
title: Déployer une application double pile IPv6 dans un réseau virtuel Azure - CLI
titlesuffix: Azure Virtual Network
description: Cet article montre comment déployer une application double pile IPv6 dans un réseau virtuel Azure à l’aide d’Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/08/2019
ms.author: kumud
ms.openlocfilehash: cc89e9284e6dbb735aef08100c99a5a7fdb87549
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68248843"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---cli-preview"></a>Déployer une application double pile IPv6 dans un réseau virtuel Azure - CLI (préversion)

Cet article montre comment déployer dans Azure une application double pile (IPv4 + IPv6) incluant un réseau virtuel double pile avec un sous-réseau double pile, un équilibreur de charge avec des configurations frontales doubles (IPv4 + IPv6), des machines virtuelles dont les cartes réseau ont une configuration double IP, des règles de Groupe de sécurité réseau doubles et des adresses IP publiques doubles.

> [!Important]
> La double pile IPv6 pour réseau virtuel Azure est actuellement en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maintenant.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous décidez d’installer et d’utiliser Azure CLI en local, ce guide de démarrage rapide nécessite que vous utilisiez Azure CLI version 2.0.49 ou ultérieure. Exécutez `az --version` pour rechercher la version installée. Pour des informations d'installation ou de mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Prérequis
Pour utiliser la fonctionnalité IPv6 pour un réseau virtuel Azure, vous devez configurer votre abonnement à l’aide d’Azure PowerShell comme suit :

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```
Il faut compter 30 minutes pour l’inscription de la fonctionnalité. Vous pouvez vérifier l’état de votre enregistrement en exécutant la commande Azure CLI suivante :

```azurelci
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```
À l’issue de l’installation, exécutez la commande suivante :

```azurelci
az provider register --namespace Microsoft.Network
```
## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Avant de pouvoir créer un réseau virtuel double pile, vous devez créer un groupe de ressources avec [az group create](/cli/azure/group). L’exemple suivant crée un groupe de ressources nommé *myRGDualStack* à l’emplacement *eastus* :

```azurecli
az group create \
--name DsResourceGroup01 \
--location eastus
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses-for-load-balancer"></a>Créer des adresses IP publiques IPv4 et IPv6 pour l’équilibreur de charge
Pour accéder à vos points de terminaison IPv4 et IPv6 sur Internet, vous avez besoin d’adresses IP publiques IPv4 et IPv6 pour l’équilibreur de charge. Créez une adresse IP publique avec la commande [az network public-ip create](/cli/azure/network/public-ip). L’exemple suivant crée des adresses IP publiques IPv4 et IPv6 nommées *dsPublicIP_v4* and *dsPublicIP_v6* dans le groupe de ressources *myRGDualStack* :

```azurecli
# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

```

## <a name="create-public-ip-addresses-for-vms"></a>Créer des adresses IP publiques pour les machines virtuelles

Pour accéder à distance à vos machines virtuelles sur Internet, vous avez besoin d’adresses IP publiques IPv4 pour les machines virtuelles. Créez une adresse IP publique avec la commande [az network public-ip create](/cli/azure/network/public-ip).

```azurecli
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
```

## <a name="create-basic-load-balancer"></a>Créer un équilibreur de charge de base

Dans cette section, vous allez établir la configuration IP frontale double (IPv4 et IPv6) et configurer le pool d’adresses principal pour l’équilibreur de charge, puis créer un équilibreur de charge de base.

### <a name="create-load-balancer"></a>Créer un équilibreur de charge

Créez l’équilibreur de charge de base à l’aide de la commande [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest). Il est nommé **dsLB** et inclut un pool frontal nommé **dsLbFrontEnd_v4** et un pool principal nommé **dsLbBackEndPool_v4**, qui est associé à l’adresse IP publique IPv4 **dsPublicIP_v4** créée à l’étape précédente. 

```azurecli
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4
```

### <a name="create-ipv6-frontend"></a>Créer une adresse IP frontale IPv6

Créez une adresse IP frontale IPV6 avec [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create). L’exemple suivant crée une configuration IP frontale nommée *dsLbFrontEnd_v6* et joint l’adresse *dsPublicIP_v6* :

```azurepowershell-interactive
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

```

### <a name="configure-ipv6-back-end-address-pool"></a>Configurer un pool d’adresses principal iPv6

Créez un pool d’adresses principal IPv6 avec [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create). L’exemple suivant crée le pool d’adresses principal nommé *dsLbBackEndPool_v6* pour inclure des machines virtuelles avec des configurations de carte réseau IPv6 :

```azurecli
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01
```

### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Une règle d’équilibrage de charge est utilisée pour définir la distribution du trafic vers les machines virtuelles. Vous définissez la configuration IP frontale pour le trafic entrant et le pool d’adresses IP principal pour recevoir le trafic, ainsi que le port source et le port de destination requis. 

Utilisez [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create) pour créer une règle d’équilibrage de charge. L’exemple suivant crée des règles d’équilibreur de charge nommées *dsLBrule_v4* et *dsLBrule_v6* et équilibre le trafic sur le port *TCP* *80* vers les configurations IP frontales IPv4 et IPv6 :

```azurecli
az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

```

## <a name="create-network-resources"></a>Créer des ressources réseau
Avant de déployer des machines virtuelles, vous devez créer des ressources réseau de prise en charge (groupe à haute disponibilité, Groupe de sécurité réseau, réseau virtuel et cartes réseau virtuelles). 
### <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité
Pour améliorer la disponibilité de votre application, placez vos machines virtuelles dans un groupe à haute disponibilité.

Créez un groupe à haute disponibilité avec la commande [az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest). L’exemple suivant permet de créer un groupe à haute disponibilité nommé *dsAVset* :

```azurecli
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2  
```

### <a name="create-network-security-group"></a>Création d’un groupe de sécurité réseau

Créez un Groupe de sécurité réseau pour les règles qui régiront la communication entrante et sortante de votre réseau virtuel.

#### <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Créez un Groupe de sécurité réseau avec la commande [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create).


```azurecli
az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

```

#### <a name="create-a-network-security-group-rule-for-inbound-and-outbound-connections"></a>Créez une règle de Groupe de sécurité réseau pour les connexions entrantes et sortantes.

Avec [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create), créez une règle de Groupe de sécurité réseau pour autoriser les connexions RDP sur le port 3389 et la connexion Internet sur le port 80, ainsi que pour les connexions sortantes.

```azurecli
# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create inbound rule for port 80
az network nsg rule create \
--name allowHTTPIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 200  \
--description "Allow HTTP In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 80  \
--destination-address-prefixes "*"  \
--destination-port-ranges 80

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 300  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"
```


### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel avec la commande [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). L’exemple suivant crée un réseau virtuel nommé *dsVNET* avec les sous-réseaux *dsSubNET_v4* et *dsSubNET_v6* :

```azurecli
# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "ace:cab:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefixes "10.0.0.0/24" "ace:cab:deca:deed::/64" \
--network-security-group dsNSG1
```

### <a name="create-nics"></a>Créer des cartes réseau

Créez des cartes réseau virtuelles pour chaque machine virtuelle avec [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). L’exemple suivant crée une carte réseau virtuelle pour chaque machine virtuelle. Chaque carte réseau a deux configurations IP (1 configuration IPv4 et 1 configuration IPv6). Vous allez créer la configuration IPV6 avec [az network nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create).
 
```azurecli
# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB
```

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez les machines virtuelles avec [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). L’exemple suivant crée deux machines virtuelles ainsi que les composants de réseau virtuel nécessaires s’ils n’existent pas encore. 

Créez la machine virtuelle *dsVM0* comme suit :

```azurecli
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest  
```
Créez la machine virtuelle *dsVM1* comme suit :

```azurecli
az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest 
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Afficher le réseau virtuel double pile IPv6 dans le Portail Azure
Vous pouvez afficher le réseau virtuel double pile IPv6 dans le Portail Microsoft Azure en procédant comme suit :
1. Dans la barre de recherche du portail, saisissez *dsVnet*.
2. Quand la mention **myVirtualNetwork** apparaît dans les résultats de recherche, sélectionnez-la. Cette opération affiche la page **Vue d’ensemble** du réseau virtuel double pile nommé *dsVnet*. Le réseau virtuel de pile double présente les deux cartes réseau, avec des configurations IPv4 et IPv6 situées dans le sous-réseau double pile nommé *dsSubnet*.

  ![Réseau virtuel double pile IPv6 dans Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> L’adresse IPv6 du réseau virtuel Azure est disponible dans le Portail Microsoft Azure en lecture seule pour cette préversion.


## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az-group-delete) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli
 az group delete --name DsRG1
```

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a permis de créer un équilibreur de charge de base avec une configuration IP front-end double (IPv4 et IPv6). Vous avez également créé deux machines virtuelles incluant des cartes réseau avec des configurations IP doubles (IPV4 + IPv6), qui ont été ajoutées au pool back-end de l’équilibreur de charge. Pour en savoir plus sur la prise en charge du protocole IPv6 dans les réseaux virtuels Azure, voir [What is IPv6 for Azure Virtual Network?](ipv6-overview.md) (Rôle d’iPv6 pour un réseau virtuel Azure).
