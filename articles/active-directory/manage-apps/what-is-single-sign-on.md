---
title: S’authentifier avec l’authentification unique auprès des applications - Azure Active Directory | Microsoft Docs
description: Découvrez comment choisir une méthode d’authentification unique lors de la configuration d’applications dans Azure Active Directory (Azure AD). Utilisez l’authentification unique pour éviter aux utilisateurs de devoir mémoriser des mots de passe pour chaque application et pour simplifier l’administration de la gestion des comptes.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: mimart
ms.reviewer: arvindh, japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3b23aa7db29390ef50a72f73fb153fef5301b92
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304832"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>S’authentifier avec l’authentification unique auprès des applications dans Azure Active Directory

L’authentification unique (SSO) est plus sûre et plus pratique quand les utilisateurs s’authentifient auprès d’applications dans Azure Active Directory (Azure AD). Cet article décrit les différentes méthodes d’authentification unique et vous aide à choisir la méthode SSO la plus appropriée lors de la configuration de vos applications.

- **Avec l’authentification unique**, les utilisateurs se connectent une seule fois avec un seul compte pour accéder à des appareils joints au domaine, à des ressources d’entreprise, à des applications Saas et à des applications web. Une fois la connexion effectuée, l’utilisateur peut lancer des applications à partir du portail Office 365 ou du panneau d’accès Azure AD MyApps. Les administrateurs peuvent centraliser la gestion des comptes d’utilisateur, et ajouter ou supprimer automatiquement l’accès utilisateur à des applications en fonction de l’appartenance à des groupes.

- **Sans l’authentification unique**, les utilisateurs doivent mémoriser des mots de passe spécifiques aux applications et se connecter à chaque application. L’équipe informatique doit créer et mettre à jour les comptes d’utilisateur pour chaque application, comme Office 365, Box et Salesforce. Les utilisateurs doivent mémoriser leurs mots de passe et passer du temps à se connecter à chaque application.

## <a name="choosing-a-single-sign-on-method"></a>Choix d’une méthode d’authentification unique

Vous pouvez configurer une application pour l’authentification unique de plusieurs façons. Le choix d’une méthode d’authentification unique dépend de la façon dont l’application est configurée pour l’authentification.

- Pour l’authentification unique, les applications cloud peuvent utiliser les méthodes suivantes : OpenID, OAuth, SAML, par mot de passe, liée ou désactivée. 
- Pour l’authentification unique, les applications locales peuvent utiliser les méthodes suivantes : par mot de passe, authentification Windows intégrée, basée sur l’en-tête, liée ou désactivée. Les choix pour les applications locales fonctionnent quand les applications sont configurées pour le proxy d’application.

Ce diagramme de flux vous permet de décider quelle méthode d’authentification unique est la plus adaptée à votre situation.

