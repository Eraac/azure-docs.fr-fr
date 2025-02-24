---
title: Créer une affectation de stratégie avec un modèle Resource Manager
description: Cet article vous guide pas à pas pour créer une affectation de stratégie à l’aide d’un modèle Resource Manager en vue d’identifier des ressources non conformes.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: f31d6197c22be4d66e0610ad7914f541a45ed995
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979572"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Démarrage rapide : Créer une affectation de stratégie pour identifier des ressources non conformes à l’aide d’un modèle Resource Manager

La première étape pour comprendre la conformité dans Azure consiste à identifier l’état de vos ressources.
Ce démarrage rapide vous guide pas à pas dans le processus de création d’une attribution de stratégie pour identifier les machines virtuelles qui n’utilisent pas de disques managés.

À la fin de ce processus, vous aurez identifié correctement les machines virtuelles qui n’utilisent pas de disques managés. Elles sont *non conformes* à l’attribution de stratégie.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie

Dans ce guide de démarrage rapide, vous allez créer une affectation de stratégie et affecter une définition de stratégie intégrée appelée *Auditer les machines virtuelles qui n’utilisent pas de disques managés*. Pour obtenir une liste partielle des stratégies intégrées disponibles, consultez [Exemples Azure Policy](./samples/index.md).

Il existe plusieurs méthodes pour créer des affectations de stratégie. Dans ce guide de démarrage rapide, vous allez utiliser un [modèle de démarrage rapide](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Voici une copie du modèle :

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Le service Azure Policy est gratuit.  Pour plus d’informations, consultez [Vue d’ensemble d’Azure Policy](./overview.md).

1. Sélectionnez l’image suivante pour vous connecter au portail Azure et ouvrir le modèle :

   [![Déployer le modèle de stratégie sur Azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Sélectionnez ou entrez les valeurs suivantes :

   | Nom | Valeur |
   |------|-------|
   | Abonnement | Sélectionnez votre abonnement Azure. |
   | Groupe de ressources | Sélectionnez **Créer**, spécifiez un nom, puis sélectionnez **OK**. Dans la capture d’écran, le nom du groupe de ressources est *mypolicyquickstart\<date au format MMJJ>rg*. |
   | Lieu | Sélectionnez une région. Par exemple, **USA Centre**. |
   | Nom de l’affectation de stratégie | Spécifiez un nom d’affectation de stratégie. Vous pouvez utiliser l’écran de définition de stratégie si vous le souhaitez. Par exemple, **Auditer les machines virtuelles qui n’utilisent pas de disques managés**. |
   | Nom du groupe de ressources | Spécifiez le nom du groupe de ressources auquel vous voulez affecter la stratégie. Dans ce démarrage rapide, utilisez la valeur par défaut **[resourceGroup().name]** . **[resourceGroup()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)**  est une fonction de modèle qui récupère le groupe de ressources. |
   | ID de définition de stratégie | Spécifiez **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**. |
   | J’accepte les termes et conditions mentionnés ci-dessus | (Sélectionner) |

1. Sélectionnez **Achat**.

Ressources supplémentaires :

- Pour trouver des exemples de modèles supplémentaires, consultez [Modèle de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Pour obtenir des informations de référence sur les modèles, accédez au [document de référence des modèles Azure](/azure/templates/microsoft.authorization/allversions).
- Pour savoir comment développer des modèles Resource Manager, consultez la [documentation Azure Resource Manager](/azure/azure-resource-manager/).
- Pour découvrir le déploiement au niveau de l’abonnement, consultez [Créer des groupes de ressources et des ressources au niveau de l’abonnement](../../azure-resource-manager/deploy-to-subscription.md).

## <a name="identify-non-compliant-resources"></a>Identifier les ressources non conformes

Sélectionnez **Conformité** dans la partie gauche de la page. Recherchez ensuite l’affectation de stratégie **Auditer les machines virtuelles qui n’utilisent pas de disques managés** que vous avez créée.

![Page de la vue d’ensemble de la conformité à la stratégie](./media/assign-policy-template/policy-compliance.png)

Si des ressources existantes ne sont pas conformes à cette nouvelle affectation, elles apparaissent sous **Ressources non conformes**.

Pour plus d’informations, consultez [Fonctionnement de la conformité](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer l’affectation créée, procédez comme suit :

1. Sélectionnez **Conformité** (ou **Affectations**) dans la partie gauche de la page Azure Policy et recherchez l’affectation de stratégie **Auditer les machines virtuelles qui n’utilisent pas de disques managés** que vous avez créée.

1. Cliquez avec le bouton droit sur l’affectation de stratégie **Auditer les machines virtuelles qui n’utilisent pas de disques managés** et sélectionnez **Supprimer l’attribution**.

   ![Supprimer une affectation dans la page de la vue d’ensemble de la conformité](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez affecté une définition de stratégie intégrée à une étendue et vous avez évalué son rapport de conformité. La définition de stratégie permet de vérifier que toutes les ressources dans l’étendue sont conformes, ainsi que d’identifier celles qui ne le sont pas.

Pour en savoir plus sur l’affectation de stratégies visant à vérifier que les nouvelles ressources sont conformes, suivez le tutoriel :

> [!div class="nextstepaction"]
> [Création et gestion des stratégies](./tutorials/create-and-manage.md)