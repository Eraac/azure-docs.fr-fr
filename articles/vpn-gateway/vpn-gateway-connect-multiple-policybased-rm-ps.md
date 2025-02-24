---
title: 'Connecter des passerelles VPN à plusieurs périphériques VPN locaux basés sur des stratégies : Azure Resource Manager : PowerShell | Microsoft Docs'
description: Configurez une passerelle VPN Azure basée sur le routage sur plusieurs périphériques VPN basés sur des stratégies à l’aide d’Azure Resource Manager et de PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: yushwang
ms.openlocfilehash: 9085d5ee21b1e955b7d9416a379ee730ba26ad3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150083"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Connecter des passerelles VPN à plusieurs périphériques VPN basés sur des stratégies via PowerShell

Cet article vous aidera à configurer une passerelle VPN Azure basée sur le routage pour connecter plusieurs périphériques VPN basés sur des stratégies locales tirant parti des stratégies IPsec/IKE personnalisées sur les connexions VPN S2S.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about"></a>À propos des passerelles VPN basées sur le routage et les stratégies

Les périphériques VPN basés sur des stratégies *et* ceux basés sur le routage diffèrent au niveau des sélecteurs de trafic IPsec et de la façon dont ils sont définis sur une connexion :

* Les périphériques VPN **basés sur des stratégies** utilisent des combinaisons de préfixes provenant des deux réseaux pour définir la façon dont le trafic est chiffré/déchiffré via les tunnels IPsec. En règle générale, ce modèle est construit sur des pare-feu qui se chargent du filtrage des paquets. Les fonctions de chiffrement et de déchiffrement de tunnel IPsec sont ajoutées au filtrage des paquets et au moteur de traitement.
* Les périphériques VPN **basés sur le routage** utilisent des sélecteurs de trafic universels (caractère générique) et laissent les tables de routage/transfert diriger le trafic vers les différents tunnels IPsec. En règle générale, ce modèle est construit sur des plateformes de routeur où chaque tunnel IPsec est modélisé en tant qu’interface réseau ou VTI (interface virtuelle de tunnel).

Les diagrammes suivants illustrent les deux modèles :

