---
title: "Didacticiel : Configurer Zscaler One pour l'approvisionnement automatique d’utilisateurs avec Azure Active Directory | Microsoft Docs"
description: Découvrez comment configurer Azure Active Directory pour approvisionner et déprovisionner automatiquement des comptes d’utilisateur sur Zscaler One.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 3d6210fa215792fc7ff049eb597df5905926c193
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670893"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Didacticiel : Configurer Zscaler One pour l'approvisionnement automatique d’utilisateurs

Ce tutoriel présente les étapes à effectuer dans Zscaler One et Azure Active Directory (Azure AD) afin de configurer Azure AD pour provisionner et déprovisionner automatiquement des utilisateurs et des groupes sur Zscaler One.

> [!NOTE]
> Ce tutoriel décrit un connecteur reposant sur le service d'approvisionnement d’utilisateurs Azure AD. Pour plus d’informations sur l’objet et le fonctionnement de ce service et pour accéder au forum aux questions, consultez [Automatisation de l’approvisionnement et du déprovisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](../active-directory-saas-app-provisioning.md).
>
> Ce connecteur est actuellement disponible en préversion. Pour plus d’informations sur les conditions d’utilisation Microsoft Azure générales relatives aux fonctionnalités d'évaluation, consultez [Conditions d’utilisation supplémentaires des préversions Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un locataire Azure AD.
* Locataire Zscaler One.
* Un compte d’utilisateur Zscaler One ayant des autorisations d’administrateur.

> [!NOTE]
> L’intégration du provisionnement Azure AD repose sur l’API SCIM Zscaler One. Cette API est disponible pour les développeurs Zscaler One disposant de comptes avec le package Entreprise.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Ajouter Zscaler One à partir de la Place de marché Azure

Avant de configurer Zscaler One pour l’approvisionnement automatique d’utilisateurs avec Azure AD, ajoutez Zscaler One à partir de la Place de marché Azure à votre liste d’applications SaaS managées.

Pour ajouter Zscaler One à partir de la Place de marché, procédez comme suit.

