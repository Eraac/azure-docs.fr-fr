---
title: Remplacer le disque du système d’exploitation d’une machine virtuelle Azure avec PowerShell | Microsoft Docs
description: Utilisez PowerShell pour remplacer le disque du système d’exploitation utilisé par une machine virtuelle Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: ae3979f7ceae4a854df00b39d9c2b9673f65f987
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720136"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Remplacer le disque du système d’exploitation utilisé par une machine virtuelle Azure à l’aide de PowerShell

Pour remplacer le disque d’une machine virtuelle existante par un disque de sauvegarde ou un autre disque de système d’exploitation, vous pouvez utiliser Azure PowerShell. Il est inutile de supprimer et de recréer la machine virtuelle. Vous pouvez même utiliser le disque managé d’un autre groupe de ressources s’il n’est pas déjà utilisé.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

Vous devez arrêter/libérer la machine virtuelle, puis remplacer l’ID de ressource du disque managé par celui d’un autre disque managé.

Vérifiez que la taille et le type de stockage de la machine virtuelle sont compatibles avec le disque à attacher. Ainsi, si le disque que vous souhaitez utiliser bénéficie d’un stockage Premium, la machine virtuelle doit être de taille suffisante pour prendre en charge ce niveau de stockage (comme une série DS). 

Obtenir la liste des disques d'un groupe de ressources à l'aide de [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk)

```azurepowershell-interactive
Get-AzDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Après avoir obtenu le nom du disque à utiliser, définissez-le comme disque du système d’exploitation pour la machine virtuelle. Cet exemple arrête/libère la machine virtuelle nommée *myVM* et affecte le disque nommé *newDisk* comme nouveau disque du système d’exploitation. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Étapes suivantes**

Pour créer une copie d’un disque, consultez [Effectuer la capture instantanée d’un disque](snapshot-copy-managed-disk.md).