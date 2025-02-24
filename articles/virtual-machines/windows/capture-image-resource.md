---
title: Créer une image managée dans Azure | Microsoft Docs
description: Créer une image managée d’une machine virtuelle ou d’un disque dur virtuel généralisé(e) dans Azure. Les images peuvent être utilisées pour créer différentes machines virtuelles utilisant des disques managés.
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
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 75f1d9b945eab49fb633f2cd3f99f498e686bfab
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719347"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Créer une image managée d’une machine virtuelle généralisée dans Azure

Une ressource d’image managée peut être créée à partir d’une machine virtuelle généralisée stockée en tant que disque managé ou non managé dans un compte de stockage. L’image peut ensuite être utilisée pour créer plusieurs machines virtuelles. Pour plus d'informations sur la facturation des images managées, reportez-vous à [Tarification de la fonctionnalité Disques managés](https://azure.microsoft.com/pricing/details/managed-disks/). 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="generalize-the-windows-vm-using-sysprep"></a>Généraliser la machine virtuelle Windows à l’aide de Sysprep

Sysprep supprime toutes vos informations de compte personnel et de sécurité, puis prépare la machine en vue de son utilisation en tant qu’image. Pour plus d’informations sur Sysprep, voir [Vue d’ensemble de Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Vérifiez que les rôles serveur exécutés sur la machine sont pris en charge par Sysprep. Pour plus d’informations, voir [Prise en charge de Sysprep pour les rôles serveur](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Après que vous avez exécuté Sysprep sur une machine virtuelle, celle-ci est considérée comme *généralisée* et ne peut plus être redémarrée. Le processus de généralisation d’une machine virtuelle n’est pas réversible. Si vous devez conserver le fonctionnement de machine virtuelle d’origine, vous devez créer une [copie de la machine virtuelle](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) et généraliser la copie. 
>
> Si vous prévoyez d’exécuter Sysprep avant de charger votre disque dur virtuel sur Azure pour la première fois, vérifiez que vous avez [préparé votre machine virtuelle](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

Pour généraliser votre machine virtuelle Windows, procédez comme suit :

1. Connectez-vous à votre machine virtuelle Windows.
   
2. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par %windir%\system32\sysprep, puis exécutez `sysprep.exe`.
   
3. Dans la boîte de dialogue **Outil de préparation du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** , puis activez la case à cocher **Généraliser**.
   
4. Dans **Options d’arrêt**, sélectionnez **Arrêter**.
   
5. Sélectionnez **OK**.
   
    ![Démarrer Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. Une fois l’exécution de Sysprep terminée, la machine virtuelle est arrêtée. Ne redémarrez pas la machine virtuelle.


## <a name="create-a-managed-image-in-the-portal"></a>Créer une image managée dans le portail 

1. Ouvrez le [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche, sélectionnez **Machines virtuelles**, puis cliquez sur la machine virtuelle dans la liste.

3. Dans la page **Machine virtuelle** relative à la machine virtuelle, dans le menu supérieur, sélectionnez **Capturer**.

   La page **Créer une image** s’affiche.

4. Pour **nom**, acceptez le nom prérempli ou entrez un nom à utiliser pour l’image.

5. Pour **Groupe de ressources**, sélectionnez **Créer** et tapez un nom, ou sélectionnez **Utiliser un groupe existant** et choisissez un groupe de ressources à utiliser dans la liste déroulante.

6. Pour supprimer la machine virtuelle source une fois que l’image a été créée, sélectionnez **Supprimer automatiquement cette machine virtuelle après avoir créé l’image**.

7. Si vous souhaitez pouvoir utiliser l’image dans toute [zone de disponibilité](../../availability-zones/az-overview.md), sélectionnez **Activée** pour l’option **Résilience aux zones**.

8. Sélectionnez **Créer** pour créer l’image.

9. Une fois l’image créée, elle apparaît en tant que ressource **Image** dans la liste des ressources du groupe de ressources.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Créer une image de machine virtuelle à l’aide de Powershell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

La création d’une image directement à partir de la machine virtuelle permet de s’assurer que celle-ci comprend tous les disques associés à la machine virtuelle, y compris le disque du système d’exploitation et tous les disques de données. Cet exemple montre comment créer une image gérée à partir d’une machine virtuelle qui utilise des disques managés.

Avant de commencer, assurez-vous que vous disposez de la dernière version du module Azure PowerShell. Pour trouver la version, exécutez `Get-Module -ListAvailable Az` dans PowerShell. Si vous devez procéder à une mise à niveau, voir [Installer Azure PowerShell sur Windows avec PowerShellGet](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, exécutez `Connect-AzAccount` pour créer une connexion avec Azure.


> [!NOTE]
> Si vous voulez stocker votre image dans un stockage redondant interzone, vous devez la créer dans une région qui prend en charge les [zones de disponibilité](../../availability-zones/az-overview.md) et inclut le paramètre `-ZoneResilient` dans la configuration de l’image (commande `New-AzImageConfig`).

Pour créer une image de machine virtuelle, procédez comme suit :

1. Définissez des variables.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Assurez-vous que la machine virtuelle a été libérée.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Définissez l’état de la machine virtuelle sur **Généralisé**. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Accédez à la machine virtuelle. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Créez la configuration de l’image.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Créez l’image.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Créer une image à partir d’un disque géré à l’aide de PowerShell

Si vous souhaitez créer une image uniquement du disque du système d’exploitation, spécifiez l’ID de disque managé en tant que le disque du système d’exploitation :

    
1. Définissez des variables. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Obtenez la machine virtuelle.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Obtenez l’ID du disque géré.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Créez la configuration de l’image.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Créez l’image.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Créer une image à partir d’une capture instantanée à l’aide de Powershell

Vous pouvez créer une image gérée à partir d’une capture instantanée d’une machine virtuelle généralisée en procédant comme suit :

    
1. Définissez des variables. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Accédez à la capture instantanée.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Créez la configuration de l’image.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Créez l’image.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vhd-in-a-storage-account"></a>Créer une image à partir d’un disque dur virtuel dans un compte de stockage

Créez une image gérée à partir d’un disque dur virtuel de système d’exploitation généralisé dans un compte de stockage. Vous devez utiliser l’URI du disque dur virtuel dans le compte de stockage, dont le format est le suivant : https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd*. Dans cet exemple, le disque dur virtuel se trouve dans *mystorageaccount*, dans un conteneur nommé *vhdcontainer*, et le nom de fichier du disque dur virtuel est *vhdfilename.vhd*.


1.  Définissez des variables.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Arrêtez/libérez la machine virtuelle.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Indiquez que la machine virtuelle est généralisée.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Créez l’image en utilisant votre disque dur virtuel généralisé de système d’exploitation.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Étapes suivantes
- [Créer une machine virtuelle à partir d’une image gérée](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    

