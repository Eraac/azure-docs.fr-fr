---
title: Appliquer la sécurité avec des stratégies sur des machines virtuelles Windows dans Azure | Microsoft Docs
description: Comment appliquer une stratégie à une machine virtuelle Azure Resource Manager Windows
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: kasing
ms.openlocfilehash: 75a8d5d48d5024b35b39792a481e600692581d95
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720001"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Appliquer des stratégies aux machines virtuelles Windows avec Azure Resource Manager
Avec les stratégies, une organisation peut appliquer différentes conventions et règles à travers l'entreprise. L’application du comportement souhaité peut vous aider à atténuer les risques tout en contribuant à la réussite de l'organisation. Dans cet article, nous expliquons comment utiliser les stratégies d’Azure Resource Manager afin de définir le comportement souhaité pour les machines virtuelles de votre entreprise.

Pour une introduction aux stratégies, consultez la page [Qu’est-ce qu’Azure Policy ?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>Machines virtuelles autorisées
Pour vous assurer que les machines virtuelles de votre entreprise sont compatibles avec une application, vous pouvez limiter les systèmes d’exploitation autorisés. Dans l’exemple de stratégie suivant, vous autorisez uniquement la création de machines virtuelles Windows Server 2012 R2 Datacenter :

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-R2-Datacenter"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Utilisez un caractère générique pour modifier la stratégie précédente afin qu’elle autorise toutes les images Windows Server Datacenter :

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Pour modifier la stratégie précédente pour autoriser toute image Windows Server 2012 R2 ou version supérieure, utilisez anyOf :

```json
{
  "anyOf": [
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2012-R2-Datacenter*"
    },
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2016-Datacenter*"
    }
  ]
}
```

Pour plus d’informations sur les champs de la stratégie, consultez les [alias de stratégie](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Disques managés

Pour exiger l’utilisation de disques managés, employez la stratégie suivante :

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Images de machines virtuelles

Pour des raisons de sécurité, vous pouvez exiger que seules les images personnalisées approuvées soient déployées dans votre environnement. Vous pouvez spécifier soit le groupe de ressources qui contient les images approuvées, soit les images approuvées.

L’exemple suivant nécessite des images provenant d’un groupe de ressources approuvées :

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

L’exemple suivant spécifie les ID des images approuvées :

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Extensions de machine virtuelle

Vous pouvez interdire l’utilisation de certains types d’extensions. Par exemple, une extension peut ne pas être compatible avec certaines images de machines virtuelles personnalisées. L’exemple suivant montre comment bloquer une extension. Il se base sur l’éditeur et sur le type pour déterminer quelle extension doit être bloquée.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

Quand vous disposez d’une licence locale, vous pouvez enregistrer les frais de licence sur vos machines virtuelles. Si vous n’avez pas de licence, vous devez interdire cette option. La stratégie suivante interdit l’utilisation d’Azure Hybrid Use Benefit (AHUB) :

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
* Après avoir défini une règle de stratégie (comme le montrent les exemples précédents), vous devez créer la définition de stratégie et l’attribuer à une étendue. L’étendue peut être un abonnement, un groupe de ressources ou une ressource. Pour assigner des stratégies, consultez [Utiliser le portail Azure pour attribuer et gérer les stratégies de ressources](../../governance/policy/assign-policy-portal.md), [Créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure en utilisant PowerShell](../../governance/policy/assign-policy-powershell.md) et [Créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure en utilisant l’interface de ligne de commande Azure](../../governance/policy/assign-policy-azurecli.md).
* Pour une introduction aux stratégies de ressources, consultez la page [Qu’est-ce qu’Azure Policy ?](../../governance/policy/overview.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](/azure/architecture/cloud-adoption-guide/subscription-governance).