---
title: Guide pratique pour modifier, supprimer ou gérer vos groupes d’administration - Gouvernance Azure
description: Découvrez comment afficher, tenir, mettre à jour et supprimer votre hiérarchie de groupes d’administration.
author: rthorn17
ms.service: governance
ms.date: 05/22/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 028b4cbf62bf9ed0b3b38f54d3b787a8c1368da0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242947"
---
# <a name="manage-your-resources-with-management-groups"></a>Gérer vos ressources avec des groupes d’administration

Si votre organisation dispose de plusieurs abonnements, vous pouvez avoir besoin d’un moyen de gérer efficacement l’accès, les stratégies et la conformité de ces abonnements. Les groupes d’administration Azure fournissent un niveau d’étendue au-delà des abonnements. Vous organisez les abonnements en conteneurs appelés « groupes d’administration » et vous appliquez vos conditions de gouvernance aux groupes d’administration. Tous les abonnements d’un groupe d’administration héritent automatiquement des conditions appliquées à ce groupe d’administration.

Les groupes d’administration vous permettent une gestion de qualité professionnelle à grande échelle, quel que soit le type de vos abonnements.  Pour plus d’informations sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="change-the-name-of-a-management-group"></a>Modifier le nom d’un groupe d’administration

Vous pouvez modifier le nom du groupe d’administration en utilisant le portail, PowerShell ou Azure CLI.

### <a name="change-the-name-in-the-portal"></a>Modifier le nom dans le portail

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** > **Groupes d’administration**.

1. Sélectionnez le groupe d’administration à renommer.

1. Sélectionnez **Détails**.

1. Sélectionnez l’option **Renommer le groupe** en haut de la page.

   ![Option Renommer le groupe de la page Groupe d’administration](./media/detail_action_small.png)

1. Lorsque le menu s’ouvre, entrez le nouveau nom à afficher.

   ![Volet Renommer le groupe pour renommer le groupe d’administration](./media/rename_context.png)

1. Sélectionnez **Enregistrer**.

### <a name="change-the-name-in-powershell"></a>Modifier le nom dans PowerShell

Pour mettre à jour le nom d’affichage, utilisez **Update-AzManagementGroup**. Par exemple, pour remplacer le nom d'affichage du groupe d’administration « Contoso IT » par « Groupe Contoso », exécutez la commande suivante :

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Modifier le nom dans Azure CLI

Pour Azure CLI, utilisez la commande update.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Supprimer un groupe d’administration

Pour supprimer un groupe d’administration, les conditions suivantes doivent être remplies :

