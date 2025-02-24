---
title: 'Didacticiel : Intégration d’Azure Active Directory à Dropbox for Business | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 591d8d28be5fc9322de8cf4886dc5924f53b1deb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103784"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Didacticiel : Intégrer Dropbox for Business à Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer Dropbox for Business à Azure Active Directory (Azure AD). Quand vous intégrez Dropbox for Business à Azure AD, vous pouvez :

* Contrôler dans Azure AD qui a accès à Dropbox for Business.
* Permettre à vos utilisateurs de se connecter automatiquement à Dropbox for Business avec leur compte Azure AD.
* Gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous n’en avez pas, vous pouvez obtenir un essai gratuit d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement Dropbox for Business pour lequel l’authentification unique est activée.

## <a name="scenario-description"></a>Description du scénario

* Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. Dropbox for Business prend en charge l’authentification unique (SSO) initiée par le **fournisseur de services**

* Dropbox for Business prend en charge l’attribution d’utilisateurs **Juste-à-temps**

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Ajout de Dropbox for Business à partir de la galerie

Pour configurer l’intégration de Dropbox for Business à Azure AD, vous devez ajouter Dropbox for Business depuis la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **Dropbox for Business** dans la zone de recherche.
1. Sélectionnez **Dropbox for Business** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Configurez et testez l’authentification unique Azure AD avec Dropbox for Business à l’aide d’un utilisateur de test appelé **Britta Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur Dropbox for Business associé.

Pour configurer et tester l’authentification unique Azure AD avec Dropbox for Business, suivez les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique Dropbox for Business](#configure-dropbox-for-business-sso)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test Dropbox for Business](#create-dropbox-for-business-test-user)** pour disposer, dans Dropbox for Business, d’un équivalent de Britta Simon lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **Dropbox for Business**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la boîte de dialogue **Configuration SAML de base**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **URL de connexion**, saisissez une URL au format suivant : `https://www.dropbox.com/sso/<id>`

    b. Dans la zone de texte **Identificateur (ID d’entité)** , tapez une valeur : `Dropbox`

    > [!NOTE]
    > La valeur URL de connexion ci-dessus n’est pas une valeur réelle. Vous mettrez à jour la valeur avec l’URL de connexion réelle. La procédure est expliquée plus loin dans le didacticiel.

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le **Certificat (Base64)** en fonction des options définies par rapport à vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/certificatebase64.png)

1. Dans la section **Configurer Dropbox for Business**, copiez la ou les URL appropriées, selon vos besoins.

    ![Copier les URL de configuration](common/copy-configuration-urls.png)

    a. URL de connexion

    b. Identificateur Azure AD

    c. URL de déconnexion

### <a name="configure-dropbox-for-business-sso"></a>Configurer l’authentification unique Dropbox for Business

1. Pour automatiser la configuration dans Dropbox for Business, vous devez installer l’**extension de connexion sécurisée à Mes applications** en cliquant sur **Installer l’extension**.

    ![Extension My apps](common/install-myappssecure-extension.png)

2. Après l’ajout de l’extension au navigateur, cliquez sur **Configurer Dropbox for Business** pour être orienté vers l’application Dropbox for Business. À partir de là, indiquez les informations d’identification de l’administrateur pour vous connecter à Dropbox for Business. Cette extension de navigateur configure automatiquement l’application pour vous et automatise les étapes 3 à 8.

    ![Configuration](common/setup-sso.png)

3. Si vous souhaitez configurer Dropbox for Business manuellement, ouvrez une nouvelle fenêtre du navigateur web, puis accédez et connectez-vous à votre locataire Dropbox for Business. Effectuez les étapes suivantes :

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/ic769509.png "Configurer l’authentification unique")

4. Cliquez sur l’**Icône Utilisateur** et sélectionnez l’onglet **Paramètres**.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure1.png "Configurer l’authentification unique")

5. Dans le volet de navigation gauche, cliquez sur **Console d’administration**.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure2.png "Configurer l’authentification unique")

6. Dans la **Console d’administration**, cliquez sur **Paramètres** dans le volet de navigation gauche.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure3.png "Configurer l’authentification unique")

7. Sélectionnez **Authentification unique** sous la section **Authentification**.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure4.png "Configurer l’authentification unique")

8. Dans la section **Authentification unique**, procédez comme suit :  

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure5.png "Configurer l’authentification unique")

    a. Sélectionnez **Requis** en tant qu’option dans la liste déroulante de l’**Authentification unique**.

    b. Cliquez sur **Ajouter une URL de connexion**, puis dans la zone de texte **URL de connexion du fournisseur d’identité**, collez la valeur **URL de connexion** que vous avez copiée dans le portail Azure, et sélectionnez **Terminé**.

    ![Configurer l’authentification unique](./media/dropboxforbusiness-tutorial/configure6.png "Configurer l’authentification unique")

    c. Cliquez sur **Charger un certificat**, puis cherchez votre **Fichier de certificat codé en Base64** que vous avez téléchargé à partir du portail Azure.

    d. Cliquez sur **Copier le lien** et collez la valeur copiée dans la zone de texte de l’**URL de connexion** de la section **Domaine et URL Dropbox for Business** du portail Azure.

    e. Cliquez sur **Enregistrer**.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le Portail Azure.

1. Dans le volet gauche du Portail Azure, sélectionnez **Azure Active Directory**, **Utilisateurs**, puis **Tous les utilisateurs**.
1. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.
1. Dans les propriétés **Utilisateur**, effectuez les étapes suivantes :
   1. Dans le champ **Nom**, entrez `Britta Simon`.  
   1. Dans le champ **Nom de l’utilisateur**, entrez username@companydomain.extension. Par exemple : `BrittaSimon@contoso.com`.
   1. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.
   1. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Dropbox for Business.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **Dropbox for Business**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-dropbox-for-business-test-user"></a>Créer un utilisateur de test Dropbox for Business

Dans cette section, un utilisateur appelé Britta Simon est créé dans Dropbox for Business. Dropbox for Business prend en charge l’attribution d’utilisateurs juste-à-temps, qui est activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore d’utilisateur dans Dropbox for Business, un utilisateur est créé après l’authentification.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez l’[équipe de prise en charge des clients Dropbox for Business](https://www.dropbox.com/business/contact).

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Quand vous cliquez sur la vignette Dropbox for Business dans le volet d’accès, vous devez être connecté automatiquement à l’application Dropbox for Business pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)