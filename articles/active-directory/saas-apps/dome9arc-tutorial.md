---
title: 'Didacticiel : Intégration d’Azure Active Directory à Check Point CloudGuard Dome9 Arc | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Check Point CloudGuard Dome9 Arc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdaaab8257d3a79130902e1ba0466f9cf15484f4
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147208"
---
# <a name="tutorial-integrate-check-point-cloudguard-dome9-arc-with-azure-active-directory"></a>Didacticiel : Intégrer Check Point CloudGuard Dome9 Arc à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer Check Point CloudGuard Dome9 Arc à Azure Active Directory (Azure AD). Quand vous intégrez Check Point CloudGuard Dome9 Arc à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Check Point CloudGuard Dome9 Arc
* Permettre aux utilisateurs de se connecter automatiquement à Check Point CloudGuard Dome9 Arc à l’aide de leurs comptes Azure AD
* Gérer vos comptes à un emplacement central : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).
* Un abonnement Check Point CloudGuard Dome9 Arc pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. Check Point CloudGuard Dome9 Arc prend en charge l’authentification unique lancée par **l’ISP (fournisseur de services) et l’IDP (fournisseur d’identité)** .

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Ajout de Check Point CloudGuard Dome9 Arc à partir de la galerie

Pour configurer l’intégration de Check Point CloudGuard Dome9 Arc à Azure AD, vous devez ajouter Check Point CloudGuard Dome9 Arc à votre liste d’applications SaaS managées à partir de la galerie.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Check Point CloudGuard Dome9 Arc** dans la zone de recherche.
1. Sélectionnez **Check Point CloudGuard Dome9 Arc** dans le volet de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Configurez et testez l’authentification unique Azure AD avec Check Point CloudGuard Dome9 Arc pour un utilisateur de test nommé **B.Simon**. Pour que l’authentification unique fonctionne, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Check Point CloudGuard Dome9 Arc associé.

