---
title: 'Appartenance à un groupe : remplacer le type statique par le type dynamique - Azure Active Directory | Microsoft Docs'
description: Comment créer des règles d’appartenance pour remplir automatiquement des groupes, et documentation de référence sur les règles.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd753ca4994975302a0bc6fede61964f80196d7c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60472081"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Changer l’appartenance au groupe statique en dynamique dans Azure Active Directory

Vous pouvez changer l’appartenance à un groupe de statique en dynamique (ou vice versa) dans Azure Active Directory (Azure AD). Azure AD conserve le même nom de groupe et ID dans le système. Par conséquent, toutes les références existantes au groupe sont toujours valides. Si, au lieu de cela, vous créez un groupe, vous devez mettre à jour ces références. L’appartenance au groupe dynamique élimine la surcharge de gestion lors de l’ajout et la suppression d’utilisateurs. Cet article vous indique comment convertir des groupes existants d’appartenance statique en appartenance dynamique à l’aide du centre d’administration d’Azure AD ou d’applets de commande PowerShell.

> [!WARNING]
> Quand vous transformez un groupe statique existant en groupe dynamique, tous les membres existants sont supprimés du groupe, puis la règle d’appartenance est traitée pour ajouter de nouveaux membres. Si le groupe est utilisé pour contrôler l’accès aux applications ou aux ressources, n’oubliez pas que les membres d’origine peuvent perdre leur accès tant que la règle d’appartenance n’a pas été totalement traitée.
>
> Nous vous recommandons de tester la nouvelle règle d’appartenance au préalable pour vous assurer que la nouvelle appartenance du groupe est conforme à votre attente.

## <a name="change-the-membership-type-for-a-group"></a>Changer le type d’appartenance pour un groupe

1. Connectez-vous au [centre d’administration Azure AD](https://aad.portal.azure.com) en utilisant un compte d’administrateur d’entreprise ou en tant qu’administrateur utilisateur dans votre locataire.
2. Sélectionnez **Groupes**.
3. Depuis la liste **Tous les groupes**, ouvrez le groupe que vous souhaitez modifier.
4. Sélectionner **Propriétés**.
5. Sur la page **Propriétés** du groupe, sélectionnez un **Type d’appartenance** entre Utilisateur affecté (statique) ou dynamique et Appareil dynamique, selon le type d’appartenance souhaité. Pour une appartenance dynamique, vous pouvez utiliser le générateur de règle pour sélectionner les options d’une règle simple, ou écrire vous-même une règle d’appartenance. 

Les étapes suivantes sont un exemple de modification de l’appartenance d’un groupe de statique à dynamique pour un groupe d’utilisateurs.

1. Sur la page **Propriétés** du groupe sélectionné, sélectionnez **Utilisateur dynamique** comme **type d’appartenance**. Cliquez ensuite sur Oui dans la boîte de dialogue expliquant les modifications apportées à l’appartenance au groupe pour continuer. 
  
   ![sélectionner utilisateur dynamique comme type d’appartenance](./media/groups-change-type/select-group-to-convert.png)
  
2. Sélectionnez **Ajouter une requête dynamique**, puis ajoutez la règle.
  
   ![Entrez la règle pour le groupe dynamique](./media/groups-change-type/enter-rule.png)
  
3. Après avoir créé la règle, sélectionnez **Ajouter une requête** en bas de la page.
4. Sélectionnez **Enregistrer** sur la page **Propriétés** du groupe pour enregistrer vos modifications. Le **type d’appartenance** du groupe est immédiatement mis à jour dans la liste de groupes.

> [!TIP]
> La conversion d’un groupe peut échouer si la règle d’appartenance que vous avez entrée est incorrecte. Une notification s’affiche alors dans le coin supérieur droit du portail. Elle contient une explication de la raison pour laquelle la règle ne peut pas être acceptée par le système. Lisez-la avec attention pour comprendre comment vous pouvez ajuster la règle pour la rendre valide. Pour obtenir des exemples de syntaxe de règle et une liste complète des propriétés, opérateurs et valeurs pris en charge pour une règle d’appartenance, consultez [Règles d’appartenance dynamique pour les groupes dans Azure Active Directory](groups-dynamic-membership.md).

## <a name="change-membership-type-for-a-group-powershell"></a>Changer le type d’appartenance pour un groupe (PowerShell)

> [!NOTE]
> Pour modifier les propriétés de groupe dynamique, vous devez utiliser les applets de commande de la **préversion** d'[Azure AD PowerShell Version 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Vous pouvez exécuter la version préliminaire à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview).

Voici un exemple de fonctions qui permettent de changer la gestion des appartenances d’un groupe existant. Dans cet exemple, une attention particulière est nécessaire pour manipuler correctement la propriété GroupTypes et conserver toutes les valeurs qui ne sont pas liées à l’appartenance dynamique.

```powershell
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Pour rendre un groupe statique :

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Pour rendre un groupe dynamique :

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Étapes suivantes

Ces articles fournissent des informations supplémentaires sur les groupes dans Azure Active Directory.

* [Consulter les groupes existants](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Création d’un nouveau groupe et ajout de membres](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Gérer les paramètres d’un groupe](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Gérer l’appartenance à un groupe](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](groups-dynamic-membership.md)
