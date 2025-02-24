---
title: Automatiser l’approvisionnement des applications avec SCIM dans Azure Active Directory | Microsoft Docs
description: Azure Active Directory peut configurer automatiquement les utilisateurs et les groupes sur une application ou un magasin d’identités avec en façade un service web avec l’interface définie dans Spécification du protocole SCIM.
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
ms.date: 05/06/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a51401bcb8d282fef10b0b06e646b652bf5f8e8
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67513400"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Utilisation du protocole SCIM (System for Cross-Domain Identity Management) pour configurer automatiquement des utilisateurs et groupes d’Azure Active Directory dans des applications

## <a name="overview"></a>Vue d'ensemble

SCIM est le protocole normalisé et le schéma qui vise à offrir une meilleure cohérence dans la gestion des identités sur les systèmes. Lorsqu’une application prend en charge un point de terminaison SCIM pour la gestion de l’utilisateur, le service d’approvisionnement utilisateur d’Azure AD peut envoyer des demandes pour créer, modifier ou supprimer des utilisateurs et groupes dans ce point de terminaison. 

Un grand nombre d’applications pour lesquelles Azure AD prend en charge l’[approvisionnement d’utilisateur automatique préintégré](../saas-apps/tutorial-list.md) intègrent SCIM en tant que moyens de recevoir des notifications de modification.  En plus de ces applications, les clients peuvent connecter des applications qui prennent en charge un profil spécifique de [spécification de protocole SCIM 2.0](https://tools.ietf.org/html/rfc7644) à l’aide de l’option d’intégration générique « ne figurant pas dans la galerie » dans le portail Azure. 

Cet article se concentre principalement sur le profil de SCIM 2.0 qu’implémente Azure AD dans le cadre de son connecteur SCIM générique pour des applications ne figurant pas dans la galerie. Toutefois, un test réussi d’une application qui prend en charge SCIM avec le connecteur Azure AD générique contribue à l’obtention d’une application listée dans la galerie Azure AD prenant en charge l’approvisionnement d’utilisateur. Pour plus d’informations sur l’obtention de votre application répertoriée dans la galerie d’applications Azure AD, consultez [ Afficher votre application dans la galerie d’applications Azure AD](../develop/howto-app-gallery-listing.md).
 

>[!IMPORTANT]
>La dernière mise à jour du comportement de l’implémentation SCIM d’Azure AD a été effectuée le 18 décembre 2018. Pour plus d’informations sur ce qui a changé, consultez [Conformité au protocole SCIM 2.0 du service de provisionnement des utilisateurs Azure AD](application-provisioning-config-problem-scim-compatibility.md).

![][0]
*Figure 1 : Approvisionnement d’Azure Active Directory à un magasin d’application ou d’identité qui implémente SCIM*

Cet article est divisé en quatre sections :

* **[Approvisionnement d’utilisateurs et de groupes dans des applications tierces qui prennent en charge SCIM 2.0](#provisioning-users-and-groups-to-applications-that-support-scim)** : si votre organisation utilise une application tierce qui implémente le profil SCIM 2.0 pris en charge par Azure AD, vous pouvez commencer à automatiser l’approvisionnement et l’annulation de l’approvisionnement d’utilisateurs et de groupes dès aujourd’hui.

* **[Comprendre l’implémentation de SCIM d’Azure AD](#understanding-the-azure-ad-scim-implementation)** : si vous créez une application qui prend en charge une API de gestion utilisateur SCIM 2.0, cette section décrit en détail comment le client SCIM d’Azure AD est implémenté et comment vous devez modéliser la gestion et les réponses de la requête du protocole SCIM.
  
* **[Créer un point de terminaison SCIM via des bibliothèques Microsoft CLI (Common Language Infrastructure)](#building-a-scim-endpoint-using-microsoft-cli-libraries)** : les bibliothèques CLI ainsi que des exemples de code vous indiquent comment développer un point de terminaison SCIM et convertir les messages SCIM.  

* **[Référence du schéma utilisateur et groupe](#user-and-group-schema-reference)** : décrit le schéma de groupe et d’utilisateur pris en charge par l’implémentation de SCIM d’Azure AD pour les applications ne figurant pas dans la galerie. 

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Approvisionnement d’utilisateurs et de groupes pour les applications qui prennent en charge SCIM
Azure AD peut être configuré pour attribuer automatiquement des utilisateurs et groupes dans les applications qui implémentent un profil spécifique du [protocole SCIM 2.0](https://tools.ietf.org/html/rfc7644). Les détails du profil sont documentés dans [Comprendre l’implémentation de SCIM d’Azure AD](#understanding-the-azure-ad-scim-implementation).

Vérifiez avec votre fournisseur d’application ou dans la documentation du fournisseur de votre application la conformité à ces exigences.

>[!IMPORTANT]
>L’implémentation de SCIM d’Azure AD repose sur le service d’approvisionnement d’utilisateur Azure AD, conçu pour conserver constamment les utilisateurs synchronisés avec Azure AD et l’application cible, et implémente un ensemble très spécifique d’opérations standard. Il est important de comprendre ces comportements afin de cerner le comportement du client SCIM d’Azure AD. Pour plus d’informations, consultez [Que se passe-t-il pendant l’approvisionnement d’utilisateurs ?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Prise en main
Les applications qui prennent en charge le profil SCIM décrit dans cet article peuvent être connectées à Azure Active Directory à l’aide de la fonctionnalité « application ne figurant pas dans la galerie » dans la galerie d’applications Azure AD. Une fois connecté, Azure AD exécute toutes les 40 minutes un processus de synchronisation au cours duquel il interroge le point de terminaison de SCIM de l’application concernant les utilisateurs et les groupes assignés, et les crée ou les modifie en fonction des détails de l’attribution.

**Pour connecter une application qui prend en charge SCIM :**

1. Connectez-vous au [portail Azure Active Directory](https://aad.portal.azure.com). 

1. Dans le volet gauche, sélectionnez **Applications d’entreprise**. Une liste de toutes les applications configurées s’affiche, dont les applications qui ont été ajoutées à partir de la galerie.

1. Sélectionnez **+ Nouvelle application** > **Toutes** > **Application ne figurant pas dans la galerie**.

1. Entrez un nom pour votre application, puis sélectionnez **Ajouter** pour créer un objet d’application. La nouvelle application est ajoutée à la liste des applications d’entreprise et s’ouvre sur son écran de gestion d’application.
    
   ![][1]
   *Figure 2 : galerie d’applications Azure AD*
    
1. Dans l’écran de gestion d’application, sélectionnez **Approvisionnement** dans le volet gauche.
1. Dans le menu **Mode d’approvisionnement**, sélectionnez **Automatique**.
    
   ![][2]
   *Figure 3 : configuration de l’approvisionnement dans le Portail Azure*
    
1. Dans le champ **URL du locataire**, entrez l’URL du point de terminaison SCIM de l’application. Exemple : https://api.contoso.com/scim/v2/
1. Si le point de terminaison SCIM requiert un jeton de porteur OAuth d’un émetteur autre qu’Azure AD, copiez le jeton de porteur OAuth requis dans le champ facultatif **Secret Token** (Jeton secret). 
1. Sélectionnez **Tester la connexion** pour qu’Azure Active Directory tente de se connecter au point de terminaison SCIM. Si la tentative échoue, des informations d’erreur s’affichent.  

    >[!NOTE]
    >**Tester la connexion** interroge le point de terminaison SCIM pour un utilisateur qui n’existe pas, en utilisant un GUID aléatoire en tant que propriété correspondante sélectionnée dans la configuration Azure AD. La réponse correcte attendue est HTTP 200 OK avec un message SCIM ListResponse vide. 

1. Si la tentative de connexion à l’application réussit, sélectionnez **Enregistrer** pour enregistrer les informations d’identification d’administrateur.
1. Dans la section **Mappages**, il existe deux ensembles sélectionnables de mappages d’attributs : un pour les objets utilisateur et un pour les objets de groupe. Sélectionnez chacun d’eux pour consulter les attributs qui sont synchronisés entre Azure Active Directory et votre application. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les utilisateurs et les groupes dans votre application pour les opérations de mise à jour. Sélectionnez **Enregistrer** pour valider les modifications.

    >[!NOTE]
    >Vous pouvez éventuellement désactiver la synchronisation des objets de groupe en désactivant le mappage « Groupes ». 

1. Sous **Paramètres**, le champ **Étendue** définit les utilisateurs et les groupes qui sont synchronisés. Sélectionnez **Sync only assigned users and groups (recommended)** (Synchroniser uniquement les utilisateurs et les groupes attribués (recommandé)) pour que seuls les utilisateurs et les groupes attribués soient synchronisés dans l’onglet **Utilisateurs et groupes**.
1. Une fois votre configuration terminée, définissez l’**état d’approvisionnement** sur **Activé**.
1. Sélectionnez **Enregistrer** pour démarrer le service d’approvisionnement Azure AD. 
1. Si vous synchronisez uniquement les utilisateurs et les groupes attribués (recommandé), veillez à sélectionner l’onglet **Utilisateurs et groupes** et à attribuer les utilisateurs ou groupes que vous souhaitez synchroniser.

Une fois la synchronisation initiale démarrée, vous pouvez sélectionner **Journaux d’audit** dans le panneau gauche pour surveiller la progression. Il indique toutes les actions effectuées par le service d’approvisionnement de votre application. Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](check-status-user-account-provisioning.md).

> [!NOTE]
> La synchronisation initiale prend plus de temps que les synchronisations suivantes, qui se produisent environ toutes les 40 minutes, tant que le service est en cours d’exécution. 

## <a name="understanding-the-azure-ad-scim-implementation"></a>Comprendre l’implémentation de SCIM d’Azure AD

Si vous créez une application qui prend en charge une API de gestion utilisateur SCIM 2.0, cette section décrit en détail comment le client SCIM d’Azure AD est implémenté et comment vous devez modéliser la gestion et les réponses de la requête du protocole SCIM. Une fois que vous avez implémenté votre point de terminaison SCIM, vous pouvez le tester en suivant la procédure décrite dans la section précédente.

Dans la [spécification du protocole SCIM 2.0](http://www.simplecloud.info/#Specification), votre application doit satisfaire aux conditions suivantes :

* Prise en charge de la création d'utilisateurs et de groupes (facultatif), conformément à la section [3.3 du protocole SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Prise en charge de la modification d'utilisateurs ou de groupes avec des demandes de correctifs, conformément à la [section 3.5.2 du protocole SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Prise en charge de la récupération d'une ressource connue pour un utilisateur ou un groupe créé plus tôt, conformément à la [section 3.4.1 du protocole SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Prise en charge de l'exécution de requêtes d'utilisateurs ou de groupes, conformément à la [section 3.4.2 du protocole SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Par défaut, les utilisateurs sont récupérés par leur `id` et interrogés via leur `username` et `externalid`, et les groupes sont interrogés via `displayName`.  
* Prise en charge de l'exécution de requêtes d'utilisateurs par ID et par gestion, conformément à la section 3.4.2 du protocole SCIM.  
* Prise en charge de l'exécution de requêtes de groupes par ID et par membre, conformément à la section 3.4.2 du protocole SCIM.  
* Acceptation d’un jeton du porteur unique pour l’authentification et l’autorisation d’Azure AD dans votre application.

Suivez ces recommandations lors de l’implémentation d’un point de terminaison SCIM pour garantir la compatibilité avec Azure AD :

* `id` est une propriété nécessaire à toutes les ressources. Chaque réponse qui retourne une ressource doit garantir que chaque ressource dispose de cette propriété, sauf pour `ListResponse` sans membre.
* La réponse à une demande de requête/filtre doit toujours être `ListResponse`.
* Les groupes sont facultatifs mais uniquement pris en charge si l’implémentation SCIM prend en charge les demandes PATCH.
* Il n’est pas nécessaire d’inclure la ressource complète dans la réponse PATCH.
* Microsoft Azure AD utilise uniquement les opérateurs suivants :  
     - `eq`
     - `and`
* Ne nécessitez pas de correspondance sensible à la casse sur des éléments structurels dans SCIM, en particulier pour les valeurs d’opération `op` PATCH, comme défini dans https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD émet les valeurs de 'op' en tant que `Add`, `Replace`, et `Remove`.
* Microsoft Azure AD effectue des requêtes pour extraire un utilisateur et un groupe aléatoires pour vous assurer que le point de terminaison et les informations d’identification sont valides. C’est également fait dans le cadre d’un flux de **Connexion test** dans le [portail Azure](https://portal.azure.com). 
* L’attribut sur lequel peuvent être interrogées les ressources doit être défini comme attribut correspondant sur l’application dans le [portail Azure](https://portal.azure.com). Pour plus d’informations, consultez [Personnalisation des mappages d’attributs d’approvisionnement d’utilisateurs pour les applications SaaS dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>Approvisionnement et annulation d’approvisionnement utilisateur
L’illustration suivante contient les messages qu’Azure Active Directory envoie à un service SCIM pour gérer le cycle de vie d’un utilisateur dans le magasin d’identités de votre application.  

![][4]
*Figure 4 : séquence d’approvisionnement et d’annulation de l’approvisionnement d’utilisateurs*

### <a name="group-provisioning-and-de-provisioning"></a>Approvisionnement et annulation d’approvisionnement d’un groupe
L’approvisionnement et l’annulation de l’approvisionnement d’un groupe sont facultatifs. L’illustration suivante contient les messages qu’Azure AD envoie à un service SCIM pour gérer le cycle de vie d’un utilisateur dans le cycle de vie d’un groupe du magasin d’identité de l'application.  Ces messages diffèrent des messages concernant les utilisateurs de deux manières : 

* Les demandes d’extraction de groupes précisent que l’attribut des membres doit être exclu des ressources fournies en réponse à la demande.  
* Les requêtes permettant de déterminer si un attribut de référence a une certaine valeur sont des requêtes sur les attributs membres.  

![][5]
*Figure 5 : séquence d’approvisionnement et d’annulation de l’approvisionnement d’un groupe*

### <a name="scim-protocol-requests-and-responses"></a>Demandes et réponses du protocole SCIM
Cette section fournit des exemples de requêtes SCIM émises par le client SCIM Azure AD et des exemples de réponses attendues. Pour de meilleurs résultats, vous devez coder votre application pour gérer ces demandes dans ce format et émettre les réponses attendues.

>[!IMPORTANT]
>Pour comprendre comment et quand le service d’approvisionnement d’utilisateur Azure AD émet les opérations décrites ci-dessous, consultez [Que se passe-t-il pendant l’approvisionnement ?](user-provisioning.md#what-happens-during-provisioning).

- [Opérations utilisateur](#user-operations)
  - [Create User](#create-user) (Créer un utilisateur)
    - [Requête](#request)
    - [Réponse](#response)
  - [Get user](#get-user) (Obtenir un utilisateur)
    - [Requête](#request-1)
    - [Réponse](#response-1)
  - [Get User by query](#get-user-by-query) (Obtenir un utilisateur par requête)
    - [Requête](#request-2)
    - [Réponse](#response-2)
  - [Get User by query - Zero results](#get-user-by-query---zero-results) (Obtenir un utilisateur par requête - sans résultat)
    - [Requête](#request-3)
    - [Réponse](#response-3)
  - [Update User [Multi-valued properties]](#update-user-multi-valued-properties) (Mettre à jour un utilisateur [Propriétés à plusieurs valeurs])
    - [Requête](#request-4)
    - [Réponse](#response-4)
  - [Update User [Single-valued properties]](#update-user-single-valued-properties) (Mettre à jour un utilisateur [Propriétés à une seule valeur])
    - [Requête](#request-5)
    - [Réponse](#response-5)
  - [Delete User](#delete-user) (Supprimer un utilisateur)
    - [Requête](#request-6)
    - [Réponse](#response-6)
- [Opérations de groupe](#group-operations)
  - [Create Group](#create-group) (Créer un groupe)
    - [Requête](#request-7)
    - [Réponse](#response-7)
  - [Get Group](#get-group) (Obtenir un groupe)
    - [Requête](#request-8)
    - [Réponse](#response-8)
  - [Get Group by displayName](#get-group-by-displayname) (Obtenir un groupe par displayName)
    - [Requête](#request-9)
    - [Réponse](#response-9)
  - [Update Group [Non-member attributes]](#update-group-non-member-attributes) (Mettre à jour un groupe [Attributs non membre])
    - [Requête](#request-10)
    - [Réponse](#response-10)
  - [Update Group [Add Members]](#update-group-add-members) (Mettre à jour un groupe [Ajouter des membres])
    - [Requête](#request-11)
    - [Réponse](#response-11)
  - [Update Group [Remove Members]](#update-group-remove-members) (Mettre à jour un groupe [Supprimer des membres])
    - [Requête](#request-12)
    - [Réponse](#response-12)
  - [Delete Group](#delete-group) (Supprimer un groupe)
    - [Requête](#request-13)
    - [Réponse](#response-13)

### <a name="user-operations"></a>Opérations utilisateur

* Les utilisateurs peuvent être interrogés via les attributs `userName` ou `email[type eq "work"]`.  

#### <a name="create-user"></a>Create User

###### <a name="request"></a>Requête
*POST /Users*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>response
*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```


#### <a name="get-user"></a>Obtenir un utilisateur

###### <a name="request-1"></a>Requête
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>Réponse
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```
#### <a name="get-user-by-query"></a>Obtenir un utilisateur par requête

##### <a name="request-2"></a>Requête
*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response-2"></a>Réponse
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Obtenir un utilisateur par requête - sans résultat

##### <a name="request-3"></a>Requête
*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response-3"></a>Réponse
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Mettre à jour un utilisateur [Propriétés à plusieurs valeurs]

##### <a name="request-4"></a>Requête
*PATCH /Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response-4"></a>Réponse
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Mettre à jour un utilisateur [Propriétés à une seule valeur]

##### <a name="request-5"></a>Requête
*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response-5"></a>Réponse
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="delete-user"></a>Supprimer l'utilisateur

##### <a name="request-6"></a>Requête
*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>Réponse
*HTTP/1.1 204 No Content*

### <a name="group-operations"></a>Opérations de groupe

* Les groupes doivent toujours être créés avec une liste de membre vide.
* Les groupes peuvent être interrogés via l’attribut `displayName`.
* La mise à jour de la requête PATCH de groupe doit renvoyer *HTTP 204 No Content* dans la réponse. Il n’est pas conseillé de renvoyer un corps avec une liste de tous les membres.
* Il n’est pas nécessaire de prendre en charge le renvoi de tous les membres du groupe.

#### <a name="create-group"></a>Créer un groupe

##### <a name="request-7"></a>Requête
*POST /Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "members": [],
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response-7"></a>Réponse
*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Obtenir un groupe

##### <a name="request-8"></a>Requête
*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response-8"></a>Réponse
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Obtenir un groupe par displayName

##### <a name="request-9"></a>Requête
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response-9"></a>Réponse
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```
#### <a name="update-group-non-member-attributes"></a>Mettre à jour un groupe [Attributs non membre]

##### <a name="request-10"></a>Requête
*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response-10"></a>Réponse
*HTTP/1.1 204 No Content*

### <a name="update-group-add-members"></a>Mettre à jour un groupe [Ajouter des membres]

##### <a name="request-11"></a>Requête
*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-11"></a>Réponse
*HTTP/1.1 204 No Content*

#### <a name="update-group-remove-members"></a>Mettre à jour un groupe [Supprimer des membres]

##### <a name="request-12"></a>Requête
*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-12"></a>Réponse
*HTTP/1.1 204 No Content*

#### <a name="delete-group"></a>Suppression d’un groupe

##### <a name="request-13"></a>Requête
*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>Réponse
*HTTP/1.1 204 No Content*


## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Création d’un point de terminaison SCIM à l’aide des bibliothèques Microsoft CLI
En créant un service Web SCIM doté d'une interface avec Azure Active Directory, vous pouvez activer l'attribution d'utilisateurs automatique pour pratiquement n'importe quelle application ou magasin d’identité.

Fonctionnement :

1. Azure AD fournit une bibliothèque Common Language Infrastructure nommée Microsoft.SystemForCrossDomainIdentityManagement, incluse avec les exemples de code décrits ci-dessous. Les développeurs et les intégrateurs système peuvent utiliser cette bibliothèque pour créer et déployer un point de terminaison de service web SCIM capable de connecter Azure AD au magasin d’identités d’application d’une application.
2. Les mappages sont mis en œuvre dans le service web pour mapper le schéma utilisateur normalisé et le protocole requis par l’application. 
3. L’URL de point de terminaison est inscrite dans Azure AD dans le cadre d’une application personnalisée dans la galerie d’applications.
4. Les utilisateurs et les groupes sont affectés à cette application dans Azure AD. Lors de l’affectation, ils sont placés dans une file d’attente pour être synchronisés avec l’application cible. Le processus de synchronisation qui gère la file d’attente s’exécute toutes les 40 minutes.

### <a name="code-samples"></a>Exemples de code
Pour faciliter ce processus, des [exemples de code](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) qui créent un point de terminaison de service Web SCIM et illustrent l'attribution automatique sont fournis. Exemple : un fournisseur qui gère un fichier avec des lignes de valeurs séparées par des virgules représentant des utilisateurs et des groupes.    

**Composants requis**

* Visual Studio 2013 ou une version ultérieure
* [Kit de développement logiciel (SDK) Azure pour .NET](https://azure.microsoft.com/downloads/)
* Machine Windows qui prend en charge l’infrastructure ASP.NET 4.5 à utiliser comme point de terminaison SCIM. Cet ordinateur doit être accessible à partir du cloud.
* [Dans un abonnement Azure avec une version d’évaluation ou sous licence d’Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Mise en route
Le moyen le plus simple d’implémenter un point de terminaison SCIM qui peut accepter des demandes de configuration d’Azure AD consiste à générer et déployer l’exemple de code qui génère les utilisateurs configurés dans un fichier de valeurs séparées par des virgules (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Pour créer un exemple de point de terminaison SCIM

1. Télécharger le package d’exemples de code à l’emplacement [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Décompressez le package et placez-le sur votre machine Windows à un emplacement tel que C:\AzureAD-BYOA-Provisioning-Samples\.
1. Dans ce dossier, lancez le projet FileProvisioning\Host\FileProvisioningService.csproj dans Visual Studio.
1. Sélectionnez **Outils** > **Gestionnaire de package NuGet** > **Console du gestionnaire de Package**, puis exécutez les commandes suivantes afin que le projet FileProvisioningService résolve les références de la solution :

   ```powershell
    Update-Package -Reinstall
   ```

1. Générez le projet FileProvisioningService.
1. Lancez l'application d'invite de commandes de Windows (en tant qu'administrateur) et utilisez la commande **cd** pour modifier le répertoire en votre dossier **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**.
1. Exécutez la commande suivante en remplaçant `<ip-address>` par l’adresse IP ou le nom de domaine de l’ordinateur Windows :

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. Dans Windows, sous **Paramètres Windows** > **Paramètres Internet & réseau**, sélectionnez **Pare-feu Windows** > **Paramètres avancés**, et créez une **règle de trafic entrant** qui autorise l’accès entrant au port 9000.
1. Si l’ordinateur Windows est placé derrière un routeur, ce dernier doit être configuré pour exécuter une traduction NAT entre son port 9000 présenté à Internet et le port 9000 sur l’ordinateur Windows. Cette configuration est obligatoire si l’on veut qu’Azure AD puisse accéder à ce point de terminaison dans le cloud.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Pour enregistrer le point de terminaison SCIM exemple dans Azure AD

1. Connectez-vous au [portail Azure Active Directory](https://aad.portal.azure.com). 

1. Dans le volet gauche, sélectionnez **Applications d’entreprise**. Une liste de toutes les applications configurées s’affiche, dont les applications qui ont été ajoutées à partir de la galerie.

1. Sélectionnez **+ Nouvelle application** > **Toutes** > **Application ne figurant pas dans la galerie**.

1. Entrez un nom pour votre application, puis sélectionnez **Ajouter** pour créer un objet d’application. L’objet d’application créé doit représenter l’application cible que vous devez configurer et sur laquelle mettre en œuvre l’authentification unique. Il ne s’agit pas d’un simple point de terminaison SCIM.

1. Dans l’écran de gestion d’application, sélectionnez **Approvisionnement** dans le volet gauche.

1. Dans le menu **Mode d’approvisionnement**, sélectionnez **Automatique**.
    
   ![][2]
   *Figure 6 : configuration de l’approvisionnement dans le Portail Azure*
    
1. Dans le champ **URL du locataire**, saisissez l’URL côté Internet et le port de votre point de terminaison SCIM. L'entrée pourrait se présenter comme suit : http://testmachine.contoso.com:9000 ou http://\< ip-address>:9000/, où \< ip-address> correspond à l'adresse IP exposée sur Internet. 

1. Si le point de terminaison SCIM requiert un jeton de porteur OAuth d’un émetteur autre qu’Azure AD, copiez le jeton de porteur OAuth requis dans le champ facultatif **Secret Token** (Jeton secret). 
1. Sélectionnez **Tester la connexion** pour qu’Azure Active Directory tente de se connecter au point de terminaison SCIM. Si la tentative échoue, des informations d’erreur s’affichent.  

    >[!NOTE]
    >**Tester la connexion** interroge le point de terminaison SCIM pour un utilisateur qui n’existe pas, en utilisant un GUID aléatoire en tant que propriété correspondante sélectionnée dans la configuration Azure AD. La réponse correcte attendue est HTTP 200 OK avec un message SCIM ListResponse vide

1. Si la tentative de connexion à l’application réussit, sélectionnez **Enregistrer** pour enregistrer les informations d’identification d’administrateur.

1. Dans la section **Mappages**, il existe deux ensembles sélectionnables de mappages d’attributs : un pour les objets utilisateur et un pour les objets de groupe. Sélectionnez chacun d’eux pour consulter les attributs qui sont synchronisés entre Azure Active Directory et votre application. Les attributs sélectionnés en tant que propriétés de **Correspondance** sont utilisés pour faire correspondre les utilisateurs et les groupes dans votre application pour les opérations de mise à jour. Sélectionnez **Enregistrer** pour valider les modifications.

1. Sous **Paramètres**, le champ **Étendue** définit les utilisateurs et/ou les groupes qui sont synchronisés. Sélectionnez **Sync only assigned users and groups (recommended)** (Synchroniser uniquement les utilisateurs et les groupes attribués (recommandé)) pour que seuls les utilisateurs et les groupes attribués soient synchronisés dans l’onglet **Utilisateurs et groupes**.

1. Une fois votre configuration terminée, définissez l’**état d’approvisionnement** sur **Activé**.

1. Sélectionnez **Enregistrer** pour démarrer le service d’approvisionnement Azure AD. 

1. Si vous synchronisez uniquement les utilisateurs et les groupes attribués (recommandé), veillez à sélectionner l’onglet **Utilisateurs et groupes** et à attribuer les utilisateurs ou groupes que vous souhaitez synchroniser.

Une fois la synchronisation initiale démarrée, vous pouvez sélectionner **Journaux d’audit** dans le panneau gauche pour surveiller la progression. Il indique toutes les actions effectuées par le service d’approvisionnement de votre application. Pour plus d’informations sur la lecture des journaux d’activité d’approvisionnement Azure AD, consultez [Création de rapports sur l’approvisionnement automatique de comptes d’utilisateur](check-status-user-account-provisioning.md).

La dernière étape de vérification de l’exemple consiste à ouvrir le fichier TargetFile.csv dans le dossier \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug sur votre machine Windows. Une fois le processus de déploiement exécuté, ce fichier affiche les détails de tous les utilisateurs et groupes affectés et configurés.

### <a name="development-libraries"></a>Bibliothèques de développement
Pour développer un service web conforme à la spécification SCIM, commencez par vous familiariser avec les bibliothèques suivantes fournies par Microsoft afin d’accélérer le processus de développement : 

- Les bibliothèques CLI (Common Language Infrastructure) sont proposées pour une utilisation avec les langages basés sur cette infrastructure, notamment C#. L’une de ces bibliothèques, Microsoft.SystemForCrossDomainIdentityManagement.Service, déclare une interface Microsoft.SystemForCrossDomainIdentityManagement.IProvider, affichée dans l’illustration suivante. Un développeur qui utilise les bibliothèques doit mettre en œuvre cette interface avec une classe pouvant être référencée de manière générale comme fournisseur que fournisseur. Les bibliothèques permettent au développeur de déployer un service web conforme à la spécification SCIM. Le service web peut être soit hébergé dans Internet Information Services, soit dans n’importe quel assembly CLI exécutable. La requête est convertie en appels aux méthodes du fournisseur, qui doivent être programmées par le développeur pour fonctionner sur un magasin d’identités.
  
   ![][3]
  
- [Des gestionnaires de routeur Express](https://expressjs.com/guide/routing.html) sont disponibles pour les objets de requête node.js d’analyse représentant des appels (comme défini par la spécification SCIM) envoyés à un service web node.js.   

### <a name="building-a-custom-scim-endpoint"></a>Création d’un point de terminaison SCIM personnalisé
Les développeurs utilisant les bibliothèques CLI peuvent héberger leurs services dans un assembly CLI exécutable ou au sein des services Internet (IIS). Voici un exemple de code pour l’hébergement d’un service dans un assembly exécutable, à l’adresse http://localhost:9000: 

   ```csharp
    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }
   ```

Ce service doit avoir une adresse HTTP et un certificat d’authentification émanant du serveur dont l’autorité de certification racine est l’un des noms suivants : 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Un certificat d’authentification serveur peut être associé à un port sur un hôte Windows à l’aide de l’utilitaire d’environnement réseau : 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Ici, la valeur fournie pour l’argument certhash est l’empreinte numérique du certificat, tandis que la valeur fournie pour l’argument appid est un identificateur global unique arbitraire.  

Pour héberger le service dans Internet Information Services, un développeur crée un assembly de bibliothèque de code CLI avec une classe nommée Démarrage dans l’espace de noms par défaut de l’assembly.  Voici un exemple de classe de ce type : 

   ```csharp
    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }
   ```

### <a name="handling-endpoint-authentication"></a>Gestion de l’authentification du point de terminaison
Les demandes d’Azure Active Directory incluent un jeton de support OAuth 2.0.   Tout service qui reçoit la demande doit authentifier l’émetteur comme étant Azure Active Directory pour le compte du client Azure Active Directory pour accéder au service web du graphique Azure Active Directory.  Dans le jeton, l’émetteur est identifié par une revendication iss, comme « iss» : « https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/ ».  Dans cet exemple, l’adresse de base de la valeur de revendication, https://sts.windows.net , identifie Azure Active Directory en tant qu’émetteur, tandis que le segment d’adresse relative, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, est un identificateur unique du locataire Azure Active Directory au nom duquel le jeton a été émis.  Si le jeton a été émis pour accéder au service web du graphique Azure Active Directory, l’identificateur de ce service, 00000002-0000-0000-c000-000000000000, doit être dans la valeur de revendication du jeton AD.  Chaque application inscrite dans un seul abonné peut recevoir la même revendication `iss` avec des requêtes SCIM.

Les développeurs qui utilisent les bibliothèques CLI fournies par Microsoft pour la création d’un service SCIM peuvent authentifier les demandes d’Azure Active Directory à l’aide du package Microsoft.Owin.Security.ActiveDirectory en exécutant les opérations suivantes : 

1. Dans un fournisseur, implémentez la propriété Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior lui faisant retourner une méthode à appeler à chaque démarrage du service : 

   ```csharp
     public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
     {
       get
       {
         return this.OnServiceStartup;
       }
     }

     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
       System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
     {
     }
   ```

2. Ajoutez le code suivant à cette méthode pour que toute demande à l’un des points de terminaison de service authentifié comme portant un jeton émis par Azure Active Directory pour le compte d’un client spécifié puisse accéder au service web du graphique d’Azure AD : 

   ```csharp
     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
       System.Web.Http.HttpConfiguration HttpConfiguration configuration)
     {
       // IFilter is defined in System.Web.Http.dll.  
       System.Web.Http.Filters.IFilter authorizationFilter = 
         new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

       // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
       // System.IdentityModel.Token.Jwt.dll.
       SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
         new TokenValidationParameters()
         {
           ValidAudience = "00000002-0000-0000-c000-000000000000"
         };

       // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
       // Microsoft.Owin.Security.ActiveDirectory.dll
       Microsoft.Owin.Security.ActiveDirectory.
       WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
         new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
         TokenValidationParameters = tokenValidationParameters,
         Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                       // identifier for this one.  
       };

       applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
     }
   ```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Gestion du provisionnement et de l’annulation du provisionnement des utilisateurs

1. Azure Active Directory interroge le service pour trouver un utilisateur avec une valeur d’attribut externalId correspondant à la valeur d’attribut mailNickname d’un utilisateur dans Azure AD. La requête est exprimée dans le protocole HTTP (Hypertext Transfer Protocol) comme dans cet exemple, jyoung étant un exemple de mailNickname d’utilisateur dans Azure Active Directory.

    >[!NOTE]
    > Il s'agit simplement d'un exemple. Tous les utilisateurs n’auront pas d’attribut mailNickname, et la valeur dont dispose un utilisateur peut ne pas être unique dans le répertoire. De plus, l’attribut utilisé pour la correspondance (dans ce cas, externalId) est configurable dans les [mappages d’attribut Azure AD](customize-application-attributes.md).

   ```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ```
   Si le service a été créé à l’aide des bibliothèques CLI fournies par Microsoft pour l’implémentation des services SCIM, la demande est traduite en appel de méthode de requête du fournisseur de service.  Voici la signature de cette méthode : 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
   ```
   Voici la définition de l’interface Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters : 
   ```csharp
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }
   ```

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   Si le service a été créé à l’aide des bibliothèques Common Language Infrastructure fournies par Microsoft pour l’implémentation des services SCIM, la demande est traduite en appel de méthode de requête du fournisseur de service.  Voici la signature de cette méthode : 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Voici la définition de l’interface Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters : 

   ```csharp
     public interface IQueryParameters: 
       Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
         System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
         { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
       system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
       { get; }
       System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
       { get; }
       string SchemaIdentifier 
       { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
     {
         Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
           { get; set; }
         string AttributePath 
           { get; } 
         Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
           { get; }
         string ComparisonValue 
           { get; }
     }

     public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
     {
         Equals
     }
   ```

   Dans l’exemple suivant de requête pour un utilisateur avec une valeur d’attribut externalId donnée, les valeurs des arguments transmis à la méthode de requête sont : 
   * parameters.AlternateFilters.Count: 1
   * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. Si la réponse à une requête du service web pour un utilisateur avec une valeur d’attribut externalId correspondant à la valeur d’attribut mailNickname d’un utilisateur ne renvoie aucun utilisateur, Azure Active Directory demande que le service approvisionne un utilisateur correspondant à celui d’Azure Active Directory.  Voici un exemple de requête : 

   ```
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/scim+json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
   ```
   Les bibliothèques CLI fournies par Microsoft pour l’implémentation de services SCIM transforment la requête en appel à la méthode de création du fournisseur du service.  Cette méthode de création a sa signature : 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
   ```
   Dans le cas d’une demande d’approvisionnement d’utilisateur, la valeur de l’argument de la ressource est une instance de Microsoft.SystemForCrossDomainIdentityManagement. La classe Core2EnterpriseUser, définie dans la bibliothèque Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Si la demande d’approvisionnement de l’utilisateur aboutit, l’implémentation de la méthode renvoie une instance de Microsoft.SystemForCrossDomainIdentityManagement. La classe Core2EnterpriseUser, avec la valeur de la propriété d’identificateur définie sur l’identificateur unique du nouvel utilisateur venant d’être approvisionné.  

3. Pour mettre à jour un utilisateur qui existe dans un magasin d’identités avec SCIM frontal, Azure Active Directory continue en demandant au service l’état actuel de cet utilisateur avec une requête de type : 
   ```
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ```
   Si un service est créé à l’aide des bibliothèques CLI fournies par Microsoft pour l’implémentation des services SCIM, la demande est traduite en appel de méthode de récupération du fournisseur de service.  Voici la signature de la méthode de récupération : 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
   ```
   Dans le cas d’une requête servant à récupérer l’état actuel d’un utilisateur, les valeurs des propriétés de l’objet fourni comme valeur d’argument des paramètres sont les suivantes : 
  
   * Identificateur : "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier : "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. Si un attribut de référence doit être mis à jour, Azure Active Directory interroge le service pour déterminer si la valeur actuelle de l’attribut de référence dans le magasin d’identités avec le service frontal correspond déjà à la valeur de cet attribut dans Azure Active Directory. Pour les utilisateurs, le seul attribut dont la valeur actuelle est interrogée de cette manière est l’attribut manager. Voici un exemple de requête visant à déterminer si l’attribut manager d’un objet utilisateur particulier a actuellement une certaine valeur : 

   Si le service a été créé à l’aide des bibliothèques CLI fournies par Microsoft pour l’implémentation des services SCIM, la demande est traduite en appel de méthode de requête du fournisseur de service. La valeur des propriétés de l’objet fourni en tant que valeur d’argument des paramètres est la suivante : 
  
   * parameters.AlternateFilters.Count: 2
   * parameters.AlternateFilters.ElementAt(x).AttributePath: « ID »
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): « ID »
   * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

   Ici, la valeur x de l’index peut être 0 et la valeur y de l’index peut être 1, ou la valeur x peut être 1 et la valeur y peut être 0, selon l’ordre des expressions de paramètre de requête du filtre.   

5. Voici un exemple de requête d’Azure Active Directory à un service SCIM pour mettre à jour un utilisateur : 
   ```
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/scim+json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```
   Les bibliothèques Microsoft CLI pour l’implémentation de services SCIM convertissent la requête en appel à la méthode de mise à jour du fournisseur du service. Voici la signature de la méthode de mise à jour : 
   ```csharp
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}
   ```

   Si le service a été créé à l’aide des bibliothèques Common Language Infrastructure fournies par Microsoft pour l’implémentation des services SCIM, la demande est traduite en appel de méthode de requête du fournisseur de service. La valeur des propriétés de l’objet fourni en tant que valeur d’argument des paramètres est la suivante : 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: « ID »
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue:  "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): « ID »
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  Ici, la valeur x de l’index peut être 0 et la valeur y de l’index peut être 1, ou la valeur x peut être 1 et la valeur y peut être 0, selon l’ordre des expressions de paramètre de requête du filtre.   

1. Voici un exemple de requête d’Azure Active Directory à un service SCIM pour mettre à jour un utilisateur : 

   ```
     PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
     Content-type: application/scim+json
     {
       "schemas": 
       [
         "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
       "Operations":
       [
         {
           "op":"Add",
           "path":"manager",
           "value":
             [
               {
                 "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                 "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```

   Les bibliothèques Microsoft Common Language Infrastructure pour l’implémentation de services SCIM convertissent la requête en appel à la méthode de mise à jour du fournisseur du service. Voici la signature de la méthode de mise à jour : 

   ```csharp
     // System.Threading.Tasks.Tasks and 
     // System.Collections.Generic.IReadOnlyCollection<T>
     // are defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
     // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

     System.Threading.Tasks.Task Update(
       Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
       string correlationIdentifier);

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
     {
     Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
       PatchRequest 
         { get; set; }
     Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
       ResourceIdentifier 
         { get; set; }        
     }

     public class PatchRequest2: 
       Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
     {
     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
         Operations
         { get;}

     public void AddOperation(
       Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
     }

     public class PatchOperation
     {
     public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
       Name
       { get; set; }

     public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
       Path
       { get; set; }

     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
       { get; }

     public void AddValue(
       Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
     }

     public enum OperationName
     {
       Add,
       Remove,
       Replace
     }

     public interface IPath
     {
       string AttributePath { get; }
       System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
       Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
     }

     public class OperationValue
     {
       public string Reference
       { get; set; }

       public string Value
       { get; set; }
     }
   ```

    Dans le cas d’une demande de mise à jour d’un utilisateur, l’objet fourni comme valeur d’argument de correctif a les valeurs de propriété suivantes : 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier:  "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
   * (PatchRequest as PatchRequest2).Operations.Count: 1
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

1. Pour annuler l’approvisionnement d’un utilisateur à partir d’un magasin d’identités avec un service SCIM frontal, Azure AD envoie une demande similaire à celle-ci : 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Si le service a été créé à l’aide des bibliothèques Common Language Infrastructure fournies par Microsoft pour l’implémentation des services SCIM, la demande est traduite en appel de méthode Delete du fournisseur de service.   Cette méthode a sa signature : 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   L’objet fourni comme valeur d’argument resourceIdentifier a ces valeurs de propriété dans le cas d’une demande d’annulation d’approvisionnement d’un utilisateur : 

1. Pour annuler l’approvisionnement d’un utilisateur à partir d’un magasin d’identités avec un service SCIM frontal, Azure AD envoie une demande similaire à celle-ci : 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   Si le service a été créé à l’aide des bibliothèques CLI fournies par Microsoft pour l’implémentation des services SCIM, la demande est traduite en appel de méthode de suppression du fournisseur de service.   Cette méthode a sa signature : 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
   ````
   L’objet fourni comme valeur d’argument resourceIdentifier a ces valeurs de propriété dans le cas d’une demande d’annulation d’approvisionnement d’un utilisateur : 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="user-and-group-schema-reference"></a>Référence du schéma des utilisateurs et des groupes
Azure Active Directory peut configurer deux types de ressources pour les services web SCIM.  Ces types de ressources sont des utilisateurs et des groupes.  

Les ressources de l’utilisateur sont identifiées par l’identificateur de schéma, `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, inclus dans cette spécification de protocole : https://tools.ietf.org/html/rfc7643.  Le mappage par défaut des attributs d’utilisateurs dans Azure Active Directory sur les attributs de ressources est renseigné dans le tableau 1.  

Les ressources du groupe sont identifiées par l’identificateur de schéma, `urn:ietf:params:scim:schemas:core:2.0:Group`. Le tableau 2 montre le mappage par défaut des attributs de groupes dans Azure Active Directory sur les attributs de ressources de groupes.  

### <a name="table-1-default-user-attribute-mapping"></a>Tableau 1 : Mappage d’attributs utilisateur par défaut

| Utilisateur Azure Active Directory | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |active |
| displayName |displayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickName |externalId |
| manager |manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| objectId |id |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tableau 2 : Mappage d’attribut de groupe par défaut

| Groupe Azure Active Directory | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |externalId |
| mail |emails[type eq "work"].value |
| mailNickName |displayName |
| membres |membres |
| objectId |id |
| proxyAddresses |emails[type eq "other"].Value |

## <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Autoriser les adresses IP utilisées par le service d’approvisionnement AD Azure à effectuer des demandes SCIM
Certaines applications autorisent le trafic entrant vers leur application. Pour que le service d’approvisionnement AD Azure puisse fonctionner comme prévu, les adresses IP utilisées doivent être autorisées. Pour obtenir la liste d’adresses IP de chaque balise de service/région, consultez le fichier JSON - [Plages d’adresses IP Azure et balises de service – Cloud Public](https://www.microsoft.com/download/details.aspx?id=56519). Vous pouvez télécharger et programmez ces adresses IP dans votre pare-feu selon vos besoins. Les plages d’adresses IP réservées pour l’approvisionnement Azure AD se trouvent sous « AzureActiveDirectoryDomainServices ».
 

## <a name="related-articles"></a>Articles connexes
* [Automatiser l’approvisionnement/le déprovisionnement des utilisateurs pour les applications SaaS](user-provisioning.md)
* [Personnalisation des mappages d’attributs pour l’approvisionnement des utilisateurs](customize-application-attributes.md)
* [Écriture d’expressions pour les mappages d’attributs](functions-for-customizing-application-data.md)
* [Filtres d’étendue pour l’approvisionnement des utilisateurs](define-conditional-rules-for-provisioning-user-accounts.md)
* [Notifications d’approvisionnement de comptes](user-provisioning.md)
* [Liste des didacticiels sur l’intégration des applications SaaS](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
