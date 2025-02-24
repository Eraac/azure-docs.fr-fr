---
title: API Graph Azure Active Directory | Microsoft Docs
description: Vue d’ensemble et guide de démarrage rapide de l’API Azure AD Graph qui permet un accès à Azure AD via les points de terminaison API REST, par l’intermédiaire d’un programme.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/01/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77730ea7302b4abd6c17ebfe5620c0dc55fa407c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544581"
---
# <a name="azure-active-directory-graph-api"></a>API Graph Azure Active Directory

> [!IMPORTANT]
>
> Depuis février 2019, nous avons lancé le processus ayant pour but de déprécier certaines versions antérieures de l’API Azure Active Directory Graph au profit de l’API Microsoft Graph. 
>
> Pour plus de détails, de mises à jour et de délais, consultez la page [Microsoft Graph ou Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) dans le centre de développement Office.
>
> Pour l’avenir, les applications doivent utiliser l’API Microsoft Graph. 



Cet article s’applique à l’API Graph Azure AD. Pour obtenir des informations similaires sur l’API Microsoft Graph, consultez [Utiliser l’API Microsoft Graph](https://docs.microsoft.com/graph/use-the-api). 

L’API Graph Azure Active Directory fournit un accès par programme à Azure AD via les points de terminaison de l’API REST. Les applications peuvent utiliser l’API Azure AD Graph pour des opérations de création, de lecture, de mise à jour et de suppression (CRUD) sur des données et objets du répertoire. Par exemple, l’API Azure AD Graph prend en charge les opérations courantes suivantes pour un objet utilisateur :

* Création d’un nouvel utilisateur dans un répertoire
* Obtention des propriétés détaillées d’un utilisateur, par exemple ses groupes
* Mise à jour des propriétés d’un utilisateur, par exemple son emplacement et son numéro de téléphone, ou modification de son mot de passe
* Vérification de l’appartenance de l’utilisateur au groupe pour les accès basés sur les rôles
* Désactivation ou suppression totale d’un compte d’utilisateur

De plus, vous pouvez effectuer des opérations similaires sur d’autres objets, comme les groupes et les applications. Pour appeler l’API Azure AD Graph sur un répertoire, votre application doit être inscrite auprès d’Azure AD. Elle doit également avoir accès à l’API Azure AD Graph. Cette autorisation fait généralement l’objet d’un consentement de la part de l’utilisateur ou de l’administrateur.

Pour commencer à utiliser l’API Azure Active Directory Graph, consultez le [Guide de démarrage rapide de l’API Graph Azure AD](active-directory-graph-api-quickstart.md) ou la [documentation interactive de référence de l’API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Caractéristiques

L’API Azure AD Graph fournit les fonctionnalités suivantes :

* **Points de terminaison de l’API REST** : l’API Graph Azure AD est un service RESTful constitué de points de terminaison accessibles via des requêtes HTTP standard. L’API Azure AD Graph prend en charge les types de contenu XML ou Javascript Objet Notation (JSON) pour les requêtes et les réponses. Pour plus d’informations, consultez [Informations de référence sur l’API REST Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Authentification avec Azure AD** : chaque requête à l’API Graph Azure AD doit être authentifiée en ajoutant un JWT (JSON Web Token) dans l’en-tête d’autorisation de la requête. Ce jeton est obtenu via une requête effectuée sur le point de terminaison du jeton Azure AD et la fourniture d’informations d’identification valides. Vous pouvez utiliser le processus d’informations d’identification client OAuth 2.0 ou le processus d’octroi de code d’autorisation afin d’obtenir un jeton pour appeler Graph. Pour plus d’informations, consultez [OAuth 2.0 dans Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Autorisation basée sur les rôles (RBAC)**  : des groupes de sécurité sont utilisés pour affecter des RBAC dans l’API Graph Azure AD. Par exemple, si vous souhaitez déterminer si un utilisateur a accès à une ressource spécifique, l’application peut appeler l’opération de [vérification de l’appartenance au groupe (opération transitive)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups), qui renvoie true ou false.
* **Requête différentielle** : la requête différentielle vous permet de vérifier les modifications apportées dans un répertoire entre deux périodes, sans avoir à envoyer des requêtes fréquentes à l’API Graph Azure AD. Ce type de demande renvoie uniquement les modifications apportées entre la demande de requête différentielle précédente et la demande en cours. Pour plus d’informations, consultez [Requête différentielle de l’API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Extensions d’annuaire** : vous pouvez ajouter des propriétés personnalisées aux objets d’annuaire sans recourir à une banque de données externe. Par exemple, si votre application nécessite une propriété d’identifiant Skype pour chaque utilisateur, vous pouvez enregistrer la nouvelle propriété dans le répertoire. Elle sera alors disponible sur chaque objet utilisateur. Pour plus d’informations, consultez [Extensions de schéma d’annuaire de l’API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Sécurisation par des étendues d’autorisation** : l’API Graph Azure AD expose les étendues d’autorisation qui permettent un accès sécurisé aux données Azure AD avec OAuth 2.0. Cette méthode prend en charge différents types d’applications clientes, notamment :
  
  * les interfaces utilisateur qui bénéficient d’un accès délégué aux données par le biais de l’autorisation de l’utilisateur connecté (délégué),
  * les applications de service/démon qui s’exécutent à l’arrière-plan sans qu’un utilisateur connecté soit présent et qui utilisent un contrôle d’accès en fonction du rôle défini par l’application
    
    Les autorisations déléguées et des applications représentent un privilège exposé par l’API Azure AD Graph. Elles peuvent être demandées par les applications clientes au moyen des fonctionnalités d’autorisation d’inscription des applications sur le [Portail Azure](https://portal.azure.com). Les [étendues d’autorisation de l’API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) fournissent des informations sur ce qui peut être utilisé par votre application cliente.

## <a name="scenarios"></a>Scénarios

L’API Azure AD Graph sert dans de nombreux scénarios d’application. Les scénarios suivants sont les plus courants :

* **Application métier (monolocataire)**  : dans ce scénario, un développeur en entreprise travaille pour une organisation qui a un abonnement Office 365. Le développeur crée une application web qui interagit avec Azure AD pour effectuer des tâches telles que l’attribution d’une licence à un utilisateur. Cette tâche nécessite l’accès à l’API Azure AD Graph. Le développeur enregistre donc l’application à un seul locataire dans Azure AD, puis configure les autorisations en lecture et en écriture pour l’API Azure AD Graph. Ensuite, l’application est configurée pour utiliser ses propres informations d’identification ou celles de l’utilisateur actuellement connecté, afin de se procurer un jeton et d’appeler l’API Azure AD Graph.
* **Application Software as a Service (multilocataire)**  : dans ce scénario, un éditeur de logiciels indépendant (ISV) développe une application web multilocataire hébergée qui fournit des fonctionnalités de gestion utilisateur à d’autres organisations qui utilisent Azure AD. Ces fonctionnalités nécessitant l’accès aux objets d’annuaire, l’application doit donc appeler l’API Azure AD Graph. Le développeur enregistre l’application dans Azure AD, la configure pour exiger des autorisations en lecture et en écriture pour l’API Azure AD Graph, puis active l’accès externe afin que d’autres organisations puissent consentir à utiliser l’application dans leur répertoire. Lorsqu’un utilisateur d’une autre organisation s’authentifie à l’application pour la première fois, une boîte de dialogue de consentement s’affiche, avec les autorisations demandées par l’application. Ce consentement accorde à l’application les autorisations demandées sur l’API Azure AD Graph dans le répertoire de l’utilisateur. Pour plus d’informations sur l’infrastructure de consentement, consultez [Vue d’ensemble de l’infrastructure de consentement](consent-framework.md).

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser l’API Graph Azure Active Directory, consultez les rubriques suivantes :

* [Guide de démarrage rapide pour l’API Graph Azure AD](active-directory-graph-api-quickstart.md)
* [Documentation REST Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
