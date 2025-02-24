---
title: Fonctions Azure Blueprints
description: Décrit les fonctions à utiliser avec les définitions et affectations Azure Blueprints.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/15/2019
ms.topic: reference
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dc72113a8f5ed978d64d35c43e94dc9e19e4cdb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209398"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Fonctions à utiliser avec Azure Blueprints

Azure Blueprints fournit des fonctions pour une définition de blueprint plus dynamique. Ces fonctions sont conçues pour être utilisées avec les définitions et artefacts de blueprint. Outre l’obtention d’une valeur dynamique grâce à un paramètre de blueprint, un artefact Azure Resource Manager permet de tirer pleinement profit des fonctions Resource Manager.

Les fonctions suivantes sont prises en charge :

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [subscription](#subscription)

## <a name="artifacts"></a>artifacts

`artifacts(artifactName)`

Retourne un objet de propriétés rempli avec ces sorties d’artefacts de blueprint.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| artifactName |OUI |string |Nom d’un artefact de blueprint. |

### <a name="return-value"></a>Valeur de retour

Objet de propriétés de sortie. Les propriétés **outputs** dépendent du type d’artefact de blueprint référencé. Tous les types suivent le format :

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Artefact d’affectation de stratégie

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Artefact de modèle Resource Manager

Les propriétés **outputs** de l’objet retourné sont définies dans le modèle Resource Manager et retournées par le déploiement.

#### <a name="role-assignment-artifact"></a>Artefact d’affectation de rôle

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Exemples

Un artefact de modèle Resource Manager avec l’ID _myTemplateArtifact_ contenant l’exemple de propriété de sortie suivant :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

Voici quelques exemples de récupération de données de l’exemple _myTemplateArtifact_ :

| Expression | Type | Valeur |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["première", "deuxième"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "première" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "ma valeur de chaîne" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | { "ma propriété": "ma valeur", "autre propriété": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "ma valeur" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Combine plusieurs valeurs de chaîne et renvoie la chaîne concaténée.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| string1 |OUI |string |La première valeur pour la concaténation. |
| arguments supplémentaires |Non |string |Valeurs supplémentaires en ordre séquentiel pour la concaténation |

### <a name="return-value"></a>Valeur de retour

Chaîne de valeurs concaténées.

### <a name="remarks"></a>Remarques

La fonction Azure Blueprint diffère de la fonction de modèle Azure Resource Manager, car elle n’est compatible qu’avec des chaînes.

### <a name="example"></a>Exemples

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Retourne une valeur de paramètre de blueprint. Le nom du paramètre spécifié doit être défini dans la définition de blueprint ou dans les artefacts de blueprint.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| parameterName |OUI |string |Nom du paramètre à retourner. |

### <a name="return-value"></a>Valeur de retour

Valeur du paramètre de blueprint ou d’artefact de blueprint spécifié.

### <a name="remarks"></a>Remarques

La fonction Azure Blueprint diffère de la fonction de modèle Azure Resource Manager, car elle n’est compatible qu’avec des paramètres de blueprint.

### <a name="example"></a>Exemples

Définissez les _principalIds_ de paramètre dans la définition de blueprint :

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Utilisez ensuite _principalIds_ comme argument pour `parameters()` dans un artefact de blueprint :

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Renvoie un objet qui représente le groupe de ressources actuel.

### <a name="return-value"></a>Valeur de retour

L’objet renvoyé présente le format suivant :

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Remarques

La fonction Azure Blueprint diffère de la fonction de modèle Azure Resource Manager. La fonction `resourceGroup()` ne peut pas être utilisée dans un artefact de niveau d’abonnement ou la définition de blueprint. Elle ne peut être utilisée que dans des artefacts de blueprint faisant partie d’un artefact de groupe de ressources.

Une utilisation courante de la fonction `resourceGroup()` consiste à créer des ressources dans le même emplacement que l’artefact de groupe de ressources.

### <a name="example"></a>Exemples

Pour utiliser l’emplacement du groupe de ressources, définissez-le dans la définition de blueprint ou lors de l’affectation comme l’emplacement d’un autre artefact, et déclarez un objet d’espace réservé de groupe de ressources dans votre définition de blueprint. Dans cet exemple, _NetworkingPlaceholder_ est le nom de l’espace réservé de groupe de ressources.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Utilisez ensuite la fonction `resourceGroup()` dans le contexte d’un artefact de blueprint qui cible un objet d’espace réservé de groupe de ressources. Dans cet exemple, l’artefact de modèle est déployé dans le groupe de ressources _NetworkingPlaceholder_ et le paramètre _resourceLocation_ est rempli de manière dynamique avec l’emplacement du groupe de ressources  _NetworkingPlaceholder_ pour le modèle. L’emplacement du groupe de ressources _NetworkingPlaceholder_ peut être défini de manière statique dans la définition de blueprint ou de manière dynamique lors de l’affectation. Dans les deux cas, l’artefact de modèle reçoit ces informations sous forme de paramètre et les utilise pour déployer les ressources dans l’emplacement approprié.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

Retourne un objet représentant l’artefact de groupe de ressources spécifié. Contrairement à `resourceGroup()`, où le contexte de l’artefact est nécessaire, cette fonction est utilisée pour obtenir les propriétés d’un espace réservé de groupe de ressources spécifique en l’absence de contexte pour ce groupe de ressources.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| placeholderName |OUI |string |Nom de l’espace réservé de l’artefact de groupe de ressources à retourner. |

### <a name="return-value"></a>Valeur de retour

L’objet renvoyé présente le format suivant :

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Exemples

Pour utiliser l’emplacement du groupe de ressources, définissez-le dans la définition de blueprint ou lors de l’affectation comme l’emplacement d’un autre artefact, et déclarez un objet d’espace réservé de groupe de ressources dans votre définition de blueprint. Dans cet exemple, _NetworkingPlaceholder_ est le nom de l’espace réservé de groupe de ressources.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Utilisez ensuite la fonction `resourceGroups()` dans le contexte d’un artefact de blueprint pour obtenir une référence à l’objet d’espace réservé de groupe de ressources. Dans cet exemple, l’artefact de modèle est déployé en dehors du groupe de ressources _NetworkingPlaceholder_ et le paramètre _artifactLocation_ est rempli de manière dynamique avec l’emplacement du groupe de ressources  _NetworkingPlaceholder_ pour le modèle. L’emplacement du groupe de ressources _NetworkingPlaceholder_ peut être défini de manière statique dans la définition de blueprint ou de manière dynamique lors de l’affectation. Dans les deux cas, l’artefact de modèle reçoit ces informations sous forme de paramètre et les utilise pour déployer les ressources dans l’emplacement approprié.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>subscription

`subscription()`

Retourne des détails concernant l’abonnement pour l’affectation de blueprint actuelle.

### <a name="return-value"></a>Valeur de retour

L’objet renvoyé présente le format suivant :

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Exemples

Utilisez le nom d’affichage de l’abonnement et la fonction `concat()` pour créer une convention de dénomination transmise sous forme de paramètre _resourceName_ à l’artefact de modèle.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrir le [cycle de vie d’un blueprint](../concepts/lifecycle.md).
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../how-to/update-existing-assignments.md).
- Résoudre les problèmes durant l’affectation d’un blueprint en suivant les étapes de [dépannage général](../troubleshoot/general.md).