---
title: 'Démarrage rapide : créer un groupe de machines virtuelles identiques Linux à l’aide d’un modèle Azure | Microsoft Docs'
description: Apprendre à créer rapidement un groupe de machines virtuelles identiques Linux avec un modèle Azure Resource Manager qui déploie un exemple d’application et configure des règles de mise à l’échelle automatique
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: cynthn
ms.openlocfilehash: 9afc2e2362721fd2905b2a241960507aec9b15a5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66151106"
---
# <a name="quickstart-create-a-linux-virtual-machine-scale-set-with-an-azure-template"></a>Démarrage rapide : Créer un groupe de machines virtuelles identiques Linux à l’aide d’un modèle Azure
Un groupe de machines virtuelles identiques vous permet de déployer et de gérer un ensemble de machines virtuelles identiques prenant en charge la mise à l’échelle automatique. Vous pouvez mettre à l’échelle manuellement le nombre de machines virtuelles du groupe identique ou définir des règles de mise à l’échelle automatique en fonction de l’utilisation des ressources telles que l’UC, la demande de mémoire ou le trafic réseau. Un équilibreur de charge Azure distribue ensuite le trafic vers les instances de machine virtuelle du groupe identique. Dans cet article de démarrage rapide, vous créez un groupe de machines virtuelles identiques et déployez un exemple d’application avec un modèle Azure Resource Manager.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.0.29 ou une version ultérieure pour poursuivre la procédure décrite dans ce didacticiel. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).


## <a name="define-a-scale-set-in-a-template"></a>Définir un groupe identique dans un modèle
Les modèles Azure Resource Manager vous permettent de déployer des groupes de ressources liées. Les modèles sont écrits en JavaScript Object Notation (JSON) et définissent l’ensemble de l’environnement d’infrastructure Azure pour votre application. Dans un modèle unique, vous pouvez créer le groupe de machines virtuelles identiques, installer des applications et configurer des règles de mise à l’échelle automatique. Avec l’utilisation de variables et de paramètres, ce modèle peut être réutilisé pour mettre à jour des groupes identiques existants ou en créer d’autres. Vous pouvez déployer des modèles via le Portail Azure, Azure CLI ou Azure PowerShell, ou à partir de pipelines d’intégration continue/de livraison continue.

Pour plus d’informations sur les modèles, consultez [Vue d’ensemble d’Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment). Pour connaître la syntaxe JSON et les propriétés, consultez les informations de référence sur les modèles [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets).

Pour créer un groupe identique avec un modèle, vous définissez les ressources appropriées. Les parties essentielles du type de ressource de groupe de machines virtuelles identiques sont :

