---
title: Prise en main des stratégies personnalisées - Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment bien démarrer avec les stratégies personnalisées dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2929c033b4744ea89f8e3d711a5e2e0df6301c14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730006"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Bien démarrer avec les stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Les [stratégies personnalisées](active-directory-b2c-overview-custom.md) sont des fichiers de configuration qui définissent le comportement de votre locataire Azure Active Directory (Azure AD) B2C. Dans cet article, vous allez créer une stratégie personnalisée qui prend en charge l’inscription ou la connexion de comptes locaux à l’aide d’une adresse e-mail et d’un mot de passe. Vous préparerez également votre environnement pour l’ajout de fournisseurs d’identité.

## <a name="prerequisites"></a>Prérequis

- Si vous n’en avez pas, vous devez [créer un locataire Azure AD B2C](tutorial-create-tenant.md) qui est lié à votre abonnement Azure.
- [Inscrivez votre application](tutorial-register-applications.md) dans le locataire que vous avez créé afin qu’il puisse communiquer avec Azure AD B2C.

## <a name="add-signing-and-encryption-keys"></a>Ajouter des clés de signature et de chiffrement

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre locataire Azure AD B2C.
2. Veillez à bien utiliser le répertoire qui contient votre locataire Azure AD B2C. Cliquez sur le **filtre Répertoire et abonnement** dans le menu supérieur et sélectionnez le répertoire qui contient votre locataire. 
3. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory B2C**.
4. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.

### <a name="create-the-signing-key"></a>Créer la clé de signature

1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
2. Pour **Options**, choisissez `Generate`.
3. Dans **Nom**, entrez `TokenSigningKeyContainer`. Il est possible que le préfixe `B2C_1A_` soit ajouté automatiquement.
4. Pour **Type de clé**, sélectionnez **RSA**.
5. Pour **Utilisation de la clé**, sélectionnez **Signature**.
6. Cliquez sur **Créer**.

### <a name="create-the-encryption-key"></a>Créer la clé de chiffrement

1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
2. Pour **Options**, choisissez `Generate`.
3. Dans **Nom**, entrez `TokenEncryptionKeyContainer`. Il est possible que le préfixe `B2C_1A`_ soit ajouté automatiquement.
4. Pour **Type de clé**, sélectionnez **RSA**.
5. Pour **Utilisation de la clé**, sélectionnez **Chiffrement**.
6. Cliquez sur **Créer**.

### <a name="create-the-facebook-key"></a>Créer la clé Facebook

Si vous disposez déjà d’un [secret pour l’application Facebook](active-directory-b2c-setup-fb-app.md), ajoutez-le en tant que clé de stratégie à votre locataire. Dans le cas contraire, vous devez créer la clé avec une valeur d’espace réservé afin que vos stratégies puissent être validées.

1. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
2. Pour **Options**, choisissez `Manual`.
3. Pour **Nom**, entrez `FacebookSecret`. Il est possible que le préfixe `B2C_1A_` soit ajouté automatiquement.
4. Dans **Secret**, entrez votre secret Facebook à partir de developers.facebook.com ou entrez `0` comme espace réservé. Cette valeur correspond au secret, pas à l’ID d’application.
5. Pour **Utilisation de la clé**, sélectionnez **Signature**.
6. Cliquez sur **Créer**.

## <a name="register-identity-experience-framework-applications"></a>Inscrire les applications de l’infrastructure d’expérience d’identité

Azure AD B2C exige que vous inscriviez deux applications utilisées pour inscrire et connecter les utilisateurs : IdentityExperienceFramework (application web) et ProxyIdentityExperienceFramework (application native) avec autorisation déléguée de l'application IdentityExperienceFramework. Les comptes locaux existent uniquement dans votre client. Vos utilisateurs se connectent avec une combinaison unique adresse e-mail/mot de passe pour accéder à vos applications inscrites auprès du locataire.

### <a name="register-the-identityexperienceframework-application"></a>Inscrire l’application IdentityExperienceFramework

1. Choisissez **Tous les services** dans le coin supérieur gauche du Portail Azure, recherchez et sélectionnez **Azure Active Directory**.
2. Dans le menu, sélectionnez **Inscriptions d’applications (héritées)** .
3. Sélectionnez **Nouvelle inscription d’application**.
4. Pour **Nom**, entrez `IdentityExperienceFramework`.
5. Pour **Type d’application**, choisissez **Application/API web**.
6. Pour **URL de connexion**, entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, où `your-tenant-name` est le nom de domaine de votre locataire Azure AD B2C. Toutes les URL doivent désormais utiliser [b2clogin.com](b2clogin.md).
7. Cliquez sur **Créer**. Après sa création, copiez l’ID d’application et enregistrez-le pour une utilisation ultérieure.

### <a name="register-the-proxyidentityexperienceframework-application"></a>Inscrire l’application ProxyIdentityExperienceFramework

