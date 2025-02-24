---
title: Extension de machine virtuelle Agent Azure Network Watcher pour Linux | Microsoft Docs
description: Déployez l’Agent Network Watcher sur une machine virtuelle Linux avec une extension de machine virtuelle.
services: virtual-machines-linux
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 5ed5e791cd6e611218769650115c78afd1869f67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60743426"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Extension de machine virtuelle Agent Network Watcher pour Linux

## <a name="overview"></a>Vue d'ensemble

[Azure Network Watcher](/azure/network-watcher/) est un service d’analyse, de diagnostic et d’analytique des performances réseau permettant de surveiller les réseaux Azure. L’extension de machine virtuelle de l’agent Network Watcher est obligatoire pour utiliser certaines fonctionnalités Network Watcher sur les machines virtuelles Azure, par exemple la capture du trafic réseau à la demande, et d’autres fonctionnalités avancées.

Cet article présente les plateformes et options de déploiement qui sont prises en charge pour l’extension de machine virtuelle de l’agent Network Watcher pour Linux. L’installation de l’agent n’est pas perturbante et ne nécessite pas de redémarrage de la machine virtuelle. Vous pouvez déployer l’extension dans les machines virtuelles que vous déployez. Si la machine virtuelle est déployée par un service Azure, consultez la documentation relative au service pour déterminer si elle autorise l’installation d’extensions dans la machine virtuelle.

## <a name="prerequisites"></a>Prérequis

### <a name="operating-system"></a>Système d’exploitation

L’extension de l’agent Network Watcher peut être configurée pour les distributions Linux suivantes :

| Distribution | Version |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 et 8 |
| Red Hat | 6 et 7 |
| Oracle Linux | 6.8+ et 7 |
| SUSE Linux Enterprise Server | 11 et 12 |
| OpenSUSE Leap | 42.3+ |
| CentOS | 6.5+ et 7 |
| CoreOS | 899.17.0+ |


### <a name="internet-connectivity"></a>Connectivité Internet

Une machine virtuelle doit être connectée à Internet pour pouvoir utiliser certaines fonctionnalités de l’agent Network Watcher. Sans la possibilité d’établir des connexions sortantes, certaines fonctionnalités de l’agent Network Watcher risquent de mal fonctionner ou de ne plus être disponibles. Pour obtenir plus d’informations sur les fonctionnalités de Network Watcher nécessaires à l’agent, consultez la [documentation de Network Watcher](/azure/network-watcher/).

## <a name="extension-schema"></a>Schéma d’extensions

Le JSON suivant illustre le schéma de l’extension Agent Network Watcher. L’extension ne demande et ne prend en charge aucun paramètre fourni par l’utilisateur. L’extension s’appuie sur sa configuration par défaut.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentLinux",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Valeurs de propriétés

| Nom | Valeur/Exemple |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Déploiement de modèle

Vous pouvez déployer les extensions de machines virtuelles Azure avec un modèle Azure Resource Manager. Pour déployer l’extension de l’agent Network Watcher, utilisez le schéma json précédent, dans votre modèle.

## <a name="azure-classic-cli-deployment"></a>Déploiement d’Azure Classic CLI

L’exemple suivant déploie l’extension de machine virtuelle de l’agent Network Watcher sur une machine virtuelle existante et déployée à l’aide du modèle de déploiement classique :

```azurecli
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Déploiement de l’interface de ligne de commande Azure

L’exemple suivant déploie l’extension de machine virtuelle de l’agent Network Watcher sur une machine virtuelle existante et déployée par le biais du Gestionnaire de ressources :

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Résolution des problèmes et support

### <a name="troubleshooting"></a>Résolution de problèmes

Vous pouvez récupérer les données sur l’état des déploiements d’extension à partir du portail Azure ou à l’aide d’Azure CLI.

L’exemple suivant montre l’état de déploiement des extensions d’une machine virtuelle déployée au moyen du modèle de déploiement classique, par le biais d’Azure Classic CLI :

```azurecli
azure config mode asm
azure vm extension get myVM1
```
La sortie de l’exécution de l’extension est enregistrée dans les fichiers qui que se trouvent dans le répertoire suivant :

```
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
```

L’exemple suivant montre l’état de déploiement de l’extension NetworkWatcherAgentLinux pour une machine virtuelle déployée avec Resource Manager, par le biais de l’interface Azure CLI :

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à tout moment dans cet article, vous pouvez vous référer à la [documentation de Network Watcher](/azure/network-watcher/) ou contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**. Pour plus d’informations sur l’utilisation du support Azure, consultez [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).