1. Dans le volet de navigation de gauche du [Portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**.

    ![Icône Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter une application, sélectionnez **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, entrez **Zscaler One**, puis, dans le panneau des résultats, sélectionnez **Zscaler One**. Pour ajouter l’application, sélectionnez **Ajouter**.

    ![Zscaler One dans la liste des résultats](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Attribuer des utilisateurs à Zscaler One

Azure Active Directory utilise un concept appelé *affectations* pour déterminer les utilisateurs devant recevoir l’accès aux applications sélectionnées. Dans le cadre de l'approvisionnement automatique d’utilisateurs, seuls les utilisateurs ou groupes affectés à une application dans Azure AD sont synchronisés.

Avant de configurer et d’activer l'approvisionnement automatique d’utilisateurs, identifiez les utilisateurs ou groupes dans Azure AD qui doivent accéder à Zscaler One. Pour affecter ces utilisateurs ou groupes à Zscaler One, suivez les instructions dans [Affecter un utilisateur ou un groupe à une application d’entreprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Conseils importants pour l’attribution d’utilisateurs à Zscaler One

* Nous vous recommandons d’attribuer un seul utilisateur Azure AD à Zscaler One afin de tester la configuration de l'approvisionnement automatique d’utilisateurs. Vous pouvez affecter des groupes ou des utilisateurs supplémentaires ultérieurement.

* Quand vous attribuez un utilisateur à Zscaler One, vous devez sélectionner un rôle d’application valide, si disponible, dans la boîte de dialogue d’attribution. Les utilisateurs dont le rôle est **Accès par défaut** sont exclus de l’approvisionnement.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Configurer l'approvisionnement automatique d’utilisateurs sur Zscaler One

Cette section vous guide tout au long des étapes de configuration du service d’approvisionnement Azure AD. Utilisez-le pour créer, mettre à jour et désactiver des utilisateurs ou des groupes dans Zscaler One en fonction des affectations d’utilisateurs ou de groupes dans Azure AD.

> [!TIP]
> Vous pouvez également activer l’authentification unique SAML pour Zscaler One. Suivez les instructions fournies dans le [tutoriel sur la l’authentification unique pour Zscaler One](zscaler-One-tutorial.md). L’authentification unique peut être configurée indépendamment de l’approvisionnement automatique d’utilisateurs, bien que ces deux fonctionnalités se complètent.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Configurer l'approvisionnement automatique d’utilisateurs pour Zscaler One dans Azure AD

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Sélectionnez **Applications d’entreprise** > **Toutes les applications** > **Zscaler One**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **Zscaler One**.

    ![Lien Zscaler One dans la liste des applications](common/all-applications.png)

3. Sélectionnez l’onglet **Approvisionnement**.

    ![Approvisionnement de Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Définissez le **Mode d’approvisionnement** sur **Automatique**.

    ![Mode d'approvisionnement de Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Dans la section **Informations d’identification d’administrateur**, complétez les zones **URL de locataire** et **Jeton secret** avec les paramètres de votre compte Zscaler One, comme à l’étape 6.

6. Pour obtenir l'URL de locataire et le jeton secret, accédez à **Administration** > **Paramètres d'authentification** dans l'interface utilisateur du portail Zscaler One. Sous **Authentication Type** (Type d’authentification), sélectionnez **SAML**.

    ![Paramètres d’authentification de Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Sélectionnez **Configurer SAML** pour accéder aux options **Configurer SAML**.

    ![Zscaler One - Configurer SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Sélectionnez **Activer l'approvisionnement SCIM** pour obtenir les paramètres dans **URL de base** et **Jeton du porteur**. Enregistrez ensuite les paramètres. Dans le portail Azure, copiez le paramètre **URL de base** dans **URL de locataire**. Dans le portail Azure, copiez **Jeton du porteur** dans **Jeton secret**.

7. Après avoir complété les zones présentées à l’étape 5, sélectionnez **Tester la connexion** pour vérifier qu’Azure AD peut se connecter à Zscaler One. Si la connexion échoue, vérifiez que votre compte Zscaler One dispose des autorisations d’administrateur et réessayez.

    ![Tester la connexion Zscaler One](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Dans la zone **E-mail de notification**, entrez l’adresse e-mail de la personne ou du groupe qui doit recevoir les notifications d’erreur d’approvisionnement. Cochez la case **Envoyer une notification par e-mail en cas de défaillance**.

    ![E-mail de notification Zscaler One](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Sélectionnez **Enregistrer**.

10. Dans la section **Mappages**, sélectionnez **Synchroniser les utilisateurs Azure Active Directory avec Zscaler One**.

    ![Synchronisation des utilisateurs Zscaler One](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Dans la section **Mappages des attributs**, passez en revue les attributs d’utilisateur qui sont synchronisés entre Azure AD et Zscaler One. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondre des comptes d’utilisateur dans Zscaler One dans le cadre des opérations de mise à jour. Pour enregistrer les modifications, sélectionnez **Enregistrer**.

    ![Zscaler One : mise en correspondance des attributs utilisateur](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Dans la section **Mappages**, sélectionnez **Synchroniser les groupes Azure Active Directory avec Zscaler One**.

    ![Synchronisation des groupes Zscaler One](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Dans la section **Mappages des attributs**, passez en revue les attributs de groupe qui sont synchronisés entre Azure AD et Zscaler One. Les attributs sélectionnés comme propriétés de **Correspondance** sont utilisés pour la mise en correspondre des groupes dans Zscaler One dans le cadre des opérations de mise à jour. Pour enregistrer les modifications, sélectionnez **Enregistrer**.

    ![Zscaler One : mise en correspondance des attributs de groupes](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Pour configurer des filtres d’étendue, suivez les instructions fournies dans le [tutoriel sur les filtres d’étendue](./../active-directory-saas-scoping-filters.md).

15. Pour activer le service d’approvisionnement Azure AD pour Zscaler One, définissez **État de l'approvisionnement** sur **Activé** dans la section **Paramètres**.

    ![État de l'approvisionnement de Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Définissez les utilisateurs ou groupes que vous souhaitez approvisionner dans Zscaler One. Dans la section **Paramètres**, sélectionnez les valeurs souhaitées sous **Étendue**.

    ![Étendue Zscaler One](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Lorsque vous êtes prêt à procéder à l'approvisionnement, sélectionnez **Enregistrer**.

    ![Sauvegarde Zscaler One](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Cette opération démarre la synchronisation initiale de tous les utilisateurs ou groupes définis sous **Étendue** dans la section **Paramètres**. La synchronisation initiale prend plus de temps que les synchronisations ultérieures. Elles se produisent toutes les 40 minutes environ, tant que le service d’approvisionnement Azure AD s’exécute. 

Vous pouvez utiliser la section **Détails de la synchronisation** pour surveiller la progression et suivre les liens vers le rapport d’activité d’approvisionnement. Ce rapport décrit toutes les actions effectuées par le service d’approvisionnement Azure AD sur Zscaler One.

Pour avoir des informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Gestion du provisionnement de comptes d’utilisateur pour les applications d’entreprise](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez comment consulter les journaux d’activité et obtenir des rapports sur l’activité d’approvisionnement](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
