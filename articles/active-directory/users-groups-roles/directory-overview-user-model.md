---
title: Vue d’ensemble des utilisateurs, groupes, licences et rôles - Azure Active Directory | Microsoft Docs
description: Relation entre utilisateurs et licences assignées, rôles d’administrateur, appartenance à un groupe dans Azure Active Directory
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.reviewer: vincesm
ms.date: 01/28/2019
ms.topic: overview
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
services: active-directory
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2053ad07e555cc04cea714524141068f1404796
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083895"
---
# <a name="users-groups-licensing-and-roles-for-large-organizations"></a>Utilisateurs, groupes, licences et rôles pour grandes entreprises

Cet article présente à l’administrateur Azure AD la relation entre les principales tâches de [gestion des identités](/azure/active-directory/fundamentals/identity-fundamentals?context=azure/active-directory/users-groups-roles/context/ugr-context) pour les utilisateurs en ce qui concerne leurs groupes, licences, applications d’entreprise déployées et rôles d’administrateur. À mesure que votre entreprise se développe, vous pouvez utiliser des groupes et rôles d’administrateur Azure AD pour :

* Assigner des licences à des groupes plutôt qu’individuellement
* Déléguer des autorisations pour répartir le travail de gestion Azure AD à des rôles de moindre privilège
* Assigner l’accès aux applications d’entreprise à des groupes

## <a name="assign-users-to-groups"></a>Assigner des utilisateurs à des groupes

Vous pouvez utiliser des groupes dans Azure AD pour assigner des licences à un grand nombre d’utilisateurs, ou pour assigner l’accès utilisateur à des applications d’entreprise déployées. Vous pouvez utiliser des groupes pour assigner des rôles d’administrateur dans Azure AD, ou vous pouvez accorder l’accès à des ressources externes, comme des applications SaaS ou des sites SharePoint.

Pour plus de flexibilité et afin de réduire le travail de gestion de l’appartenance à un groupe, vous pouvez utiliser des [groupes dynamiques](groups-create-rule.md) dans Azure AD pour étendre et limiter automatiquement l’appartenance à un groupe. Vous aurez besoin d’une licence Azure AD Premium P1 pour chaque utilisateur unique membre d’un ou de plusieurs groupes dynamiques.

## <a name="assign-licenses-to-groups"></a>Assigner des licences à des groupes

L’affectation à ou la suppression de licences d’utilisateurs individuellement peut prendre du temps et monopoliser votre attention. Si, au lieu de cela, vous [assignez des licences à des groupes](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context), vous pouvez simplifier votre gestion des licences à grande échelle.

Dans Azure AD, lorsque des utilisateurs rejoignent un groupe sous licence, les licences appropriées leur sont automatiquement assignées. Lorsque les utilisateurs quittent le groupe, Azure AD supprime leurs affectations de licence. Sans groupes Azure AD, vous devriez écrire un script PowerShell ou utiliser l’API Graph pour ajouter ou supprimer en bloc des licences utilisateur pour les utilisateurs intégrant ou quittant l’entreprise.

Si le nombre de licences disponibles est insuffisant, ou si un problème se produit, comme des plans de services ne pouvant pas être assignés simultanément, vous pouvez voir l’état d’un quelconque problème de licence pour le groupe dans le Portail Azure.

>[!NOTE]
>La fonctionnalité de licences basées sur le groupe est actuellement disponible en préversion publique. Pendant la période de préversion, la fonctionnalité est disponible dans tout plan de licence Azure Active Directory (Azure AD) payant ou essai.

## <a name="delegate-administrator-roles"></a>Déléguer des rôles d’administrateur

Nombre de grandes entreprises souhaitent disposer d’options permettant à leurs utilisateurs d’obtenir les autorisations suffisantes pour réaliser leurs tâches professionnelles sans pour autant assigner le rôle Administrateur général, par exemple, aux utilisateurs devant inscrire des applications. Voici un exemple de nouveaux rôles d’administrateur Azure AD qui vous aideront à répartir de manière plus granulaire le travail de gestion des applications :

 Nom de rôle | Résumé des autorisations
 --------- | -------------------
 **Administrateur d’application** | Peut ajouter et gérer les applications d’entreprise et les inscriptions d’application, et configurer les paramètres de proxy d’application. Les administrateurs d’application peuvent consulter les stratégies d’accès conditionnel et les appareils, mais ne peuvent pas les gérer.
 **Administrateur d’application cloud** | Peut ajouter et gérer les applications d’entreprise et les inscriptions d’applications d’entreprise. Ce rôle dispose de toutes les autorisations de l’administrateur d’application, à l’exception près qu’il ne peut pas gérer les paramètres de proxy d’application.
**Développeur d’applications** | Peut ajouter et mettre à jour les inscriptions d’applications d’entreprise, mais ne peut pas gérer les applications d’entreprise ou configurer un proxy d’application.

De nouveaux rôles d’administrateur Azure AD sont ajoutés. Consultez le Portail Azure ou la [référence d’autorisation de rôle d’administrateur](directory-assign-admin-roles.md) pour les rôles disponibles actuels.

## <a name="assign-app-access"></a>Assigner l’accès aux applications

Vous pouvez utiliser Azure AD pour assigner l’accès de groupe aux [applications d’entreprise qui sont déployées dans votre locataire Azure AD](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups?context=azure/active-directory/users-groups-roles/context/ugr-context). Si vous combinez des groupes dynamiques avec affectation de groupe aux applications, vous pouvez automatiser vos affectations d’accès utilisateur aux applications à mesure que votre entreprise se développe. Vous aurez besoin d’une licence Azure Active Directory Premium P1 ou Premium P2 pour assigner l’accès aux applications d’entreprise.

Azure AD vous permet également de contrôler plus précisément les données qui transitent entre l’application et les groupes auxquels vous assignez l’accès. Dans [Applications d’entreprise](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps), ouvrez une application et sélectionnez **Provisioning** (Approvisionnement) pour :

* Configurer l’approvisionnement automatique pour les applications qui le prennent en charge
* Fournir des informations d’identification pour se connecter à l’API de gestion des utilisateurs de l’application
* Configurer les mappages qui contrôlent les attributs utilisateur qui transitent entre Azure AD et l’application lorsque des comptes d’utilisateur sont approvisionnés ou mis à jour
* Démarrer et arrêter le service d’approvisionnement Azure AD pour une application, effacer le cache d’approvisionnement ou redémarrer le service
* Afficher le **rapport d’activité d’approvisionnement** qui fournit un journal de tous les utilisateurs et groupes créés, mis à jour et supprimés entre Azure AD et l’application, et le **rapport d’erreurs d’approvisionnement** qui fournit des messages d’erreur plus détaillés

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes un administrateur Azure AD novice, découvrez les principes de base dans [Azure Active Directory Fundamentals](https://docs.microsoft.com/azure/active-directory/fundamentals/index) (Notions de base d’Azure Active Directory).

Vous pouvez également commencer à [créer des groupes](/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context), [assigner des licences](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context), [assigner l’accès aux applications](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups?context=azure/active-directory/users-groups-roles/context/ugr-context) ou [assigner des rôles d’administrateur](directory-assign-admin-roles.md).