1. Dans **Inscriptions d’applications (héritées)** , sélectionnez **Nouvelle inscription d’application**.
2. Pour **Nom**, entrez `ProxyIdentityExperienceFramework`.
3. Pour **Type d’application**, choisissez **Native**.
4. Pour **URI de redirection**, entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, où `your-tenant-name` est votre locataire Azure AD B2C.
5. Cliquez sur **Créer**. Après sa création, copiez l’ID d’application et enregistrez-le pour une utilisation ultérieure.
6. Dans la page Paramètres, sélectionnez **Autorisations requises**, puis **Ajouter**.
7. Choisissez **Sélectionner une API**, recherchez et sélectionnez **IdentityExperienceFramework**, puis cliquez sur **Sélectionner**.
9. Cochez la case en regard d’**Accéder à IdentityExperienceFramework**, cliquez sur **Sélectionner**, puis sur **Terminé**.
10. Sélectionnez **Accorder des autorisations** puis confirmez en sélectionnant **Oui**.

## <a name="download-starter-pack-and-modify-policies"></a>Télécharger le pack de démarrage et modifier les stratégies

Les stratégies personnalisées sont un ensemble de fichiers XML qui doivent être chargés sur votre client Azure AD B2C. Des packs de démarrage de fichiers sont fournis pour vous aider à être rapidement opérationnel. Chaque pack de démarrage de la liste suivante contient le plus petit nombre possible de profils techniques et de parcours utilisateur nécessaires pour réaliser les scénarios décrits :

- LocalAccounts : vous permet d’utiliser uniquement des comptes locaux.
- SocialAccounts : vous permet d’utiliser uniquement des comptes sociaux (ou fédérés).
- SocialAndLocalAccounts : vous permet d’utiliser à la fois des comptes locaux et des comptes sociaux.
- SocialAndLocalAccountsWithMFA : active les options social, local et authentification multifacteur.

Chaque pack de démarrage contient :

- Le fichier de base. Il n’y a que peu de modifications à apporter la base.
- Le fichier d’extension.  C’est sur ce fichier que portent la plupart des modifications de la configuration.
- Les fichiers de partie de confiance. Il s’agit de fichiers propres aux tâches appelés par votre application.

>[!NOTE]
>Si votre éditeur XML prend en charge la validation, validez la conformité des fichiers au schéma XML TrustFrameworkPolicy_0.3.0.0.xsd, qui se trouve dans le dossier racine du pack de démarrage. La validation du schéma XML identifie les erreurs avant le chargement.

1. [Téléchargez le fichier .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) ou exécutez :

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

2. Dans le dossier SocialAndLocalAccounts, modifiez tous les fichiers en remplaçant `yourtenant` par le nom de votre locataire. Par exemple : `contosoTenant.onmicrosoft.com`. Si vous avez besoin d’un éditeur XML, [essayez Visual Studio Code](https://code.visualstudio.com/download), un éditeur multiplateforme léger.

### <a name="add-application-ids-to-the-custom-policy"></a>Ajouter des ID d’applications à la stratégie personnalisée

Ajoutez les ID d’applications au fichier d’extensions *TrustFrameworkExtensions.xml*.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml* et recherchez l’élément `<TechnicalProfile Id="login-NonInteractive">`.
2. Remplacez les deux instances de `IdentityExperienceFrameworkAppId` par l’ID de l’application d’infrastructure d’expérience d’identité que vous avez créée précédemment. Remplacez les deux instances de `ProxyIdentityExperienceFrameworkAppId` par l’ID de l’application Infrastructure d’expérience d’identité de proxy que vous avez créée précédemment.
3. Enregistrez votre fichier d’extensions.

## <a name="upload-the-policies"></a>Charger les stratégies

1. Dans la page Stratégies personnalisées de l’Infrastructure d’expérience d’identité, sélectionnez **Charger une stratégie**.
1. Dans cet ordre, chargez *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* et *PasswordReset.xml*. Lorsqu’un fichier est chargé, son nom est précédé de `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Tester la stratégie personnalisée

1. Dans la page Stratégies personnalisées, sélectionnez **B2C_1A_signup_signin**.
2. Pour **sélectionner une application** dans la page de présentation de la stratégie personnalisée, sélectionnez l’application web nommée *webapp1* que vous avez inscrite précédemment. Assurez-vous que l’**URL de réponse** soit `https://jwt.ms`.
3. Sélectionnez **Exécuter maintenant**.
4. Vous devriez pouvoir vous inscrire à l’aide d’une adresse de messagerie.
5. Connectez-vous avec le même compte pour vérifier que votre configuration est correcte.

## <a name="add-facebook-as-an-identity-provider"></a>Ajouter Facebook en tant que fournisseur d’identité

1. Configurez une [application Facebook](active-directory-b2c-setup-fb-app.md).
2. Dans le fichier *TrustFrameworkExtensions.xml*, remplacez la valeur de `client_id` par l’ID d’application Facebook :

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
3. Chargez le fichier *TrustFrameworkExtensions.xml* sur votre locataire.
4. Testez en utilisant **Exécuter maintenant** ou en appelant la stratégie directement à partir de votre application inscrite.

## <a name="next-steps"></a>Étapes suivantes

- Ajoutez Azure Active Directory en tant que fournisseur d’identité. Le fichier de base utilisé dans ce guide de démarrage contient déjà une partie du contenu dont vous avez besoin pour ajouter d’autres fournisseurs d’identité. Pour plus d’informations sur la configuration des connexions, consultez l’article [Configurer l’inscription et la connexion avec un compte Azure Active Directory à l’aide de stratégies personnalisées Active Directory B2C](active-directory-b2c-setup-aad-custom.md).
