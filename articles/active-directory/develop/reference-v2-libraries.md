---
title: Bibliothèques d’authentification de plateforme d’identité Microsoft | Microsoft Docs
description: Bibliothèques clientes et bibliothèques de middleware de serveur compatibles, et liens vers la bibliothèque, la source et des exemples connexes pour le point de terminaison de plateforme d’identité Microsoft.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c3edfd9ef346407529eea1d887efd795e647808
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440807"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Bibliothèques d’authentification de plateforme d’identité Microsoft

Le [point de terminaison de plateforme d’identité Microsoft](active-directory-v2-compare.md) prend en charge les protocoles standard tels qu’OAuth 2.0 et OpenID Connect 1.0. La bibliothèque d’authentification Microsoft (MSAL) est conçue pour fonctionner avec le point de terminaison de plateforme d’identité Microsoft. Vous pouvez également utiliser des bibliothèques open source qui prennent en charge OAuth 2.0 et OpenID Connect 1.0.

Nous vous recommandons d’utiliser des bibliothèques écrites par des experts du domaine du protocole qui respectent une méthodologie Security Development Lifecycle (SDL). Ces bibliothèques incluent [la méthodologie suivie par Microsoft][Microsoft-SDL]. Si vous codez de façon manuelle pour les protocoles, vous devez suivre une méthodologie comme Microsoft SDL. Prêtez une attention toute particulière aux considérations de sécurité dans les spécifications de normes pour chaque protocole.

> [!NOTE]
> À la recherche de la bibliothèque d’authentification Active Directory (ADAL) ? Consultez le [guide des bibliothèques ADAL](active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Types de bibliothèques

Le point de terminaison de plateforme d’identité Microsoft fonctionne avec deux types de bibliothèques :

* **Bibliothèques clientes** : Les serveurs et clients natifs utilisent les bibliothèques clientes afin d’obtenir des jetons d’accès pour appeler une ressource, comme Microsoft Graph.
* **Bibliothèques de middleware de serveur** : Les applications web utilisent des bibliothèques de middleware de serveur pour la connexion des utilisateurs. Les API web utilisent des bibliothèques de middleware de serveur pour valider les jetons qui sont envoyés par des clients natifs ou par d’autres serveurs.

## <a name="library-support"></a>Prise en charge de la bibliothèque

Les bibliothèques sont classées en deux catégories de prise en charge :

* **Pris en charge par Microsoft** : Microsoft fournit des correctifs de ces bibliothèques, auxquelles il a appliqué la méthodologie SDL standard.
* **Compatible** : Microsoft a testé ces bibliothèques dans des scénarios de base et a confirmé que celles-ci fonctionnent avec le point de terminaison de plateforme d’identité Microsoft. Microsoft ne fournit pas de correctifs pour ces bibliothèques et n’a pas vérifié ces bibliothèques. Les problèmes et les demandes de fonctionnalités doivent être orientés vers le projet open source de la bibliothèque.

Pour obtenir la liste des bibliothèques qui fonctionnent avec le point de terminaison de plateforme d’identité Microsoft, consultez les sections suivantes.

## <a name="microsoft-supported-client-libraries"></a>Bibliothèques clientes prises en charge par Microsoft

Utilisez les bibliothèques d’authentification clientes pour obtenir un jeton permettant d’appeler une API web protégée.

| Plateforme | Bibliothèque | Téléchargement | Code source | Exemple | Informations de référence | Doc conceptuel | Feuille de route |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [Application à page unique](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Référence](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core/docs/classes/_useragentapplication_.useragentapplication.html) | [Docs conceptuels](msal-overview.md)| [Feuille de route](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL Angular (préversion) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Application de bureau](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#conceptual-documentation) | [Feuille de route](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python (préversion) | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Exemples](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Feuille de route](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java (préversion) | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Exemples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | | | [Feuille de route](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| ![iOS / Objective C ou swift](media/sample-v2-code/logo_iOS.png) | MSAL obj_c (préversion) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Application iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
|![Android / Java](media/sample-v2-code/logo_Android.png) | MSAL (préversion) | [Référentiel Central](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Application Android](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | | |

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliothèques de middleware de serveur prises en charge par Microsoft

Utilisez les bibliothèques de middleware pour protéger les applications web et les API web. Les applications web ou API web avec ASP.NET ou ASP.NET Core utilisent les bibliothèques de middleware.

| Plateforme | Bibliothèque | Téléchargement | Code source | Exemple | Informations de référence
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | Sécurité ASP.NET |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[Application MVC](quickstart-v2-aspnet-webapp.md) |[Informations de référence sur l’API ASP.NET](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Extensions IdentityModel pour .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [Application MVC](quickstart-v2-aspnet-webapp.md) |[Référence](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Application web](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="compatible-client-libraries"></a>Bibliothèques clientes compatibles

| Plateforme | Nom de la bibliothèque | Version testée | Code source | Exemple |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | Version 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [Version 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [Scribe Java](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Bibliothèque Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | [Version 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Bibliothèque Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | [Version 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-client](https://github.com/panva/node-openid-client) | [Version 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-client](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [The PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [Version 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth : 1.3.1<br />omniauth-oauth2 : 1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| ![iOS](media/sample-v2-code/logo_iOS.png) ![Android](media/sample-v2-code/logo_Android.png) | [Authentification de l’application React Native](https://github.com/FormidableLabs/react-native-app-auth) | [Version 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Authentification de l’application React Native](https://github.com/FormidableLabs/react-native-app-auth) | |

Pour toute bibliothèque conforme aux normes, vous pouvez utiliser le point de terminaison de plateforme d’identité Microsoft. Il est important de savoir à qui vous adresser pour connaître la prise en charge :

* Pour tout problème ou demande de nouvelle fonctionnalité au niveau du code de la bibliothèque, adressez-vous au propriétaire de la bibliothèque.
* Pour tout problème ou demande de nouvelle fonctionnalité au niveau de l’implémentation du protocole côté service, adressez-vous à Microsoft.
* [Envoyez une demande de nouvelle fonctionnalité](https://feedback.azure.com/forums/169401-azure-active-directory) pour les fonctionnalités supplémentaires que vous aimeriez voir dans le protocole.
* [Créez une demande de support](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) si vous détectez un problème pour lequel le point de terminaison de plateforme d’identité Microsoft n’est pas compatible avec OAuth 2.0 ni OpenID Connect 1.0.

## <a name="related-content"></a>Contenu connexe

Pour plus d’informations sur le point de terminaison de plateforme d’identité Microsoft, consultez la [vue d’ensemble de la plateforme d’identité Microsoft][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
