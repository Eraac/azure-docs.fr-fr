---
title: Fournir un HttpClient et un proxy (MSAL.NET) | Azure
description: Découvrez la fourniture de vos propres HttpClient et proxy pour vous connecter à Azure AD à l’aide de Microsoft Authentication Library pour .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72ab8a85ecc5649352382469e09d7dfd83a5ddfa
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305720"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Fourniture de vos propres HttpClient et proxy à l’aide de MSAL.NET
Lors de l’[initialisation d’une application cliente publique](msal-net-initializing-client-applications.md), vous pouvez utiliser la méthode `.WithHttpClientFactory method` pour fournir votre propre HttpClient.  La fourniture de votre propre HttpClient permet des scénarios avancés tels qu’un contrôle précis d’un proxy HTTP, la personnalisation des en-têtes de l’agent utilisateur ou l’obligation pour MSAL d’utiliser un HttpClient spécifique (par exemple dans les applications web/API ASP.NET Core).

## <a name="initialize-with-httpclientfactory"></a>Initialiser avec HttpClientFactory
L’exemple suivant montre comment créer un `HttpClientFactory` , puis initialiser une application cliente publique avec celui-ci :

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient et Xamarin iOS
Lorsque vous utilisez Xamarin iOS, il est recommandé de créer un `HttpClient` qui utilise explicitement le gestionnaire basé sur `NSURLSession` pour iOS 7 et versions ultérieures. MSAL.NET crée automatiquement un `HttpClient` qui utilise `NSURLSessionHandler` pour iOS 7 et versions ultérieures. Pour plus d’informations, consultez la [documentation Xamarin iOS pour HttpClient](/xamarin/cross-platform/macios/http-stack).