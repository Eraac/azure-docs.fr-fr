---
title: Script Azure PowerShell - Opérations de clé de compte et de chaîne de connexion pour un compte Azure Cosmos
description: Exemple de script Azure PowerShell - Opérations de clé de compte et de chaîne de connexion pour un compte Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 55f28ef6c6ff82f0b82b10ac1dd7931ac46aa2fc
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67602509"
---
# <a name="connection-string-and-account-key-operations-for-an-azure-cosmos-account-using-powershell"></a>Opérations de clé de compte et de chaîne de connexion pour un compte Cosmos à l’aide de PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exemple de script

Ces exemples requièrent le groupe de ressources et le compte. Utilisez un exemple de création PowerShell existant pour approvisionner d'abord un compte.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-keys-connection-strings.ps1 "Connection strings and account keys for Azure Cosmos account")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
|**Ressources Azure**| |
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | Appelle une action sur une ressource. |
|**Groupes de ressources Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

Vous trouverez des exemples supplémentaires de scripts Azure Cosmos DB PowerShell sur la page [Scripts PowerShell Azure Cosmos DB](../../../powershell-samples.md).