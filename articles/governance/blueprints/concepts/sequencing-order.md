---
title: Comprendre l’ordre de la séquence de déploiement
description: Découvrez le cycle de vie que traverse une définition de blueprint, ainsi que les détails de chaque phase.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: b05a7ce260e8cc1da4ac8a0c186694ae097a3b1e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721291"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Comprendre la séquence de déploiement dans les blueprints Azure

Azure Blueprints utilise un **ordre de séquencement** pour déterminer l’ordre de création des ressources pendant le traitement de l’affectation d’une définition de blueprint. Cet article explique les concepts suivants :

- Ordre de séquencement par défaut qui est utilisé
- Procédure de personnalisation de cet ordre
- Mode de traitement de l’ordre personnalisé

Les exemples JSON contiennent des variables que vous devez remplacer par vos propres valeurs :

- `{YourMG}` - À remplacer par le nom de votre groupe d’administration

## <a name="default-sequencing-order"></a>Ordre de séquencement par défaut

Si la définition de blueprint ne contient aucune directive pour l’ordre de déploiement des artefacts ou si la directive a la valeur null, l’ordre utilisé est le suivant :

- Artefacts d’**attribution de rôle** au niveau de l’abonnement triés par nom d’artefact
- Artefacts d’**attribution de stratégie** au niveau de l’abonnement triés par nom d’artefact
- Artefacts de **modèle Azure Resource Manager** au niveau de l’abonnement triés par nom d’artefact
- Artefacts de **groupe de ressources** (englobant les artefacts enfants) triés par nom d’espace réservé

Dans chaque artefact de **groupe de ressources**, l’ordre de séquence suivant est utilisé pour les artefacts devant être créés dans le groupe de ressources en question :

- Artefacts d’**attribution de rôle** enfant de groupe de ressources triés par nom d’artefact
- Artefacts d’**attribution de stratégie** enfant de groupe de ressources triés par nom d’artefact
- Artefacts de **modèle Azure Resource Manager** enfant de groupe de ressources triés par nom d’artefact

> [!NOTE]
> L’utilisation de [artifacts()](../reference/blueprint-functions.md#artifacts) crée une dépendance implicite sur l’artefact référencé.

## <a name="customizing-the-sequencing-order"></a>Personnalisation de l’ordre de séquencement

Au moment de composer des définitions de blueprints de grande taille, il peut être nécessaire de créer des ressources dans un ordre spécifique. Ce cas de figure se présente plus particulièrement quand une définition de blueprint inclut plusieurs modèles Azure Resource Manager. À cet effet, les blueprints permettent de définir l’ordre de séquencement.

Le classement est effectué en définissant une propriété `dependsOn` dans le JSON. La définition de blueprint, pour les groupes de ressources, et les objets artefact prennent en charge cette propriété. `dependsOn` est un tableau de chaînes de noms d’artefacts que l’artefact en question doit créer au préalable.

### <a name="example---ordered-resource-group"></a>Exemple - Groupe de ressources classé

Cet exemple de définition de blueprint présente un groupe de ressources pour lequel un ordre de séquencement personnalisé a été défini en déclarant une valeur pour `dependsOn`, ainsi qu’un groupe de ressources standard. Dans ce cas, l’artefact nommé **assignPolicyTags** est traité avant le groupe de ressources **ordered-rg**.
**standard-rg** est traité selon l’ordre de séquencement par défaut.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint",
    "type": "Microsoft.Blueprint/blueprints",
    "name": "mySequencedBlueprint"
}
```

### <a name="example---artifact-with-custom-order"></a>Exemple d’artefact avec un ordre personnalisé

Cet exemple est un artefact de stratégie qui dépend d’un modèle Azure Resource Manager. Selon l’ordre par défaut, un artefact de stratégie serait créé avant le modèle Azure Resource Manager. Ce classement permet à l’artefact de stratégie d’attendre que le modèle Azure Resource Manager soit créé.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/assignPolicyTags",
    "type": "Microsoft.Blueprint/artifacts",
    "name": "assignPolicyTags"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Exemple - Artefact de modèle de niveau d’abonnement dépendant d’un groupe de ressources

Cet exemple concerne un modèle Resource Manager déployé au niveau de l’abonnement pour dépendre d’un groupe de ressources. Dans le classement par défaut, les artefacts de niveau d’abonnement seront créés avant les groupes de ressources et les artefacts enfants dans ces groupes de ressources. Le groupe de ressources est défini dans la définition de blueprint comme suit :

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

L’artefact de modèle de niveau d’abonnement dépendant du groupe de ressources **wait-for-me** est défini comme suit :

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/subtemplateWaitForRG",
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "name": "subtemplateWaitForRG"
}
```

## <a name="processing-the-customized-sequence"></a>Traitement de la séquence personnalisée

Pendant le processus de création, un tri topologique est appliqué pour créer le graphe de dépendance des artefacts des blueprints. La vérification permet de garantir que chaque niveau de dépendance entre les groupes de ressources et les artefacts est pris en charge.

Si une dépendance d’artefact qui n’est pas susceptible de modifier l’ordre par défaut est déclarée, aucune modification n’est apportée. Tel est le cas par exemple d’un groupe de ressources qui dépend d’une stratégie au niveau de l’abonnement ou d’une affectation de stratégie enfant « standard-rg » de groupe de ressources qui dépend d’une affectation de rôle enfant « standard-rg » de groupe de ressources. Dans les deux cas, `dependsOn` n’aurait pas modifié l’ordre de séquencement par défaut et aucune modification n’aurait été apportée.

## <a name="next-steps"></a>Étapes suivantes

- Découvrir le [cycle de vie d’un blueprint](lifecycle.md).
- Comprendre comment utiliser les [paramètres statiques et dynamiques](parameters.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../how-to/update-existing-assignments.md).
- Résoudre les problèmes durant l’affectation d’un blueprint en suivant les étapes de [dépannage général](../troubleshoot/general.md).