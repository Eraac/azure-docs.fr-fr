---
title: Utiliser Azure PowerShell pour gérer les droits d’accès Azure AD aux données blob et de file d’attente avec RBAC - Stockage Azure
description: Utilisez Azure PowerShell pour octroyer l’accès aux conteneurs et files d’attente avec le contrôle d’accès en fonction du rôle (RBAC). Le Stockage Azure prend en charge les rôles RBAC intégrés et personnalisés pour l’authentification via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bf888b72cca806822ca7a37542e71a5be0c8d5c3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443720"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-powershell"></a>Octroyer l’accès aux données blob et de file d’attente Azure avec RBAC à l’aide de PowerShell

Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées via [RBAC (contrôle d’accès en fonction du rôle)](../../role-based-access-control/overview.md). Le Stockage Azure définit un ensemble de rôles RBAC intégrés qui englobent les ensembles communs d’autorisations permettant d’accéder aux conteneurs ou aux files d’attente. 

Lorsqu’un rôle RBAC est attribué à un principal de sécurité Azure AD, Azure octroie l’accès à ces ressources pour ce principal de sécurité. L’accès peut être limité au niveau de l’abonnement, du groupe de ressources, du compte de stockage ou d’un conteneur ou d’une file d’attente individuelle. Un principal de sécurité Azure AD peut correspondre à un utilisateur, un groupe ou un principal du service d’application, ou à une [identité managée pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Cet article décrit comment utiliser Azure PowerShell pour répertorier les rôles RBAC intégrés et les attribuer aux utilisateurs. Pour plus d’informations sur l’utilisation Azure PowerShell, consultez la [Vue d’ensemble d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Rôles RBAC pour objets blob et files d’attente

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Déterminer l’étendue de la ressource

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Répertorier les rôles RBAC disponibles

Pour répertorier les rôles RBAC intégrés disponibles avec Azure PowerShell, utilisez la commande [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) :

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Les rôles de données Stockage Azure intégrés sont répertoriés, ainsi que d’autres rôles intégrés pour Azure :

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Attribuer un rôle RBAC à un principal de sécurité

Pour attribuer un rôle RBAC à un principal de sécurité, utilisez la commande [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Le format de la commande peut varier selon l’étendue de l’affectation. Les exemples suivants montrent comment attribuer un rôle à un utilisateur dans des étendues diverses, mais vous pouvez utiliser la même commande pour attribuer un rôle à n’importe quel principal de sécurité.

### <a name="container-scope"></a>Étendue du conteneur

Pour affecter un rôle limité à un conteneur, spécifiez une chaîne contenant l’étendue du conteneur pour le paramètre `--scope`. L’étendue d’un conteneur a la forme :

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

L’exemple suivant affecte le rôle **Contributeur aux données Blob du stockage** à un utilisateur, limité à un conteneur nommé *sample-container*. Veillez à remplacer les valeurs de l’exemple et les valeurs d’espace réservé entre les crochets par vos propres valeurs : 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Étendue de la file d’attente

Pour affecter un rôle limité à une file d’attente, spécifiez une chaîne contenant l’étendue de la file d’attente pour le paramètre `--scope`. L’étendue d’une file d’attente a la forme :

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

L’exemple suivant affecte le rôle **Contributeur aux données en file d’attente du stockage** à un utilisateur, limité à une file d’attente nommée *sample-queue*. Veillez à remplacer les valeurs de l’exemple et les valeurs d’espace réservé entre les crochets par vos propres valeurs : 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Étendue du compte de stockage

Pour affecter un rôle limité au compte de stockage, spécifiez l’étendue de la ressource de compte de stockage pour le paramètre `--scope`. L’étendue d’un compte de stockage a la forme :

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

L’exemple suivant montre comment étendre l’attribution du rôle **Lecteur des données Blob du stockage** à un utilisateur au niveau du compte de stockage. N’oubliez pas de remplacer les valeurs de l’exemple par vos propres valeurs : 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Étendue du groupe de ressources

Pour affecter un rôle limité au groupe de ressources, spécifiez le nom ou l’ID du groupe de ressources pour le paramètre `--resource-group`. L’exemple suivant affecte le rôle **Lecteur des données en file d’attente du stockage** à un utilisateur au niveau du groupe de ressources. Veillez à remplacer les valeurs de l’exemple et les valeurs d’espace réservé entre les crochets par vos propres valeurs : 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Étendue d’abonnement

Pour affecter un rôle limité à l’abonnement, spécifiez l’étendue de l’abonnement pour le paramètre `--scope`. L’étendue d’un abonnement a la forme :

```
/subscriptions/<subscription>
```

L’exemple suivant montre comment affecter le rôle **Lecteur des données Blob du stockage** à un utilisateur au niveau du compte de stockage. N’oubliez pas de remplacer les valeurs de l’exemple par vos propres valeurs : 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Étapes suivantes

- [Gérer l’accès aux ressources Azure à l’aide du contrôle RBAC et d’Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Octroyer l’accès aux données d’objet blob et de file d’attente Azure avec RBAC à l’aide d’Azure CLI](storage-auth-aad-rbac-cli.md)
- [Octroyer l’accès aux données d’objet blob et de file d’attente Azure avec RBAC dans le Portail Azure](storage-auth-aad-rbac-portal.md)
