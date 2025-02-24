---
title: Créer, modifier ou supprimer une adresse IP publique Azure | Microsoft Docs
description: Découvrez comment créer, modifier ou supprimer une adresse IP publique.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 98810af66b0be6d925229b7e05dc01f62106e7cd
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "64728737"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Créer, modifier ou supprimer une adresse IP publique

Découvrez les adresses IP publiques et apprenez à en créer, modifier et supprimer une. Une adresse IP publique est une ressource disposant de ses propres paramètres configurables. L’attribution d’une adresse IP publique à une ressource Azure qui prend en charge les adresses IP publiques permet :
- Communication entrante d’Internet vers la ressource, par exemple les machines virtuelles Azure (VM), les passerelles Azure Application Gateway, les équilibreurs de charge Azure Load Balancer, les passerelles VPN Azure, etc. Vous pouvez toujours communiquer avec des ressources telles qu’une machine virtuelle, à partir d’Internet si aucune adresse IP publique ne lui est attribuée à condition que la machine virtuelle fasse partie d’un pool principal à équilibreur de charge et qu’une adresse IP publique soit attribuée à l’équilibreur de charge. Pour déterminer si une ressource d’un service Azure spécifique peut se voir attribuer une adresse IP publique ou si des communications peuvent être établies avec elle via l’adresse IP publique d’une autre ressource Azure, consultez la documentation du service.
- Connectivité sortante vers Internet à l’aide d’une adresse IP prédictible. Par exemple, une machine virtuelle peut communiquer en sortie vers Internet sans adresse IP publique attribuée, mais son adresse fait l’objet d’une traduction d’adresse réseau par Azure en adresse publique non prédictible par défaut. L’attribution d’une adresse IP publique à une ressource vous permet de savoir quelle adresse IP est utilisée pour la connexion sortante. Bien que prédictible, l’adresse peut changer en fonction de la méthode d’attribution choisie. Pour plus d’informations, consultez [Créer une adresse IP publique](#create-a-public-ip-address). Pour en savoir plus sur les connexions sortantes des ressources Azure, consultez [Comprendre les connexions sortantes dans Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Avant de suivre les étapes décrites dans les sections de cet article, accomplissez les tâches suivantes :

- Si vous n’avez pas encore de compte, inscrivez-vous pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/free).
- Si vous utilisez le portail, ouvrez https://portal.azure.com, puis connectez-vous avec votre compte Azure.
- Si vous utilisez des commandes PowerShell pour accomplir les tâches décrites dans cet article, exécutez-les dans l’[Azure Cloud Shell](https://shell.azure.com/powershell), ou en exécutant PowerShell à partir de votre ordinateur. Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Ce didacticiel requiert le module Azure PowerShell version 1.0.0 ou version ultérieure. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzAccount` pour créer une connexion avec Azure.
- Si vous utilisez des commandes de l’interface de ligne de commande (CLI) Azure pour accomplir les tâches décrites dans cet article, exécutez les commandes dans [Azure Cloud Shell](https://shell.azure.com/bash) ou en exécutant Azure CLI sur votre ordinateur. Ce tutoriel requiert Azure CLI version 2.0.31 ou ultérieure. Exécutez `az --version` pour rechercher la version installée. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli). Si vous exécutez Azure CLI localement, vous devez également exécuter `az login` pour créer une connexion avec Azure.

Le compte auquel vous vous connectez, ou avec lequel vous vous connectez à Azure, doit avoir le rôle [contributeur réseau](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou un [rôle personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) disposant des autorisations appropriées, listées dans [Autorisations](#permissions).

Le coût des adresses IP publiques est modique. Pour voir les prix, consultez la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

1. Dans l’angle supérieur gauche du portail, sélectionnez **+ Créer une ressource**.
2. Entrez *adresse IP publique* dans la zone *Rechercher dans le Marketplace*. Quand la mention **Adresse IP publique** s’affiche dans les résultats de la recherche, sélectionnez-la.
3. Sous **Adresse IP publique**, sélectionnez **Créer**.
4. Entrez ou sélectionnez des valeurs pour les paramètres suivants et, sous **Créer une adresse IP publique**, sélectionnez **Créer** :

   |Paramètre|Requis ?|Détails|
   |---|---|---|
   |Nom|OUI|Le nom doit être unique au sein du groupe de ressources que vous avez sélectionné.|
   |SKU|OUI|Toutes les adresses IP publiques créées avant l’introduction des références SKU sont des adresses IP publiques **De base**. Une fois l’adresse IP publique créée, vous ne pouvez pas modifier la référence SKU. Les machines virtuelles autonomes, les machines virtuelles comprises dans un groupe à haute disponibilité et les groupes de machines virtuelles identiques peuvent utiliser les références SKU De Base ou Standard. Toutefois, les machines virtuelles d’un même groupe à haute disponibilité ou groupe identique ne peuvent pas utiliser des références SKU différentes. Référence **De base** : Si vous créez une adresse IP publique dans une région prenant en charge les zones de disponibilité, le paramètre **Zone de disponibilité** a la valeur *Aucun* par défaut. Vous pouvez sélectionner une zone de disponibilité afin de garantir une zone spécifique pour votre adresse IP publique. Référence **Standard** : Une adresse IP publique de référence Standard peut être associée à une machine virtuelle ou à un équilibreur de charge front-end. Si vous créez une adresse IP publique dans une région qui prend en charge les zones de disponibilité, le paramètre **Zone de disponibilité** est défini sur *Redondant interzone* par défaut. Pour plus d’informations sur les zones de disponibilité, consultez **Zone de disponibilité**. La référence SKU Standard est nécessaire si vous associez l’adresse à un équilibreur de charge Standard. Pour plus d’informations sur les équilibreurs de charge Standard, consultez [Référence SKU d’Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Quand vous assignez une adresse IP publique de référence SKU Standard à l’interface réseau d’une machine virtuelle, vous devez explicitement autoriser le trafic prévu avec un [groupe de sécurité réseau](security-overview.md#network-security-groups). La communication avec la ressource est possible uniquement si vous créez et associez un groupe de sécurité réseau et que vous autorisez explicitement le trafic prévu.|
   |Version de l’adresse IP|OUI| Sélectionnez IPv4 ou IPv6. Alors que les adresses IPv4 publiques peuvent être assignées à plusieurs ressources Azure, les adresses IP publiques IPv6 ne peuvent être assignées qu’à un équilibreur de charge accessible sur Internet. L’équilibreur de charge permet d’équilibrer le trafic IPv6 vers les machines virtuelles Azure. En savoir plus sur [l’équilibrage de charge du trafic IPv6 vers les machines virtuelles](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Si vous avez sélectionné la **référence SKU Standard**, vous ne pouvez pas sélectionner *IPv6*. Lorsque vous utilisez la **référence SKU Standard**, vous pouvez uniquement créer une adresse IPv4.|
   |Affectation d’adresses IP|OUI|**Dynamique :** Les adresses dynamiques sont attribuées uniquement une fois qu’une adresse IP publique a été associée à une ressource Azure, et que la ressource démarre pour la première fois. Les adresses dynamiques peuvent changer si elles sont attribuées à une ressource, comme une machine virtuelle, et que la machine virtuelle est arrêtée (désallouée), puis redémarrée. L’adresse reste la même si une machine virtuelle est redémarrée ou arrêtée (mais pas désallouée). Les adresses dynamiques sont libérées quand une ressource d’adresse IP publique est dissociée d’une ressource à laquelle elle est associée. **Statique :** les adresses statiques sont attribuées durant la création d’une adresse IP publique. Les adresses statiques ne sont pas libérées tant qu’une ressource d’adresse IP publique n’est pas supprimée. Si l’adresse n’est pas associée à une ressource, vous pouvez changer la méthode d’attribution après la création de l’adresse. Si l’adresse est associée à une ressource, il est possible que vous ne puissiez pas changer la méthode d’attribution. Si vous sélectionnez *IPv6* comme **Version IP**, la seule méthode d’attribution disponible est *Dynamique*. Si vous sélectionnez *Standard* comme **Référence SKU**, la méthode d’attribution est *Statique*.|
   |Délai d’inactivité (minutes)|Non|Durée (en minutes) de maintien d’une connexion TCP ou HTTP ouverte sans utiliser les clients pour envoyer des messages keep-alive. Si vous sélectionnez IPv6 pour **Version IP**, cette valeur ne peut pas être modifiée. |
   |Étiquette du nom DNS|Non|Doit être unique dans l’emplacement Azure où vous créez le nom (pour tous les abonnements et tous les clients). Azure inscrit automatiquement le nom et l’adresse IP dans son DNS pour que vous puissiez vous connecter à une ressource avec le nom. Azure ajoute un sous-réseau par défaut comme *emplacement.cloudapp.azure.com* (où emplacement est l’emplacement que vous sélectionnez) au nom que vous indiquez pour créer le nom DNS complet. Si vous choisissez de créer les deux versions d’adresse, le même nom DNS est assigné aux adresses IPv4 et IPv6. Le DNS par défaut d’Azure contient à la fois les enregistrements de nom AAAA IPv4 A et IPv6, et il renvoie les deux enregistrements lors de la recherche du nom DNS. Le client choisit l’adresse (IPv4 ou IPv6) avec laquelle communiquer. À la place ou en plus de l’étiquette de nom DNS avec le suffixe par défaut, vous pouvez utiliser le service Azure DNS pour configurer un nom DNS avec un suffixe personnalisé qui se résout en adresse IP publique. Pour plus d’informations, consultez [Utiliser Azure DNS avec une adresse IP publique Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Créer une adresse IPv6 (ou IPv4)|Non| L’affichage de l’adresse IPv6 ou IPv4 dépend du paramétrage de **Version IP**. Par exemple, si vous sélectionnez **IPv4** pour **Version IP**, **IPv6** s’affiche ici. Si vous sélectionnez *Standard* comme **Référence SKU**, vous ne pouvez pas créer d’adresse IPv6.
   |Nom (visible uniquement si vous avez coché la case **Créer une adresse IPv6 (ou IPv4)** )|Oui, si vous cochez la case **Créer une adresse IPv6** (ou IPv4).|Le nom doit être différent de celui que vous entrez pour le premier **Nom** dans cette liste. Si vous choisissez de créer une adresse IPv4 et une adresse IPv6, le portail crée deux ressources d’adresse IP publique distinctes : une pour chaque version d’adresse IP qui lui est assignée.|
   |Affectation d’adresses IP (visible uniquement si vous avez coché la case **Créer une adresse IPv6 (ou IPv4)** )|Oui, si vous cochez la case **Créer une adresse IPv6** (ou IPv4).|Si la case à cocher indique **Créer une adresse IPv4**, vous pouvez sélectionner une méthode d’attribution. Si la case à cocher indique **Créer une adresse IPv6**, vous ne pouvez pas sélectionner de méthode d’attribution, car cette dernière doit être **Dynamique**.|
   |Abonnement|OUI|Doit exister dans le même [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que la ressource à laquelle vous voulez associer l’adresse IP publique.|
   |Groupe de ressources|OUI|Peut exister dans un [groupe de ressources](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) identique ou différent de celui de la ressource à laquelle vous voulez associer l’adresse IP publique.|
   |Lieu|OUI|Doit exister au même [emplacement](https://azure.microsoft.com/regions) (également appelé région) que la ressource à laquelle vous voulez associer l’adresse IP publique.|
   |Zone de disponibilité| Non | Ce paramètre s’affiche uniquement si vous sélectionnez un emplacement pris en charge. Pour obtenir la liste des emplacements pris en charge, consultez [Vue d’ensemble des zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Si vous avez sélectionné la référence SKU **De base**, l’option *Aucun* est automatiquement sélectionnée. Si vous préférez garantir une zone spécifique, vous pouvez en sélectionner une. Ces deux options sont sans redondance interzone. Si vous avez sélectionné la référence **Standard** : l’option Redondant interzone est automatiquement sélectionnée et permet la résilience de votre chemin de données en cas de défaillance de la zone. Si vous préférez garantir une zone spécifique qui ne soit pas résiliente en cas de défaillance, vous pouvez en sélectionner une.

**Commandes**

Bien que le portail permette de créer deux ressources d’adresse IP publique (IPv4 et IPv6), les commandes CLI et PowerShell suivantes créent une ressource avec une adresse pour une version IP ou l’autre. Si vous souhaitez deux ressources d’adresse IP publique, une pour chaque version IP, vous devez exécuter la commande à deux reprises, en spécifiant des versions et des noms différents pour les ressources d’adresse IP publique.

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Afficher une adresse IP publique, modifier ses paramètres ou la supprimer

1. Dans la zone qui contient le texte *Rechercher des ressources* en haut du portail Azure, entrez *adresses ip publique*. Quand la mention **Adresses IP publiques** s’affiche dans les résultats de la recherche, sélectionnez-la.
2. Sélectionnez le nom de l’adresse IP publique que vous voulez afficher, dont vous souhaitez modifier les paramètres ou que vous voulez supprimer de la liste.
3. Choisissez l’une des options suivantes, selon que vous souhaitez afficher l’adresse IP publique, la supprimer ou la modifier.
   - **Afficher** : la section **Vue d’ensemble** affiche les paramètres clés de l’adresse IP publique, par exemple l’interface réseau à laquelle elle est associée (si l’adresse est associée à une interface réseau). Le portail n’affiche pas la version de l’adresse (IPv4 ou IPv6). Pour afficher les informations de version, utilisez la commande PowerShell ou CLI afin d’afficher l’adresse IP publique. Si la version d’adresse IP est IPv6, le portail, PowerShell et l’interface CLI n’affichent pas l’adresse assignée.
   - **Supprimer** : pour supprimer l’adresse IP publique, sélectionnez **Supprimer** dans la section **Vue d’ensemble**. Si l’adresse est actuellement associée à une configuration IP, elle ne peut pas être supprimée. Si l’adresse est associée à une configuration, sélectionnez **Dissocier** pour la dissocier de la configuration IP.
   - **Modifier** : sélectionnez **Configuration**. Modifiez les paramètres souhaités en utilisant les informations indiquées à l’étape 4 de la section [Créer une adresse IP publique](#create-a-public-ip-address). Pour modifier l’attribution d’une adresse IPv4 et passer des adresses statiques à des adresses dynamiques, vous devez commencer par dissocier l’adresse IPv4 publique de la configuration IP à laquelle elle est associée. Vous pouvez ensuite changer la méthode d’affectation et la définir sur dynamique, puis sélectionner **Associer** pour associer l’adresse IP à la même configuration IP ou à une autre configuration. Vous pouvez aussi la dissocier. Pour dissocier une adresse IP publique, dans la section **Vue d’ensemble**, sélectionnez **Dissocier**.

   >[!WARNING]
   >Lorsque vous modifiez la méthode d’affectation pour passer des adresses statiques à des adresses dynamiques, vous perdez l’adresse IP qui a été affectée à l’adresse IP publique. Si les serveurs DNS publics Azure conservent un mappage entre les adresses statiques ou dynamiques et une étiquette de nom DNS (si vous en avez défini une), une adresse IP dynamique peut être modifiée au démarrage de la machine virtuelle après qu’elle a été arrêtée (libérée). Pour empêcher toute modification de l’adresse, affectez une adresse IP statique.

**Commandes**

|Outil|Commande|
|---|---|
|Interface de ligne de commande|[az network public-ip list](/cli/azure/network/public-ip#az-network-public-ip-list) pour répertorier les adresses IP publiques, [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) pour afficher les paramètres, [az network public-ip update](/cli/azure/network/public-ip#az-network-public-ip-update) pour mettre à jour, [az network public-ip delete](/cli/azure/network/public-ip#az-network-public-ip-delete) pour supprimer|
|PowerShell|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) pour récupérer un objet d’adresse IP publique et afficher ses paramètres, [Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) pour mettre à jour les paramètres, [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) pour supprimer|

## <a name="assign-a-public-ip-address"></a>Assigner une adresse IP publique

Découvrez comment attribuer une adresse IP publique aux ressources suivantes :

- Une machine virtuelle [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (lors de la création), ou une [machine virtuelle existante](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Équilibreur de charge accessible sur Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway Azure](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Connexion site à site à l’aide d’une passerelle VPN Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Groupe de machines virtuelles identiques Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Autorisations

Pour effectuer des tâches sur des adresses IP publiques, votre compte doit avoir le rôle de [contributeur de réseaux](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou avoir un rôle [personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) assigné aux actions appropriées répertoriées dans le tableau suivant :

| Action                                                             | Nom                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Lire une adresse IP publique                                          |
| Microsoft.Network/publicIPAddresses/write                          | Créer ou mettre à jour une adresse IP publique                           |
| Microsoft.Network/publicIPAddresses/delete                         | Supprimer une adresse IP publique                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Associer une adresse IP publique à une ressource                    |

## <a name="next-steps"></a>Étapes suivantes

- Créez une adresse IP publique avec les exemples de scripts [PowerShell](powershell-samples.md) ou [Azure CLI](cli-samples.md), ou à l’aide des [modèles Azure Resource Manager](template-samples.md)
- Créez et appliquez une [stratégie Azure](policy-samples.md) pour des adresses IP publiques