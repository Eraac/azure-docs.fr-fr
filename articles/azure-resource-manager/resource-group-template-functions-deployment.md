---
title: Fonctions de modèle Azure Resource Manager - déploiement| Microsoft Docs
description: Décrit les fonctions à utiliser dans un modèle Azure Resource Manager pour récupérer des informations de déploiement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/03/2019
ms.author: tomfitz
ms.openlocfilehash: 9cf81058d79d474a4d61195850636e428a1dbd0d
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206469"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Fonctions de déploiement pour les modèles Azure Resource Manager 

Resource Manager offre les fonctions ci-après pour l’obtention de valeurs à partir des sections du modèle et de valeurs associées au déploiement :

* [deployment](#deployment)
* [parameters](#parameters)
* [variables](#variables)

Pour obtenir des valeurs de ressources, de groupes de ressources ou d’abonnements, consultez [Fonctions de ressource](resource-group-template-functions-resource.md).

<a id="deployment" />

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment"></a>deployment
`deployment()`

Renvoie des informations sur l’opération de déploiement actuelle.

### <a name="return-value"></a>Valeur de retour

Cette fonction retourne l’objet transmis au cours du déploiement. Les propriétés de l’objet renvoyé diffèrent selon que l’objet de déploiement est passé sous forme de lien ou d’objet inline. Quand l’objet de déploiement est passé inline, comme lors de l’utilisation du paramètre **-TemplateFile** dans Azure PowerShell pour pointer vers un fichier local, l’objet renvoyé a le format suivant :

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Quand l’objet est passé comme lien, par exemple lors de l’utilisation du paramètre **-TemplateUri** pour pointer vers un objet distant, l’objet est retourné dans le format suivant : 

```json
{
    "name": "",
    "properties": {
        "templateLink": {
            "uri": ""
        },
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Lors d’un [déploiement sur un abonnement Azure](deploy-to-subscription.md), l’objet retourné inclut une propriété `location` au lieu d’un groupe de ressources. La propriété d’emplacement est incluse lors du déploiement d’un modèle local ou d’un modèle externe.

### <a name="remarks"></a>Remarques

Vous pouvez utiliser deployment() pour établir une liaison à un autre modèle en fonction de l’URI du modèle parent.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Si vous redéployez un modèle à partir de l’historique de déploiement dans le portail, le modèle est déployé comme un fichier local. La propriété `templateLink` n’est pas retournée dans la fonction de déploiement. Si votre modèle s’appuie sur `templateLink` pour construire un lien vers un autre modèle, n’utilisez pas le portail pour redéployer. À la place, utilisez les commandes dont vous vous êtes servi pour déployer le modèle à l’origine.

### <a name="example"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) suivant retourne l’objet de déploiement :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

L’exemple précédent retourne l’objet suivant :

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/deployment.json
```

Pour un modèle de niveau d’abonnement qui utilise la fonction de déploiement, consultez [Fonction de déploiement d’abonnement](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). Le déploiement s’effectue à l’aide des commandes `az deployment create` ou `New-AzDeployment`.

<a id="parameters" />

## <a name="parameters"></a>parameters
`parameters(parameterName)`

Retourne une valeur de paramètre. Le nom de paramètre spécifié doit être défini dans la section parameters du modèle.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| parameterName |OUI |string |Nom du paramètre à retourner. |

### <a name="return-value"></a>Valeur de retour

La valeur du paramètre spécifié.

### <a name="remarks"></a>Remarques

En général, vous utilisez les paramètres pour définir les valeurs de la ressource. L’exemple suivant définit le nom du site web sur la valeur du paramètre transmise au cours du déploiement.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) suivant montre une utilisation simplifiée de la fonction parameters.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
        },
        "crossParameter": {
            "type": "string",
            "defaultValue": "[parameters('stringParameter')]"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "stringOutput": {
            "value": "[parameters('stringParameter')]",
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| stringOutput | String | option 1 |
| intOutput | Int | 1 |
| objectOutput | Object | {"one": "a", "two": "b"} |
| arrayOutput | Array | [1, 2, 3] |
| crossOutput | String | option 1 |

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/parameters.json
```

<a id="variables" />

## <a name="variables"></a>variables
`variables(variableName)`

Retourne la valeur de la variable. Le nom de variable spécifié doit être défini dans la section variables du modèle.

### <a name="parameters"></a>parameters

| Paramètre | Obligatoire | Type | Description |
|:--- |:--- |:--- |:--- |
| variableName |OUI |String |Nom de la variable à retourner. |

### <a name="return-value"></a>Valeur de retour

La valeur de la variable spécifiée.

### <a name="remarks"></a>Remarques

En général, vous utilisez les variables pour simplifier votre modèle en créant des valeurs complexes une seule fois. L’exemple suivant crée un nom unique pour un compte de stockage.

```json
"variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
    },
    {
        "type": "Microsoft.Compute/virtualMachines",
        "dependsOn": [
            "[variables('storageName')]"
        ],
        ...
    }
],
```

### <a name="example"></a>Exemples

[L’exemple de modèle](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) suivant retourne différentes valeurs de variables.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
        "var3": "[ variables('var1') ]",
        "var4": {
            "property1": "value1",
            "property2": "value2"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput1": {
            "value": "[variables('var1')]",
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

La sortie de l’exemple précédent avec les valeurs par défaut se présente comme suit :

| Nom | type | Valeur |
| ---- | ---- | ----- |
| exampleOutput1 | String | myVariable |
| exampleOutput2 | Array | [1, 2, 3, 4] |
| exampleOutput3 | String | myVariable |
| exampleOutput4 |  Object | {"property1": "value1", "property2": "value2"} |

Pour déployer cet exemple de modèle avec Azure CLI, utilisez :

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

Pour déployer cet exemple de modèle avec PowerShell, utilisez :

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/variables.json
```

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir une description des sections d’un modèle Azure Resource Manager, consultez [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
* Pour fusionner plusieurs modèles, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).
* Pour itérer un nombre de fois spécifié lors de la création d'un type de ressource, consultez [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).
* Pour savoir comment déployer le modèle que vous avez créé, consultez [Déployer une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).

