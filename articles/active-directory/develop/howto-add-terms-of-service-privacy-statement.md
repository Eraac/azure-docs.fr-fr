---
title: Conditions d’utilisation du service et déclaration de confidentialité pour les applications | Azure
description: Découvrez comment vous pouvez configurer les conditions d’utilisation et la déclaration de confidentialité des applications inscrites pour utiliser Azure AD.
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
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0a01b50573405964b09339d03e84c62dbdd8582
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482862"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Activation Configuration des conditions d’utilisation du service et de la déclaration de confidentialité d’une application

Les développeurs qui génèrent et gèrent des applications qui s’intègrent à Azure Active Directory (Azure AD) et à des comptes Microsoft doivent ajouter des liens vers les conditions d’utilisation et la déclaration de confidentialité de ces applications. Les conditions d’utilisation et la déclaration de confidentialité sont présentées aux utilisateurs par le biais de l’expérience de consentement de l’utilisateur. Elles permettent à vos utilisateurs de savoir qu’ils peuvent faire confiance à votre application. Les conditions d’utilisation et la déclaration de confidentialité sont particulièrement importantes pour les applications multilocataires accessibles aux utilisateurs (applications utilisées par plusieurs annuaires ou disponibles pour n’importe quel compte Microsoft).

Il vous incombe de créer les documents des conditions d’utilisation et de la déclaration de confidentialité pour votre application, ainsi que de fournir les URL permettant d’accéder à ces documents. Pour les applications multilocataires qui ne fournissent pas ces liens, l’expérience de consentement de l’utilisateur pour votre application affiche une alerte, ce qui peut décourager les utilisateurs de donner leur consentement pour votre application.

> [!NOTE]
> * Les applications monolocataires n’affichent pas d’alerte.
> * Si l’un des liens, ou des deux, sont manquants, votre application affiche une alerte.

## <a name="user-consent-experience"></a>Expérience de consentement de l'utilisateur

Les exemples suivants montrent l’expérience de consentement de l’utilisateur quand les conditions d’utilisation et la déclaration de confidentialité sont configurées et que ces liens ne le sont pas.

![Captures d’écran avec et sans déclaration de confidentialité et conditions d’utilisation fournies](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Mise en forme des liens vers les documents de conditions d’utilisation et de déclaration de confidentialité

Avant d’ajouter des liens vers les documents des conditions d’utilisation et de la déclaration de confidentialité de votre application, vérifiez que les URL respectent les instructions suivantes.

| Instruction     | Description                           |
|---------------|---------------------------------------|
| Format        | URL valide                             |
| Schémas valides | HTTP et HTTPS<br/>Nous recommandons HTTPS |
| Longueur maximale    | 2 048 caractères                       |

Exemples : `https://myapp.com/terms-of-service` et `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Ajout de liens aux conditions d’utilisation et à la déclaration de confidentialité

Quand les conditions d’utilisation et la déclaration de confidentialité sont prêtes, vous pouvez ajouter des liens vers ces documents dans votre application à l’aide de l’une des méthodes suivantes :

* [À l’aide du portail Azure](#azure-portal)
* [À l’aide de l’objet JSON de l’application](#app-object-json)
* [À l’aide de l’API REST de MSGraph version bêta](#msgraph-beta-rest-api)

### <a name="azure-portal"></a>À l’aide du Portail Azure
Suivez les étapes ci-dessous dans le portail Azure.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Accédez à la section **Inscriptions d’applications** et sélectionnez votre application.
3. Ouvrez le volet **Personnalisation**.
4. Remplissez les champs **URL des conditions d’utilisation** et **URL de la déclaration confidentialité**.
5. Enregistrez vos modifications.

    ![Les propriétés de l’application contiennent les URL des conditions d’utilisation du service et de la déclaration de confidentialité](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>À l’aide de l’objet JSON de l’application

Si vous préférez modifier directement l’objet JSON de l’application, vous pouvez utiliser l’éditeur de manifeste dans le portail Azure ou le portail d’inscription des applications pour inclure des liens vers les conditions d’utilisation et la déclaration de confidentialité de votre application.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>À l’aide de l’API REST de MSGraph version bêta

Pour mettre à jour toutes vos applications par programmation, vous pouvez utiliser l’API REST de MSGraph version bêta afin d’inclure des liens vers les documents des conditions d’utilisation et de la déclaration de confidentialité de votre application.

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Veillez à ne pas remplacer les valeurs préexistantes que vous avez affectées à l’un des champs suivants : `supportUrl`, `marketingUrl` et `logoUrl`.
> * L’API REST de MSGraph version bêta fonctionne uniquement quand vous vous connectez avec un compte Azure AD. Les comptes Microsoft personnels ne sont pas pris en charge.
