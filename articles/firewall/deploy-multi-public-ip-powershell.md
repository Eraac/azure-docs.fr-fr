---
title: Déployer un pare-feu Azure avec plusieurs adresses IP publiques à l’aide d’Azure PowerShell
description: Dans cet article, vous allez apprendre à déployer un pare-feu Azure avec plusieurs adresses IP publiques à l’aide d’Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: ce47612f18ee64caa3a053001deb5448f7c27bfd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703982"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Déployer un pare-feu Azure avec plusieurs adresses IP publiques à l’aide d’Azure PowerShell

> [!IMPORTANT]
> Le Pare-feu Azure avec plusieurs adresses IP publiques est disponible via Azure PowerShell, Azure CLI, REST et les modèles. L’interface utilisateur du portail est ajoutée progressivement aux régions et sera disponible dans toutes les régions après le lancement.

Vous pouvez déployer un pare-feu Azure avec jusqu’à 100 adresses IP publiques.

Cette fonctionnalité donne accès aux scénarios suivants :

- **DNAT** -Vous pouvez traduire plusieurs instances de ports standard vers vos serveurs principaux. Par exemple, si vous avez deux adresses IP publiques, vous pouvez traduire le port TCP 3389 (RDP) pour ces deux adresses IP.
- **SNAT** - Des ports supplémentaires sont disponibles pour les connexions SNAT sortantes, réduisant ainsi le risque de pénurie de ports SNAT. À ce stade, Pare-feu Azure sélectionne aléatoirement l’adresse IP publique source à utiliser pour une connexion. Si votre réseau est doté d’un filtrage en aval, vous devez autoriser toutes les adresses IP publiques associées à votre pare-feu.

Les exemples de Azure PowerShell suivants montrent comment vous pouvez configurer, ajouter et supprimer des adresses IP publiques pour le Pare-feu Azure.

> [!NOTE]
> Vous ne pouvez pas supprimer la configuration IP de la page de configuration de l’adresse IP publique du Pare-feu Azure. Si vous souhaitez modifier l’adresse IP, vous pouvez utiliser Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Créer un pare-feu avec deux ou plusieurs adresses IP publiques

Cet exemple crée un pare-feu associé au réseau virtuel *vnet* avec deux adresses IP publiques.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Ajouter une adresse IP publique à un pare-feu existant

Dans cet exemple, l’adresse IP publique *azFwPublicIp1* est associée au pare-feu.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Supprimer une adresse IP publique d’un pare-feu existant

Dans cet exemple, l’adresse IP publique *azFwPublicIp1* est dissociée du pare-feu.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Superviser les journaux d’activité de Pare-feu Azure](./tutorial-diagnostics.md)
