---
title: 'Didacticiel : Intégration d’Azure Active Directory à RingCentral | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: jeedes
ms.openlocfilehash: c374c9e8fd91d50b7e6589f22f9bed09fbe0de39
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092873"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Didacticiel : Intégrer RingCentral à Azure Active Directory

Dans ce tutoriel, vous allez découvrir comment intégrer RingCentral à Azure AD (Azure Active Directory). Quand vous intégrez RingCentral à Azure AD, vous pouvez :

* Contrôler qui a accès à RingCentral dans Azure AD.
* Permettre aux utilisateurs de se connecter automatiquement à RingCentral avec leur compte Azure AD.
* Gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prérequis

Pour commencer, vous devez disposer de ce qui suit :

* Un abonnement Azure AD Si vous n’en avez pas, vous pouvez obtenir un essai gratuit d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/)
* Un abonnement RingCentral pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce tutoriel, vous allez configurer et tester l’authentification unique Azure AD dans un environnement de test. RingCentral prend en charge l’authentification unique lancée par le **fournisseur d’identité**

## <a name="adding-ringcentral-from-the-gallery"></a>Ajout de RingCentral depuis la galerie

Pour configurer l’intégration de RingCentral à Azure AD, vous devez ajouter RingCentral à partir de la galerie à votre liste d’applications SaaS gérées.

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Dans le panneau de navigation gauche, sélectionnez le service **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.
1. Pour ajouter une nouvelle application, sélectionnez **Nouvelle application**.
1. Dans la section **Ajouter à partir de la galerie**, tapez **RingCentral** dans la zone de recherche.
1. Sélectionnez **RingCentral** dans le panneau de résultats, puis ajoutez l’application. Patientez quelques secondes pendant que l’application est ajoutée à votre locataire.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Configurez et testez l’authentification unique Azure AD avec RingCentral à l’aide d’un utilisateur de test appelé **Britta Simon**. Pour que l’authentification unique fonctionne, vous devez établir une relation entre un utilisateur Azure AD et l’utilisateur RingCentral associé.

Pour configurer et tester l’authentification unique Azure AD avec RingCentral, suivez les indications des sections ci-après :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-sso)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique RingCentral](#configure-ringcentral-sso)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test RingCentral](#create-ringcentral-test-user)** pour avoir un équivalent de Britta Simon dans RingCentral, lié à la représentation Azure AD de l’utilisateur.
6. **[Tester l’authentification unique](#test-sso)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Effectuez les étapes suivantes pour activer l’authentification unique Azure AD dans le portail Azure.

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **RingCentral**, recherchez la section **Gérer** et sélectionnez **Authentification unique**.
1. Dans la page **Sélectionner une méthode d’authentification unique**, sélectionnez **SAML**.
1. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône de modification/stylet pour **Configuration SAML de base** afin de modifier les paramètres.

   ![Modifier la configuration SAML de base](common/edit-urls.png)

1. Dans la section **Configuration SAML de base**, si vous disposez d’un **fichier de métadonnées du fournisseur de services**, suivez les étapes ci-dessous :

    1. Cliquez sur **Charger un fichier de métadonnées**.
    1. Cliquez sur le **logo du dossier** pour sélectionner le fichier de métadonnées, puis cliquez sur **Charger**.
    1. Une fois le fichier de métadonnées chargé, les valeurs **Identificateur** et **URL de réponse** sont automatiquement renseignées dans la section **Configuration SAML de base**.

    > [!Note]
    > Sur la page de configuration de l’authentification unique de RingCentral, vous obtenez le **fichier de métadonnées du fournisseur de services**, qui sera expliqué plus tard dans le didacticiel.

1. Si vous n’avez pas de **fichier de métadonnées du fournisseur de services**, entrez les valeurs pour les champs suivants :

    a. Dans la zone de texte **Identificateur**, tapez une URL :

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. Dans la zone de texte **URL de réponse**, tapez l’URL au format suivant :

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Dans la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur le bouton Copier pour copier l’**URL des métadonnées de fédération d’application**, puis enregistrez-la sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/copy-metadataurl.png)

### <a name="configure-ringcentral-sso"></a>Configurer l’authentification unique RingCentral

1. Ouvrez une autre fenêtre de navigateur web et connectez-vous à RingCentral en tant qu’administrateur de la sécurité.

1. En haut de la page, cliquez sur **Outils**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Accédez à **Authentification unique**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. Sur la page **Authentification unique**, dans la section **Configuration SSO**, cliquez sur **Modifier** dans l’**étape 1**, puis procédez comme suit :

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Sur la page **Configurer l’authentification unique**, effectuez les étapes suivantes :

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Cliquez sur **Parcourir** pour charger le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.

    b. Après avoir chargé les métadonnées, les valeurs sont automatiquement renseignées dans la section **SSO General Information** (Informations générales sur l’authentification unique).

    c. Dans la section **Mappage d’attributs**, définissez **Map Email Attribute to** (Mapper l’attribut d’e-mail à) sur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Cliquez sur **Enregistrer**.

    e. Dans l’**étape 2**, cliquez sur **Télécharger** pour télécharger le **fichier de métadonnées du fournisseur de services**. Ensuite, chargez-le dans la section **Configuration SAML de base** afin de renseigner automatiquement les valeurs **Identificateur** et **URL de réponse** dans le portail Azure.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Sur la même page, accédez à la section **Activer l’authentification unique** et procédez comme suit :

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Sélectionnez **Activer le service d’authentification unique**.

    * Sélectionnez **Allow users to log in with SSO or RingCentral credential** (Autoriser les utilisateurs à se connecter avec des informations d’identification de l’authentification unique ou RingCentral).

    * Cliquez sur **Enregistrer**.

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

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à RingCentral.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, puis **Toutes les applications**.
1. Dans la liste des applications, sélectionnez **RingCentral**.
1. Dans la page de vue d’ensemble de l’application, recherchez la section **Gérer** et sélectionnez **Utilisateurs et groupes**.

   ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

1. Sélectionnez **Ajouter un utilisateur**, puis **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Lien Ajouter un utilisateur](common/add-assign-user.png)

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.
1. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-ringcentral-test-user"></a>Créer un utilisateur de test RingCentral

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans RingCentral. Collaborez avec l’ [équipe de support technique RingCentral](https://success.ringcentral.com/RCContactSupp)  pour ajouter les utilisateurs dans la plateforme RingCentral. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="test-sso"></a>Tester l’authentification unique (SSO)

Quand vous sélectionnez la vignette RingCentral dans le volet d’accès, vous devez être connecté automatiquement à l’application RingCentral pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
