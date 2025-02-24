---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 44ee258567ca357687feb24337f2d5974e2532b0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177677"
---
## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec la commande [az group create](/cli/azure/group). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Créez un compte de stockage à usage général avec la commande [az storage account create](/cli/azure/storage/account). Le compte de stockage à usage général peut être utilisé pour les quatre services : objets blob, fichiers, tables et files d’attente. 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Spécifier les informations d’identification de compte de stockage

L’interface de ligne de commande Azure a besoin de vos informations d’identification de compte de stockage pour la plupart des commandes de ce didacticiel. Bien que plusieurs options s’offrent à vous, l’une des méthodes les plus simples pour fournir ces informations d’identification consiste à définir les variables d’environnement `AZURE_STORAGE_ACCOUNT` et `AZURE_STORAGE_KEY`.

Tout d’abord, affichez les clés de votre compte de stockage à l’aide de la commande [az storage account keys list](/cli/azure/storage/account/keys) :

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

Ensuite, définissez les variables d’environnement `AZURE_STORAGE_ACCOUNT` et `AZURE_STORAGE_KEY`. Pour cela, vous pouvez utiliser l’interpréteur de commandes Bash à l’aide de la commande `export` :

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_KEY="myStorageAccountKey"
```
