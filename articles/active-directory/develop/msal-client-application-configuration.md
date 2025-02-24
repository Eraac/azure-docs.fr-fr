---
title: Configuration d’application cliente (bibliothèque d’authentification Microsoft) | Azure
description: Découvrez les options de configuration pour les applications clientes publiques et confidentielles dans la bibliothèque d’authentification Microsoft (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5e175a8cdd1622add90bd80df63303fe914ab9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66430813"
---
# <a name="application-configuration-options"></a>Options de configuration d’application

Dans votre code, vous initialisez une nouvelle application cliente publique ou confidentielle (ou agent utilisateur pour MSAL.js) afin d’authentifier et d’acquérir des jetons. Vous pouvez définir un certain nombre d’options de configuration lorsque vous initialisez l’application cliente dans la bibliothèque d’authentification Microsoft (MSAL). Ces options se répartissent en deux groupes :

- Options d’inscription, à savoir :
    - [Autorité](#authority) (composée de l’[instance](#cloud-instance) de fournisseur d’identité et de l’[audience](#application-audience) de connexion pour l’application et, éventuellement, l’ID de locataire).
    - [ID client](#client-id).
    - [URI de redirection](#redirect-uri).
    - [Clé secrète client](#client-secret) (pour les applications clientes confidentielles).
- [Options de journalisation](#logging) incluant le niveau de journalisation, le contrôle de données personnelles et le nom du composant utilisant la bibliothèque.

## <a name="authority"></a>Authority
L’autorité est une URL indiquant un annuaire dont la bibliothèque d’authentification Microsoft peut demander des jetons. Les autorités courantes sont les suivantes :

- https://login.microsoftonline.com/&lt ;tenant&gt; /, où &lt; tenant&gt; est l’ID de locataire du locataire Azure Active Directory (Azure AD) ou d’un domaine associé à celui-ci. Utilisé uniquement pour connecter des utilisateurs d’une organisation spécifique.
- https://login.microsoftonline.com/common/. Utilisé pour connecter des utilisateurs disposant de comptes professionnels ou scolaires, ou de comptes Microsoft personnels.
- https://login.microsoftonline.com/organizations/. Utilisé pour connecter des utilisateurs disposant de comptes professionnels ou scolaires.
- https://login.microsoftonline.com/consumers/. Utilisé pour connecter des utilisateurs disposant uniquement de comptes Microsoft personnels (anciennement appelés comptes Windows Live ID).

Le paramètre d’autorité doit être cohérent avec ce qui est déclaré dans le portail d’inscription d’application.

L’URL d’autorité est composée de l’instance et de l’audience.

L’autorité peut être :
- Une autorité de cloud Azure AD.
- Une autorité Azure AD B2C. Voir [Spécificités B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Une autorité de services de fédération Active Directory (AD FS). Voir [Prise en charge d’ADFS](https://aka.ms/msal-net-adfs-support).

Les autorités de cloud Azure AD comprennent deux parties :
- L’*instance* de fournisseur d’identité.
- L’*audience* de connexion pour l’application.

L’instance et l’audience peuvent être concaténées et fournies en tant que qu’URL de l’autorité. Dans les versions de MSAL.NET antérieures à MSAL 3.*x*, vous deviez composer l’autorité vous-même, sur la base du cloud que vous souhaitiez cibler et de l’audience de connexion.  Ce diagramme montre comment l’URL de l’autorité est composée :

![Composition de l’URL de l’autorité](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Instance cloud
L’*instance* est utilisée pour spécifier si votre application connecte des utilisateurs à partir du cloud public Azure ou de clouds nationaux. En utilisant MSAL dans votre code, vous pouvez définir l’instance cloud Azure à l’aide d’une énumération ou en transmettant l’URL à l’[instance cloud nationale](authentication-national-cloud.md#azure-ad-authentication-endpoints) en tant que membre `Instance` (si vous le connaissez).

MSAL.NET lève une exception explicite si les deux valeurs `Instance` et `AzureCloudInstance` sont spécifiées.

Si vous ne spécifiez pas d’instance, votre application cible l’instance cloud publique Azure (instance de l’URL `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Audience de l’application

L’audience de connexion varie selon les besoins métier de votre application :
- Si vous êtes un développeur d’application métier (LOB), vous allez probablement produire une application à locataire unique qui sera utilisée uniquement dans votre organisation. Dans ce cas, vous devez spécifier l’organisation, soit par son ID de locataire (ID de votre instance Azure AD) ou par un nom de domaine associé à l’instance Azure AD.
- Si vous êtes un ISV, vous pouvez connecter des utilisateurs avec leurs comptes professionnels et scolaires dans toute organisation ou dans certaines organisations (application mutualisée). Mais vous pouvez également amener les utilisateurs à se connecter avec leurs comptes Microsoft personnels.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Comment spécifier l’audience dans votre code/configuration
En utilisant MSAL dans votre code, vous spécifiez l’audience à l’aide de l’une des valeurs suivantes :
- L’énumération d’audience d’autorité Azure AD.
- L’ID de locataire, qui peut être :
  - Un GUID (ID de votre instance Azure AD) pour des applications à locataire unique.
  - Un nom de domaine associé à votre instance Azure AD (également pour des applications à locataire unique).
- Un de ces espaces réservés comme un ID de locataire à la place de l’énumération d’audience d’autorité Azure AD :
    - `organizations` pour une application mutualisée.
    - `consumers` pour connecter les utilisateurs uniquement avec leurs comptes personnels.
    - `common` pour connecter des utilisateurs avec leurs comptes professionnels et scolaires, ou leurs comptes Microsoft personnels.

MSAL lève une exception significative si vous spécifiez l’audience d’autorité Azure AD et l’ID client.

Si vous ne spécifiez pas d’audience, votre application cible Azure AD et les comptes Microsoft personnels en tant qu’audience (autrement dit, elle se comporte comme si `common` était spécifié).

### <a name="effective-audience"></a>Audience effective
L’audience effective pour votre application sera la valeur minimale (s’il existe une intersection) de l’audience que vous définissez dans votre application et de l’audience spécifiée dans l’inscription d’application. En fait, l’expérience [inscriptions d’applications](https://aka.ms/appregistrations) vous permet de spécifier l’audience (les types de comptes pris en charge) pour l’application. Pour plus d’informations, consultez [Démarrage rapide : Inscrire une application à l’aide de la plateforme d’identités Microsoft](quickstart-register-app.md).

Actuellement, la seule façon d’obtenir qu’une application connecte des utilisateurs disposant uniquement de comptes Microsoft personnels consiste à configurer ces deux paramètres :
- Définissez l’audience d’inscription d’application sur `Work and school accounts and personal accounts`.
- Définissez l’audience dans votre code/configuration sur `AadAuthorityAudience.PersonalMicrosoftAccount` (ou `TenantID` ="consumers").

## <a name="client-id"></a>ID client
L’ID client est l’ID (client) d’application unique qu’Azure AD attribue à votre application lors de l’inscription de l’application.

## <a name="redirect-uri"></a>URI de redirection
L’URI de redirection est l’URI auquel le fournisseur d’identité renvoie les jetons de sécurité.

### <a name="redirect-uri-for-public-client-apps"></a>URI de redirection pour les applications clientes publiques
Si vous êtes un développeur d’application cliente publique utilisant MSAL :
- Vous n’avez pas besoin de transmettre la valeur `RedirectUri`, car elle est calculée automatiquement par MSAL. Cet URI de redirection est défini sur l’une des valeurs suivantes, selon la plateforme :
   - `urn:ietf:wg:oauth:2.0:oob` pour toutes les plateformes Windows.
   - `msal{ClientId}://auth` pour Xamarin Android et iOS.

- Vous n’avez pas besoin de configurer l’URI de redirection dans [Inscriptions d’applications](https://aka.ms/appregistrations) :

   ![URI de redirection dans Inscriptions d’applications](media/msal-client-application-configuration/redirect-uri.png)

Vous pouvez remplacer l’URI de redirection à l’aide de la propriété `RedirectUri` (par exemple, si vous utilisez des répartiteurs). Voici quelques exemples d’URI de redirection pour ce scénario :

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

Pour plus de détails, voir la [documentation pour Android et iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).

### <a name="redirect-uri-for-confidential-client-apps"></a>URI de redirection pour les applications clientes confidentielles
Pour les applications web, l’URI de redirection (ou URI de réponse) est l’URI qu’Azure AD utilise pour renvoyer le jeton à l’application. Il peut s’agir de l’URL de l’application web/API Web si l’application confidentielle est un des applications suivantes. L’URI de redirection doit être inscrit dans l’inscription d’application. Cette inscription est particulièrement importante lorsque vous déployez une application que vous avez initialement testée localement. Vous devez ensuite ajouter l’URL de réponse de l’application déployée dans le portail d’inscription d’application.

Pour les applications démon, vous n’avez pas besoin de spécifier un URI de redirection.

## <a name="client-secret"></a>Clé secrète client
Cette option spécifie la clé secrète client pour l’application cliente confidentielle. Ce secret (mot de passe) est fourni par le portail de l’inscription d’application ou, pour Azure AD, fourni lors de l’inscription de l’application avec PowerShell AzureAD, PowerShell AzureRM ou Azure CLI.

## <a name="logging"></a>Journalisation
Les autres options de configuration activent la journalisation et la résolution des problèmes. Pour plus d’informations sur la façon de les utiliser, voir l’article [Journalisation](msal-logging.md).

## <a name="next-steps"></a>Étapes suivantes
Découvrez l’[instanciation d’applications clientes avec MSAL.NET](msal-net-initializing-client-applications.md).

Découvrez l’[instanciation d’applications clientes avec MSAL.js](msal-js-initializing-client-applications.md).
