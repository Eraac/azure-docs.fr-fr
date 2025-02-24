---
title: Exemple de script Azure PowerShell - Changer la plage de ports RDP | Microsoft Docs
description: Exemple de script Azure PowerShell - Changer la plage de ports RDP d’un cluster déployé
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 4470cf8898f1d174f8ec2bb6860e2e30aeff9fe2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592314"
---
# <a name="update-the-rdp-port-range-values"></a>Mettre à jour les valeurs de la plage de ports RDP

Cet exemple de script change les valeurs de la plage de ports RDP sur les machines virtuelles de nœud de cluster une fois que le cluster a été déployé.  Azure PowerShell est utilisé afin que les machines virtuelles sous-jacentes ne s’interrompent pas.  Le script obtient la ressource `Microsoft.Network/loadBalancers` dans le groupe de ressources du cluster et met à jour les valeurs `inboundNatPools.frontendPortRangeStart` et `inboundNatPools.frontendPortRangeEnd`. Personnalisez les paramètres selon vos besoins.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Obtient la ressource `Microsoft.Network/loadBalancers`. |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|Met à jour la ressource `Microsoft.Network/loadBalancers`.|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure Service Fabric sur la page [Azure PowerShell Samples](../service-fabric-powershell-samples.md) (Exemples Azure PowerShell).
