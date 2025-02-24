---
title: Exemple de script Azure PowerShell - Ouvrir le port d’application dans l’équilibreur de charge | Microsoft Docs
description: Exemple de script Azure PowerShell - Ouvrir un port dans l’équilibrage de charge Azure pour une application de Service Fabric.
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
ms.date: 05/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: acf26a9ed3c2f2f569c453d5a6d01c913e63fa2e
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597531"
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Ouvrir un port d’application dans l’équilibreur de charge Azure

Une application Service Fabric s’exécutant dans Azure se trouve derrière l’équilibrage de charge Azure. Cet exemple de script ouvre un port dans un équilibreur de charge Azure pour qu’une application Service Fabric puisse communiquer avec des clients externes. Personnalisez les paramètres selon vos besoins. Si votre cluster se trouve dans un groupe de sécurité réseau, pensez à [ajouter une règle de groupe de sécurité réseau entrant](service-fabric-powershell-add-nsg-rule.md) pour autoriser le trafic entrant.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Si nécessaire, installez le module Service Fabric PowerShell avec le [Kit de développement logiciel (SDK) Service Fabric](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Obtient une ressource Azure.  |
| [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) | Obtient l’équilibreur de charge Azure. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) | Ajoute une configuration de sonde pour un équilibreur de charge.|
| [Get-AzLoadBalancerProbeConfig](/powershell/module/az.network/get-azloadbalancerprobeconfig) | Obtient une configuration de sonde pour un équilibreur de charge. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) | Ajoute une configuration de règle pour un équilibreur de charge. |
| [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) | Définit l’état visé d’un équilibrage de charge. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts PowerShell pour Azure Service Fabric sur la page [Exemples Azure PowerShell](../service-fabric-powershell-samples.md).
