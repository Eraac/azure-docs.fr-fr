---
title: 'Tutoriel : Intégration d’Azure Active Directory à ScaleX Enterprise | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et ScaleX Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64edf2aa47211c1d2a598417a7b2edc00f260075
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60321165"
---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>Tutoriel : Intégration d’Azure Active Directory à ScaleX Enterprise

Dans ce didacticiel, vous allez apprendre à intégrer ScaleX Enterprise à Azure Active Directory (Azure AD).

L’intégration du logiciel ScaleX Enterprise à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à ScaleX Enterprise
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à ScaleX Enterprise (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration de l’application SaaS avec Azure AD, consultez Qu’est-ce que l’accès aux applications et l’authentification unique avec [Azure Active Directory](../manage-apps/what-is-single-sign-on.md) ?

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à ScaleX Enterprise, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement ScaleX Enterprise pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de ScaleX Enterprise à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Ajout de ScaleX Enterprise à partir de la galerie
Pour configurer l’intégration de ScaleX Enterprise à Azure AD, vous devez ajouter ScaleX Enterprise depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter ScaleX Enterprise à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **ScaleX Enterprise**.

    ![Création d’un utilisateur de test Azure AD](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

1. Dans le volet de résultats, sélectionnez **ScaleX Enterprise**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec ScaleX Enterprise avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur ScaleX Enterprise équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur ScaleX Enterprise associé doit être établie.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans ScaleX Enterprise.

Pour configurer et tester l’authentification unique Azure AD avec ScaleX Enterprise, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Création d’un utilisateur de test ScaleX Enterprise](#creating-a-scalex-enterprise-test-user)** pour avoir un équivalent de Britta Simon dans ScaleX Enterprise, lié à la représentation Azure AD associée.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application ScaleX Enterprise.

**Pour configurer l’authentification unique Azure AD avec ScaleX Enterprise, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **ScaleX Enterprise**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

1. Dans la section **Domaines et URL ScaleX Enterprise**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP**:

    ![Configurer l'authentification unique](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. Dans la zone de texte **Identificateur**, tapez la valeur au format suivant : `https://platform.rescale.com/saml2/<company id>/`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://platform.rescale.com/saml2/<company id>/acs/`

1. Cochez **Afficher les paramètres d’URL avancés** si vous souhaitez configurer l’application en mode lancé par le **fournisseur de service** :

    ![Configurer l'authentification unique](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    Dans la zone de texte **URL de connexion**, tapez la valeur au format suivant : `https://platform.rescale.com/saml2/<company id>/sso/`
     
    > [!NOTE] 
    > Il ne s’agit pas des valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels et l’URL de connexion. Pour obtenir ces valeurs, contactez [l’équipe de support technique ScaleX Enterprise](https://info.rescale.com/contact_sales). 

1. Votre application ScaleX s’attend à recevoir les assertions SAML dans un format spécifique, ce qui vous oblige à modifier des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML. Cliquez sur la case **Afficher et modifier tous les autres attributs utilisateur** pour ouvrir les attributs personnalisés.

    ![Configurer l'authentification unique](./media/scalexenterprise-tutorial/scalex_attributes.png)
    
    a. Cliquez avec le bouton droit sur l’attribut **name** et cliquez sur Supprimer.

    ![Configurer l'authentification unique](./media/scalexenterprise-tutorial/delete_attribute_name.png)

    b. Cliquez sur l’attribut **emailaddress** pour ouvrir la fenêtre Modifier l’attribut. Modifiez sa valeur de **user.mail** en **user.userprincipalname** et cliquez sur OK.

    ![Configurer l'authentification unique](./media/scalexenterprise-tutorial/edit_email_attribute.png) 
    
1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l'authentification unique](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/scalexenterprise-tutorial/tutorial_general_400.png)
    
1. Dans la section **Configuration de ScaleX Enterprise** , cliquez sur **Configurer ScaleX Enterprise** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’ID d’entité SAML** et **l’URL du service d’authentification unique SAML** à partir de la section **Référence rapide.**

    ![Configurer l'authentification unique](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

1. Pour configurer l’authentification unique sur **ScaleX Enterprise**, connectez-vous au site web ScaleX Enterprise de votre société en tant qu’administrateur.

1. Cliquez sur le menu dans le coin supérieur droit et sélectionnez **Contoso Administration**.

    > [!NOTE] 
    > Contoso est simplement un exemple. Ce doit être le nom réel de votre société. 

    ![Configurer l'authentification unique](./media/scalexenterprise-tutorial/Test_Admin.png) 

1. Sélectionnez **Intégrations** dans le menu supérieur, puis **Authentification unique**.

    ![Configurer l'authentification unique](./media/scalexenterprise-tutorial/admin_sso.png) 

1. Remplissez le formulaire comme suit :

    ![Configurer l'authentification unique](./media/scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. Sélectionnez **Créer n’importe quel utilisateur pouvant s’authentifier avec l’authentification unique.**

    b. **Service Provider saml** (SAML du fournisseur de services) : Collez la valeur ***urn:oasis:names:tc:SAML:2.0:nameid-format:persistent***

    c. **Name of Identity Provider email field in ACS response** (Nom du champ d’adresse e-mail du fournisseur d’identité dans la réponse ACS) : Collez la valeur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Identity Provider EntityDescriptor Entity ID** (ID d’entité EntityDescriptor du fournisseur d’identité) : Collez la valeur **ID d’entité SAML** copiée à partir du portail Azure.

    e. **URL SingleSignOnService du fournisseur d’identité :** Collez l’**URL du service d’authentification unique SAML** à partir du portail Azure.

    f. **Identity Provider public X509 certificate** (Certificat X509 public du fournisseur d’identité) : Ouvrez le certificat X509 téléchargé à partir d’Azure dans le bloc-notes et collez le contenu dans cette zone. Assurez-vous qu’aucun saut de ligne ne se trouve au milieu du contenu du certificat.
    
    g. Cochez les cases suivantes : **Enabled (Activé), Encrypt NameID (Chiffrer NameID) et Sign AuthnRequests (Signer AuthnRequests).**

    h. Cliquez sur **Mettre à jour les paramètres de l’authentification unique** pour enregistrer les paramètres.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, accédez à : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/scalexenterprise-tutorial/create_aaduser_01.png) 

1. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/scalexenterprise-tutorial/create_aaduser_02.png) 

1. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/scalexenterprise-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/scalexenterprise-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-scalex-enterprise-test-user"></a>Création d’un utilisateur de test ScaleX Enterprise

Pour permettre aux utilisateurs d’Azure AD de se connecter à ScaleX Enterprise, vous devez les configurer dans ScaleX Enterprise. Dans le cas de ScaleX Enterprise, cette configuration est une tâche automatique et aucune opération manuelle n’est nécessaire. Tout utilisateur authentifié avec des informations d’authentification unique est automatiquement configuré du côté ScaleX.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à ScaleX Enterprise.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à ScaleX Enterprise, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **ScaleX Enterprise**.

    ![Configurer l'authentification unique](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Cliquez sur la mosaïque ScaleX Enterprise dans le volet d’accès pour vous connecter automatiquement à votre application ScaleX Enterprise. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/scalexenterprise-tutorial/tutorial_general_01.png
[2]: ./media/scalexenterprise-tutorial/tutorial_general_02.png
[3]: ./media/scalexenterprise-tutorial/tutorial_general_03.png
[4]: ./media/scalexenterprise-tutorial/tutorial_general_04.png

[100]: ./media/scalexenterprise-tutorial/tutorial_general_100.png

[200]: ./media/scalexenterprise-tutorial/tutorial_general_200.png
[201]: ./media/scalexenterprise-tutorial/tutorial_general_201.png
[202]: ./media/scalexenterprise-tutorial/tutorial_general_202.png
[203]: ./media/scalexenterprise-tutorial/tutorial_general_203.png