1. Le groupe d’administration ne contient pas de groupes d’administration enfants ni d’abonnements.

   - Pour déplacer un abonnement en dehors d’un groupe d’administration, consultez [Déplacer un abonnement vers un autre groupe d’administration](#move-subscriptions-in-the-hierarchy).

   - Pour déplacer un groupe d’administration vers un autre groupe d’administration, consultez [Déplacer des groupes d’administration dans la hiérarchie](#move-management-groups-in-the-hierarchy).

1. Vous avez des autorisations en écriture sur le groupe d'administration (« Propriétaire », « Contributeur » ou « Contributeur du groupe d’administration »). Pour connaître vos autorisations, sélectionnez le groupe d’administration, puis sélectionnez **IAM**. Pour en savoir plus sur les rôles RBAC, consultez [Gérer l’accès et les autorisations avec le contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Supprimer dans le portail

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** > **Groupes d’administration**.

1. Sélectionnez le groupe d’administration à supprimer.

1. Sélectionnez **Détails**.

1. Sélectionnez **Supprimer**.

    > [!TIP]
    > Si l’icône est désactivée, placez le curseur de la souris au-dessus d’elle pour en connaître la raison.

   ![Renommer l'option Groupe](./media/delete.png)

1. Une fenêtre s’ouvre pour que vous confirmiez la suppression du groupe d’administration.

   ![Fenêtre de confirmation de suppression du groupe](./media/delete_confirm.png)

1. Sélectionnez **Oui**.

### <a name="delete-in-powershell"></a>Supprimer dans PowerShell

Utilisez la commande **Remove-AzManagementGroup** dans PowerShell pour supprimer des groupes d’administration.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Supprimer dans Azure CLI

Avec Azure CLI, utilisez la commande az account management-group delete.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Afficher des groupes d’administration

Vous pouvez afficher tous les groupes pour lesquels vous avez un rôle RBAC direct ou hérité.  

### <a name="view-in-the-portal"></a>Afficher dans le portail

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** > **Groupes d’administration**.

1. La page de la hiérarchie des groupes de gestion se charge. Cette page vous permet d'explorer tous les groupes d’administration et abonnements auxquels vous avez accès. Sélectionner le nom du groupe vous fait descendre d’un niveau dans la hiérarchie. La navigation fonctionne comme dans un explorateur de fichiers.

1. Pour afficher les détails du groupe d’administration, sélectionnez le lien **(détails)** en regard du titre du groupe d’administration. Si ce lien n’est pas disponible, vous n’avez pas les autorisations pour afficher ce groupe d’administration.

   ![Principal](./media/main.png)

### <a name="view-in-powershell"></a>Afficher dans PowerShell

Utilisez la commande Get-AzManagementGroup pour récupérer tous les groupes.  Consultez les modules [Az.Resources](/powershell/module/az.resources/Get-AzManagementGroup) pour obtenir la liste complète des commandes GET PowerShell de groupe d'administration.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Pour consulter les détails d’un seul groupe d’administration, utilisez le paramètre -GroupName.

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Pour renvoyer un groupe d’administration spécifique et tous ses niveaux de hiérarchie sous-jacents, utilisez les paramètres **-Expand** and **-Recurse**.  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Afficher dans Azure CLI

Utilisez la commande list pour récupérer tous les groupes.  

```azurecli-interactive
az account management-group list
```

Pour consulter les détails d’un seul groupe d’administration, utilisez la commande show.

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Pour renvoyer un groupe d’administration spécifique et tous ses niveaux de hiérarchie sous-jacents, utilisez les paramètres **-Expand** and **-Recurse**.

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="move-subscriptions-in-the-hierarchy"></a>Déplacer des abonnements dans la hiérarchie

L’une des raisons de créer un groupe d’administration est de regrouper des abonnements. Seuls les groupes d’administration et les abonnements peuvent être enfants d’un autre groupe d’administration. Un abonnement déplacé vers un groupe d’administration hérite de toutes les stratégies et de tous les accès utilisateur du groupe d’administration parent.

Pour déplacer l’abonnement, toutes les autorisations RBAC suivantes doivent définies sur true :

- Rôle de « propriétaire » sur l’abonnement enfant.
- Rôle de « propriétaire », « contributeur » ou « contributeur du groupe d’administration » sur le groupe d'administration parent cible.
- Rôle de « propriétaire », « contributeur » ou « contributeur du groupe d’administration » sur le groupe d'administration parent existant.

Si le groupe d'administration parent cible ou existant correspond au groupe d'administration racine, les exigences en matière d'autorisations ne s'appliquent pas. Le groupe d’administration racine correspondant à l'emplacement de destination de tous les nouveaux groupes d’administration et abonnements, vous ne devez pas disposer d'autorisations sur ce dernier pour déplacer un élément.

Si le rôle Propriétaire de l'abonnement est hérité du groupe d’administration actuel, vos cibles de déplacement sont limitées. Vous pouvez uniquement déplacer l’abonnement vers un autre groupe d’administration pour lequel vous détenez le rôle Propriétaire. Vous ne pouvez pas le déplacer vers un groupe d’administration pour lequel vous détenez un rôle Contributeur si vous perdez la propriété de l’abonnement. Si vous vous voyez attribuer directement le rôle Propriétaire de l'abonnement (non hérité du groupe d’administration), vous pouvez le déplacer vers un groupe d’administration au sein duquel vous détenez un rôle Contributeur.

Pour connaître vos autorisations dans le portail Azure, sélectionnez le groupe d’administration, puis sélectionnez **IAM**. Pour en savoir plus sur les rôles RBAC, consultez [Gérer l’accès et les autorisations avec le contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Déplacer des abonnements dans le portail

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Ajouter un abonnement existant à un groupe d’administration

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** > **Groupes d’administration**.

1. Sélectionnez le groupe d’administration que vous envisagez d’utiliser comme parent.

1. En haut de la page, sélectionnez **Ajouter un abonnement**.

1. Sélectionnez l’abonnement dans la liste portant le bon ID.

   ![Abonnements disponibles pour ajouter un groupe d’administration](./media/add_context_sub.png)

1. Sélectionnez « Enregistrer ».

#### <a name="remove-a-subscription-from-a-management-group"></a>Supprimer un abonnement d’un groupe d’administration

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** > **Groupes d’administration**.

1. Sélectionnez le groupe d’administration qui est le parent actuel.  

1. Dans la liste, sélectionnez les points de suspension situés en fin de la ligne de l’abonnement à déplacer.

   ![Option Déplacer dans un groupe d’administration](./media/move_small.png)

1. Sélectionnez **Déplacer**.

1. Dans le menu qui s’ouvre, sélectionnez le **groupe d’administration parent**.

   ![Volet Déplacer pour modifier le groupe parent](./media/move_small_context.png)

1. Sélectionnez **Enregistrer**.

### <a name="move-subscriptions-in-powershell"></a>Déplacer des abonnements dans PowerShell

Pour déplacer un abonnement dans PowerShell, utilisez la commande New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Pour supprimer le lien entre un abonnement et un groupe d’administration, utilisez la commande Remove-AzManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Déplacer des abonnements dans Azure CLI

Pour déplacer un abonnement dans CLI, utilisez la commande add.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Pour supprimer l’abonnement du groupe d’administration, utilisez la commande subscription remove.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>Déplacer des groupes d’administration dans la hiérarchie  

Lorsque vous déplacez un groupe d’administration parent, la hiérarchie située sous ce groupe se déplace avec lui. Pour plus d'informations sur l'accès dont vous avez besoin pour déplacer des groupes d’administration, consultez [Accès aux groupes d’administration](index.md#management-group-access).

### <a name="move-management-groups-in-the-portal"></a>Déplacer des groupes d’administration dans le portail

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** > **Groupes d’administration**.

1. Sélectionnez le groupe d’administration que vous envisagez d’utiliser comme parent.

1. En haut de la page, sélectionnez **Ajouter un groupe d’administration**.

1. Dans le menu qui s’ouvre, indiquez si vous souhaitez créer un groupe d’administration ou en utiliser un existant.

   - Si vous sélectionnez Nouveau, vous créez un groupe d’administration.
   - Si vous sélectionnez un groupe existant, une liste déroulante répertoriant tous les groupes d’administration s’affiche. Vous pouvez les déplacer vers ce groupe d’administration.  

   ![Déplacer un groupe d’administration vers un groupe nouveau ou existant](./media/add_context_MG.png)

1. Sélectionnez **Enregistrer**.

### <a name="move-management-groups-in-powershell"></a>Déplacer des groupes d’administration dans PowerShell

Utilisez la commande Update-AzManagementGroup dans PowerShell pour déplacer un groupe d’administration sous un autre groupe.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Déplacer des groupes d’administration dans Azure CLI

Utilisez la commande update pour déplacer un groupe d’administration avec Azure CLI.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Auditer les groupes d’administration à l’aide des journaux d’activité

Les groupes d’administration sont pris en charge dans le [journal d’activité Azure](../../azure-monitor/platform/activity-logs-overview.md). Vous pouvez interroger tous les événements qui se produisent dans un groupe d’administration au même emplacement central, tout comme d’autres ressources Azure.  Par exemple, vous pouvez voir tous les changements d’attributions de rôles ou de stratégie apportés à un groupe d’administration spécifique.

![Journaux d’activité avec les groupes d’administration](media/al-mg.png)

Quand vous cherchez à interroger les groupes d’administration en dehors du portail Azure, l’étendue cible pour les groupes d’administration ressemble à **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** .

## <a name="referencing-management-groups-from-other-resource-providers"></a>Référencer des groupes d’administration à partir d’autres fournisseurs de ressources

Lorsque vous référencez des groupes d’administration à partir d'actions d'un autre fournisseur de ressources, utilisez le chemin suivant en tant qu'étendue. Ce chemin d’accès est utilisé avec PowerShell, Azure CLI et les API REST.  

>"/providers/Microsoft.Management/managementGroups/{yourMgID}"

À titre d'exemple, vous pouvez utiliser ce chemin d'accès lors de l'attribution d'un nouveau rôle à un groupe d’administration dans PowerShell

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Le même chemin d'étendue est utilisé lors de la récupération d’une définition de stratégie auprès d'un groupe d’administration.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les groupes d’administration, consultez :

- [Créer des groupes d’administration pour organiser les ressources Azure](create.md)
- [Guide pratique pour modifier, supprimer ou gérer vos groupes d’administration](manage.md)
- [Consulter les groupes d’administration dans le module Azure PowerShell Resources](/powershell/module/az.resources#resources)
- [Consulter les groupes d’administration dans l’API REST](/rest/api/resources/managementgroups)
- [Consulter les groupes d’administration dans Azure CLI](/cli/azure/account/management-group)