| Propriété                     | Description de la propriété                                  | Exemple de valeur de modèle                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| Type                         | Type de ressource Azure à créer                            | Microsoft.Compute/virtualMachineScaleSets |
| Nom                         | Nom du groupe identique                                       | myScaleSet                                |
| location                     | Emplacement de création du groupe identique                     | USA Est                                   |
| sku.name                     | Taille de machine virtuelle pour chaque instance de groupe identique                  | Standard_A1                               |
| sku.capacity                 | Nombre d’instances de machines virtuelles à créer initialement           | 2                                         |
| upgradePolicy.mode           | Mode de mise à niveau d’instance de machine virtuelle lorsque des modifications se produisent              | Automatique                                 |
| imageReference               | Plateforme ou image personnalisée à utiliser pour les instances de machine virtuelle | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | Préfixe du nom de chaque instance de machine virtuelle                     | myvmss                                    |
| osProfile.adminUsername      | Nom d’utilisateur de chaque instance de machine virtuelle                        | azureuser                                 |
| osProfile.adminPassword      | Mot de passe de chaque instance de machine virtuelle                        | P@ssw0rd!                                 |

 L’exemple suivant montre la définition de ressource de groupe identique essentielle. Pour personnaliser un modèle de groupe identique, vous pouvez modifier la taille de machine virtuelle ou la capacité initiale, ou utiliser une autre plateforme ou une image personnalisée.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 Pour que l’exemple reste court, la configuration de carte d’interface réseau virtuelle n’est pas affichée. En outre, des composants supplémentaires, tels qu’un équilibreur de charge, ne sont pas affichés. Un modèle de groupe identique complet figure [à la fin de cet article](#deploy-the-template).


## <a name="add-a-sample-application"></a>Ajouter un exemple d’application
Pour tester votre groupe identique, installez une application web de base. Lorsque vous déployez un groupe identique, les extensions de machine virtuelle peuvent fournir des tâches d’automatisation et de configuration après le déploiement, telles que l’installation d’une application. Des scripts peuvent être téléchargés à partir de Stockage Azure ou de GitHub, ou fournis dans le portail Azure lors de l’exécution de l’extension. Pour appliquer une extension à votre groupe identique, vous ajoutez la section *extensionProfile* à l’exemple de ressource précédent. En règle générale, le profil d’extension définit les propriétés suivantes :

- Type d’extension
- Éditeur d’extension
- Version d’extension
- Emplacement des scripts de configuration ou d’installation
- Commandes à exécuter sur les instances de machine virtuelle

Le modèle de [serveur HTTP Python sur Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) utilise l’extension de script personnalisé pour installer [Bottle](https://bottlepy.org/docs/dev/), une infrastructure web Python, et un serveur HTTP simple. 

Deux scripts sont définis dans **fileUris** - *installserver.sh* et *workserver.py*. Ces fichiers sont téléchargés à partir de GitHub, puis *commandToExecute* exécute `bash installserver.sh` pour installer et configurer l’application :

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
          ],
          "commandToExecute": "bash installserver.sh"
        }
      }
    }
  ]
}
```


## <a name="deploy-the-template"></a>Déployer le modèle
Vous pouvez déployer le modèle de [serveur HTTP Python sur Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) avec le bouton **Déployer sur Azure** suivant. Ce bouton ouvre le portail Azure, charge le modèle complet et vous invite à renseigner quelques paramètres comme le nom du groupe identique, le nombre d’instances et les informations d’identification d’administrateur.

[![Déployer le modèle sur Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-bottle-autoscale%2Fazuredeploy.json)

Vous pouvez également utiliser Azure CLI pour installer le serveur HTTP Python sur Linux avec [az group deployment create](/cli/azure/group/deployment) comme suit :

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Renseignez le nom du groupe identique, le nombre d’instances et les informations d’identification d’administrateur pour les instances de machine virtuelle. Quelques minutes sont nécessaires pour que le groupe identique et les ressources associées soient créés.


## <a name="test-your-scale-set"></a>Tester votre groupe identique
Pour voir votre groupe identique en action, accédez à l’exemple d’application web dans un navigateur web. Obtenez l’adresse IP publique de l’équilibreur de charge avec [az network public-ip list](/cli/azure/network/public-ip) comme suit :

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Entrez l’adresse IP publique de l’équilibreur de charge dans un navigateur web au format *http:\//publicIpAddress:9000/do_work*. L’équilibreur de charge répartit le trafic vers l’une de vos instances de machine virtuelle, comme illustré dans l’exemple suivant :

![Page web par défaut dans NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)


## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group) pour supprimer le groupe de ressources, le groupe identique et toutes les ressources associées, comme suit. Le paramètre `--no-wait` retourne le contrôle à l’invite de commandes sans attendre que l’opération se termine. Le paramètre `--yes` confirme que vous souhaitez supprimer les ressources sans passer par une invite supplémentaire à cette fin.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Étapes suivantes
Dans cet article de démarrage rapide, vous avez créé un groupe identique Linux avec un modèle Azure et vous avez utilisé l’extension de script personnalisé pour installer un serveur web Python de base sur les instances de machine virtuelle. Pour en savoir plus, passez au didacticiel dédié à la création et à la gestion de groupes de machines virtuelles identiques Azure.

> [!div class="nextstepaction"]
> [Créer et gérer des groupes de machines virtuelles identiques Azure](tutorial-create-and-manage-cli.md)
