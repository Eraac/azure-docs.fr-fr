---
title: Commandes PowerShell courantes pour les machines virtuelles Azure | Microsoft Docs
description: Commandes PowerShell courantes pour vous aider à démarrer la création et la gestion de vos machines virtuelles Windows dans Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: cb7e6dd6569cdb05b769f9f79b8dd55e234adcde
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723019"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Commandes PowerShell courantes permettant de créer et de gérer des machines virtuelles Azure

Cet article présente certaines des commandes Azure PowerShell que vous pouvez utiliser pour créer et gérer des machines virtuelles dans votre abonnement Azure.  Pour en savoir plus sur les commutateurs et options de ligne de commande spécifiques, vous pouvez utiliser la **commande** *Get-Help*.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

Ces variables peuvent être utiles si vous utilisez plusieurs des commandes de cet article :

- $location : l’emplacement de la machine virtuelle. Vous pouvez utiliser [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) pour rechercher une [région géographique](https://azure.microsoft.com/regions/) qui vous convient.
- $myResourceGroup - Le nom du groupe de ressources qui contient les machines virtuelles.
- $myVM - Le nom de la machine virtuelle.

## <a name="create-a-vm---simplified"></a>Créer une machine virtuelle simplifiée

| Tâche | Commande |
| ---- | ------- |
| Créer une machine virtuelle simple | [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -Name $myVM <BR></BR><BR></BR> New-AzVM contient un ensemble de paramètres *simplifiés*, nécessitant uniquement un nom unique. La valeur de -Name sera utilisée comme nom de toutes les ressources requises pour la création d’une nouvelle machine virtuelle. Vous pouvez en spécifier davantage, mais ce n’est pas obligatoire.|
| Créer une machine virtuelle à partir d’une image personnalisée | New-AzVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Location $location  <BR></BR><BR></BR>Vous devez avoir déjà créé votre propre [image managée](capture-image-resource.md). Vous pouvez utiliser une image pour rendre plusieurs machines virtuelles identiques. |



## <a name="create-a-vm-configuration"></a>Créer une configuration de machine virtuelle

| Tâche | Commande |
| ---- | ------- |
| Créer une configuration de machine virtuelle |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>La configuration de machine virtuelle est utilisée pour définir ou mettre à jour les paramètres d’une machine virtuelle. La configuration est initialisée avec le nom de la machine virtuelle et sa [taille](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Ajouter des paramètres de configuration |$vm = [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Les paramètres de système d’exploitation, y compris les [informations d’identification](https://technet.microsoft.com/library/hh849815.aspx), sont ajoutés à l’objet de configuration que vous avez créé précédemment à l’aide de New-AzVMConfig. |
| Ajouter une interface réseau |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Une machine virtuelle doit être associée à une [interface réseau](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour pouvoir communiquer au sein d’un réseau virtuel. Vous pouvez également utiliser la commande [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) pour récupérer un objet d’interface réseau existant. |
| Spécifier une image de plateforme |$vm = [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>[Des informations sur l’image](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) sont ajoutées à l’objet de configuration que vous avez créé, via New-AzVMConfig. L’objet renvoyé par cette commande est utilisé uniquement lorsque vous définissez le disque du système d’exploitation pour l’utilisation d’une image de plateforme. |
| Créer une machine virtuelle |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>Toutes les ressources sont créées dans un [groupe de ressources](../../azure-resource-manager/manage-resource-groups-powershell.md). Avant d’exécuter cette commande, exécutez les commandes New-AzVMConfig, Set-AzVMOperatingSystem, Set-AzVMSourceImage, Add-AzVMNetworkInterface et Set-AzVMOSDisk. |
| Mettre à jour une machine virtuelle |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Obtenez la configuration de machine virtuelle actuelle à l’aide de la commande Get-AzVM, modifiez les paramètres de configuration sur l’objet de machine virtuelle, puis exécutez cette commande. |

## <a name="get-information-about-vms"></a>Obtenir des informations sur les machines virtuelles

| Tâche | Commande |
| ---- | ------- |
| Répertorier les machines virtuelles au sein d’un abonnement |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Répertorier les machines virtuelles au sein d’un groupe de ressources |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Pour obtenir une liste de groupes de ressources dans votre abonnement, utilisez la commande [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup). |
| Obtenir des informations sur une machine virtuelle |Get-AzVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Gérer les machines virtuelles
| Tâche | Commande |
| --- | --- |
| Démarrer une machine virtuelle |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Arrêter une machine virtuelle |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Redémarrer une machine virtuelle en cours d’exécution |[Restart-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Supprimer une machine virtuelle |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>Étapes suivantes
* Consultez les étapes de base pour la création d’une machine virtuelle, décrites dans la rubrique [Création d’une machine virtuelle Windows à l’aide de Resource Manager et de PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

