---
title: 'Lier un réseau virtuel à un circuit ExpressRoute : PowerShell : classique : Azure | Microsoft Docs'
description: Ce document explique comment lier des réseaux virtuels à des circuits ExpressRoute à l’aide du modèle de déploiement classique et de PowerShell.
services: expressroute
documentationcenter: na
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: cherylmc
ms.openlocfilehash: 21676ff329613f792d6570713f044bb7440e58d4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370626"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Connectez un réseau virtuel à un circuit ExpressRoute à l’aide de PowerShell (classique)
> [!div class="op_single_selector"]
> * [Portail Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interface de ligne de commande Azure](howto-linkvnet-cli.md)
> * [Vidéo - portail Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (classique)](expressroute-howto-linkvnet-classic.md)
>

Cet article est conçu pour vous aider à lier des réseaux virtuels à des circuits ExpressRoute d’Azure à l’aide de PowerShell. Un réseau virtuel unique peut être lié à quatre circuits ExpressRoute maximum. Utilisez les étapes décrites dans cet article afin de créer un lien pour chaque circuit ExpressRoute auquel vous vous connectez. Les circuits ExpressRoute peuvent être dans le même abonnement, dans des abonnements différents ou dans une combinaison des deux. Cet article s’applique aux réseaux virtuels créés à l’aide du modèle de déploiement classique.

Vous pouvez lier jusqu’à 10 réseaux virtuels à un circuit ExpressRoute. Tous les réseaux virtuels doivent être situés dans la même région géopolitique. Si vous avez activé le module complémentaire Premium d’ExpressRoute, vous pouvez lier un plus grand nombre de réseaux virtuels à votre circuit ExpressRoute ou des réseaux virtuels situés dans d’autres régions géopolitiques. Pour plus d’informations sur le module complémentaire Premium, consultez le [FAQ](expressroute-faqs.md).

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**À propos des modèles de déploiement Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configuration-prerequisites"></a>Prérequis de configuration

* Avant de commencer la configuration, examinez les [conditions préalables](expressroute-prerequisites.md), la [configuration requise pour le routage](expressroute-routing.md) et les [flux de travail](expressroute-workflows.md).
* Vous devez disposer d’un circuit ExpressRoute actif.
   * Suivez les instructions pour [créer un circuit ExpressRoute](expressroute-howto-circuit-classic.md) et faites-le activer par votre fournisseur de service de connectivité.
   * Vérifiez que l’homologation privée Azure est configurée pour votre circuit. Pour obtenir des instructions sur le routage, consultez l’article [Configurer le routage](expressroute-howto-routing-classic.md) .
   * Vérifiez que l’homologation privée Azure est être configurée, et que l’homologation BGP entre votre réseau et Microsoft est être opérationnelle pour pouvoir activer la connectivité de bout en bout.
   * Vous devez disposer d'un réseau virtuel et d’une passerelle de réseau virtuel créés et totalement approvisionnés. Suivez les instructions pour [configurer un réseau virtuel pour ExpressRoute](expressroute-howto-vnet-portal-classic.md).

### <a name="download-the-latest-powershell-cmdlets"></a>Télécharger les dernières applets de commande PowerShell

Installez les dernières versions des modules PowerShell Azure Service Management (SM) et du module ExpressRoute. Si vous utilisez l’exemple suivant, sachez que le numéro de version (dans cet exemple, 5.1.1) évoluera au fur et à mesure de la publication de nouvelles versions des cmdlets.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Pour plus d’informations sur Azure PowerShell, et notamment une aide détaillée pour configurer son ordinateur de façon à pouvoir utiliser les modules Azure PowerShell, voir [Bien démarrer avec les cmdlets Azure PowerShell](/powershell/azure/overview).

### <a name="sign-in"></a>Se connecter

Pour vous connecter à votre compte Azure, utilisez les exemples suivants :

1. Ouvrez la console PowerShell avec des droits élevés et connectez-vous à votre compte.

   ```powershell
   Connect-AzAccount
   ```
2. Vérifiez les abonnements associés au compte.

   ```powershell
   Get-AzSubscription
   ```
3. Si vous avez plusieurs abonnements, sélectionnez celui que vous souhaitez utiliser.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

