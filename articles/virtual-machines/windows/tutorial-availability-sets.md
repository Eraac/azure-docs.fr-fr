---
title: 'Didacticiel : haute disponibilité pour les machines virtuelles Windows dans Azure | Microsoft Docs'
description: Avec ce didacticiel, vous allez apprendre à utiliser Azure PowerShell pour déployer des machines virtuelles hautement disponibles dans les groupes à haute disponibilité
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d9e3fc52b9cdf5126cb8d58778a59b8e8691f80a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708149"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>Didacticiel : Créer et déployer des machines virtuelles hautement disponibles avec Azure PowerShell

Dans ce tutoriel, vous allez apprendre à augmenter la disponibilité et la fiabilité de vos machines virtuelles à l’aide de groupes à haute disponibilité. Les groupes à haute disponibilité garantissent que les machines virtuelles que vous déployez sur Azure sont distribuées, sur plusieurs nœuds matériels isolés, dans un cluster. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un groupe à haute disponibilité
> * Créer une machine virtuelle dans un groupe à haute disponibilité
> * Vérifier les tailles de machines virtuelles disponibles
> * Vérifier Azure Advisor


## <a name="availability-set-overview"></a>Vue d’ensemble des groupes à haute disponibilité

Un groupe à haute disponibilité est une fonctionnalité de regroupement logique qui permet d’isoler les ressources de machine virtuelle les unes des autres quand elles sont déployées. Azure veille à ce que les machines virtuelles que vous placez dans un groupe à haute disponibilité s’exécutent sur plusieurs serveurs physiques, racks de calcul, unités de stockage et commutateurs réseau. Si une défaillance matérielle ou logicielle se produit, seul un sous-ensemble de vos machines virtuelles est affecté et votre solution globale reste opérationnelle. Les groupes à haute disponibilité sont indispensables pour créer des solutions cloud fiables.

Prenons l’exemple d’une solution basée sur une machine virtuelle classique pour laquelle vous disposez de 4 serveurs web frontaux et de 2 machines virtuelles principales. Avec Azure, vous pouvez définir deux groupes à haute disponibilité avant de déployer vos machines virtuelles : un pour la couche web et un pour la couche arrière. Quand vous créez une machine virtuelle, vous spécifiez le groupe à haute disponibilité en tant que paramètre. Azure permet de s’assurer que les machines virtuelles sont isolées sur plusieurs ressources matérielles physiques. Si le matériel physique sur lequel s’exécute un de vos serveurs rencontre un problème, vous savez que les autres instances de vos serveurs continuent de s’exécuter car elles se trouvent sur un matériel différent.

Utilisez des groupes à haute disponibilité quand vous souhaitez déployer des solutions fiables basées sur des machines virtuelles dans Azure.

## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. 

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur Entrée pour les exécuter.

## <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité

Le matériel situé à un emplacement est divisé en plusieurs domaines de mise à jour et d’erreur. Un **domaine de mise à jour** est un groupe de machines virtuelles et d’équipements physiques sous-jacents pouvant être redémarrés en même temps. Les machines virtuelles d’un même **domaine d’erreur** partagent un espace de stockage commun ainsi qu’une source d’alimentation et un commutateur réseau.  

Vous pouvez créer un groupe à haute disponibilité avec la commande [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset). Dans cet exemple, le nombre de domaines de mise à jour et d’erreur s’élève à *2* et le groupe à haute disponibilité est nommé *myAvailabilitySet*.

Créez un groupe de ressources.

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

Créez un groupe à haute disponibilité managé à l’aide de [New-AzAvailabilitySet](https://docs.microsoft.com/powershell/module/az.compute/new-azavailabilityset) avec le paramètre `-sku aligned`.

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Créer des machines virtuelles dans un groupe à haute disponibilité
Vous devez créer des machines virtuelles au sein du groupe à haute disponibilité pour vous assurer qu’elles sont correctement réparties dans le matériel. Vous ne pouvez pas ajouter une machine virtuelle existante à un groupe à haute disponibilité après sa création. 


Quand vous créez une machine virtuelle à l’aide de [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm), vous utilisez le paramètre `-AvailabilitySetName` pour spécifier le nom du groupe à haute disponibilité.

Tout d’abord, définissez un nom d’utilisateur administrateur et un mot de passe pour la machine virtuelle avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell-interactive
$cred = Get-Credential
```

Créez maintenant deux machines virtuelles avec [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) dans le groupe à haute disponibilité.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

La création et la configuration des deux machines virtuelles prennent quelques minutes. Quand vous avez terminé, vous disposez de deux machines virtuelles réparties sur le matériel sous-jacent. 

Si vous examinez le groupe à haute disponibilité dans le portail en accédant à **Groupes de ressources** > **myResourceGroupAvailability** > **myAvailabilitySet**, vous devez voir la façon dont les machines virtuelles sont réparties entre les deux domaines d’erreur et de mise à jour.

![Groupe à haute disponibilité dans le portail](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Vérifier les tailles de machines virtuelles disponibles 

Vous pouvez ajouter ultérieurement d’autres machines virtuelles au groupe à haute disponibilité, mais vous devez connaître les tailles des machines virtuelles qui sont disponibles sur le matériel. Utilisez [Get-AzVMSize](https://docs.microsoft.com/powershell/module/az.compute/get-azvmsize) pour lister toutes les tailles disponibles sur le cluster matériel pour le groupe à haute disponibilité.

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Vérifier Azure Advisor 

Vous pouvez également utiliser Azure Advisor pour obtenir plus d’informations sur les façons d’améliorer la disponibilité de vos machines virtuelles. Azure Advisor analyse votre configuration et vos données de télémétrie d’utilisation, puis recommande des solutions qui peuvent vous aider à améliorer la rentabilité, les performances, la disponibilité et la sécurité de vos ressources Azure.

Connectez-vous au [portail Azure](https://portal.azure.com), sélectionnez **Tous les services** et saisissez **Advisor**. Le tableau de bord Advisor propose des recommandations personnalisées pour l’abonnement sélectionné. Pour plus d’informations, consultez [Bien démarrer avec Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer un groupe à haute disponibilité
> * Créer une machine virtuelle dans un groupe à haute disponibilité
> * Vérifier les tailles de machines virtuelles disponibles
> * Vérifier Azure Advisor

Passez au didacticiel suivant pour en savoir plus sur les groupes de machines virtuelles identiques.

> [!div class="nextstepaction"]
> [Créer un groupe de machines virtuelles identiques](tutorial-create-vmss.md)


