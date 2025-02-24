---
title: Guide de formation sur les inscriptions d’applications dans le Portail Azure - Azure
description: Générez des flux d’authentification intégrés et sans navigateur à l’aide de l’octroi de code d’appareil.
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 118c6ecb16d325a384246a0b3d9e685f6f6f04ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870116"
---
# <a name="training-guide-app-registrations-in-the-azure-portal"></a>Guide de formation : inscriptions d’applications dans le Portail Azure  

La nouvelle expérience relative aux [inscriptions d’applications](https://go.microsoft.com/fwlink/?linkid=2083908) dans le Portail Azure a été considérablement améliorée. Si vous aviez l’habitude d’utiliser le processus hérité, consultez ce guide de formation pour vous familiariser avec la nouvelle expérience.

## <a name="key-changes"></a>Principales modifications

- Les inscriptions d’applications ne se limitent pas aux **applications/API web** ou aux applications **natives**. Vous pouvez utiliser le même processus d’inscription d’application pour ces dernières en inscrivant les URI de redirection correspondantes.
- L’expérience héritée prenait en charge les applications qui connectaient uniquement les comptes d’organisation (Azure AD). Les applications étaient inscrites en tant qu’applications à locataire unique (ne prenant en charge que les comptes d’organisation du répertoire dans lequel elles avaient été inscrites) et pouvaient devenir mutualisées (de manière à prendre en charge tous les comptes d’organisation). Le nouveau processus vous permet d’inscrire des applications pouvant gérer ces deux options, ainsi qu’une troisième option : tous les comptes d’organisation et comptes Microsoft personnels.
- L’expérience héritée était uniquement disponible lorsque vous vous connectiez au Portail Azure à l’aide d’un compte d’organisation. Dans le cadre du nouveau processus, vous pouvez utiliser des comptes Microsoft personnels qui ne sont pas associés à un répertoire.

## <a name="list-of-applications"></a>Liste des applications

- La nouvelle liste d’applications présente les applications qui ont été inscrites par le biais de l’expérience d’inscription d’application héritée dans le Portail Azure (applications qui connectent les comptes Azure AD), ainsi que les applications inscrites par l’intermédiaire du [Portail d’inscription d’application](https://apps.dev.microsoft.com/) (applications qui connectent les comptes Azure AD et les comptes Microsoft personnels).
- La nouvelle liste d’applications ne comporte aucune colonne **Type d’application** (étant donné qu’une inscription d’application unique peut présenter plusieurs types) et comprend deux nouvelles colonnes : une colonne **Créé le** et une colonne **Certificats et secrets** qui présente l’état (actuel, arrive bientôt à expiration, arrivé à expiration) des informations d’identification inscrites sur l’application.

## <a name="new-app-registration"></a>Nouveau processus d’inscription d’application

Dans le cadre de l’expérience d’inscription d’application héritée, vous deviez fournir les informations suivantes : **Nom**, **Type d’application** et **URL de connexion/URI de redirection**. Les applications qui étaient créées constituaient uniquement des applications à locataire unique Azure AD, ce qui signifie qu’elles ne prenaient en charge que les comptes d’organisation du répertoire dans lequel elles avaient été inscrites.

Avec le nouveau processus, vous devez fournir un **Nom** pour l’application et choisir les **Types de comptes pris en charge**. Si vous le souhaitez, vous pouvez également indiquer un **URI de redirection**. Si vous fournissez un URI de redirection, vous devez spécifier s’il s’agit d’un URI web/public (mobile et de bureau). Pour plus d’informations sur l’inscription d’une application à l’aide de la nouvelle expérience d’inscription d’application, consultez [ce démarrage rapide](quickstart-register-app.md).

## <a name="the-legacy-properties-page"></a>Page de propriétés héritée

L’expérience héritée comportait une page **Propriétés** qui n’apparaît pas dans le cadre du nouveau processus. Le panneau **Propriétés** présentait les champs suivants : **Nom**, **ID d’objet**, **ID d’application**, **URI ID d’application**, **Logo**, **URL de la page d’accueil**, **URL de déconnexion**, **URL des conditions d’utilisation du service**, **URL de la déclaration de confidentialité**, **Type d’application** et **Mutualisé**.

Dans la nouvelle expérience, les fonctionnalités équivalentes apparaissent aux emplacements suivants :

- Les champs **Nom**, **Logo**, **URL de la page d’accueil**, **URL des conditions d’utilisation du service** et **URL de la déclaration de confidentialité** figurent désormais sur la page **Personnalisation** de l’application.
- Les champs **ID d’objet** et **ID d’application (client)** apparaissent sur la page **Vue d’ensemble**.
- Les fonctionnalités contrôlées par la bascule **Mutualisé** dans l’expérience héritée ont été remplacées par le champ **Types de comptes pris en charge** sur la page **Authentification**. Pour plus d’informations sur la correspondance entre les options de la fonctionnalité Mutualisé et celles des types de compte pris en charge, consultez [ce démarrage rapide](quickstart-modify-supported-accounts.md).
- Le champ **URL de déconnexion** figure désormais sur la page **Authentification**.
- Le champ **Type d’application** n’est plus valide. À la place, les URI de redirection (qui apparaissent sur la page **Authentification**) déterminent les types d’applications pris en charge.
- Le champ **URI ID d’application** s’intitule désormais **URI de l’ID d’application** et figure dans le panneau **Exposer une API**. Dans l’expérience héritée, cette propriété était inscrite automatiquement au format suivant : `https://{tenantdomain}/{appID}` (par exemple, `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). Dans le nouveau format, elle est automatiquement générée sous la forme `api://{appID}`, mais elle doit être enregistrée explicitement.

## <a name="reply-urlsredirect-urls"></a>URL de réponse/URl de redirection

Dans l’expérience héritée, une application comportait une page **URL de réponse**. Dans la nouvelle expérience, les URL de réponse figurent dans la section **Authentification** d’une application. En outre, elles sont appelées **URI de redirection**. Par ailleurs, le format des URI de redirection a changé. Vous devez désormais les associer à un type d’application (web ou public). En outre, pour des raisons de sécurité, les caractères génériques et les schémas http:// ne sont pas pris en charge (à l’exception de http://localhost) ).

## <a name="keyscertificates--secrets"></a>Clés/Certificats et secrets

Dans l’expérience héritée, une application comportait une page **Clés**. Dans la nouvelle expérience, cette page s’intitule désormais **Certificats et secrets**. En outre, les **Clés publiques** sont appelées **Certificats**, et les **Mots de passe** sont désignés sous le terme de **Secrets client**.

## <a name="required-permissionsapi-permissions"></a>Autorisations nécessaires/API autorisées

- Dans l’expérience héritée, une application comportait une page **Autorisations nécessaires**. Dans la nouvelle expérience, cette page s’intitule désormais **API autorisées**.
- Lorsque vous sélectionniez une API dans l’expérience héritée, vous pouviez la choisir dans une liste d’API Microsoft restreinte ou la rechercher par le biais des principaux de service dans le locataire. Dans la nouvelle expérience, vous pouvez choisir l’API parmi plusieurs onglets : **API Microsoft**, **API utilisées par mon organisation** ou **Mes API**. La barre de recherche figurant sur **API utilisées par mon organisation** utilise les recherches d’onglet par le biais des principaux de service dans le locataire. 

   > [!NOTE]
   > Cet onglet n’apparaît pas si votre application n’est pas associée à un locataire. Pour plus d’informations sur la procédure à suivre pour demander des autorisations à l’aide de la nouvelle expérience, consultez [ce démarrage rapide](quickstart-configure-app-access-web-apis.md).

- Dans le cadre de l’expérience héritée, un bouton **Accorder des autorisations** figurait en haut de la page **Autorisations demandées**. Dans la nouvelle expérience, une section **Donner son consentement** comportant un bouton **Accorder le consentement administrateur** apparaît dans la section **API autorisées** d’une application. En outre, les boutons fonctionnent différemment entre les deux expériences :
   - Dans l’expérience héritée, la logique variait en fonction de l’utilisateur connecté et des autorisations demandées. La logique était la suivante :
      - Si les demandes portaient uniquement sur des autorisations avec consentement de l’utilisateur et que l’utilisateur connecté n’était pas un administrateur, l’utilisateur était en mesure d’accorder le consentement de l’utilisateur pour les autorisations demandées.
      - Si au moins une autorisation nécessitant le consentement de l’administrateur était demandée et que l’utilisateur connecté n’était pas un administrateur, l’utilisateur obtenait un message d’erreur lorsqu’il tentait de donner son consentement.
      - Si l’utilisateur connecté était un administrateur, le consentement de l’administrateur était accordé pour toutes les autorisations demandées.
   - Dans la nouvelle expérience, seul un administrateur peut donner son consentement. Lorsqu’un administrateur sélectionne le bouton **Accorder le consentement administrateur**, le consentement de l’administrateur est accordé pour toutes les autorisations demandées.

## <a name="deleting-an-app-registration"></a>Suppression d’une inscription d’application

Dans l’expérience héritée, une application ne pouvait être supprimée que s’il s’agissait d’une application à locataire unique. Le bouton de suppression était désactivé pour les applications mutualisées. Dans la nouvelle expérience, les applications sont supprimables quel que soit leur état, mais vous devez confirmer cette action. Pour plus d’informations sur la suppression d’inscriptions d’applications, consultez [ce démarrage rapide](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifeste d’application

La nouvelle expérience n’utilise pas la même version de format JSON que l’expérience héritée dans l’éditeur de manifeste. Pour plus d’informations, consultez l’article [Manifeste d’application](reference-app-manifest.md).

## <a name="new-ui"></a>Nouvelle interface utilisateur

Il existe une nouvelle interface utilisateur pour les propriétés, alors qu’auparavant, ces dernières étaient uniquement définissables à l’aide de l’éditeur de manifeste ou de l’API, ou n’existaient pas du tout.

- Le **flux d’octroi implicite** (oauth2AllowImplicitFlow) figure sur la page **Authentification**. Contrairement à l’expérience héritée, vous pouvez activer des **jetons d’accès** et/ou des **jetons d’ID**.
- Les champs **Étendues définies par cette API** (oauth2Permissions) et **Applications clientes autorisées** (preAuthorizedApplications) sont configurables par le biais de la page **Exposer une API**. Pour plus d’informations sur la configuration d’une application en tant qu’API web et sur l’exposition des autorisations/étendues, consultez [ce démarrage rapide](quickstart-configure-app-expose-web-apis.md).
- Le champ **Domaine de l’éditeur** (présenté aux utilisateurs dans [l’invite de consentement de l’application](application-consent-experience.md)) figure sur le panneau **Personnalisation**. Pour plus d’informations sur la configuration d’un domaine d’éditeur, consultez [cette procédure](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Limites

La nouvelle expérience présente les limites suivantes :

- La nouvelle expérience n’est actuellement pas disponible dans les locataires Azure AD B2C.
- Le format des clés secrètes clients (mots de passe d’application) diffère de celui de l’expérience héritée et arrête l’interface de ligne de commande.
- L’interface utilisateur ne gère pas la modification de la valeur des comptes pris en charge. Vous devez utiliser le manifeste de l’application, sauf si vous basculez entre une application Azure AD à locataire unique et une application mutualisée.