4. Utilisez l’applet de commande suivante pour ajouter votre abonnement Azure à PowerShell pour le modèle de déploiement classique.

   ```powershell
   Add-AzureAccount
   ```

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Connecter un réseau virtuel du même abonnement à un circuit
Vous pouvez lier un réseau virtuel à un circuit ExpressRoute à l’aide de l’applet de commande suivante. Assurez-vous que la passerelle de réseau virtuel est créée et prête pour la liaison avant d’exécuter l’applet de commande.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Supprimer une liaison de réseau virtuel à un circuit
Vous pouvez supprimer une liaison de réseau virtuel à un circuit ExpressRoute avec l’applet de commande suivante. Assurez-vous que l’abonnement actuel est bien sélectionné pour le réseau virtuel donné. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Connecter un réseau virtuel d’un autre abonnement à un circuit
Vous pouvez partager un circuit ExpressRoute entre plusieurs abonnements. La figure suivante montre un schéma simple sur le fonctionnement du partage de circuits ExpressRoute entre plusieurs abonnements.

Chacun des petits clouds dans le cloud principal est utilisé pour représenter les abonnements appartenant à différents services au sein d’une organisation. Chacun des services au sein de l’organisation peut utiliser son propre abonnement pour déployer ses services, mais ils peuvent partager un même circuit ExpressRoute pour se connecter à votre réseau local. Un seul service (dans cet exemple : le service informatique) peut détenir le circuit ExpressRoute. D’autres abonnements au sein de l’organisation peuvent utiliser le circuit ExpressRoute.

> [!NOTE]
> Les frais de connectivité et de bande passante pour le circuit dédié s’appliquent au propriétaire du circuit ExpressRoute. Tous les réseaux virtuels partagent la même bande passante.
> 
> 

![Connectivité entre abonnements](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administration
Le *propriétaire du circuit* est l’administrateur/coadministrateur de l’abonnement dans lequel le circuit ExpressRoute est créé. Le propriétaire du circuit peut autoriser les administrateurs/coadministrateurs d’autres abonnements (appelés *utilisateurs du circuit*) à utiliser le circuit dédié dont ils sont propriétaires. Une fois autorisés, les utilisateurs du circuit peuvent lier le réseau virtuel dans leur abonnement au circuit ExpressRoute.

Le propriétaire du circuit a le pouvoir de modifier et de révoquer les autorisations à tout moment. La révocation d’une autorisation entraîne la suppression de tous les liens de l’abonnement dont l’accès a été révoqué.

### <a name="circuit-owner-operations"></a>Opérations du propriétaire du circuit

**Création d’une autorisation**

Le propriétaire du circuit autorise les administrateurs d’autres abonnements à utiliser le circuit spécifié. Dans l’exemple ci-dessous, l’administrateur du circuit (Service informatique de Contoso) permet à l’administrateur d’un autre abonnement (Dev-Test) de lier jusqu’à deux réseaux virtuels au circuit. Le service informatique de Contoso le permet en spécifiant l’ID Microsoft de Dev-Test. L'applet de commande n'envoie pas de courrier électronique à l’ID Microsoft spécifié. Le propriétaire du circuit doit notifier explicitement au propriétaire de l’autre abonnement que l’autorisation a pris fin.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Renvoie :

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Vérification des autorisations**

Le propriétaire du circuit peut vérifier toutes les autorisations émises sur un circuit donné en exécutant l’applet de commande suivante :

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Renvoie :

  ```powershell
  Description         : EngineeringTeam
  Limit               : 3
  LinkAuthorizationId : ####################################
  MicrosoftIds        : engadmin@contoso.com
  Used                : 1

  Description         : MarketingTeam
  Limit               : 1
  LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
  MicrosoftIds        : marketingadmin@contoso.com
  Used                : 0

  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : salesadmin@contoso.com
  Used                : 2
  ```

**Mise à jour des autorisations**

Le propriétaire du circuit peut modifier les autorisations à l’aide de l’applet de commande suivante :

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  Renvoie :

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Suppression des autorisations**

Le propriétaire du circuit peut révoquer/supprimer les autorisations accordées à l’utilisateur en exécutant l’applet de commande suivante :

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Opérations de l’utilisateur du circuit

**Vérification des autorisations**

L’utilisateur du circuit peut vérifier les autorisations à l’aide de l’applet de commande suivante :

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Renvoie :

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : ContosoIT
  Location                         : Washington DC
  MaximumAllowedLinks              : 2
  ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  UsedLinks                        : 0
  ```

**Échange des autorisations de lien**

L’utilisateur du circuit peut exécuter l’applet de commande suivante pour échanger une autorisation de lien :

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Renvoie :

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Exécutez cette commande dans l’abonnement qui vient d’être lié pour le réseau virtuel :

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur ExpressRoute, consultez la [FAQ sur ExpressRoute](expressroute-faqs.md).
