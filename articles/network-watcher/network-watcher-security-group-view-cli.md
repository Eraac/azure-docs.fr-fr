---
title: Analyser la sécurité réseau avec la vue Groupe de sécurité réseau Network Watcher - Azure CLI | Microsoft Docs
description: Cet article décrit comment utiliser l’interface de ligne de commande Azure pour analyser la sécurité des machines virtuelles par le biais de la vue Groupe de sécurité.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 68222a90eb60ab4f84a34b5e46833128ea081ec1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724441"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Analyser la sécurité de votre machine virtuelle par le biais de la vue Groupe de sécurité dans l’interface de ligne de commande Azure

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Interface de ligne de commande Azure](network-watcher-security-group-view-cli.md)
> - [API REST](network-watcher-security-group-view-rest.md)

La vue Groupe de sécurité renvoie des règles de sécurité de réseau configurées et efficaces, appliquées à une machine virtuelle. Cette fonctionnalité permet d’auditer et de diagnostiquer les groupes de sécurité réseau ainsi que les règles configurées sur une machine virtuelle afin de garantir l’autorisation ou le refus appropriés du trafic. Dans cet article, nous vous montrons comment récupérer des règles de sécurité configurées et efficaces pour une machine virtuelle à l’aide de l’interface de ligne de commande Azure

Pour exécuter la procédure indiquée dans cet article, vous devez [installer l’interface de ligne de commande Azure pour Mac, Linux et Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Avant de commencer

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Create a Network Watcher (Créer une instance Network Watcher)](network-watcher-create.md) pour créer une instance Network Watcher.

## <a name="scenario"></a>Scénario

Le scénario décrit dans cet article récupère des règles de sécurité configurées et efficaces pour une machine virtuelle donnée.

## <a name="get-a-vm"></a>Obtenir une machine virtuelle

Une machine virtuelle est requise pour l’exécution de l’applet de commande `vm list`. La commande suivante répertorie les machines virtuelles dans un groupe de ressources :

```azurecli
az vm list -resource-group resourceGroupName
```

Une fois que vous connaissez la machine virtuelle, vous pouvez utiliser l’applet de commande `vm show` pour obtenir son identifiant de ressource :

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Récupérer la vue Groupe de sécurité

L’étape suivante consiste à récupérer le résultat de la vue Groupe de sécurité.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Affichage des résultats

L’exemple suivant est une réponse abrégée des résultats renvoyés. Les résultats présentent toutes les règles de sécurité efficaces et appliquées sur la machine virtuelle, réparties en plusieurs groupes : **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** et **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Consultez la page [Auditing Network Security Groups (NSG) with Network Watcher (Audit des groupes de sécurité réseau avec Network Watcher)](network-watcher-nsg-auditing-powershell.md) pour découvrir comment automatiser la validation des groupes de sécurité réseau.

Pour en savoir plus sur les règles de sécurité appliquées à vos ressources réseau, consultez la page [Security group view overview (Vue d’ensemble de la vue Groupe de sécurité)](network-watcher-security-group-view-overview.md)