### <a name="policy-based-vpn-example"></a>Exemple de VPN basé sur des stratégies
![basé sur des stratégies](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Exemple de VPN basé sur le routage
![basé sur le routage](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Prise en charge par Azure des VPN basés sur des stratégies
Actuellement, Azure prend en charge les deux modes de passerelles VPN : les passerelles VPN basées sur le routage et les passerelles VPN basées sur des stratégies. Les deux modes sont construits sur différentes plateformes internes aux caractéristiques différentes :

|                          | **Passerelle VPN basée sur des stratégies** | **Passerelle VPN basée sur le routage**               |
| ---                      | ---                         | ---                                      |
| **Référence SKU de passerelle Azure**    | De base                       | Basic, Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3 |
| **Version IKE**          | IKEv1                       | IKEv2                                    |
| **IOPS Connexions S2S** | **1**                       | Basic/Standard : 10<br> HighPerformance : 30 |
|                          |                             |                                          |

Avec une stratégie IPsec/IKE personnalisée, vous pouvez désormais configurer les passerelles VPN Azure basées sur le routage pour utiliser des sélecteurs de trafic basés sur les préfixes avec l’option «**PolicyBasedTrafficSelectors**», afin de vous connecter à des périphériques VPN basés sur des stratégies locales. Cette fonctionnalité vous permet de vous connecter à partir d’un réseau virtuel Azure et d’une passerelle VPN à plusieurs périphériques VPN/pare-feu basés sur des stratégies locales, en supprimant ainsi la limite d’une connexion unique à partir des passerelles VPN Azure basées sur des stratégies locales.

> [!IMPORTANT]
> 1. Pour activer cette connectivité, vos périphériques VPN basés sur des stratégies locales doivent prendre en charge **IKEv2** pour la connexion aux passerelles VPN Azure basées sur le routage. Vérifiez les caractéristiques de votre périphérique VPN.
> 2. Les réseaux locaux qui utilisent ce mécanisme pour se connecter via des périphériques VPN basés sur des stratégies ne peuvent se connecter qu’au réseau virtuel Azure ; c’est-à-dire qu’**ils ne peuvent pas transiter vers d’autres réseaux locaux ou virtuels via la même passerelle VPN Azure**.
> 3. L’option de configuration fait partie de la stratégie de connexion personnalisée IPsec/IKE. Vous devez spécifier la stratégie complète (algorithmes de chiffrement et d’intégrité IPsec/IKE, puissance des clés et durée de vie des associations de sécurité) si vous activez l’option Sélecteur de trafic basé sur des stratégies.

Le diagramme suivant montre pourquoi le routage de transit via la passerelle VPN Azure ne fonctionne pas avec l’option basée sur des stratégies :

![transit basé sur des stratégies](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Comme illustré dans le diagramme, les sélecteurs de trafic de la passerelle VPN Azure vont du réseau virtuel vers chaque préfixe de réseau local, mais les connexions croisées ne sont pas possibles. Par exemple, les sites locaux 2, 3 et 4 peuvent tous communiquer avec VNet1, mais ils ne peuvent pas se connecter entre eux via la passerelle VPN Azure. Le diagramme montre les sélecteurs de trafic croisés qui ne sont pas disponibles dans cette configuration de la passerelle VPN Azure.

## <a name="configurepolicybased"></a>Configurer des sélecteurs de trafic basés sur des stratégies sur une connexion

Les instructions fournies dans cet article suivent le même exemple que l’article [Configurer la stratégie IPsec/IKE pour des connexions VPN S2S ou de réseau virtuel à réseau virtuel](vpn-gateway-ipsecikepolicy-rm-powershell.md) pour établir une connexion VPN S2S. Cette situation est présentée dans le diagramme suivant :

![stratégie S2S](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

Le flux de travail pour activer cette connectivité est le suivant :
1. Créez le réseau virtuel, la passerelle VPN et la passerelle de réseau local pour votre connexion entre sites.
2. Créez une stratégie IPsec/IKE.
3. Appliquez la stratégie lorsque vous créez une connexion S2S ou entre deux réseaux virtuels, et **activez les sélecteurs de trafic basés sur des stratégies** sur la connexion
4. Si la connexion est déjà créée, vous pouvez appliquer ou mettre à jour la stratégie sur une connexion existante

## <a name="before-you-begin"></a>Avant de commencer

Assurez-vous de disposer d’un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enablepolicybased"></a>Activer des sélecteurs de trafic basés sur des stratégies sur une connexion

Assurez-vous d’avoir terminé la [partie 3 de l’article sur la configuration d’une stratégie IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) avant d’aborder cette section. L’exemple suivant utilise les mêmes paramètres et étapes :

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Étape 1 : création du réseau virtuel, de la passerelle VPN et de la passerelle de réseau local

#### <a name="1-connect-to-your-subscription-and-declare-your-variables"></a>1. Vous connecter à votre abonnement et déclarer vos variables

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

Déclarez vos variables. Dans cet exercice, nous utilisons les variables suivantes :

```azurepowershell-interactive
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. Créer le réseau virtuel, la passerelle VPN et la passerelle de réseau local

Créez un groupe de ressources.

```azurepowershell-interactive
New-AzResourceGroup -Name $RG1 -Location $Location1
```

Utilisez l’exemple suivant pour créer le réseau virtuel TestVNet1 avec trois sous-réseaux et la passerelle VPN. Si vous souhaitez substituer des valeurs, pensez à toujours nommer votre sous-réseau de passerelle « GatewaySubnet ». Si vous le nommez autrement, la création de votre passerelle échoue.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Étape 2 : création d’une connexion VPN S2S avec une stratégie IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Créez une stratégie IPsec/IKE.

> [!IMPORTANT]
> Vous devez créer une stratégie IPsec/IKE afin d’activer l’option « UsePolicyBasedTrafficSelectors » sur la connexion.

L’exemple de script suivant crée une stratégie IPsec/IKE avec les paramètres et les algorithmes suivants :
* IKEv2 : AES256, SHA384, DHGroup24
* IPsec : AES256, SHA256, PFS None, SA Lifetime 14400 seconds & 102400000KB

```azurepowershell-interactive
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Créez la connexion VPN S2S avec les sélecteurs de trafic basés sur des stratégies et la stratégie IPsec/IKE.
Créez une connexion VPN S2S et appliquez la stratégie IPsec/IKE créée dans l’étape précédente. Prenez connaissance de l’utilisation du paramètre supplémentaire « -UsePolicyBasedTrafficSelectors $True ». Il permet d’activer les sélecteurs de trafic basés sur des stratégies sur la connexion.

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Une fois que vous aurez terminé ces étapes, la connexion VPN S2S utilisera la stratégie IPsec/IKE définie et activera sur la connexion les sélecteurs de trafic basés sur des stratégies. Vous pouvez répéter les mêmes étapes pour ajouter davantage de connexions à des périphériques VPN supplémentaires basés sur des stratégies locales à partir de la même passerelle VPN Azure.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Mettre à jour des sélecteurs de trafic basés sur des stratégies pour une connexion
La dernière section indique comment mettre à jour l’option des sélecteurs de trafic basés sur des stratégies pour une connexion VPN S2S existante.

### <a name="1-get-the-connection"></a>1. Obtenez la connexion.
Commencez par obtenir la ressource de connexion.

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Vérifiez l’option des sélecteurs de trafic basés sur des stratégies.
La ligne suivante indique si les sélecteurs de trafic basés sur des stratégies sont utilisés pour la connexion :

```azurepowershell-interactive
$connection6.UsePolicyBasedTrafficSelectors
```

Si la ligne renvoie « **True** », alors les sélecteurs de trafic basés sur des stratégies sont configurés sur la connexion ; sinon, elle indique « **False** ».

### <a name="3-enabledisable-the-policy-based-traffic-selectors-on-a-connection"></a>3. Activer/désactiver les sélecteurs de trafic basés sur des stratégies sur une connexion
Une fois que vous avez obtenu la ressource de connexion, vous pouvez activer ou désactiver l’option.

#### <a name="to-enable-usepolicybasedtrafficselectors"></a>Pour activer UsePolicyBasedTrafficSelectors
Dans l’exemple suivant, l’option des sélecteurs du trafic basés sur des stratégies est désactivée, mais ne modifie pas la stratégie IPsec/IKE :

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

#### <a name="to-disable-usepolicybasedtrafficselectors"></a>Pour désactiver UsePolicyBasedTrafficSelectors
Dans l’exemple suivant, l’option des sélecteurs du trafic basés sur des stratégies est désactivée, mais ne modifie pas la stratégie IPsec/IKE :

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

## <a name="next-steps"></a>Étapes suivantes
Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Consultez [Création d’une machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour connaître les différentes étapes.

Pour en savoir plus sur les stratégies IPsec/IKE personnalisées, consultez [Configure IPsec/IKE policy for S2S VPN or VNet-to-VNet connections](vpn-gateway-ipsecikepolicy-rm-powershell.md) (Configuration d’une stratégie IPsec/IKE pour les connexions VPN S2S ou entre deux réseaux virtuels).