![Organigramme de décision pour la méthode d’authentification unique](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

Le tableau suivant récapitule les méthodes d’authentification unique et contient des liens vers plus d’informations.

| Méthode d’authentification unique | Types d’applications | Quand utiliser |
| :------ | :------- | :----- |
| [OpenID Connect et OAuth](#openid-connect-and-oauth) | Cloud uniquement | Utilisez OpenID Connect et OAuth lors du développement d’une nouvelle application. Ce protocole simplifie la configuration de l’application, propose des kits SDK faciles à utiliser et permet à votre application d’utiliser MS Graph.
| [SAML](#saml-sso) | Cloud et locales | Choisissez SAML autant que possible pour les applications existantes qui n’utilisent pas OpenID Connect ou OAuth. SAML fonctionne pour les applications qui s’authentifient par le biais d’un protocole SAML.|
| [Par mot de passe](#password-based-sso) | Cloud et locales | Choisissez la méthode par mot de passe quand l’application authentifie avec un nom d’utilisateur et un mot de passe. L’authentification unique par mot de passe permet de sécuriser le stockage et la lecture des mots de passe des applications avec une extension de navigateur web ou une application mobile. Cette méthode utilise le processus de connexion existant fourni par l’application, mais permet aux administrateurs de gérer les mots de passe. |
| [Liée](#linked-sign-on) | Cloud et locales | Choisissez l’authentification liée quand l’application est configurée pour l’authentification unique dans un autre service de fournisseur d’identité. Cette option n’ajoute pas l’authentification unique à l’application. Cependant, il se peut que l’authentification unique soit déjà implémentée dans l’application avec un autre service, comme les services de fédération Active Directory (AD FS).|
| [Désactivé](#disabled-sso) | Cloud et locales | Choisissez l’authentification unique désactivée quand l’application n’est pas prête à être configurée pour l’authentification unique. Les utilisateurs doivent entrer leur nom d’utilisateur et leur mot de passe chaque fois qu’ils lancent cette application.|
| [Authentification Windows intégrée (IWA)](#integrated-windows-authentication-iwa-sso) | Locales uniquement | Choisissez l’authentification unique IWA pour les applications qui utilisent l’[authentification Windows intégrée (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication), ou pour les applications prenant en charge les revendications. Pour IWA, les connecteurs de proxy d’application utilisent la délégation Kerberos contrainte (KCD) pour authentifier les utilisateurs auprès de l’application. |
| [Basée sur l’en-tête](#header-based-sso) | Locales uniquement | Utilisez l’authentification unique basée sur l’en-tête quand l’application utilise des en-têtes pour l’authentification. L’authentification unique basée sur l’en-tête nécessite PingAccess pour Azure AD. Le proxy d’application utilise Azure AD pour authentifier l’utilisateur, puis transmet le trafic via le service du connecteur.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect et OAuth

Lors du développement de nouvelles applications, utilisez des protocoles modernes comme OpenID Connect et OAuth afin de bénéficier de la meilleure expérience d’authentification unique pour votre application sur plusieurs plateformes de périphériques. OAuth permet aux utilisateurs ou aux administrateurs de [donner leur consentement](configure-user-consent.md) pour des ressources protégées comme [Microsoft Graph](/graph/overview). Nos [SDK](../develop/reference-v2-libraries.md) sont faciles à adopter pour votre application, laquelle est prête à utiliser [Microsoft Graph](/graph/overview).

Pour plus d'informations, consultez les pages suivantes :

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Guide du développeur sur la plateforme d’identités Microsoft](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>SAML SSO

Avec l’**authentification unique SAML**, Azure AD s’authentifie auprès de l’application en utilisant le compte Azure AD de l’utilisateur. Azure AD communique les informations d’authentification à l’application via un protocole de connexion. Avec l’authentification unique SAML, vous pouvez mapper les utilisateurs à des rôles d’application spécifiques en fonction de règles que vous définissez dans vos revendications SAML.

Choisissez l’authentification unique basée sur SAML lorsque l’application la prend en charge.

L’authentification unique SAML est prise en charge pour les applications qui utilisent un de ces protocoles :

- SAML 2.0
- Un certificat de fournisseur d'identité WS-Federation

Pour configurer une application SaaS pour l’authentification unique via SAML, consultez [Configurer l’authentification unique via SAML](configure-single-sign-on-portal.md). De plus, de nombreuses applications SaaS (Software as a service) offrent un [tutoriel spécifique à l’application](../saas-apps/tutorial-list.md) qui vous guide dans la configuration de l’authentification unique basée sur SAML.

Pour configurer une application pour WS-Federation, procédez de la même façon que pour configurer une application pour l’authentification unique via SAML, consultez [Configurer l’authentification unique via SAML](configure-single-sign-on-portal.md). Lors de l’étape de configuration de l’application pour utiliser Azure AD, vous devez remplacer l’URL de connexion Azure AD par le point de terminaison `https://login.microsoftonline.com/<tenant-ID>/wsfed` WS-Federation.

Pour configurer une application locale pour l’authentification unique via SAML, consultez [Authentification unique via SAML pour applications locales par le biais du proxy d’application](application-proxy-configure-single-sign-on-on-premises-apps.md).

Pour plus d’informations sur le protocole SAML, consultez [Protocole d’authentification unique SAML](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Authentification unique par mot de passe

Avec l’authentification par mot de passe, les utilisateurs se connectent à l’application au moyen d’un nom d’utilisateur et d’un mot de passe lorsqu’ils y accèdent la première fois. Après la première connexion, Azure AD fournit le nom d’utilisateur et le mot de passe à l’application.

L’authentification unique par mot de passe utilise le processus d’authentification existant fourni par l’application. Quand vous activez l’authentification unique par mot de passe pour une application, Azure AD collecte et stocke de façon sécurisée les noms d’utilisateur et les mots de passe pour l’application. Les informations d’identification de l’utilisateur sont stockées à l’état chiffré dans l’annuaire.

Choisissez l’authentification unique par mot de passe quand :

- Une application ne prend pas en charge le protocole d’authentification unique SAML.
- Une application s’authentifie avec un nom d’utilisateur et un mot de passe au lieu de jetons d’accès et d’en-têtes.

L’authentification unique par mot de passe est prise en charge pour toutes les applications cloud qui ont une page de connexion HTML. L’utilisateur peut utiliser un des navigateurs suivants :

- Internet Explorer 11 sur Windows 7 ou ultérieur
   > [!NOTE]
   > Internet Explorer fait l’objet d’une prise en charge limitée et ne reçoit plus les nouvelles mises à jour logicielles. Microsoft Edge est le navigateur recommandé.

- Microsoft Edge sur Windows 10 Édition anniversaire ou version ultérieure
- Chrome sur Windows 7 ou version ultérieure, et sur Mac OS X ou version ultérieure
- Firefox 26.0 ou version ultérieure sur Windows XP SP2 ou version ultérieure, et sur Mac OS X 10.6 ou version ultérieure

Pour configurer une application cloud pour l’authentification unique par mot de passe, consultez [Configurer l’application pour l’authentification unique par mot de passe](application-sign-in-problem-password-sso-gallery.md#configure-the-app-for-password-sso).

Pour configurer une application locale pour l’authentification unique via le proxy d’application, consultez [Authentification unique avec mise au coffre des mots de passe par le biais du proxy d’application](application-proxy-configure-single-sign-on-password-vaulting.md).

### <a name="how-authentication-works-for-password-based-sso"></a>Fonctionnement de l’authentification unique par mot de passe

Pour authentifier un utilisateur auprès d’une application, Azure AD récupère les informations d’identification de l’utilisateur dans l’annuaire et les entre dans la page de connexion de l’application.  Azure AD transmet les informations d’identification de l’utilisateur de façon sécurisée via une extension de navigateur web ou une application mobile. Ce processus permet à un administrateur de gérer les informations d’identification des utilisateurs et n’oblige pas les utilisateurs à mémoriser leur mot de passe.

> [!IMPORTANT]
> Les informations d’identification sont masquées pour l’utilisateur pendant le processus d’authentification automatisé. Cependant, les informations d’identification peuvent être découvertes avec des outils de débogage web. Les utilisateurs et les administrateurs doivent suivre les mêmes stratégies de sécurité que si les informations d’identification étaient entrées directement par l’utilisateur.

### <a name="managing-credentials-for-password-based-sso"></a>Gestion des informations d’identification pour l’authentification unique avec mot de passe

Les mots de passe pour chaque application peuvent être gérés par l’administrateur Azure AD ou par les utilisateurs.

Quand l’administrateur Azure AD gère les informations d’identification :  

- L’utilisateur n’a pas besoin de réinitialiser ou de mémoriser le nom d’utilisateur et le mot de passe. L’utilisateur peut accéder à l’application en cliquant sur celle-ci dans son panneau d’accès ou via un lien fourni.
- L’administrateur peut effectuer des tâches de gestion sur les informations d’identification. Par exemple, l’administrateur peut mettre à jour les accès aux applications en fonction des appartenances de groupe des utilisateurs et du statut des employés.
- L’administrateur peut utiliser des informations d’identification d’administration pour fournir l’accès à des applications partagées entre plusieurs utilisateurs. Par exemple, l’administrateur peut autoriser tous les utilisateurs qui peuvent accéder à une application à accéder à un réseau social ou à une application de partage de documents.

Quand l’utilisateur final gère les informations d’identification :

- Les utilisateurs peuvent gérer leurs mots de passe en les mettant à jour ou en les supprimant.
- Les administrateurs sont toujours en mesure de définir de nouvelles informations d’identification pour l’application.

## <a name="linked-sign-on"></a>Authentification liée

L’authentification unique liée permet à Azure AD de fournir l’authentification unique à une application qui est déjà configurée pour l’authentification unique dans un autre service. L’application liée peut apparaître aux utilisateurs finaux dans le portail Office 365 ou dans le portail Azure AD MyApps. Par exemple, un utilisateur peut lancer une application qui est configurée pour l’authentification unique dans les services de fédération Active Directory (AD FS) 2.0 à partir du portail Office 365. Des rapports supplémentaires sont également disponibles pour les applications liées qui sont lancées à partir du portail Office 365 ou du portail Azure AD MyApps.

### <a name="linked-sign-on-for-application-migration"></a>Authentification liée pour la migration de l’application

L’authentification liée peut fournir une expérience utilisateur homogène lorsque vous migrez des applications sur un certain laps de temps. Si vous migrez des applications vers Azure Active Directory, vous pouvez utiliser l’authentification liée pour publier rapidement des liens vers toutes les applications que vous voulez migrer.  Les utilisateurs peuvent trouver tous les liens dans le [portail MyApps](../user-help/active-directory-saas-access-panel-introduction.md) ou dans le [Lanceur d’applications Office 365](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Les utilisateurs ne savent pas qu’ils accèdent à une application liée ou à une application migrée.  

Une fois qu’un utilisateur s’est authentifié avec une application liée, un enregistrement de compte doit être créé avant que l’utilisateur final obtienne l’accès par authentification unique. Le provisionnement de cet enregistrement de compte peut se produire automatiquement ou il peut être effectué manuellement par un administrateur.

## <a name="disabled-sso"></a>Authentification unique désactivée

Le mode désactivé signifie que l’authentification unique n’est pas utilisée pour l’application. Quand l’authentification unique est désactivée, les utilisateurs peuvent avoir besoin de s’authentifier deux fois. Les utilisateurs s’authentifient d’abord auprès d’Azure AD, puis ils se connectent à l’application.

Utilisez le mode d’authentification unique désactivé :

- Si vous n’êtes pas prêt à intégrer cette application à l’authentification unique Azure AD, ou
- Si vous testez d’autres aspects de l’application, ou
- Comme couche de sécurité pour une application locale qui n’exige pas que les utilisateurs s’authentifient. Avec le mode désactivé, l’utilisateur doit s’authentifier.

## <a name="integrated-windows-authentication-iwa-sso"></a>Authentification unique Authentification Windows intégrée (IWA)

Le [proxy d’application](application-proxy.md) fournit l’authentification unique aux applications qui utilisent l’[authentification Windows intégrée (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication), ou aux applications prenant en charge les revendications. Si votre application utilise l’authentification Windows intégrée, le proxy d’application s’authentifie auprès de l’application avec la délégation contrainte Kerberos. Pour une application prenant en charge les revendications qui fait confiance à Azure Active Directory, l’authentification unique fonctionne, car l’utilisateur a déjà été authentifié avec Azure AD.

Choisissez le mode d'authentification unique Authentification Windows intégrée pour fournir une authentification unique à une application locale qui s’authentifie auprès d'IWA.

Pour configurer une application locale pour l’authentification Windows intégrée, consultez [Délégation contrainte Kerberos pour l’authentification unique auprès de vos applications avec le proxy d’application](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>Fonctionnement de l’authentification unique avec KCD
Ce diagramme explique le flux quand un utilisateur accède à une application locale qui utilise I’authentification Windows intégrée.

![Diagramme de flux de l’authentification Microsoft Azure AD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. L’utilisateur entre l’URL pour accéder à l’application locale via le proxy d’application.
1. Le proxy d’application redirige la demande vers les services d’authentification d’Azure AD pour effectuer la préauthentification. À ce stade, Azure AD applique les stratégies d’authentification et d’autorisation applicables, comme l’authentification multifacteur. Si l’utilisateur est validé, Azure AD crée un jeton et l’envoie à l’utilisateur.
1. L’utilisateur transmet le jeton au proxy d’application.
1. Le proxy d’application valide le jeton et récupère le nom d’utilisateur principal auprès du jeton. Il envoie ensuite la demande, le nom d’utilisateur principal et le nom de principal du service au connecteur, via un canal sécurisé doublement authentifié.
1. Le connecteur utilise la négociation de délégation contrainte Kerberos avec Active Directory local, en empruntant l’identité de l’utilisateur pour obtenir un jeton Kerberos pour l’application.
1. Active Directory envoie le jeton Kerberos de l’application au connecteur.
1. Le connecteur envoie la demande d’origine au serveur d’applications, en utilisant le jeton Kerberos reçu d’Active Directory.
1. L’application envoie la réponse au connecteur, qui est ensuite retournée au service de proxy d’application et enfin à l’utilisateur.

## <a name="header-based-sso"></a>Authentification unique basée sur l’en-tête

Utilisez l’authentification unique basée sur l’en-tête pour les applications qui utilisent des en-têtes pour l’authentification. Cette méthode d’authentification utilise un service d’authentification tiers appelé PingAccess. Un utilisateur doit seulement s’authentifier auprès d’Azure AD.

Choisissez l’authentification unique basée sur l’en-tête lorsque le proxy d’application et PingAccess sont configurés pour l’application.

Pour configurer l’authentification basée sur l’en-tête, consultez [Authentification basée sur l’en-tête pour l’authentification unique avec le proxy d’application](application-proxy-configure-single-sign-on-with-ping-access.md).

### <a name="what-is-pingaccess-for-azure-ad"></a>Présentation de PingAccess pour Azure AD

Avec PingAccess pour Azure AD, les utilisateurs peuvent accéder aux applications qui utilisent des en-têtes pour l’authentification et s’y connecter avec l’authentification unique. Le proxy d’application traite ces applications comme n’importe quelle autre application, en utilisant Azure AD pour authentifier l’accès, puis pour faire transiter le trafic via le service de connecteur. Une fois l’authentification effectuée, le service PingAccess traduit le jeton d’accès Azure AD en un format d’en-tête qui est envoyé à l’application.

Vos utilisateurs ne voient pas de différence quand ils se connectent pour utiliser vos applications d’entreprise. Ils pourront toujours travailler depuis n’importe où et sur n’importe quel appareil. Les connecteurs du proxy d’application redirigent le trafic distant vers l’ensemble des applications, et ils continuent à équilibrer la charge automatiquement.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Comment obtenir une licence pour PingAccess ?

Étant donné que ce scénario est proposé par le biais d’un partenariat entre Azure AD et PingAccess, vous avez besoin de licences pour les deux services. Toutefois, les abonnements Azure AD Premium incluent une licence PingAccess de base qui couvre jusqu’à 20 applications. Si vous devez publier plus de 20 applications basées sur un en-tête, vous pouvez acquérir une licence supplémentaire auprès de PingAccess.

Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="related-articles"></a>Articles connexes

- [Tutoriels pour l’intégration d’applications SaaS avec Azure Active Directory](../saas-apps/tutorial-list.md)
- [Tutoriel pour la configuration de l’authentification unique](configure-single-sign-on-portal.md)
- [Introduction à la gestion de l’accès aux applications](what-is-access-management.md)
- Lien de téléchargement : [Plan de déploiement de l’authentification unique](https://aka.ms/SSODeploymentPlan)
