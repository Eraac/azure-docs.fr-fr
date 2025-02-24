---
title: Activer une liste Microsoft AppSource et Place de marché Microsoft Azure à l’aide d’Azure Active Directory | Azure
description: Activer un type de liste à l’aide d’Azure Active Directory dans la Place de marché Azure et AppSource pour les éditeurs d’application et de service.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 09/12/2018
ms.author: pabutler
ms.openlocfilehash: 0b68687a2bbaa817f2776757ccab9571638c0fd5
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875996"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Activer une liste AppSource et Place de marché en utilisant Azure Active Directory

 Azure Active Directory (Azure AD) est un service d’identité cloud qui permet de s’authentifier avec un compte Microsoft. Azure AD utilise les infrastructures standard du secteur. [En savoir plus sur Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Avantages d’Azure AD

Les clients de Microsoft AppSource et de la Place de marché Azure utilisent les expériences intégrées au produit pour effectuer des recherches dans les catalogues. Ces actions nécessitent que les clients se connectent au produit. L’intégration Azure AD offre les avantages suivants :

- Un engagement plus rapide et une expérience client optimisée
- Une authentification unique (SSO) pour des millions d’utilisateurs en entreprise
- Une expérience d’authentification cohérente parmi les applications publiées par différents partenaires
- Une authentification multiplateforme scalable pour les applications mobiles et cloud

## <a name="offers-that-require-azure-ad"></a>Offres qui nécessitent Azure AD

Aux différents [types d’offre et options de référencement](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) pour AppSource et la Place de marché Azure correspondent différentes exigences pour l’implémentation d’Azure AD. Pour plus de détails, consultez le tableau suivant :

| **Type d’offre**    | **Authentification unique Azure AD nécessaire ?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Me contacter | Version d’évaluation | Version d’évaluation | Transaction |
| Machine virtuelle | N/A | Non | Non | Non |
| Applications Azure ( modèle de solution)  | N/A | N/A | N/A | N/A |
| Applications managées  | N/A | N/A | N/A | Non |
| SaaS  | Non | OUI | OUI | OUI |
| Containers  | N/A | N/A | N/A | Non |
| Services de conseil  | Non | N/A | N/A | N/A |

Pour plus d’informations sur les exigences techniques SaaS, consultez le [guide de publication de l’offre des applications SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Intégration Azure AD

- Pour savoir comment activer l’authentification unique en intégrant Azure AD dans votre liste, consultez [Azure Active Directory pour les développeurs]( https://aka.ms/aaddev).
- Pour obtenir des détails sur l’authentification unique Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Activer une liste d’essai

La configuration automatisée des clients peut accroître les chances de conversion. Quand un client sélectionne votre liste d’essai et qu’il est redirigé vers votre environnement d’essai, vous pouvez configurer le client directement sans avoir besoin d’étapes de connexion supplémentaires.

Pendant l’authentification, Azure AD envoie un jeton à votre application ou offre. Les informations utilisateur fournies par le jeton permettent la création d’un compte d’utilisateur dans votre application ou offre. Pour plus d’informations, consultez [Exemples de jetons](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

Quand vous utilisez Azure AD pour activer l’authentification en un clic dans votre application ou liste d’essai, vous :

- Simplifiez l’expérience du client entre la Place de marché et votre liste d’essai.
- Laissez l’impression d’une utilisation dans le produit, même quand l’utilisateur est redirigé de la Place de marché vers votre domaine ou environnement d’essai.
- Réduisez la probabilité d’un abandon quand les utilisateurs sont redirigés du fait de l’absence d’étapes de connexion supplémentaires.
- Réduisez les obstacles de déploiement pour les nombreux utilisateurs d’Azure AD.

## <a name="verify-azure-ad-integration"></a>Vérifier l’intégration Azure AD

### <a name="multitenant-solutions"></a>Solutions multi-locataires

Utilisez Azure AD pour permettre les actions suivantes :

- Inscrire votre application dans l’une des vitrines de la Place de marché. Pour plus d’informations, consultez [Inscription d’une application](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) ou [Certification AppSource](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).
- Activez la fonctionnalité de prise en charge multilocataire dans Azure AD pour bénéficier de l’expérience de version d’évaluation en un clic.

Si vous n’avez jamais utilisé l’authentification unique fédérée Azure AD, effectuez les étapes suivantes :

1. Inscrivez votre application sur la Place de marché.
1. Développez l’authentification unique avec Azure AD en utilisant [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) ou [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Activez la fonctionnalité de prise en charge multilocataire dans Azure AD pour fournir une expérience de version d’évaluation en un clic.

### <a name="single-tenant-solutions"></a>Solutions à un seul locataire

Utilisez Azure AD pour permettre l’une des actions suivantes :

- Ajouter des utilisateurs invités à votre annuaire en utilisant [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Configurer manuellement des versions d’évaluation pour les clients en utilisant l’option de publication **Me contacter**.
- Développer un test drive par client.
- Créez un exemple d’application de démonstration mutualisée utilisant le SSO.

## <a name="next-steps"></a>Étapes suivantes

- Vérifiez que vous êtes [inscrit sur la Place de marché Azure](https://azuremarketplace.microsoft.com/sell).
- Pour plus d’informations sur la façon de créer ou de compléter votre offre, consultez [Créer un compte Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).
