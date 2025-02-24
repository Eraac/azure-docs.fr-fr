---
title: Créer une capture instantanée d’un disque dur virtuel dans Azure | Microsoft Docs
description: Découvrez comment créer une copie d’un disque dur virtuel dans Azure pour l’utiliser comme sauvegarde ou pour la résolution de problèmes.
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9f2f3ac3668f0e48716fc30fb69cd1782dbd4e56
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706970"
---
# <a name="create-a-snapshot"></a>Créer un instantané 

Prenez une capture instantanée d’un système d’exploitation ou d’un disque de données pour la sauvegarde ou pour résoudre les problèmes de la machine virtuelle. Une capture instantanée est une copie complète en lecture seule d’un disque dur virtuel. 

## <a name="use-azure-cli"></a>Utiliser l’interface de ligne de commande Microsoft Azure 

L’exemple suivant nécessite l’utilisation de [Cloud Shell](https://shell.azure.com/bash) ou l’installation d’Azure CLI.

Les étapes suivantes montrent comment effectuer une capture instantanée à l’aide de la commande **az snapshot create** avec le paramètre **--source-disk**. L’exemple suivant suppose qu’il existe une machine virtuelle nommée *myVM* dans le groupe de ressources *myResourceGroup*.

Obtenez l’ID de disque à l’aide de la commande [az vm show](/cli/azure/vm#az-vm-show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Prenez une capture instantanée nommée *osDisk-backup* à l’aide de la commande [az snapshot create](/cli/azure/snapshot#az-snapshot-create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Si vous souhaitez stocker votre capture instantanée dans le stockage résilient aux zones, vous devez la créer dans une région qui prend en charge les [zones de disponibilité](../../availability-zones/az-overview.md) et inclure le paramètre **--sku Standard_ZRS**.

Pour voir la liste des captures instantanées, utilisez la commande [az snapshot list](/cli/azure/snapshot#az-snapshot-list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Utiliser le portail Azure 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans l’angle supérieur gauche, cliquez sur **Créer une ressource** et recherchez **Capture instantanée**. Sélectionnez **Capture instantanée** dans les résultats de recherche.
3. Dans le panneau **Capture instantanée**, cliquez sur **Créer**.
4. Entrez un **nom** pour la capture instantanée.
5. Sélectionnez un groupe de ressources existant ou tapez le nom d’un nouveau groupe. 
7. Dans **Disque source**, sélectionnez le disque managé dont vous souhaitez obtenir une capture instantanée.
8. Sélectionnez le **type de compte** à utiliser pour stocker la capture instantanée. Utilisez **Standard HDD**, sauf si vous en avez besoin de la stocker sur un disque SSD hautes performances.
9. Cliquez sur **Créer**.


## <a name="next-steps"></a>Étapes suivantes

 Créez une machine virtuelle à partir d’une capture instantanée en créant un disque managé à partir de la capture instantanée, puis en attachant le nouveau disque managé comme disque de système d’exploitation. Pour plus d’informations, consultez le script [Créer une machine virtuelle à partir d’une capture instantanée](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json).