Pour configurer et tester l’authentification unique Azure AD avec Check Point CloudGuard Dome9 Arc, suivez les indications des modules ci-dessous :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurez Check Point CloudGuard Dome9 Arc](#configure-check-point-cloudguard-dome9-arc)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créez un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec B.Simon.
4. **[Affectez l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à B.Simon d’utiliser l’authentification unique Azure AD.
5. **[Créez l’utilisateur de test Check Point CloudGuard Dome9 Arc](#create-check-point-cloudguard-dome9-arc-test-user)** pour avoir un équivalent de B.Simon dans Check Point CloudGuard Dome9 Arc lié à la représentation Azure AD de cet utilisateur.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le Portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), accédez à la page d’intégration de l’application **Check Point CloudGuard Dome9 Arc**, recherchez la section **Gérer**, puis sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, si vous souhaitez configurer l’application en mode lancé par le **fournisseur d’identité**, effectuez les étapes suivantes :

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://secure.dome9.com/`

    b. Dans la zone de texte **URL de réponse**, tapez une URL au format suivant : `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Vous allez sélectionner le nom de votre société dans le portail d’administration dome9, comme expliqué plus loin dans le didacticiel.

5. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de services**, cliquez sur **Définir des URL supplémentaires**, puis effectuez les étapes suivantes :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez [l’équipe du support technique Check Point CloudGuard Dome9 Arc](mailto:Dome9@checkpoint.com). Vous pouvez également consulter les modèles figurant à la section **Configuration SAML de base** dans le portail Azure.

6. L’application Check Point CloudGuard Dome9 Arc s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à la configuration de vos attributs de jeton SAML. La capture d’écran suivante montre la liste des attributs par défaut. Cliquez sur l’icône  **Modifier** pour ouvrir la boîte de dialogue Attributs d’utilisateur.

    ![image](common/edit-attribute.png)

7. En plus de ce qui précède, l’application Check Point CloudGuard Dome9 Arc s’attend à ce que quelques attributs supplémentaires soient passés dans la réponse SAML. Dans la section **Revendications des utilisateurs** de la boîte de dialogue **Attributs utilisateur**, effectuez les étapes suivantes pour ajouter le jeton SAML comme indiqué dans le tableau ci-dessous : 

    | Nom |  Attribut source|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    a. Cliquez sur le bouton **Ajouter une nouvelle revendication** pour ouvrir la boîte de dialogue **Gérer les revendications des utilisateurs**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Laissez le champ **Espace de noms** vide.

    d. Sélectionnez Source comme **Attribut**.

    e. Dans la liste **Attribut de la source**, tapez la valeur d’attribut indiquée pour cette ligne.

    f. Cliquez sur **OK**.

    g. Cliquez sur **Enregistrer**.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, recherchez **Certificat (Base64)** , puis sélectionnez **Télécharger** pour télécharger le certificat et l’enregistrer sur votre ordinateur.

   ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Check Point CloudGuard Dome9 Arc**, copiez les URL appropriées en fonction des exigences.

   ![Copier les URL de configuration](common/copy-configuration-urls.png)

### <a name="configure-check-point-cloudguard-dome9-arc"></a>Configurer Check Point CloudGuard Dome9 Arc

1. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Check Point CloudGuard Dome9 Arc en tant qu’administrateur.

2. Cliquez sur les **paramètres du profil** dans l’angle supérieur droit puis cliquez sur les **paramètres du compte**. 

    ![Configuration de Check Point CloudGuard Dome9 Arc](./media/dome9arc-tutorial/configure1.png)

3. Accédez à **SSO**, puis cliquez sur **ACTIVER**.

    ![Configuration de Check Point CloudGuard Dome9 Arc](./media/dome9arc-tutorial/configure2.png)

4. Dans la section de la configuration de l’authentification unique, effectuez les étapes suivantes :

    ![Configuration de Check Point CloudGuard Dome9 Arc](./media/dome9arc-tutorial/configure3.png)

    a. Entrez le nom de la société dans la zone de texte **Account ID** (ID de compte). Cette valeur doit être utilisée dans l’URL de réponse mentionnée dans la section **Configuration SAML de base** du portail Azure.

    b. Dans la zone de texte **Issuer (Émetteur)** , collez la valeur de l’**identificateur Azure AD** que vous avez copiée à partir du portail Azure.

    c. Dans la zone de texte **Idp endpoint url** (URL du point de terminaison IDP), collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure.

    d. Ouvrez le certificat codé en base64 que vous avez téléchargé dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **X.509 certificate** (Certificat X.509).

    e. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé B.Simon dans le portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `B.Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `B.Simon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B.Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Check Point CloudGuard Dome9 Arc.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Check Point CloudGuard Dome9 Arc**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B.Simon** parmi la liste des utilisateurs, puis cliquez au bas de l’écran sur le bouton **Sélectionner**.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Créer un utilisateur de test Check Point CloudGuard Dome9 Arc

Pour permettre aux utilisateurs Azure AD de se connecter à Check Point CloudGuard Dome9 Arc, vous devez les provisionner dans l’application. Check Point CloudGuard Dome9 Arc prend en charge le provisionnement juste-à-temps. Mais pour que cela fonctionne correctement, l’utilisateur doit sélectionner un **rôle** particulier et se l’attribuer.

   >[!Note]
   >Pour la création de **rôle** et pour d’autres informations, contactez [l’équipe du support technique Check Point CloudGuard Dome9 Arc](mailto:Dome9@checkpoint.com).

**Pour provisionner un compte d’utilisateur manuellement, effectuez les étapes suivantes :**

1. Connectez-vous au site d’entreprise Check Point CloudGuard Dome9 Arc en tant qu’administrateur.

2. Cliquez sur **Users & Roles** (Utilisateurs et rôles), puis cliquez sur **Users** (utilisateurs).

    ![Ajouter un employé](./media/dome9arc-tutorial/user1.png)

3. Cliquez sur**ADD USER** (Ajouter un utilisateur).

    ![Ajouter un employé](./media/dome9arc-tutorial/user2.png)

4. Dans la section **Create User** , procédez comme suit :

    ![Ajouter un employé](./media/dome9arc-tutorial/user3.png)

    a. Dans la zone de texte **Email**, entrez l’adresse e-mail de l’utilisateur, par exemple, B.Simon@contoso.com.

    b. Dans la zone de texte **First Name** (Prénom), tapez le prénom de l’utilisateur, par exemple Britta.

    c. Dans la zone de texte **Last Name**, entrez le nom de l’utilisateur, par exemple Simon.

    d. Définissez **SSO User** (SSO utilisateur) sur **On** (Activé).

    e. Cliquez sur **CREATE** (Créer).

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Quand vous sélectionnez la vignette Check Point CloudGuard Dome9 Arc dans le volet d’accès, vous devez être automatiquement connecté à l’application Check Point CloudGuard Dome9 Arc pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)