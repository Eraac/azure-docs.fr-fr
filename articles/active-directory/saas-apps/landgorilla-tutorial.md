---
title: 'Didacticiel : Intégration d’Azure Active Directory à Land Gorilla Client | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Land Gorilla Client.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d12519a2bd0a5e3951d0ca9ae51a54bd728a782b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65987894"
---
# <a name="tutorial-azure-active-directory-integration-with-land-gorilla-client"></a>Didacticiel : Intégration d’Azure Active Directory à Land Gorilla Client

Dans ce didacticiel, vous allez apprendre à intégrer Land Gorilla Client à Azure Active Directory (Azure AD).

L’intégration d’Azure AD dans Land Gorilla Client offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Land Gorilla Client
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Land Gorilla Client (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).


## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD dans Land Gorilla Client, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Land Gorilla Client pour lequel l’authentification unique est activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Land Gorilla Client à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD


## <a name="adding-land-gorilla-client-from-the-gallery"></a>Ajout de Land Gorilla Client à partir de la galerie
Pour configurer l’intégration d’un client Land Gorilla à Azure AD, vous devez ajouter le client Land Gorilla à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Land Gorilla Clientt à partir de la galerie, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone Rechercher, entrez **Land Gorilla Client**.

    ![Création d’un utilisateur de test Azure AD](./media/landgorilla-tutorial/tutorial_landgorilla_search.png)

1. Dans le volet des résultats, sélectionnez **Land Gorilla Client**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/landgorilla-tutorial/tutorial_landgorilla_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Land Gorilla Client avec un utilisateur de test appelé « B. Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Land Gorilla Client équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Land Gorilla Client associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Land Gorilla Client.

Pour configurer et tester l’authentification unique Azure AD avec Land Gorilla Client, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD sur un groupe limité.
1. **[Création d’un utilisateur de test Land Gorilla](#creating-a-land-gorilla-test-user)** pour tester l’authentification unique Azure AD avec B. Simon.
1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à B. Simon d’utiliser l’authentification unique Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application Land Gorilla Client.

**Pour configurer l’authentification unique Azure AD avec Land Gorilla Client, procédez comme suit :**

1. Dans le portail de gestion Azure, sur la page d’intégration de l’application **Land Gorilla Client**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/landgorilla-tutorial/tutorial_landgorilla_samlbase.png)

1. Dans la section **Domaine et URL Land Gorilla Client**, procédez comme suit :

    ![Configurer l'authentification unique](./media/landgorilla-tutorial/tutorial_landgorilla_url_02.png)

    a. Dans la zone de texte **Identificateur**, tapez la valeur en utilisant un des formats suivants : 
    
    `https://<customer domain>.landgorilla.com/` 
    
    `https://www.<customer domain>.landgorilla.com`

    b. Dans la zone de texte **URL de réponse** , tapez une URL en respectant l’un des formats suivants :

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`
    
    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`

    > [!NOTE] 
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Nous vous suggérons d’utiliser ici la valeur de chaîne unique dans l’identificateur. Pour obtenir ces valeurs, contactez [l’équipe de support technique Land Gorilla Client](https://www.landgorilla.com/support/). 

1. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Configure Single Sign-On](./media/landgorilla-tutorial/tutorial_landgorilla_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/landgorilla-tutorial/tutorial_general_400.png) 

1. Pour obtenir la configuration SSO complète pour votre application côté Land Gorilla, contactez [l’équipe de support technique Land Gorilla](https://www.landgorilla.com/support/) et fournissez-lui le fichier **« Metadata XML »** téléchargé.


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé B. Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/landgorilla-tutorial/create_aaduser_01.png) 

1. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/landgorilla-tutorial/create_aaduser_02.png) 

1. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/landgorilla-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/landgorilla-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, saisissez **B. Simon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez l’**adresse de messagerie** de B. Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**. 

### <a name="creating-a-land-gorilla-test-user"></a>Création d’un utilisateur de test Land Gorilla

Collaborez avec [l’équipe du support technique Land Gorilla](https://www.landgorilla.com/support/) pour ajouter des utilisateurs dans la plateforme Land Gorilla.
    
### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser B. Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Land Gorilla Client.

![Affecter des utilisateurs][200] 

**Pour affecter B. Simon à Land Gorilla Client, procédez comme suit :**

1. Dans le portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires et à **Applications d’entreprise** puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Land Gorilla Client**.

    ![Configurer l'authentification unique](./media/landgorilla-tutorial/tutorial_landgorilla_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **B. Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Land Gorilla Client dans le volet d’accès, vous devez être connecté automatiquement à votre application Land Gorilla Client.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/landgorilla-tutorial/tutorial_general_01.png
[2]: ./media/landgorilla-tutorial/tutorial_general_02.png
[3]: ./media/landgorilla-tutorial/tutorial_general_03.png
[4]: ./media/landgorilla-tutorial/tutorial_general_04.png

[100]: ./media/landgorilla-tutorial/tutorial_general_100.png
[200]: ./media/landgorilla-tutorial/tutorial_general_200.png
[201]: ./media/landgorilla-tutorial/tutorial_general_201.png
[202]: ./media/landgorilla-tutorial/tutorial_general_202.png
[203]: ./media/landgorilla-tutorial/tutorial_general_203.png
