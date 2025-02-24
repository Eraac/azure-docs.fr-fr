---
title: Créer une table d’itinéraires de hub virtuel Azure Virtual WAN pour diriger vers une appliance réseau virtuelle | Microsoft Docs
description: Table d’itinéraires de hub virtuel Azure Virtual WAN pour diriger le trafic vers une appliance réseau virtuelle.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: fc8dd6770efa1c057a56374ddc0094c2d88d2eb5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60457603"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Créer une table d’itinéraires de hub virtuel pour diriger le trafic vers une appliance réseau virtuelle

Dans cet article, découvrez comment diriger le trafic à partir d’un hub virtuel vers une appliance réseau virtuelle. 

![Diagramme WAN virtuel](./media/virtual-wan-route-table/vwanroute.png)

Dans cet article, vous apprendrez comment :

* Créer un WAN
* Créer un hub
* Créer des connexions de réseau virtuel du hub
* Créer un itinéraire de hub
* Créer une table de routage
* Appliquer la table d’itinéraires

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vérifiez que vous respectez les critères suivants :

1. Vous avez une appliance virtuelle réseau (NVA). Il s’agit d’un logiciel tiers de votre choix qui est généralement provisionné à partir de la Place de marché Azure dans un réseau virtuel.
2. Vous disposez d’une adresse IP privée assignée à l’interface de l’appliance réseau virtuelle. 
3. L’appliance réseau virtuelle ne peut pas être déployée dans le hub virtuel. Elle doit être déployée dans un réseau virtuel distinct. Dans cet article, le réseau virtuel NVA est appelé « DMZ VNet ».
4. Un ou plusieurs réseaux virtuels peuvent être connectés au réseau « DMZ VNet ». Dans cet article, ce réseau virtuel est appelé « Indirect spoke VNet ». Ces réseaux virtuels peuvent être connectés au réseau DMZ VNet à l’aide de l’homologation.
5. Vérifiez que les deux réseaux virtuels sont créés. Ils seront utilisés en tant que réseaux virtuels spokes. Dans cet article, le réseau virtuel spoke bénéficie des espaces d’adressage 10.0.2.0/24 et 10.0.3.0/24. Si vous avez besoin d’informations sur la création d’un réseau virtuel, consultez l’article [Créer un réseau virtuel à l’aide de PowerShell](../virtual-network/quick-create-powershell.md).
6. Veillez à ce qu’aucune passerelle de réseau virtuel n’existe dans les réseaux virtuels.

## <a name="signin"></a>1. Se connecter

Assurez-vous d’avoir installé la dernière version des cmdlets PowerShell de Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/install-az-ps). Ceci est important, car les versions antérieures des cmdlets ne contiennent pas les valeurs actuelles dont vous avez besoin pour cet exercice.

1. Ouvrez la console PowerShell avec des privilèges élevés, puis connectez-vous à votre compte Azure. Ce cmdlet vous invite à entrer vos informations d’identification. Une fois que vous êtes connecté, vos paramètres de compte sont téléchargés afin de les mettre à disposition d’Azure PowerShell.

   ```powershell
   Connect-AzAccount
   ```
2. Obtenez la liste de vos abonnements Azure.

   ```powershell
   Get-AzSubscription
   ```
3. Spécifiez l’abonnement à utiliser.

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="rg"></a>2. Créer des ressources

1. Créez un groupe de ressources.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Créez un WAN virtuel.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Créez un hub virtuel.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24"
   ```

## <a name="connections"></a>3. Créer des connexions

Créez des connexions de réseau virtuel du hub à partir des réseaux Indirect Spoke VNet et DMZ VNet vers le hub virtuel.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="route"></a>4. Créer un itinéraire de hub virtuel

Dans cet article, le réseau Indirect Spoke VNet bénéficie des espaces d’adressage 10.0.2.0/24 et 10.0.3.0/24 et l’adresse IP privée de l’interface du réseau DMZ VNet est 10.0.4.5.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="applyroute"></a>5. Créer une table d’itinéraires de hub virtuel

Créez une table d’itinéraires de hub virtuel, puis appliquez-y l’itinéraire créé.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="commit"></a>6. Valider les modifications

Validez les modifications pour le hub virtuel.

```powershell
Update-AzVirtualHub -VirtualWanId $virtualWan.Id -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le WAN virtuel, consultez la page [Vue d'ensemble de WAN virtuel](virtual-wan-about.md).
