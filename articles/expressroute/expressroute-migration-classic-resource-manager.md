---
title: 'Migrer les réseaux virtuels du déploiement classique vers Resource Manager - ExpressRoute : Azure : PowerShell | Microsoft Docs'
description: Cette page explique comment migrer des réseaux virtuels associés à ExpressRoute vers Resource Manager après avoir déplacé votre circuit.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 9f15431015e847d882e51b8d88c5b8a8c121d0ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67115422"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrer des réseaux virtuels associés à ExpressRoute d’un déploiement classique vers Resource Manager

Cet article explique comment migrer des réseaux virtuels associés à ExpressRoute du modèle de déploiement classique au modèle de déploiement Azure Resource Manager après avoir déplacé votre circuit ExpressRoute. 

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Vérifiez que vous disposez des dernières versions des modules Azure PowerShell. Pour plus d’informations, consultez [Installer et configurer Azure PowerShell](/powershell/azure/overview). Pour installer le module PowerShell Service Management (qui est nécessaire pour le modèle de déploiement classique), consultez [Installer le module Azure PowerShell Service Management](/powershell/azure/servicemanagement/install-azure-ps).
* Veillez à consulter les [conditions préalables](expressroute-prerequisites.md), la [configuration requise pour le routage](expressroute-routing.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
* Examinez les informations fournies sous [Transfert des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](expressroute-move.md). Vous devez avoir bien compris les limites et les limitations.
* Vérifiez que le circuit est totalement opérationnel dans le modèle de déploiement classique.
* Assurez-vous que vous disposez d’un groupe de ressources créé dans le modèle de déploiement Resource Manager.
* Passez en revue la documentation de migration de ressources suivante :

    * [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [FAQ : Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Passer en revue les erreurs les plus courantes de la migration et leur prévention](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Scénarios pris en charge et non pris en charge

* Il est possible de migrer un circuit ExpressRoute de l’environnement classique à l’environnement Resource Manager sans aucun temps d’arrêt. Vous pouvez déplacer n’importe quel circuit ExpressRoute de l’environnement classique à l’environnement Resource Manager sans temps d’arrêt. Suivez les instructions relatives au [déplacement des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager à l’aide de PowerShell](expressroute-howto-move-arm.md). C’est une condition requise pour déplacer des ressources connectées au réseau virtuel.
* Il est possible de migrer les réseaux virtuels, les passerelles et les déploiements associés au sein du réseau virtuel qui sont attachés à un circuit ExpressRoute se trouvant dans le même abonnement vers l’environnement Resource Manager sans le moindre temps d’arrêt. Vous pouvez suivre les étapes décrites plus loin pour migrer des ressources telles que des réseaux virtuels, des passerelles et des machines virtuelles déployées au sein du réseau virtuel. Vous devez vous assurer que les réseaux virtuels sont configurés correctement avant de les migrer. 
* La migration des réseaux virtuels, des passerelles et des déploiements associés au sein du réseau virtuel qui ne se trouvent pas dans le même abonnement que le circuit ExpressRoute nécessite un temps d’arrêt. La dernière section du document décrit les étapes à suivre pour migrer des ressources.
* Un réseau virtuel doté d’une passerelle ExpressRoute et d’une passerelle VPN ne peut pas être migré.
* La migration entre abonnements de circuit ExpressRoute n’est pas prise en charge. Pour plus d’informations, consultez [Services ne pouvant pas être déplacés](../azure-resource-manager/resource-group-move-resources.md#services-that-cannot-be-moved).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Déplacer un circuit ExpressRoute d’un déploiement classique vers Resource Manager
Vous devez déplacer un circuit ExpressRoute de l’environnement classique à l’environnement Resource Manager avant d’essayer de migrer des ressources attachées au circuit ExpressRoute. Pour accomplir cette tâche, consultez les articles suivants :

* Examinez les informations fournies sous [Transfert des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](expressroute-move.md).
* [Migrer un circuit d’un déploiement classique vers Resource Manager à l’aide d’Azure PowerShell](expressroute-howto-move-arm.md).
* Utilisez le portail de gestion des services Azure. Vous pouvez suivre le flux de travail pour [créer un circuit ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) et sélectionner l’option d’importation. 

Cette opération n’implique pas de temps d’arrêt. Vous pouvez continuer à transférer des données entre votre site et Microsoft pendant la migration.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrer des réseaux virtuels, des passerelles et les déploiements associés

Les étapes à suivre pour migrer varient selon que vos ressources se trouvent dans le même abonnement, dans des abonnements différents ou les deux.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrer les réseaux virtuels, les passerelles et les déploiements associés se trouvant dans le même abonnement que le circuit ExpressRoute
Cette section décrit les étapes à suivre pour migrer un réseau virtuel, une passerelle et les déploiements associés se trouvant dans le même abonnement que le circuit ExpressRoute. Cette migration n’implique aucun temps d’arrêt. Vous pouvez continuer à utiliser toutes les ressources pendant le processus de migration. Le plan de gestion est verrouillé pendant la migration. 

1. Assurez-vous que le circuit ExpressRoute a été déplacé de l’environnement classique à l’environnement Resource Manager.
2. Assurez-vous que le réseau virtuel a été préparé correctement pour la migration.
3. Inscrivez votre abonnement pour la migration de ressources. Pour inscrire votre abonnement pour la migration de ressources, utilisez l’extrait de code PowerShell suivant :

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Effectuez la validation, la préparation et la migration. Pour déplacer le réseau virtuel, utilisez l’extrait de code PowerShell suivant :

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   Vous pouvez également abandonner la migration en exécutant l’applet de commande PowerShell suivante :

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Étapes suivantes
* [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [FAQ : Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Passer en revue les erreurs les plus courantes de la migration et leur prévention](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
