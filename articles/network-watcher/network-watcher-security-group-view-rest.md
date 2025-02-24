---
title: Analyser la sécurité réseau avec la vue Groupe de sécurité réseau Network Watcher - API REST | Microsoft Docs
description: Cet article décrit comment utiliser PowerShell pour analyser la sécurité des machines virtuelles par le biais de la vue Groupe de sécurité.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: a2f418fe-f5d2-43ed-8dc3-df0ed2a4d4ac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 86fff39605fa91c1b09c1547dd0efa97b8fd26cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64687861"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analyser la sécurité de votre machine virtuelle par le biais de la vue Groupe de sécurité dans l’API REST

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Interface de ligne de commande Azure](network-watcher-security-group-view-cli.md)
> - [API REST](network-watcher-security-group-view-rest.md)

La vue Groupe de sécurité renvoie des règles de sécurité de réseau configurées et efficaces, appliquées à une machine virtuelle. Cette fonctionnalité permet d’auditer et de diagnostiquer les groupes de sécurité réseau ainsi que les règles configurées sur une machine virtuelle afin de garantir l’autorisation ou le refus appropriés du trafic. Dans cet article, nous vous montrons comment récupérer des règles de sécurité efficaces et appliquées à une machine virtuelle à l’aide de l’API REST


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Avant de commencer

Dans ce scénario, vous appelez l’API REST de Network Watcher pour obtenir la vue de groupe de sécurité pour une machine virtuelle. ARMclient permet d’appeler l’API REST à l’aide de PowerShell. ARMClient est accessible sur le site chocolatey à partir de la page [ARMClient sur Chocolatey](https://chocolatey.org/packages/ARMClient).

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Create a Network Watcher (Créer une instance Network Watcher)](network-watcher-create.md) pour créer une instance Network Watcher. Ce scénario suppose également qu’un groupe de ressources avec une machine virtuelle valide existe et peut être utilisé.

## <a name="scenario"></a>Scénario

Le scénario décrit dans cet article récupère des règles de sécurité efficaces et appliquées pour une machine virtuelle donnée.

## <a name="log-in-with-armclient"></a>Se connecter à ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Récupérer une machine virtuelle

Exécutez le script suivant pour renvoyer une machine virtuelle. Le code suivant nécessite des variables :

- **subscriptionId** - L’ID d’abonnement peut également être récupéré avec l’applet de commande **Get-AzSubscription**.
- **resourceGroupName** - Le nom d’un groupe de ressources qui contient les machines virtuelles.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

L’information nécessaire est l’**UD** sous le type `Microsoft.Compute/virtualMachines` dans la réponse, comme illustré dans l’exemple suivant :

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>Obtenir la vue de groupe de sécurité pour la machine virtuelle

L’exemple suivant demande la vue de groupe de sécurité d’une machine virtuelle cible. Les résultats de cet exemple peuvent être comparés aux règles et à la sécurité définies par l’origine afin de rechercher des différences de configuration.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>Afficher la réponse

L’exemple suivant est la réponse renvoyée par la commande précédente. Les résultats présentent toutes les règles de sécurité efficaces et appliquées sur la machine virtuelle, réparties en plusieurs groupes : **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** et **EffectiveSecurityRules**.

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Consultez la page [Auditing Network Security Groups (NSG) with Network Watcher (Audit des groupes de sécurité réseau avec Network Watcher)](network-watcher-security-group-view-powershell.md) pour découvrir comment automatiser la validation des groupes de sécurité réseau.


