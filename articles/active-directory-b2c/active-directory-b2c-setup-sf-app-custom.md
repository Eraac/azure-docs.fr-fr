---
title: Configurer la connexion avec un fournisseur SAML Salesforce en utilisant des stratégies personnalisées dans Azure Active Directory B2C
description: Configurez la connexion avec un fournisseur SAML Salesforce en utilisant des stratégies personnalisées dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7cbde2beb03c174facbd145954387a31f6158a9a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654187"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Configurer la connexion avec un fournisseur SAML Salesforce en utilisant des stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article explique comment autoriser la connexion d’utilisateurs à partir d’une organisation Salesforce à l’aide de [stratégies personnalisées](active-directory-b2c-overview-custom.md) dans Azure Active Directory (Azure AD) B2C. Vous allez activer la connexion en ajoutant un [profil technique SAML](saml-technical-profile.md) à une stratégie personnalisée.

## <a name="prerequisites"></a>Prérequis

- Suivez les étapes de l’article [Prise en main des stratégies personnalisées dans Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Si vous n’avez pas déjà fait, inscrivez-vous pour un [compte édition développeur gratuit](https://developer.salesforce.com/signup). Cet article utilise le [Lightning Experience de Salesforce](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- Vous avez déjà [configuré un domaine My Domain](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) pour votre organisation Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Configurer Salesforce en tant que fournisseur d’identité

1. [Connectez-vous à Salesforce](https://login.salesforce.com/).
2. Dans le menu de gauche, sous **Paramètres**, développez **Identité**, puis sélectionnez **Fournisseur d’identité**.
3. Cliquez sur **Activer le fournisseur d’identité**.
4. Sous **Select the certificate (Sélectionner le certificat)** , sélectionnez le certificat que vous souhaitez que Salesforce utilise pour communiquer avec Azure AD B2C Vous pouvez utiliser le certificat par défaut.
5. Cliquez sur **Enregistrer**.

### <a name="create-a-connected-app-in-salesforce"></a>Créer une application connectée dans Salesforce

1. Sur la page **Fournisseur d’identité**, sélectionnez **Les fournisseurs de services sont maintenant créés via des applications connectées. Cliquez ici.**
2. Sous **Basic Information (Informations de base)** , entrez les valeurs requises pour votre application connectée.
3. Sous **Paramètres de l’application web**, cochez la case **Activer SAML**.
4. Dans le champ **Entity ID (ID d’entité)** , entrez l’URL suivante. Assurez-vous de remplacer la valeur de `your-tenant` par le nom de votre locataire Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Dans le champ **ACS URL (URL ACS)** , entrez l’URL suivante. Assurez-vous de remplacer la valeur de `your-tenant` par le nom de votre locataire Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Faites défiler vers le bas de la liste, puis cliquez sur **Enregistrer**.

### <a name="get-the-metadata-url"></a>Obtenir l’URL des métadonnées

1. Dans la page de présentation de votre application connectée, cliquez sur **Manage (Gérer)** .
2. Copiez la valeur de **point de terminaison de découverte des métadonnées**, puis enregistrez-la. Vous l’utiliserez plus loin dans cet article.

### <a name="set-up-salesforce-users-to-federate"></a>Configurer des utilisateurs Salesforce pour fédérer

1. Dans la page **Gérer** de votre application connectée, cliquez sur **Gérer les profils**.
2. Sélectionnez les profils (ou groupes d’utilisateurs) que vous souhaitez fédérer avec Azure AD B2C. En tant qu’un administrateur système, activez la case à cocher **Administrateur système** afin de pouvoir fédérer à l’aide de votre compte Salesforce.

## <a name="generate-a-signing-certificate"></a>Générer un certificat de signature

Les demandes envoyées à Salesforce doivent être signées par Azure AD B2C. Pour générer un certificat de signature, ouvrez Azure PowerShell, puis exécutez les commandes suivantes.

> [!NOTE]
> Veillez à mettre à jour le nom et le mot de passe du locataire dans les deux lignes du haut.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Vous devez enregistrer le certificat que vous avez créé dans votre locataire Azure AD B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
5. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
6. Pour **Options**, choisissez `Upload`.
7. Entrez un **nom** pour la stratégie. Par exemple, SAMLSigningCert. Le préfixe `B2C_1A_` est automatiquement ajouté au nom de votre clé.
8. Recherchez et sélectionnez le certificat B2CSigningCert.pfx que vous avez créé.
9. Entrez le **mot de passe** du certificat.
3. Cliquez sur **Créer**.

## <a name="add-a-claims-provider"></a>Ajout d’un fournisseur de revendications

Si vous souhaitez que les utilisateurs se connectent à l’aide d’un compte Salesforce, vous devez définir le compte en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer par le biais d’un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié.

Vous pouvez définir un compte Salesforce en tant que fournisseur de revendications en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
3. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Mettez à jour la valeur de **PartnerEntity** avec l’URL des métadonnées de Salesforce que vous avez copiée précédemment.
5. Mettez à jour la valeur des deux instances de **StorageReferenceId** avec le nom de la clé de votre certificat de signature. Par exemple, B2C_1A_SAMLSigningCert.

### <a name="upload-the-extension-file-for-verification"></a>Télécharger le fichier d’extension pour la vérification

À ce stade, vous avez configuré votre stratégie afin qu’Azure AD B2C sache comment communiquer avec votre compte Salesforce. Essayez de télécharger le fichier d’extension de votre stratégie juste pour confirmer qu’il ne présente aucun problème pour le moment.

1. Sur la page **Stratégies personnalisées** dans votre locataire Azure AD B2C, sélectionnez **Charger une stratégie**.
2. Activez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustFrameworkExtensions.xml*.
3. Cliquez sur **Télécharger**.

## <a name="register-the-claims-provider"></a>Inscription du fournisseur de revendications

À ce stade, le fournisseur d’identité a été configuré, mais il n’est disponible dans aucun des écrans d’inscription ou de connexion. Pour changer cela, vous créez un doublon d’un modèle de parcours utilisateur, puis le modifiez afin qu’il dispose également du fournisseur d’identité Salesforce.

1. Ouvrez le fichier *TrustFrameworkBase.xml* à partir du pack de démarrage.
2. Recherchez et copiez l’intégralité du contenu de l’élément **UserJourney** comprenant `Id="SignUpOrSignIn"`.
3. Ouvrez le fichier *TrustFrameworkExtensions.xml*, puis recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.
4. Collez l’intégralité du contenu de l’élément **UserJourney** que vous avez copié en tant qu’enfant de l’élément **UserJourneys**.
5. Renommez l’ID du parcours utilisateur. Par exemple : `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Afficher le bouton

L’élément **ClaimsProviderSelection** est analogue à un bouton de fournisseur d’identité sur un écran d’inscription ou de connexion. Si vous ajoutez un élément **ClaimsProviderSelection** pour un compte LinkedIn, un nouveau bouton s’affiche quand un utilisateur arrive sur la page.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="1"` dans le parcours utilisateur que vous venez de créer.
2. Sous **ClaimsProviderSelects**, ajoutez l’élément suivant. Définissez la valeur de l’élément **TargetClaimsExchangeId** sur une valeur appropriée, par exemple `SalesforceExchange` :

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action

Maintenant que vous avez un bouton en place, vous devez le lier à une action. L’action est, dans ce cas, la communication d’Azure AD B2C avec un compte Salesforce pour recevoir un jeton.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="2"` dans le parcours utilisateur.
2. Ajoutez l’élément **ClaimsExchange** suivant en veillant à utiliser pour l’**ID** la même valeur que celle que vous avez utilisée pour **TargetClaimsExchangeId** :

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    Mettez à jour la valeur de **TechnicalProfileReferenceId** sur **l’ID** du profil technique que vous avez créé précédemment. Par exemple : `LinkedIn-OAUTH`.

3. Enregistrez le fichier *TrustFrameworkExtensions.xml* et rechargez-le à des fins de vérification.

## <a name="create-an-azure-ad-b2c-application"></a>Création d’une application Azure AD B2C

La communication avec Azure AD B2C s’effectue via une application que vous créez dans votre locataire. Cette section indique les étapes facultatives que vous pouvez effectuer pour créer une application de test si vous ne l’avez pas déjà fait.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Applications**, puis **Ajouter**.
5. Entrez un nom pour l’application (par exemple, *testapp1*).
6. Pour **Application/API web**, sélectionnez `Yes`, puis entrez `https://jwt.ms` pour l’**URL de réponse**.
7. Cliquez sur **Créer**.

## <a name="update-and-test-the-relying-party-file"></a>Mettre à jour et tester le fichier de partie de confiance

Mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous venez de créer :

1. Faites une copie de *SignUpOrSignIn.xml* dans votre répertoire de travail, puis renommez-le. Par exemple, renommez-le en *SignUpSignInSalesforce.xml*.
2. Ouvrez le nouveau fichier et définissez une valeur unique pour l’attribut **PolicyId** de **TrustFrameworkPolicy**. Par exemple : `SignUpSignInSalesforce`.
3. Mettez à jour la valeur de **PublicPolicyUri** avec l’URI de la stratégie. Par exemple, `http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Définissez l’attribut **ReferenceId** dans **DefaultUserJourney** sur l’ID du parcours utilisateur que vous avez créé (SignUpSignInSalesforce).
5. Enregistrez vos modifications, chargez le fichier, puis sélectionnez la nouvelle stratégie dans la liste.
6. Vérifiez que l’application Azure AD B2C que vous avez créée est sélectionnée dans le champ **Sélectionner une application**, puis testez-la en cliquant sur **Exécuter maintenant**.
