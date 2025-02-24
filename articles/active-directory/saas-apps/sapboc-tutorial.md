---
title: 'Didacticiel : Intégration d’Azure Active Directory à SAP Business Object Cloud | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et SAP Business Object Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ad2ffddf96aa6ecc886ac5653d2d0b8dcfb0856
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091711"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Didacticiel : Intégration d’Azure Active Directory à SAP Business Object Cloud

L’objectif de ce didacticiel est de vous apprendre à intégrer SAP Business Object Cloud avec Azure Active Directory (Azure AD).
Intégrer SAP Business Object Cloud avec Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler l’accès à SAP Business Object Cloud.
* Vous pouvez permettre aux utilisateurs de se connecter automatiquement à SAP Business Object Cloud (par le biais de l’authentification unique) avec leur compte Azure AD.
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec SAP Business Object Cloud, vous avez besoin des éléments suivants :

* Un abonnement Azure AD Si vous n’avez pas d’environnement Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).
* Abonnement SAP Business Object Cloud pour lequel l’authentification unique est activée

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous configurez et testez l’authentification unique Azure AD dans un environnement de test.

* SAP Business Object Cloud prend en charge l’authentification unique initiée par le **fournisseur de services**

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>Ajouter SAP Business Object Cloud à partir de la galerie

Pour configurer l’intégration de SAP Business Object Cloud à Azure AD, vous devez ajouter SAP Business Object Cloud, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter SAP Business Object Cloud à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)** , cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](common/select-azuread.png)

2. Accédez à **Applications d’entreprise**, puis sélectionnez l’option **Toutes les applications**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application](common/add-new-app.png)

