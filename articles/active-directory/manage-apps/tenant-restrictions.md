---
title: Utiliser des restrictions liées au locataire pour gérer l’accès aux applications cloud SaaS - Azure | Microsoft Docs
description: Comment utiliser des restrictions liées au locataire pour gérer les utilisateurs qui peuvent accéder aux applications en fonction de leur client Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: mimart
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a340663a1ec4ddf748c6dc2bc3a4e2ce0c4228e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65824377"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Utiliser des restrictions liées au locataire pour gérer l’accès aux applications cloud SaaS

Les organisations de grande taille qui se préoccupent de la sécurité veulent adopter les services cloud, comme Office 365, tout en s’assurant que leurs utilisateurs ne pourront accéder qu’à des ressources approuvées. En général, pour gérer l’accès, les entreprises limitent les noms de domaine ou les adresses IP. Cette approche échoue dans un monde où les applications software as a service (ou SaaS) sont hébergées dans un cloud public et s’exécutent sur des noms de domaine partagés comme [outlook.office.com](https://outlook.office.com/) et [login.microsoftonline.com](https://login.microsoftonline.com/). Le fait de bloquer ces adresses empêche les utilisateurs d’accéder à Outlook sur le web entièrement, au lieu de restreindre simplement leur accès à des identités et des ressources approuvées.

La solution Azure Active Directory (Azure AD) consiste en une fonctionnalité appelée Restrictions liées au locataire. Grâce aux restrictions liées au locataire, les organisations peuvent contrôler l’accès aux applications cloud SaaS, en fonction du client Azure AD utilisé par les applications pour l’authentification unique. Par exemple, vous souhaitez peut-être autoriser l’accès aux applications Office 365 de votre organisation, tout en empêchant l’accès aux instances d’autres organisations de ces mêmes applications.  

Grâce aux restrictions liées au locataire, les organisations peuvent spécifier la liste des locataires auxquels leurs utilisateurs sont autorisés à accéder. Dans ce cas, Azure AD accorde uniquement l’accès à ces clients autorisés.

Cet article se concentre sur les restrictions liées au locataire pour Office 365, mais cette fonctionnalité doit fonctionner avec n’importe quelle application cloud SaaS qui utilise des protocoles d’authentification moderne avec Azure AD pour l’authentification unique. Si vous utilisez des applications SaaS avec un client Azure AD différent du client utilisé par Office 365, assurez-vous que tous les clients nécessaires sont autorisés. Pour plus d’informations sur les applications cloud SaaS, consultez [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Fonctionnement

La solution comprend les composants suivants :

1. **Azure AD** : si le paramètre `Restrict-Access-To-Tenants: <permitted tenant list>` est présent, Azure AD émet uniquement des jetons de sécurité pour les clients autorisés.

2. **Infrastructure de serveur proxy locale** : cette infrastructure est un appareil proxy capable d’inspection SSL (Secure Sockets Layer). Vous devez configurer le serveur proxy pour insérer l’en-tête contenant la liste des locataires autorisés dans le trafic destiné à Azure AD.

3. **Logiciel client** : pour prendre en charge les restrictions liées au locataire, le logiciel client doit demander des jetons directement à Azure AD, afin que l’infrastructure du proxy puisse intercepter le trafic. Actuellement, les applications Office 365 basées sur un navigateur prennent en charge les restrictions liées au locataire, comme les clients Office qui utilisent l’authentification moderne (comme OAuth 2.0).

4. **Authentification moderne** : les services cloud doivent utiliser l’authentification moderne pour utiliser les restrictions liées au locataire et bloquer l’accès à tous les locataires non autorisés. Vous devez configurer les services cloud Office 365 pour qu’ils utilisent les protocoles d’authentification moderne par défaut. Pour plus d’informations sur la prise en charge de l’authentification moderne par Office 365, consultez [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) (Authentification moderne Office 365 mise à jour).

Le schéma suivant illustre le flux de trafic de niveau supérieur. Les restrictions liées au locataire nécessitent l’inspection SSL uniquement pour le trafic vers Azure AD, pas pour le trafic vers les services cloud Office 365. Cette distinction est importante, car le volume de trafic pour l’authentification auprès d’Azure AD est généralement beaucoup plus faible que le volume de trafic vers les applications SaaS comme Exchange Online et SharePoint Online.

![Flux de trafic des restrictions liées au locataire - Schéma](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Configurer les restrictions liées au locataire

Deux étapes sont nécessaires pour bien démarrer avec les restrictions liées au locataire. Premièrement, assurez-vous que vos locataires peuvent se connecter aux adresses correctes. Deuxièmement, configurez l’infrastructure de votre proxy.

### <a name="urls-and-ip-addresses"></a>URL et adresses IP

Pour utiliser les restrictions liées au locataire, vos locataires doivent être en mesure de se connecter aux URL AD Azure suivantes pour s’authentifier : [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/) et [login.windows.net](https://login.windows.net/). En outre, pour accéder à Office 365, vos locataires doivent également être en mesure de se connecter aux noms de domaine complets (FQDN)/URL et adresses IP définis dans [URL et plages d’adresses IP Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Configuration du proxy et conditions requises

La configuration suivante est nécessaire pour activer les restrictions liées au locataire dans l’infrastructure de votre proxy. Ce guide est générique, donc consultez la documentation du fabricant de votre proxy pour obtenir les étapes d’implémentation spécifiques.

#### <a name="prerequisites"></a>Prérequis

- Le proxy doit être en mesure d’effectuer l’interception SSL, l’insertion d’en-tête HTTP et de filtrer les destinations à l’aide des noms de domaine complets/URL.

- Les clients doivent approuver la chaîne de certificat présentée par le proxy pour les communications SSL. Par exemple, si des certificats d’une [infrastructure à clé publique](/windows/desktop/seccertenroll/public-key-infrastructure) interne sont utilisés, le certificat d’autorité de certificat racine émetteur interne doit être approuvé.

- Cette fonctionnalité est incluse dans les abonnements Office 365, mais si vous souhaitez utiliser les restrictions liées au locataire pour contrôler l’accès à d’autres applications SaaS, alors vous aurez besoin de licences Premium 1 Azure AD.

#### <a name="configuration"></a>Configuration

Pour chaque demande entrante sur login.microsoftonline.com, login.microsoft.com et login.windows.net, insérez deux en-têtes HTTP : *Restrict-Access-To-Tenants* et *Restrict-Access-Context*.

Les éléments suivants doivent être inclus dans les en-têtes :

- Pour *Restrict-Access-To-Tenants*, utilisez une valeur de \<permitted tenant list\> (liste de locataires autorisés), qui est une liste séparée par des virgules des locataires auxquels vous souhaitez que les utilisateurs puissent accéder. N’importe quel domaine qui est inscrit auprès d’un client peut être utilisé pour identifier le client dans cette liste. Par exemple, pour autoriser l’accès aux clients Contoso et Fabrikam, la paire nom/valeur ressemble à :  `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Pour *Restrict-Access-Context*, utilisez une valeur d’ID de répertoire unique, déclarant quel locataire définit les restrictions liées au locataire. Par exemple, pour déclarer Contoso en tant que locataire qui définit la stratégie de restrictions liées au locataire, la paire nom/valeur ressemble à :  `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Vous trouverez votre ID de répertoire dans le [portail Azure Active Directory](https://aad.portal.azure.com/). Connectez-vous en tant qu’administrateur, sélectionnez **Azure Active Directory**, puis sélectionnez **Propriétés**.

Pour empêcher les utilisateurs d’insérer leur propre en-tête HTTP avec des locataires non approuvés, le proxy doit remplacer l’en-tête *Restrict-Access-To-Tenants* si celui-ci est déjà présent dans la requête entrante.

Les clients doivent être forcés à utiliser le proxy pour toutes les demandes à login.microsoftonline.com, login.microsoft.com et login.windows.net. Par exemple, si des fichiers PAC sont utilisés pour indiquer aux locataires d’utiliser le proxy, les utilisateurs finaux ne doivent pas être en mesure de modifier ou de désactiver les fichiers PAC.

## <a name="the-user-experience"></a>Expérience utilisateur

Cette section décrit l’expérience des utilisateurs finaux et des administrateurs.

### <a name="end-user-experience"></a>Expérience de l’utilisateur final

Par exemple, un utilisateur sur le réseau Contoso tente d’accéder à l’instance Fabrikam d’une application SaaS partagée comme Outlook Online. Si Fabrikam est un locataire non autorisé pour l’instance Contoso, l’utilisateur voit un message de refus d’accès, qui indique que vous tentez d’accéder à une ressource qui appartient à une organisation non approuvée par votre service informatique.

### <a name="admin-experience"></a>Expérience administrateur

Bien que la configuration des restrictions liées au locataire est effectuée sur l’infrastructure du proxy d’entreprise, les administrateurs peuvent accéder aux rapports sur les restrictions liées au locataire directement dans le portail Azure. Pour afficher les rapports :

1. Connectez-vous au [portail Azure Active Directory](https://aad.portal.azure.com/). Le tableau de bord du **Centre d’administration Azure Active Directory** s’affiche.

2. Sélectionnez **Azure Active Directory** dans le volet de gauche. La vue d’ensemble d’Azure Active Directory s’affiche.

3. Sous le titre **Autres fonctionnalités**, sélectionnez **Restrictions liées au locataire**.

L’administrateur du locataire spécifié en tant que locataire Restricted-Access-Context peut utiliser ce rapport pour afficher les connexions bloquées en raison de la stratégie de restrictions liées au locataire, notamment l’identité utilisée et l’ID du répertoire cible. Les connexions sont incluses si le client définissant la restriction est le client de l’utilisateur, ou le client de la ressource pour la connexion.

Comme pour les autres rapports dans le portail Azure, vous pouvez utiliser des filtres pour spécifier l’étendue de votre rapport. Vous pouvez filtrer par intervalle de temps, utilisateur, application, locataire ou état spécifique. Si vous sélectionnez le boulon **Colonnes**, vous pouvez choisir d’afficher les données avec n’importe quelle combinaison des champs suivants :

- **Utilisateur**
- **Application**
- **État**
- **Date**
- **Date (UTC)** (où UTC est le temps universel coordonné)
- **Méthode d’authentification MFA** (méthode d’authentification multifacteur)
- **Détail d’authentification MFA** (détail d’authentification multifacteur)
- **Résultat MFA**
- **Adresse IP**
- **Client**
- **Nom d’utilisateur**
- **Lieu**
- **ID du locataire cible**

## <a name="office-365-support"></a>Prise en charge d’Office 365

Les applications Office 365 doivent répondre à deux critères pour prendre pleinement en charge les restrictions liées au locataire :

1. Le locataire utilisé prend en charge l’authentification moderne.
2. L’authentification moderne est activée comme protocole d’authentification par défaut pour le service cloud.

Consultez [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) (Authentification moderne Office 365 mise à jour) pour plus d’informations sur les clients Office qui prennent actuellement en charge l’authentification moderne. Cette page inclut également des liens vers des instructions relatives à l’activation de l’authentification moderne sur des clients Exchange Online et Skype Entreprise Online spécifiques. SharePoint Online active déjà l’authentification moderne par défaut.

Les applications Office 365 basées sur un navigateur (Portail Office, Yammer, sites SharePoint, Outlook sur le web, etc.) prennent actuellement en charge les restrictions liées au locataire. Les locataires lourds (Outlook, Skype Entreprise, Word, Excel, PowerPoint, etc.) peuvent appliquer des restrictions liées au locataire uniquement lorsque vous utilisez l’authentification moderne.  

Les locataires Outlook et Skype Entreprise qui prennent en charge l’authentification moderne peuvent continuer à utiliser les protocoles hérités sur les locataires pour lesquels l’authentification moderne n’est pas activée, contournant ainsi les restrictions liées au locataire. Les restrictions liées au locataire peuvent bloquer des applications qui utilisent des protocoles hérités si elles contactent login.microsoftonline.com, login.microsoft.com ou login.windows.net lors de l’authentification.

Pour Outlook sur Windows, les clients peuvent choisir d’implémenter des restrictions qui empêchent les utilisateurs finaux d’ajouter des comptes de messagerie non approuvés à leurs profils. Par exemple, consultez le paramètre de stratégie de groupe [Empêcher l’ajout de comptes Exchange personnalisés](https://gpsearch.azurewebsites.net/default.aspx?ref=1).

## <a name="testing"></a>Test

Si vous souhaitez tester les restrictions liées au locataire avant de les implémenter pour l’ensemble de votre organisation, deux options sont à votre disposition : une approche basée sur l’hôte à l’aide d’un outil comme Fiddler ou un déploiement par étapes des paramètres du proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler pour une approche basée sur hôte

Fiddler est un proxy de débogage web gratuit qui peut être utilisé pour capturer et modifier le trafic HTTP/HTTPS, notamment l’insertion d’en-têtes HTTP. Pour configurer Fiddler afin de tester les restrictions liées au locataire, procédez comme suit :

1. [Téléchargez et installez Fiddler](https://www.telerik.com/fiddler).

2. Configurez Fiddler pour déchiffrer le trafic HTTPS, conformément à [la documentation d’aide de Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Configurez Fiddler pour insérer les en-têtes *Restrict-Access-To-Tenants* et *Restrict-Access-Context* à l’aide de règles personnalisées :

   1. Dans l’outil débogueur web Fiddler, sélectionnez le menu **Règles** et sélectionnez **Personnaliser les règles...** pour ouvrir le fichier CustomRules.

   2. Ajoutez les lignes suivantes au début de la fonction `OnBeforeRequest`. Remplacez le \<domaine du locataire\> par un domaine enregistré auprès de votre locataire (par exemple, `contoso.onmicrosoft.com`). Remplacez \<l’ID de répertoire\> par l’identificateur GUID Azure AD de votre client.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      Si vous avez besoin d’autoriser plusieurs clients, utilisez une virgule pour séparer les noms des clients. Par exemple :

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Enregistrez et fermez le fichier CustomRules.

Après avoir configuré Fiddler, vous pouvez capturer le trafic en accédant au menu **Fichier** et en sélectionnant **Capturer le trafic**.

### <a name="staged-rollout-of-proxy-settings"></a>Déploiement par étapes des paramètres du proxy

En fonction des capacités de votre infrastructure de proxy, vous pourriez être en mesure d’étalonner le déploiement des paramètres pour vos utilisateurs. Voici deux options principales à prendre en compte :

1. Utilisez des fichiers PAC pour pointer les utilisateurs test vers une infrastructure de proxy test, tandis que les utilisateurs normaux continuent à utiliser l’infrastructure du proxy de production.
2. Certains serveurs proxy peuvent prendre en charge des configurations différentes à l’aide de groupes.

Pour obtenir des informations spécifiques, consultez la documentation de votre serveur proxy.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [l’authentification moderne Office 365 mise à jour](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Consultez les [URL et plages d’adresses IP Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
