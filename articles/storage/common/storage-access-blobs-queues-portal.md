---
title: Utiliser le Portail Microsoft Azure pour accéder aux données d’objet blob et de file d’attente - Stockage Azure
description: Lorsque vous accéder aux données d’objet blob et de file d’attente à l’aide du Portail Microsoft Azure, le portail fait des requêtes à Azure Storage en arrière-plan. Ces requêtes au stockage Azure peuvent être authentifiées et autorisées à l’aide de votre compte Azure AD ou de la clé d’accès au compte de stockage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 00f34fa9a1932aebd467163e0ed7441c993387df
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154006"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Utiliser le Portail Microsoft Azure pour accéder aux données d’objet blob et de file d’attente

Lorsque vous accéder aux données d’objet blob et de file d’attente à l’aide du [Portail Microsoft Azure](https://portal.azure.com), le portail fait des requêtes à Azure Storage en arrière-plan. Ces requêtes au stockage Azure peuvent être authentifiées et autorisées à l’aide de votre compte Azure AD ou de la clé d’accès au compte de stockage. Le portail indique quelle méthode d’authentification vous utilisez et vous permet de basculer entre les deux si vous disposez des autorisations appropriées.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Autorisations nécessaires pour accéder aux données d’objet blob et de file d’attente

Selon la façon dont vous souhaitez authentifier l’accès aux données blob ou aux données de la file d’attente dans le Portail Microsoft Azure, vous devrez disposer d’autorisations spécifiques. Dans la plupart des cas, ces autorisations sont fournies via le contrôle d’accès en fonction du rôle (RBAC). Pour plus d’informations sur RBAC, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle (RBAC) ?](../../role-based-access-control/overview.md)

### <a name="account-access-key"></a>Clé d’accès au compte

Pour accéder aux données d’objet blob et de file d’attente avec la clé d’accès au compte, vous devez disposer d’un rôle RBAC qui vous est attribué et qui inclut l’action RBAC **Microsoft.Storage/storageAccounts/listkeys/action**. Ce rôle RBAC peut être intégré ou il peut s’agit d’un rôle personnalisé. Les rôles intégrés qui prennent en charge **Microsoft.Storage/storageAccounts/listkeys/action** incluent :

