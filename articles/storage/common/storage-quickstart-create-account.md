---
title: Créer un compte de stockage - Stockage Azure
description: Dans cet article sur les procédures, vous apprenez à créer un compte de stockage à l’aide du Portail Azure, d’Azure PowerShell ou de l’interface Azure CLI. Un compte de stockage Azure fournit un espace de noms unique dans Microsoft Azure pour stocker les objets de données que vous créez dans le stockage Azure et y accéder.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: article
ms.date: 05/06/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8375f4c54dc436ecf0694ec5f629c81d3591594d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65234184"
---
# <a name="create-a-storage-account"></a>Créez un compte de stockage.

Un compte de stockage Azure contient tous vos objets de données de stockage Azure : objets blob, fichiers, files d’attente, tables et disques. Le compte de stockage fournit pour vos données de stockage Azure un espace de noms unique, accessible de n’importe où dans le monde via HTTP ou HTTPS. Les données dans votre compte de stockage Azure sont durables et hautement disponibles, sécurisées et massivement évolutives.

Dans cet article sur les procédures, vous apprenez à créer un compte de stockage à l’aide du [Portail Azure](https://portal.azure.com/), d’[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), de l’[interface Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) ou d’un [modèle Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Aucune.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Cet article sur les procédures nécessite le module Az Azure PowerShell version 0.7 ou ultérieure. Exécutez `Get-Module -ListAvailable Az` pour rechercher votre version actuelle. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Vous pouvez vous connecter à Azure et exécuter des commandes Azure CLI de l’une des deux façons suivantes :

- Vous pouvez exécuter des commandes CLI à partir du Portail Azure, dans Azure Cloud Shell.
- Vous pouvez installer l’interface CLI et exécuter des commandes CLI localement.

### <a name="use-azure-cloud-shell"></a>Utiliser Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. L’interface Azure CLI est préinstallée et configurée pour être utilisée avec votre compte. Cliquez sur le bouton **Cloud Shell** du menu situé dans la section supérieure droite de la fenêtre du Portail Azure :

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Ce bouton lance un interpréteur de commandes interactif que vous pouvez utiliser pour exécuter les étapes décrites dans cet article de procédure :

[![Capture d’écran représentant la fenêtre Cloud Shell du portail](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installer la CLI localement

Vous pouvez également installer et utiliser Azure CLI localement. Pour les besoins de cet article de procédure, vous devez utiliser Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli). 

# <a name="templatetabtemplate"></a>[Modèle](#tab/template)

Aucune.

---

## <a name="sign-in-to-azure"></a>Connexion à Azure

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Connectez-vous au [Portail Azure](https://portal.azure.com).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Connectez-vous à votre abonnement Azure avec la commande `Connect-AzAccount` et suivez les instructions à l’écran pour l’authentification.

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Pour lancer Azure Cloud Shell, connectez-vous au [Portail Azure](https://portal.azure.com).

Pour vous connecter à votre installation locale de l’interface CLI, exécutez la commande [az login](/cli/azure/reference-index#az-login) :

```cli
az login
```

# <a name="templatetabtemplate"></a>[Modèle](#tab/template)

N/A

---

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

À présent, vous êtes prêt à créer votre compte de stockage.

Chaque compte de stockage doit appartenir à un groupe de ressources Azure. Un groupe de ressources est un conteneur logique servant à grouper vos services Azure. Lorsque vous créez un compte de stockage, vous avez le choix entre créer un groupe de ressources ou utiliser un groupe de ressources existant. Cet article montre comment créer un groupe de ressources.

Un compte de stockage **v2 à usage général** fournit un accès à tous les services de Stockage Azure : objets blob, fichiers, files d’attente, tables et disques. Les étapes décrites ici créent un compte de stockage v2 universel, mais les étapes de création d’un autre type de compte de stockage sont similaires.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Tout d’abord, créez un groupe de ressources avec PowerShell au moyen de la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Si vous ne savez pas quelle région spécifier pour le paramètre `-Location`, vous pouvez récupérer la liste des régions prises en charge pour votre abonnement avec la commande [Get-AzLocation](/powershell/module/az.resources/get-azlocation) :

```powershell
Get-AzLocation | select Location
$location = "westus"
```

Ensuite, créez un compte de stockage universel v2 avec l’option Stockage géo-redondant avec accès en lecture (RA-GRS) en utilisant la commande [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). N’oubliez pas que le nom du compte de stockage doit être unique dans Azure. Par conséquent, remplacez la valeur d’espace réservé entre crochets par votre propre valeur unique :

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2 
```

Pour créer un compte de stockage universel v2 avec une option de réplication différente, remplacez la valeur souhaitée pour le paramètre **SkuName** dans le tableau ci-dessous.

|Option de réplication  |Paramètre SkuName  |
|---------|---------|
|Stockage localement redondant (LRS)     |Standard_LRS         |
|Stockage redondant interzone (ZRS)     |Standard_ZRS         |
|Stockage géo-redondant (GRS)     |Standard_GRS         |
|Stockage géo-redondant avec accès en lecture (RA-GRS)     |Standard_RAGRS         |

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Tout d’abord, créez un groupe de ressources avec Azure CLI par le biais de la commande [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

Si vous ne savez pas quelle région spécifier pour le paramètre `--location`, vous pouvez récupérer la liste des régions prises en charge pour votre abonnement avec la commande [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Ensuite, créez un compte de stockage universel v2 avec l’option Stockage géo-redondant avec accès en lecture en utilisant la commande [az storage account create](/cli/azure/storage/account#az_storage_account_create). N’oubliez pas que le nom du compte de stockage doit être unique dans Azure. Par conséquent, remplacez la valeur d’espace réservé entre crochets par votre propre valeur unique :

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

Pour créer un compte de stockage universel v2 avec une option de réplication différente, remplacez la valeur souhaitée pour le paramètre **sku** dans le tableau ci-dessous.

|Option de réplication  |Paramètre sku  |
|---------|---------|
|Stockage localement redondant (LRS)     |Standard_LRS         |
|Stockage redondant interzone (ZRS)     |Standard_ZRS         |
|Stockage géo-redondant (GRS)     |Standard_GRS         |
|Stockage géo-redondant avec accès en lecture (RA-GRS)     |Standard_RAGRS         |

# <a name="templatetabtemplate"></a>[Modèle](#tab/template)

Vous pouvez utiliser Azure PowerShell ou Azure CLI pour déployer un modèle Resource Manager afin de créer un compte de stockage. Le modèle utilisé dans cet article de procédure est issu des [Modèles de démarrage rapide Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Pour exécuter les scripts, sélectionnez **Essayer** pour ouvrir Azure Cloud shell. Pour coller le script, cliquez avec le bouton droit dans l’interpréteur de commandes, puis sélectionnez **Coller**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-file "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Pour savoir comment créer des modèles, voir :

- [Documentation Azure Resource Manager](/azure/azure-resource-manager/).
- [Référence de modèle de compte de stockage](/azure/templates/microsoft.storage/allversions).
- [Exemples supplémentaires de modèles de compte de stockage](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Pour plus d’informations sur les options de réplication disponibles, consultez [Options de réplication de stockage](storage-redundancy.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez supprimer les ressources créées par cet article de procédure, vous pouvez supprimer le groupe de ressources. La suppression du groupe de ressources efface également le compte de stockage associé et d’autres ressources liées au groupe de ressources.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Pour supprimer un groupe de ressources dans le portail Azure :

1. Sur le portail Azure, développez le menu de gauche pour ouvrir le menu des services, et sélectionnez **Groupes de ressources** pour afficher la liste de vos groupes de ressources.
2. Recherchez le groupe de ressources à supprimer, puis faites un clic droit sur le bouton **Plus** ( **...** ) se trouvant à droite de la liste.
3. Sélectionnez **Supprimer le groupe de ressources** et confirmez.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pour supprimer le groupe de ressources et les ressources associées, y compris le nouveau compte de stockage, utilisez la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) :

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Pour supprimer le groupe de ressources et les ressources associées, y compris le nouveau compte de stockage, utilisez la commande [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name storage-resource-group
```

# <a name="templatetabtemplate"></a>[Modèle](#tab/template)

Pour supprimer le groupe de ressources et les ressources associées, y compris le nouveau compte de stockage, utilisez Azure PowerShell ou Azure CLI.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Étapes suivantes

Dans cet article de procédure, vous avez créé un compte de stockage standard à usage général v2. Pour savoir comment charger et télécharger des objets blob vers/à partir de votre compte de stockage, passez au guide de démarrage rapide du stockage Blob.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Utiliser des objets blob avec le portail Azure](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Utiliser des objets blob avec PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Utiliser des objets blob avec l’interface Azure CLI](../blobs/storage-quickstart-blobs-cli.md)

# <a name="templatetabtemplate"></a>[Modèle](#tab/template)

> [!div class="nextstepaction"]
> [Utiliser des objets blob avec le portail Azure](../blobs/storage-quickstart-blobs-portal.md)

---
