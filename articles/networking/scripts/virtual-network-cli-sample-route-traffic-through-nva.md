---
title: Exemple de script Azure CLI - Acheminer le trafic via une appliance virtuelle réseau | Microsoft Docs
description: Exemple de script Azure CLI - Acheminer le trafic via une appliance virtuelle réseau de pare-feu.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: timlt
ms.openlocfilehash: f2ba4d919cc17d1c454a39490377477e99aa17e2
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875511"
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Acheminer le trafic via une appliance virtuelle réseau

Cet exemple de script permet de créer un réseau virtuel avec des sous-réseaux frontaux et principaux. Il crée également une machine virtuelle sur laquelle le transfert IP est activé pour acheminer le trafic entre les deux sous-réseaux. Après avoir exécuté le script, vous pouvez déployer un logiciel réseau, telle qu’une application de pare-feu, sur la machine virtuelle.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exemple de script


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, un réseau virtuel et les groupes de sécurité réseau. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az network vnet create](/cli/azure/network/vnet) | Crée un réseau virtuel et un sous-réseau frontal Azure. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Crée des sous-réseaux principaux et DMZ. |
| [az network public-ip create](/cli/azure/network/public-ip) | Crée une adresse IP publique pour accéder à la machine virtuelle à partir d’Internet. |
| [az network nic create](/cli/azure/network/nic) | Crée une interface réseau virtuelle et active le transfert IP pour celle-ci. |
| [az network nsg create](/cli/azure/network/nsg) | Crée un groupe de sécurité réseau (NSG). |
| [az network nsg rule create](/cli/azure/network/nsg/rule) | Crée des règles NSG qui autorisent des ports HTTP et HTTPS entrants sur la machine virtuelle. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet)| Associe les NSG et les tables de routage aux sous-réseaux. |
| [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create)| Crée une table de routage pour tous les itinéraires. |
| [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create)| Créer des itinéraires pour acheminer le trafic entre les sous-réseaux et Internet via la machine virtuelle. |
| [az vm create](/cli/azure/vm) | Crée une machine virtuelle et lui associe la carte d’interface réseau. Cette commande spécifie également l’image de machine virtuelle à utiliser ainsi que les informations d’identification d’administration. |
| [az group delete](/cli/azure/group) | Supprime un groupe de ressources, ainsi que toutes ses ressources. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous pouvez trouver des exemples supplémentaires de scripts CLI de mise en réseau dans la [documentation Vue d’ensemble de la mise en réseau Azure](../cli-samples.md).