4. Dans la zone de recherche, tapez **SAP Business Object Cloud**, sélectionnez **SAP Business Object Cloud** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

     ![SAP Business Object Cloud dans la liste des résultats](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SAP Business Object Cloud, grâce à un utilisateur de test appelé **Britta Simon**.
Pour que l’authentification unique fonctionne, une relation entre un utilisateur Azure AD et l’utilisateur associé dans SAP Business Object Cloud doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec SAP Business Object Cloud, vous devez compléter les blocs de construction suivants :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Configurer l’authentification unique SAP Business Object Cloud](#configure-sap-business-object-cloud-single-sign-on)** pour configurer les paramètres de l’authentification unique côté application.
3. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Créer un utilisateur de test SAP Business Object Cloud](#create-sap-business-object-cloud-test-user)** pour avoir un équivalent de Britta Simon dans SAP Business Object Cloud lié à la représentation d’un utilisateur Azure AD.
6. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure.

Pour configurer l’authentification unique Azure AD avec SAP Business Object Cloud, effectuez les étapes suivantes :

1. Dans le [portail Azure](https://portal.azure.com/), dans la page d’intégration de l’application **SAP Business Object Cloud**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique](common/select-sso.png)

2. Dans la boîte de dialogue **Sélectionner une méthode d’authentification unique**, sélectionnez le mode **SAML/WS-Fed** afin d’activer l’authentification unique.

    ![Mode de sélection de l’authentification unique](common/select-saml-option.png)

3. Dans la page **Configurer l’authentification unique avec SAML**, cliquez sur l’icône **Modifier** pour ouvrir la boîte de dialogue **Configuration SAML de base**.

    ![Modifier la configuration SAML de base](common/edit-urls.png)

4. Dans la section **Configuration SAML de base**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans la section Domaine et URL SAP Business Object Cloud](common/sp-identifier.png)

    a. Dans la zone de texte **URL de connexion**, entrez une URL au format suivant :

    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. Dans la zone de texte **Identificateur (ID d’entité)** , saisissez une URL au format suivant :
    
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Les valeurs de ces URL servent uniquement à des fins de démonstration. Mettez à jour les valeurs avec les URL de connexion et de l’identificateur réels. Pour obtenir l’URL de connexion, contactez [l’équipe de support technique SAP Business Object Cloud Client](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Vous pouvez obtenir l’URL de l’identificateur en téléchargeant les métadonnées SAP Business Object Cloud depuis la console d’administration. Une explication sera fournie plus loin dans ce didacticiel.

4. Sur la page **Configurer l’authentification unique avec SAML**, dans la section **Certificat de signature SAML**, cliquez sur **Télécharger** pour télécharger le fichier **XML de métadonnées de fédération** en fonction des options définies selon vos besoins, puis enregistrez-le sur votre ordinateur.

    ![Lien Téléchargement de certificat](common/metadataxml.png)

### <a name="configure-sap-business-object-cloud-single-sign-on"></a>Configurer l’authentification unique SAP Business Object Cloud

1. Dans une autre fenêtre du navigateur Web, ouvrez une session sur votre site d’entreprise SAP Business Object Cloud en tant qu’administrateur.

2. Sélectionnez **Menu** > **Système** > **Administration**.
    
    ![Sélectionner Menu, puis Système et Administration](./media/sapboc-tutorial/config1.png)

3. Sous l’onglet **Sécurité**, sélectionnez l’icône (stylet) **Modifier**.
    
    ![Sous l’onglet Sécurité, sélectionner l’icône Modifier](./media/sapboc-tutorial/config2.png)  

4. Sélectionnez **Méthode d’authentification unique SAML (SSO)** comme **Méthode d’authentification**.

    ![Sélectionner Authentification unique SAML comme méthode d’authentification](./media/sapboc-tutorial/config3.png)  

5. Pour télécharger les métadonnées du fournisseur de services (étape 1), sélectionnez **Télécharger**. Dans le fichier de métadonnées, recherchez et copiez la valeur **entityID**. Dans le portail Azure, dans la boîte de dialogue **Configuration SAML de base**, collez la valeur dans la zone **Identificateur**.

    ![Copier et coller la valeur entityID](./media/sapboc-tutorial/config4.png)  

6. Pour télécharger les métadonnées du fournisseur de services (étape 2) dans le fichier que vous avez téléchargé depuis le portail Azure, sous **Charger les métadonnées du fournisseur d’identité**, sélectionnez **Charger**.  

    ![Sous Charger les métadonnées du fournisseur d’identité, sélectionnez Charger](./media/sapboc-tutorial/config5.png)

7. Dans la liste **Attribut utilisateur**, sélectionnez l’attribut utilisateur (étape 3) que vous souhaitez utiliser pour votre mise en œuvre. Cet attribut utilisateur est mappé au fournisseur d’identité. Pour entrer un attribut personnalisé sur la page de l’utilisateur, utilisez l’option **Mappage SAML personnalisé**. Ou bien, vous pouvez sélectionner **E-mail** ou **ID UTILISATEUR** en tant qu’attribut utilisateur. Dans notre exemple, nous avons sélectionné **E-mail**, car nous avons mappé la revendication de l’identificateur d’utilisateur avec l’attribut **userprincipalname** dans la section **Attributs utilisateur et revendications** dans le portail Azure. Cela fournit un e-mail de l’utilisateur unique, qui est envoyé à l’application SAP Business Object Cloud dans chaque réponse SAML correcte.

    ![Sélectionner un attribut utilisateur](./media/sapboc-tutorial/config6.png)

8. Pour vérifier le compte avec le fournisseur d’identité (étape 4), dans la boîte **Informations d’identification de connexion (e-mail)** , entrez l’adresse e-mail de l’utilisateur. Ensuite, sélectionnez **Vérifier le compte**. Le système ajoute les informations d’identification de connexion au compte utilisateur.

    ![Entrer l’e-mail et sélectionner Vérifier le compte](./media/sapboc-tutorial/config7.png)

9. Sélectionnez l’icône **Enregistrer**.

    ![Icône Enregistrer](./media/sapboc-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD 

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**, sélectionnez **Utilisateurs**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](common/users.png)

2. Sélectionnez **Nouvel utilisateur** dans la partie supérieure de l’écran.

    ![Bouton Nouvel utilisateur](common/new-user.png)

3. Dans les propriétés de l’utilisateur, effectuez les étapes suivantes.

    ![Boîte de dialogue Utilisateur](common/user-properties.png)

    a. Dans le champ **Nom**, entrez **BrittaSimon**.
  
    b. Dans le champ **Nom d’utilisateur**, tapez **brittasimon\@domainedevotreentreprise.extension**.  
    Par exemple, BrittaSimon@contoso.com

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ Mot de passe.

    d. Cliquez sur **Créer**.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SAP Business Object Cloud.

1. Dans le portail Azure, sélectionnez **Applications d’entreprise**, **Toutes les applications**, puis **SAP Business Object Cloud**.

    ![Panneau Applications d’entreprise](common/enterprise-applications.png)

2. Dans la liste des applications, sélectionnez **SAP Business Object Cloud**.

    ![Lien SAP Business Object Cloud dans la liste des applications](common/all-applications.png)

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »](common/users-groups-blade.png)

4. Cliquez sur le bouton **Ajouter un utilisateur**, puis sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une attribution**.

    ![Volet Ajouter une attribution](common/add-assign-user.png)

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste Utilisateurs, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

6. Si vous attendez une valeur de rôle dans l’assertion SAML, dans la boîte de dialogue **Sélectionner un rôle**, sélectionnez le rôle approprié pour l’utilisateur dans la liste, puis cliquez sur le bouton **Sélectionner** en bas de l’écran.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.

### <a name="create-sap-business-object-cloud-test-user"></a>Créer un utilisateur de test pour SAP Business Object Cloud

Les utilisateurs Azure AD doivent être approvisionnés dans SAP Business Object Cloud avant de pouvoir se connecter à SAP Business Object Cloud. Dans SAP Business Object Cloud, l’approvisionnement est une tâche manuelle.

Pour approvisionner un compte d’utilisateur :

1. Connectez-vous en tant qu’administrateur à votre site d’entreprise SAP Business Object Cloud.

2. Sélectionnez **Menu** > **Sécurité** > **Utilisateurs**.

    ![Ajouter un employé](./media/sapboc-tutorial/user1.png)

3. Sur la page **Utilisateurs**, pour ajouter de nouvelles informations de l’utilisateur, sélectionnez **+** . 

    ![Page Ajouter des utilisateurs](./media/sapboc-tutorial/user4.png)

    Effectuez ensuite les tâches suivantes :

    a. Dans la boîte **ID UTILISATEUR**, entrez l’ID utilisateur de l’utilisateur, par exemple **Britta**.

    b. Dans la boîte **PRÉNOM**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    c. Dans la boîte **NOM**, entrez le prénom de l’utilisateur, par exemple **Simon**.

    d. Dans la boîte **NOM COMPLET**, tapez le nom complet de l’utilisateur, par exemple **Britta Simon**.

    e. Dans la zone de texte **E-mail**, entrez l’adresse e-mail de l’utilisateur, par exemple **brittasimon\@contoso.com**.

    f. Dans la page **Sélectionner des rôles**, sélectionnez le rôle approprié pour l’utilisateur, puis **OK**.

      ![Sélectionner un rôle](./media/sapboc-tutorial/user3.png)

    g. Sélectionnez l’icône **Enregistrer**.    

### <a name="test-single-sign-on"></a>Tester l’authentification unique 

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

En cliquant sur la vignette SAP Business Object Cloud dans le panneau d’accès, vous êtes automatiquement connecté à l’application SAP Business Object Cloud pour laquelle vous avez configuré l’authentification unique. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