- Le rôle [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) d’Azure Resource Manager
- Le rôle [Contributeur](../../role-based-access-control/built-in-roles.md#contributor) d’Azure Resource Manager
- Le rôle [Contributeur de compte de stockage](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Lorsque vous tentez d’accéder aux données d’objet blob et de file d’attente dans le Portail Microsoft Azure, le portail commence par vérifier si un rôle RBAC vous a été attribué avec **Microsoft.Storage/storageAccounts/listkeys/action**. Si un rôle avec cette action vous a été attribué, le portail utilise la clé de compte pour l’accès aux données d’objet blob et de file d’attente. Si un rôle avec cette action ne vous a pas été attribué, le portail tente d’accéder aux données à l’aide de votre compte Azure AD.

> [!NOTE]
> Les rôles d’administrateur d’abonnement classique Administrateur de service et Co-administrateur incluent l’équivalent du rôle [Propriétaire](../../role-based-access-control/built-in-roles.md#owner) d’Azure Resource Manager. Le rôle **Propriétaire** inclut toutes les actions, y compris **Microsoft.Storage/storageAccounts/listkeys/action**, pour qu’un utilisateur avec l’un de ces rôles d’administration puisse accéder également aux données d’objet blob et de file d’attente avec la clé de compte. Pour plus d’informations, consultez [Rôles d’administrateur d’abonnements classiques](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Compte Azure AD

Pour accéder aux données d’objet blob ou de file d’attente à partir du Portail Microsoft Azure à l’aide de votre compte Azure AD, vous devez remplir ces deux conditions :

- Le rôle [Lecteur](../../role-based-access-control/built-in-roles.md#reader) Azure Resource Manager vous a été attribué, au moins, limité au niveau du compte de stockage ou à un niveau supérieur. Le rôle **Lecteur** octroie les autorisations les plus restreintes, mais l’utilisation d’un autre rôle Azure Resource Manager accordant l’accès aux ressources de gestion de compte de stockage est également acceptable.
- Un rôle intégré ou personnalisé qui fournit l’accès aux données d’objet blob ou de file d’attente vous a été attribué.

L’attribution de rôle **Lecteur** ou l’attribution d’un autre rôle Azure Resource Manager est nécessaire pour que l’utilisateur puisse afficher et parcourir les ressources de gestion du compte de stockage dans le Portail Microsoft Azure. Les rôles RBAC qui accordent l’accès aux données d’objet blob ou de file d’attente n’accordent pas d’accès aux ressources de gestion du compte de stockage. Pour accéder aux données d’objet blob ou de file d’attente dans le portail, l’utilisateur a besoin d’autorisations pour parcourir les ressources de compte de stockage. Pour plus d’informations sur cette exigence, consultez [Attribuer le rôle Lecteur pour accéder au portail](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Les rôles intégrés qui prennent en charge les accès à vos données d’objet blob ou de file d’attente sont les suivants :

- [Propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) : Pour le contrôle d’accès dans Azure Data Lake Storage Gen2.
- [Contributeur aux données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) : Autorisations en lecture/écriture/suppression pour les objets blob.
- [Lecteur des données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) : Autorisations en lecture seule pour les objets blob.
- [Contributeur aux données en file d’attente du stockage](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) : Autorisations en lecture/écriture/suppression pour les file d’attente.
- [Lecteur des données en file d’attente du stockage](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader) : Autorisations en lecture seule pour les files d’attente.
    
Les rôles personnalisés peuvent prendre en charge différentes combinaisons des mêmes autorisations fournies par les rôles intégrés. Pour plus d’informations sur la création des rôles RBAC personnalisés, consultez [Rôles personnalisés pour les ressources Azure](../../role-based-access-control/custom-roles.md) et [Comprendre les définitions de rôles pour les ressources Azure](../../role-based-access-control/role-definitions.md).

> [!NOTE]
> Le référencement des files d’attente avec un rôle d’administrateur d’abonnement classique n’est pas pris en charge. Pour répertorier les files d’attente, un utilisateur doit leur avoir attribué le rôle **Lecteur** Azure Resource Manager, le rôle **Lecteur de données de file d’attente de stockage** ou le rôle **Contributeur aux données en file d’attente de stockage**.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Accéder aux objets blob ou aux files d’attente sur le portail

Pour afficher les données d’objet blob ou de file d’attente sur le portail, accédez à la **Vue d’ensemble** pour votre compte de stockage, puis cliquez sur les liens pour **Objets blob** ou **Files d’attente**. Vous pouvez également naviguer jusqu’aux sections **Service Blob** et **Service de file d’attente** dans le menu. 

![Accédez aux données d’objet blob et de file d’attente dans le Portail Microsoft Azure](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Déterminer la méthode d’authentification actuelle

Lorsque vous accédez à un conteneur ou à une file d’attente, le portail Azure indique si vous utilisez actuellement la clé d’accès au compte ou votre compte Azure AD pour vous authentifier.

Les exemples de cette section montrent l’accès à un conteneur et ses objets blob, mais le portail affiche le même message lorsque vous accédez à une file d’attente et à ses messages, ou lorsque vous répertoriez des files d’attente.

### <a name="account-access-key"></a>Clé d’accès au compte

Si vous vous authentifiez à l’aide de la clé d’accès au compte, vous verrez la méthode **Clé d’accès** spécifiée comme méthode d’authentification dans le portail :

![Accès actuel aux données du conteneur avec la clé de compte](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Pour passer à l’utilisation du compte Azure AD, cliquez sur le lien mis en surbrillance dans l’image. Si vous disposez des autorisations appropriées via les rôles RBAC qui vous sont attribués, vous pourrez poursuivre. Toutefois, si vous n’avez pas les autorisations appropriées, vous verrez un message d’erreur semblable à celui-ci :

![Erreur affichée si le compte Azure AD ne prend pas en charge l’accès](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Notez qu’aucun objet blob n’apparaît dans la liste si votre compte Azure AD ne dispose pas des autorisations pour les afficher. Cliquez sur le lien **Basculer sur clé d’accès** pour utiliser à nouveau la clé d’accès pour l’authentification.

### <a name="azure-ad-account"></a>Compte Azure AD

Si vous vous authentifiez à l’aide de votre compte Azure AD, vous verrez la méthode **Clé d’accès** spécifiée comme méthode d’authentification dans le portail :

![Accès actuel aux données du conteneur avec un compte Azure AD](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Pour passer à l’utilisation d’une clé d’accès au compte, cliquez sur le lien mis en surbrillance dans l’image. Si vous avez accès à la clé de compte, vous pourrez poursuivre. Toutefois, si vous n’avez pas accès à la clé de compte, vous verrez un message d’erreur semblable à celui-ci :

![Erreur affichée si vous n’avez pas accès à la clé de compte](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Notez qu’aucun objet blob n’apparaît dans la liste si vous n’avez pas accès aux clés de compte. Cliquez sur le lien **Basculer sur Compte d’utilisateur Azure AD** pour vous authentifier à nouveau.

## <a name="next-steps"></a>Étapes suivantes

- [Authentifier l’accès aux objets blob et aux files d’attente Azure à l’aide d’Azure Active Directory](storage-auth-aad.md)
- [Octroyer l’accès aux conteneurs et files d’attente Azure avec RBAC dans le portail Azure](storage-auth-aad-rbac-portal.md)
- [Octroyer l’accès aux données blob et de file d’attente Azure avec RBAC à l’aide d’Azure CLI](storage-auth-aad-rbac-cli.md)
- [Octroyer l’accès aux données blob et de file d’attente Azure avec RBAC à l’aide de PowerShell](storage-auth-aad-rbac-powershell.md)
