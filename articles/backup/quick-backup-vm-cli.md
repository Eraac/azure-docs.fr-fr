---
title: 'Démarrage rapide d’Azure : Sauvegarder une machine virtuelle avec Azure CLI'
description: Découvrez comment sauvegarder vos machines virtuelles avec Azure CLI
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 1d431cceee80175710f339e4734972340ed3469d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467249"
---
# <a name="back-up-a-virtual-machine-in-azure-with-the-cli"></a>Sauvegarder une machine virtuelle dans Azure avec l’interface de ligne de commande
L’interface de ligne de commande (CLI) Azure permet de créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Vous pouvez protéger vos données en effectuant des sauvegardes à intervalles réguliers. La sauvegarde Azure crée des points de récupération pouvant être stockés dans des coffres de récupération géo-redondants. Cet article explique comment sauvegarder une machine virtuelle (VM) dans Azure avec Azure CLI. Vous pouvez également effectuer ces étapes avec [Azure PowerShell](quick-backup-vm-powershell.md) ou dans le [portail Azure](quick-backup-vm-portal.md).

Ce démarrage rapide permet la sauvegarde sur une machine virtuelle Azure existante. Si vous devez créer une machine virtuelle, vous pouvez [créer une machine virtuelle avec Azure CLI](../virtual-machines/linux/quick-create-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pour installer et utiliser l’interface CLI en local, vous devez exécuter Azure CLI version 2.0.18 ou une version ultérieure. Pour trouver la version de CLI, exécutez `az --version`. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli). 


## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services
Un coffre Recovery Services est un conteneur logique qui stocke les données de sauvegarde de chaque ressource protégée, telles que des machines virtuelles Azure. Lorsque le travail de sauvegarde d’une ressource protégée s’exécute, il crée un point de récupération à l’intérieur du coffre Recovery Services. Vous pouvez ensuite utiliser un de ces points de récupération pour restaurer des données à un moment donné dans le temps.

Créez un coffre Recovery Services avec [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az-backup-vault-create). Spécifiez le même groupe de ressources et le même emplacement que ceux de la machine virtuelle que vous souhaitez protéger. Si vous avez utilisé la fonction de [démarrage rapide de machine virtuelle](../virtual-machines/linux/quick-create-cli.md), alors vous avez créé les éléments suivants :

- un groupe de ressources appelé *myResourceGroup* ;
- une machine virtuelle nommée *myVM* ;
- les ressources situées à l’emplacement *eastus*.

```azurecli-interactive 
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

Par défaut, le coffre Recovery Services est défini pour le stockage géoredondant. Le stockage géoredondant (GRS) s’assure que les données de sauvegarde sont répliquées dans une région Azure secondaire située à des centaines de kilomètres de la région primaire. Si vous devez modifier le paramètre de redondance de stockage, utilisez l’applet de commande [az backup vault backup-properties set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set).

```azurecli
az backup vault backup-properties set \
    --name myRecoveryServicesVault  \
    --resource-group myResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant" 
```


## <a name="enable-backup-for-an-azure-vm"></a>Activer la sauvegarde pour une machine virtuelle Azure
Créez une stratégie de protection pour définir à quel moment exécuter un travail de sauvegarde, ainsi que la durée de stockage des points de récupération. La stratégie de protection par défaut exécute un travail de sauvegarde chaque jour, et conserve les points de récupération pendant 30 jours. Vous pouvez utiliser ces valeurs de stratégie par défaut pour protéger rapidement votre machine virtuelle. Pour activer la protection des sauvegardes pour une machine virtuelle, utilisez la commande [az backup protection enable-for-vm](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-enable-for-vm). Spécifiez le groupe de ressources et la machine virtuelle à protéger, puis la stratégie à utiliser :

```azurecli-interactive 
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

> [!NOTE]
> Si la machine virtuelle n’est pas dans le même groupe de ressources que celui du coffre, alors myResourceGroup fait référence au groupe de ressources dans lequel le coffre a été créé. Au lieu du nom de la machine virtuelle, indiquez l’ID de la machine virtuelle comme indiqué ci-dessous.

```azurecli-interactive 
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm $(az vm show -g VMResourceGroup -n MyVm --query id | tr -d '"') \
    --policy-name DefaultPolicy
```

## <a name="start-a-backup-job"></a>Démarrer un travail de sauvegarde
Pour démarrer une sauvegarde maintenant sans attendre que la stratégie par défaut exécute le travail à l’heure planifiée, utilisez [az backup protection backup-now](https://docs.microsoft.com/cli/azure/backup/protection#az-backup-protection-backup-now). Ce premier travail de sauvegarde crée un point de récupération complet. Chaque travail de sauvegarde après cette sauvegarde initiale crée des points de récupération incrémentielle. Les points de récupération incrémentielle constituent un mode de stockage rapide et efficace, car ils transfèrent uniquement les modifications apportées depuis la dernière sauvegarde.

Les paramètres suivants sont utilisés pour sauvegarder la machine virtuelle :

- `--container-name` correspond au nom de votre machine virtuelle
- `--item-name` correspond au nom de votre machine virtuelle
- La valeur `--retain-until` doit être définie sur la dernière date disponible à laquelle vous souhaitez que le point de récupération soit disponible, dans le format d’heure UTC (**jj-mm-aaaa**).

L’exemple suivant sauvegarde la machine virtuelle nommée *myVM* et définit l’expiration du point de récupération au 18 octobre 2017 :

```azurecli-interactive 
az backup protection backup-now \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --retain-until 18-10-2017
```


## <a name="monitor-the-backup-job"></a>Surveiller le travail de sauvegarde
Pour surveiller l’état des travaux de sauvegarde, utilisez [az backup job list](https://docs.microsoft.com/cli/azure/backup/job#az-backup-job-list) :

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

La sortie est similaire à l’exemple suivant, qui montre que le travail de sauvegarde est *En cours* :

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Lorsque l’*état* des rapports des travaux de sauvegarde correspond à *Terminé*, votre machine virtuelle est protégée par Recovery Services et présente un point de récupération complet stocké.


## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Lorsqu’elle n’est plus nécessaire, vous pouvez désactiver la protection sur la machine virtuelle, supprimer les points de restauration et le coffre Recovery Services, puis supprimer le groupe de ressources et les ressources de machine virtuelle associées. Si vous avez utilisé une machine virtuelle existante, vous pouvez ignorer la commande finale [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete) pour laisser en place le groupe de ressources et la machine virtuelle.

Si vous souhaitez consulter un didacticiel de sauvegarde qui explique comment restaurer des données de votre machine virtuelle, accédez à la section [Étapes suivantes](#next-steps). 

```azurecli-interactive 
az backup protection disable \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --delete-backup-data true
az backup vault delete \
    --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez créé un coffre Recovery Services, activé la protection sur une machine virtuelle et créé le point de récupération initial. Pour en savoir plus sur la sauvegarde Azure et Recovery Services, passez aux didacticiels.

> [!div class="nextstepaction"]
> [Sauvegarder plusieurs machines virtuelles Azure](./tutorial-backup-vm-at-scale.md)
