---
title: 'Didacticiel : Configurer ServiceNow pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs'
description: Découvrez comment approvisionner et annuler l’approvisionnement automatiquement des comptes utilisateur d’Azure AD vers ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19b3e4cc5ba4bc0173721947bd1e1a680ca7b3a3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60869830"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>Didacticiel : Configurer ServiceNow pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory

L’objectif de ce didacticiel est de vous montrer la procédure à suivre dans ServiceNow et Azure AD pour approvisionner et retirer automatiquement des comptes utilisateur Azure AD vers ServiceNow.

> [!NOTE]
> Ce didacticiel décrit un connecteur reposant sur le service d’attribution d’utilisateurs Azure AD. Pour découvrir les informations importantes sur ce que fait ce service, comment il fonctionne et consulter le forum aux questions, reportez-vous à l’article [Automatiser l’attribution et l’annulation de l’attribution des utilisateurs dans les applications SaaS avec Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à ServiceNow, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Pour ServiceNow, une instance ou un locataire ServiceNow, version Calgary ou supérieure
- Pour ServiceNow Express, une instance ServiceNow Express, version Helsinki ou supérieure

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).


## <a name="assigning-users-to-servicenow"></a>Affectation d’utilisateurs à ServiceNow

Azure Active Directory utilise un concept appelé « affectations » pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l’approvisionnement automatique des comptes d’utilisateur, seuls les utilisateurs et les groupes qui ont été « affectés » à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer le service d’approvisionnement, vous devez déterminer quels utilisateurs et/ou groupes dans Azure AD représentent les utilisateurs qui ont besoin d’accéder à votre application ServiceNow. Une fois que vous avez choisi, vous pouvez affecter ces utilisateurs à votre application ServiceNow en suivant les instructions fournies ici : [Affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   Il est recommandé de n’assigner qu’un seul utilisateur Azure AD à ServiceNow afin de tester la configuration de l’approvisionnement. Les autres utilisateurs et/ou groupes peuvent être affectés ultérieurement.
>*   Lorsque vous assignez un utilisateur à ServiceNow, vous devez sélectionner un rôle d’utilisateur valide. Le rôle « Accès par défaut » ne fonctionne pas pour l’approvisionnement.

## <a name="enable-automated-user-provisioning"></a>Activer l’approvisionnement automatique des utilisateurs

Cette section vous guide à travers la connexion de votre Azure AD au compte d’utilisateur de ServiceNow fournissant l’API et la configuration du service d’approvisionnement pour créer, mettre à jour et désactiver les comptes utilisateur affectés dans ServiceNow en fonction des attributions d’utilisateurs et de groupes dans Azure AD.

> [!TIP]
>Vous pouvez également choisir d’activer l’authentification unique basée sur SAML pour ServiceNow, grâce aux instructions disponibles dans le [portail Azure](https://portal.azure.com). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique, bien que chacune de ces deux fonctionnalités compléte l’autre.

### <a name="configure-automatic-user-account-provisioning"></a>Configurer l’approvisionnement automatique d’un compte utilisateur

1. Sur le [portail Azure](https://portal.azure.com), accédez à la section **Azure Active Directory > Applications d’entreprise > Toutes les applications**.

1. Si vous avez déjà configuré ServiceNow pour l’authentification unique, recherchez votre instance de ServiceNow à l’aide du champ de recherche. Sinon, sélectionnez **Ajouter** et effectuer une recherche pour **ServiceNow** dans la galerie d’applications. Dans les résultats de la recherche, sélectionnez ServiceNow, puis ajoutez-le à votre liste d’applications.

1. Sélectionnez votre instance de ServiceNow, puis sélectionnez l’onglet **Approvisionnement**.

1. Définissez le **Mode d’approvisionnement** sur **Automatique**. 

    ![approvisionnement](./media/servicenow-provisioning-tutorial/provisioning.png)

1. Dans la section Informations d’identification de l’administrateur, effectuez les étapes suivantes :
   
    a. Dans la zone de texte **Nom de l'Instance ServiceNow** , tapez le nom d'instance ServiceNow.

    b. Dans la zone de texte **Nom d’utilisateur admin ServiceNow** , tapez le nom d’utilisateur d’un administrateur.

    c. Dans la zone de texte **Mot de passe de l’admin ServiceNow** , tapez le mot de passe de l’administrateur.

1. Dans le portail Azure, cliquez sur **Tester la connexion** pour vous assurer qu’Azure AD peut se connecter à votre application ServiceNow. Si la connexion échoue, vérifiez que votre compte ServiceNow dispose des autorisations d’administrateur d’équipe, puis revenez à l’étape **Informations d’identification de l’administrateur**.

1. Entrez l’adresse e-mail d’une personne ou d’un groupe qui doit recevoir les notifications d’erreur d’approvisionnement dans le champ **E-mail de notification**, puis cochez la case.

1. Cliquez sur **Enregistrer.**

1. Dans la section Mappages, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec ServiceNow**.

1. Dans la section **Mappages des attributs**, passez en revue les attributs utilisateur qui sont synchronisés d’Azure AD vers ServiceNow. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les comptes d’utilisateur dans ServiceNow pour les opérations de mise à jour. Cliquez sur le bouton Enregistrer pour valider les modifications.

1. Pour activer le service d’approvisionnement Azure AD pour ServiceNow, modifiez le paramètre **État d’approvisionnement** sur **Activé** dans la section Paramètres.

1. Cliquez sur **Enregistrer.**

Cette commande démarre la synchronisation initiale des utilisateurs et/ou des groupes affectés à ServiceNow dans la section Utilisateurs et Groupes. La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent toutes les 40 minutes environ tant que le service est en cours d’exécution. Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers les journaux d’activité de provisionnement, qui décrivent toutes les actions effectuées par le service de provisionnement dans votre application ServiceNow.

Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)
* [Configurer l’authentification unique](servicenow-tutorial.md)


