---
title: Paramétrer les URL de redirection sur b2clogin.com - Azure Active Directory B2C | Microsoft Docs
description: Découvrez l’utilisation de b2clogin.com dans vos URL de redirection pour Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4d70fe3f3f19723cd37080ae09dce97bfd8f3d34
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511695"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Paramétrer les URL de redirection sur b2clogin.com pour Azure Active Directory B2C

Lorsque vous configurez un fournisseur d’identité pour l’inscription et la connexion à votre application Azure Active Directory (Azure AD) B2C, vous devez spécifier une URL de redirection. Auparavant login.microsoftonline.com, il s’agit maintenant de b2clogin.com.

L’utilisation de b2clogin.com vous offre des avantages supplémentaires, tels que :

- L’espace utilisé dans l’en-tête de cookie par les services Microsoft est réduit.
- Vos URL n’incluent plus de référence à Microsoft. Par exemple : `https://your-tenant-name.b2clogin.com/tenant-id/oauth2/authresp`.

>[!NOTE]
> Vous pouvez utiliser le nom du locataire et son identificateur global unique comme suit :
> * `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` (qui fait toujours référence à `onmicrosoft.com`)
> * `https://your-tenant-name.b2clogin.com/your-tenant-guid` (s’il n’y a aucune référence à Microsoft)
>
> Toutefois, vous ne pouvez pas utiliser de _domaine personnalisé_ pour votre locataire Azure Active Directory B2C. Par exemple, `https://your-tenant-name.b2clogin.com/your-custom-domain-name` ne _fonctionne pas_.

Tenez compte du fait que ces paramètres devront peut-être être modifiés lors de l’utilisation de b2clogin.com :

- Paramétrez les URL de redirection dans vos applications de fournisseur d’identité de façon à utiliser b2clogin.com. 
- Paramétrez votre application Azure AD B2C pour utiliser b2clogin.com pour les références de flux d’utilisateur et les points de terminaison de jeton. 
- Si vous utilisez MSAL, vous devez définir la propriété **ValidateAuthority** sur `false`.
- Veillez à remplacer les **origines autorisées** que vous avez définies dans les paramètres CORS de [personnalisation de l’interface utilisateur](active-directory-b2c-ui-customization-custom-dynamic.md).  

## <a name="change-redirect-urls"></a>Modifier les URL de redirection

Pour utiliser b2clogin.com, dans les paramètres de votre application de fournisseur d’identité, recherchez et modifiez la liste des URL approuvées pour redirection vers Azure AD B2C.  Actuellement, elle est probablement configurée pour rediriger vers un site login.microsoftonline.com. 

Vous devez modifier l’URL de redirection afin d’autoriser `your-tenant-name.b2clogin.com`. Veillez à remplacer `your-tenant-name` par le nom de votre abonné Azure AD B2C et à supprimer `/te` s’il existe dans l’URL. Il existe de légères variations à cette URL pour chaque fournisseur d’identité, par conséquent, consultez la page correspondante pour obtenir l’URL exacte.

Vous trouverez des informations de configuration pour les fournisseurs d’identité dans les articles suivants :

- [Compte Microsoft](active-directory-b2c-setup-msa-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Google](active-directory-b2c-setup-goog-app.md)
- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn](active-directory-b2c-setup-li-app.md)
- [Twitter](active-directory-b2c-setup-twitter-app.md)
- [GitHub](active-directory-b2c-setup-github-app.md)
- [Weibo](active-directory-b2c-setup-weibo-app.md)
- [QQ](active-directory-b2c-setup-qq-app.md)
- [WeChat](active-directory-b2c-setup-wechat-app.md)
- [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)
- [Custom OIDC](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>Mettre à jour votre application

Votre application Azure AD B2C fait probablement référence à `login.microsoftonline.com` à plusieurs emplacements, par exemple au niveau des références de flux d’utilisateur ou des points de terminaison de jeton.  Assurez-vous que votre point de terminaison d’autorisation, votre point de terminaison de jeton et votre émetteur ont été mis à jour pour utiliser `your-tenant-name.b2clogin.com`.  

## <a name="set-the-validateauthority-property"></a>Définir la propriété ValidateAuthority

Si vous utilisez MSAL, affectez la valeur `false` à la propriété **ValidateAuthority**. Quand **ValidateAuthority** a la valeur `false`, les redirections vers b2clogin.com sont autorisées. 

L’exemple suivant montre une façon de définir la propriété :

Dans [MSAL pour .NET (GitHub)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) :

```CSharp
 ConfidentialClientApplication client = new ConfidentialClientApplication(...); // can also be PublicClientApplication
 client.ValidateAuthority = false;
```

Et dans [MSAL pour JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js) :

```Javascript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```
