---
title: Découvrez comment fournir des revendications facultatives à votre application Azure AD | Microsoft Docs
description: Guide pour l’ajout de revendications personnalisées ou supplémentaires aux jetons web JSON (JWT) et SAML 2.0 émis par Azure Active Directory.
documentationcenter: na
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/03/2019
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98b0ec2e1defc4701bff798b2fa93900ec8a9a64
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595159"
---
# <a name="how-to-provide-optional-claims-to-your-azure-ad-app"></a>Activation Fournir des revendications facultatives à votre application Azure AD

Les développeurs d’applications peuvent utiliser des revendications facultatives dans leurs applications Azure AD pour spécifier les revendications souhaitées dans les jetons envoyés à leur application. 

Vous pouvez utiliser des revendications facultatives pour :

- Sélectionner des revendications supplémentaires à inclure dans les jetons pour votre application.
- Modifier le comportement de certaines revendications retournées par Azure AD dans les jetons.
- Ajouter et accéder à des revendications personnalisées pour votre application.

Pour obtenir la liste de revendications standard, voir les documents sur les revendications [jeton d’accès](access-tokens.md) et [id_token](id-tokens.md). 

Si les revendications facultatives sont prises en charge dans les jetons aux formats v1.0 et v2.0, ainsi que dans les jetons SAML, elles révèlent l’essentiel de leur valeur lors du passage de v1.0 à v2.0. L’un des objectifs du [Point de terminaison de la plateforme d’identités Microsoft v2.0](active-directory-appmodel-v2-overview.md) est de réduire la taille des jetons afin de garantir des performances optimales des clients. Ainsi, plusieurs revendications précédemment incluses dans les jetons d’accès et d’ID ne sont plus présentes dans les jetons v2.0 et doivent être demandées spécifiquement pour chaque application.

**Tableau 1 : Applicabilité**

| Type de compte | Jetons v1.0 | Jetons v2.0  |
|--------------|---------------|----------------|
| Compte Microsoft personnel  | N/A  | Pris en charge |
| Compte Azure AD      | Pris en charge | Pris en charge |

## <a name="v10-and-v20-optional-claims-set"></a>Ensemble de revendications facultatives v1.0 et v2.0

L’ensemble de revendications facultatives disponible par défaut pour les applications est répertorié ci-dessous. Pour ajouter des revendications personnalisées facultatives pour votre application, consultez [Extensions d’annuaire](#configuring-directory-extension-optional-claims) ci-dessous. Lors de l’ajout de revendications au **jeton d’accès**, cela s’applique aux jetons d’accès demandés *pour* l’application (API web), et non à ceux demandés *par* l’application. Cela garantit que, quelque soit le client qui accède à votre API, les données correctes seront sur le jeton d’accès qu’il utilise pour s’authentifier auprès de votre API.

> [!NOTE]
> La plupart de ces revendications peuvent figurer dans les jetons JWT pour les jetons v1.0 et v2.0, mais pas dans les jetons SAML, sauf indication contraire dans la colonne Type de jeton. Les comptes consommateur prennent en charge un sous-ensemble de ces revendications, indiqué dans la colonne « Type d’utilisateur ».  La plupart des revendications répertoriées ne s’appliquent pas aux utilisateurs consommateurs (comme ils n’ont pas de locataire, `tenant_ctry` n’a pas de valeur).  

**Tableau 2 : ensemble de revendications facultatives v1.0 et v2.0**

| Nom                       |  Description   | Type de jeton | Type d’utilisateur | Notes  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Heure de dernière authentification de l’utilisateur. Voir les spécifications OpenID Connect.| JWT        |           |  |
| `tenant_region_scope`      | Région du locataire de ressource. | JWT        |           | |
| `home_oid`                 | Pour les utilisateurs invités, il s’agit de l’ID d’objet de l’utilisateur dans le locataire de base de l’utilisateur.| JWT        |           | |
| `sid`                      | ID de session utilisé pour la déconnexion de l’utilisateur après chaque session. | JWT        |  Comptes personnels et Azure AD.   |         |
| `platf`                    | Plateforme d’appareil.    | JWT        |           | Limité aux appareils gérés qui peuvent vérifier le type d’appareil.|
| `verified_primary_email`   | Obtenu à partir du PrimaryAuthoritativeEmail de l’utilisateur.      | JWT        |           |         |
| `verified_secondary_email` | Obtenu à partir du SecondaryAuthoritativeEmail de l’utilisateur.   | JWT        |           |        |
| `enfpolids`                | ID des stratégies appliquées. Liste des ID des stratégies qui ont été évaluées pour l’utilisateur actuel. | JWT |  |  |
| `vnet`                     | Informations sur le spécificateur de réseau virtuel. | JWT        |           |      |
| `fwd`                      | Adresse IP.| JWT    |   | Ajoute l’adresse IPv4 d’origine du client demandeur (quand il se trouve sur un réseau virtuel). |
| `ctry`                     | Pays de l’utilisateur. | JWT |  | Azure AD retourne la revendication facultative `ctry` si elle est présente. La valeur de la revendication est un code de pays à deux lettres standard, tel que FR, JP, SZ, etc. |
| `tenant_ctry`              | Pays du locataire de ressource. | JWT | | |
| `xms_pdl`          | Emplacement de données par défaut   | JWT | | Pour les locataires multigéographiques, il s’agit du code à trois lettres indiquant la région géographique où se trouve l’utilisateur. Pour plus d’informations, voir la [documentation Azure AD Connect sur l’emplacement par défaut des données](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-preferreddatalocation).<br/>Par exemple : `APC` pour l’Asie-Pacifique. |
| `xms_pl`                   | Langue par défaut de l’utilisateur  | JWT ||La langue par défaut de l’utilisateur, si celle-ci a été définie. Dans les scénarios d’accès invité, provient du locataire de base. Au format langue-pays (« fr-fr »). |
| `xms_tpl`                  | Langue par défaut du locataire| JWT | | Langue par défaut du locataire de la ressource, si celle-ci est définie. Au format langue (« fr »). |
| `ztdid`                    | ID de déploiement sans intervention | JWT | | Identité d’appareil utilisée pour [Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Adresse e-mail de l'utilisateur, le cas échéant.  | JWT, SAML | MSA, Azure AD | Cette valeur est incluse par défaut si l’utilisateur est un invité du locataire.  Pour les utilisateurs gérés (à l’intérieur du locataire), elle doit être demandée via cette revendication facultative ou, sur la version 2.0 uniquement, avec l’étendue OpenID.  Pour les utilisateurs gérés, l’adresse e-mail doit être définie dans le [portail d’administration Office](https://portal.office.com/adminportal/home#/users).| 
| `groups`| Mise en forme facultative des revendications de groupe |JWT, SAML| |Utilisé conjointement avec le paramètre GroupMembershipClaims dans le [manifeste d’application](reference-app-manifest.md) qui doit également être défini. Pour plus d’informations, voir [Revendications de groupe](#Configuring-group-optional claims) ci-dessous. Pour plus d’informations sur les revendications de groupe, voir [Comment configurer des revendications de groupe](../hybrid/how-to-connect-fed-group-claims.md)
| `acct`             | Statut du compte utilisateur dans le client. | JWT, SAML | | Si l’utilisateur est membre du client, la valeur est `0`. S’il est un invité, la valeur est `1`. |
| `upn`                      | Revendication UserPrincipalName. | JWT, SAML  |           | Bien que cette revendication soit incluse automatiquement, vous pouvez la spécifier en tant que revendication facultative pour attacher des propriétés supplémentaires afin de modifier son comportement en cas d’utilisateur invité.  |

### <a name="v20-optional-claims"></a>Revendications facultatives v2.0

Ces revendications sont toujours incluses dans les jetons Azure AD v1.0, mais pas dans les jetons v2.0, sauf sur demande. Ces revendications s’appliquent uniquement aux jetons web JSON (jetons d’ID et jetons d’accès). 

**Tableau 3 : revendications facultatives spécifiques de V2.0**

| Revendication JWT     | Nom                            | Description                                | Notes |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Adresse IP                      | Adresse IP à partir de laquelle le client s’est connecté.   |       |
| `onprem_sid`  | Identificateur de sécurité local |                                             |       |
| `pwd_exp`     | Heure d’expiration du mot de passe        | Date et heure d’expiration du mot de passe. |       |
| `pwd_url`     | Modifier l’URL de mot de passe             | URL à laquelle l’utilisateur peut accéder pour modifier son mot de passe.   |   |
| `in_corp`     | Dans le périmètre du réseau d’entreprise        | Indique si le client se connecte à partir du réseau d’entreprise. Dans le cas contraire, la revendication n’est pas incluse.   |  Basé sur les [adresses IP approuvées](../authentication/howto-mfa-mfasettings.md#trusted-ips) définies dans MFA.    |
| `nickname`    | Surnom                        | Autre nom de l’utilisateur, distinct du nom de famille et du prénom. | 
| `family_name` | Nom                       | Fournit le nom de famille de l’utilisateur, tel que défini sur l’objet utilisateur. <br>"family_name":"Miller" | Pris en charge dans MSA et Azure AD   |
| `given_name`  | Prénom                      | Fournit le prénom de l’utilisateur, tel que défini sur l’objet utilisateur.<br>"given_name": "Frank"                   | Pris en charge dans MSA et Azure AD  |
| `upn`         | Nom d’utilisateur principal | Identificateur de l'utilisateur qui peut être utilisé avec le paramètre username_hint.  Il ne s'agit pas d'un identificateur durable pour l'utilisateur et il ne doit pas être utilisé pour saisir des données. | Consultez les [propriétés supplémentaires](#additional-properties-of-optional-claims) ci-dessous pour en savoir plus sur la configuration de la revendication. |

### <a name="additional-properties-of-optional-claims"></a>Propriétés supplémentaires des revendications facultatives

Certaines revendications facultatives peuvent être configurées pour modifier la façon dont la revendication est retournée. Ces propriétés supplémentaires servent principalement à faciliter la migration d'applications locales dont les attentes sont différentes vis-à-vis des données (par exemple, `include_externally_authenticated_upn_without_hash` aide les clients qui ne prennent pas en charge les signes dièse (`#`) dans l'UPN)

**Tableau 4 : Valeurs de configuration des revendications facultatives**

| Nom de la propriété  | Nom de la propriété supplémentaire | Description |
|----------------|--------------------------|-------------|
| `upn`          |                          | Peut être utilisée pour les réponses SAML et JWT, ainsi que pour les jetons v1.0 et v2.0. |
|                | `include_externally_authenticated_upn`  | Inclut l’UPN de l’invité tel que stocké dans le locataire de ressource. Par exemple, `foo_hometenant.com#EXT#@resourcetenant.com` |             
|                | `include_externally_authenticated_upn_without_hash` | Comme ci-dessus, sauf que les signes dièse (`#`) sont remplacés par des traits de soulignement (`_`), par exemple `foo_hometenant.com_EXT_@resourcetenant.com`. |

#### <a name="additional-properties-example"></a>Exemple de propriétés supplémentaires

```json
 "optionalClaims": 
   {
       "idToken": [ 
             { 
                "name": "upn", 
            "essential": false,
                "additionalProperties": [ "include_externally_authenticated_upn"]  
              }
        ]
}
```

Cet objet OptionalClaims renvoie au client le jeton d’ID pour y inclure un autre UPN avec des informations supplémentaires sur le locataire de base et le locataire de ressource. Cela modifie uniquement la revendication `upn` du jeton si l’utilisateur est un invité du locataire (qui utilise un fournisseur d’identité différent pour l’authentification). 

## <a name="configuring-optional-claims"></a>Configuration des revendications facultatives

Vous pouvez configurer des revendications facultatives pour votre application en modifiant le manifeste de l’application (voir l’exemple ci-dessous). Pour plus d’informations, consultez l’[article de présentation des manifestes d’applications Azure AD](reference-app-manifest.md).

> [!IMPORTANT]
> Les jetons d’accès sont **toujours** générés à l’aide du manifeste de la ressource, pas du client.  Donc, dans la requête `...scope=https://graph.microsoft.com/user.read...`, la ressource est Graph.  Ainsi, le jeton d’accès est créé à l’aide du manifeste Graph, et non du manifeste du client.  Si vous modifiez le manifeste de votre application, les jetons pour Graph ne changent jamais.  Pour vérifier que vos modifications de `accessToken` sont effectives, demandez un jeton pour votre application, pas pour une autre application.  

**Exemple de schéma :**

```json
"optionalClaims":  
   {
      "idToken": [
            {
                  "name": "auth_time", 
                  "essential": false
             }
      ],
      "accessToken": [
             {
                    "name": "ipaddr", 
                    "essential": false
              }
      ],
      "saml2Token": [
              {
                    "name": "upn", 
                    "essential": false
               },
               {
                    "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                    "source": "user", 
                    "essential": false
               }
       ]
   }
```

### <a name="optionalclaims-type"></a>Type OptionalClaims

Déclare les revendications facultatives demandées par une application. Une application peut configurer des revendications facultatives à retourner dans chacun des trois types de jetons (jeton d’ID, jeton d’accès, jeton SAML 2) qu’elle peut recevoir à partir du service d’émission de jeton de sécurité. L’application peut configurer un ensemble différent de revendications facultatives à retourner dans chaque type de jeton. La propriété OptionalClaims de l’entité Application est un objet OptionalClaims.

**Tableau 5 : Propriétés de type OptionalClaims**

| Nom        | type                       | Description                                           |
|-------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Collection (OptionalClaim) | Revendications facultatives retournées dans le jeton d’ID JWT. |
| `accessToken` | Collection (OptionalClaim) | Revendications facultatives retournées dans le jeton d’accès JWT. |
| `saml2Token`  | Collection (OptionalClaim) | Revendications facultatives retournées dans le jeton SAML.   |

### <a name="optionalclaim-type"></a>Type OptionalClaim

Contient une revendication facultative associée à une application ou à un principal de service. Les propriétés idToken, accessToken et saml2Token du type [OptionalClaims](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) sont une collection d’OptionalClaim.
En cas de prise en charge par une revendication spécifique, vous pouvez également modifier le comportement de l’OptionalClaim à l’aide du champ AdditionalProperties.

**Tableau 6 : Propriétés de type OptionalClaim**

| Nom                 | type                    | Description                                                                                                                                                                                                                                                                                                   |
|----------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Nom de la revendication facultative.                                                                                                                                                                                                                                                                           |
| `source`               | Edm.String              | Source (objet d’annuaire) de la revendication. Il existe des revendications prédéfinies et définies par l’utilisateur à partir des propriétés d’extension. Si la valeur source est null, la revendication est une revendication facultative prédéfinie. Si la valeur source est user, la valeur de la propriété name est la propriété d’extension à partir de l’objet utilisateur. |
| `essential`            | Edm.Boolean             | Si la valeur est true, la revendication spécifiée par le client est nécessaire afin de garantir une expérience d’autorisation fluide pour la tâche demandée par l’utilisateur final. La valeur par défaut est false.                                                                                                             |
| `additionalProperties` | Collection (Edm.String) | Propriétés supplémentaires de la revendication. Si une propriété existe dans cette collection, elle modifie le comportement de la revendication facultative spécifiée dans la propriété name.                                                                                                                                               |
## <a name="configuring-directory-extension-optional-claims"></a>Configuration des revendications facultatives d’extension d’annuaire

En plus de l’ensemble de revendications facultatives standard, vous pouvez configurer des jetons pour inclure des extensions de schéma d’annuaire. Pour plus d’informations, voir [Extensions de schéma d’annuaire](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions). Cette fonctionnalité est utile pour joindre des informations utilisateur supplémentaires utilisables par votre application, par exemple un identificateur supplémentaire ou une option de configuration importante que l’utilisateur a définie. 

> [!Note]
> - Les extensions de schéma d’annuaire sont une fonctionnalité spécifique d’Azure AD. Par conséquent, si le manifeste de votre application demande une extension personnalisée et qu’un utilisateur MSA se connecte à votre application, ces extensions ne sont pas retournées.
> - Des revendications facultatives AD Azure fonctionnent uniquement avec l’extension Azure AD, et non avec l’extension d’annuaire Microsoft Graph. Les deux API nécessitent l’autorisation `Directory.ReadWriteAll` qui ne peut être accordée que par des administrateurs.

### <a name="directory-extension-formatting"></a>Mise en forme d’extension d’annuaire

Pour les attributs d’extension, utilisez le nom complet de l’extension (au format `extension_<appid>_<attributename>`) dans le manifeste de l’application. `<appid>` doit correspondre à l’ID de l’application demandant la revendication. 

Dans les jetons JWT, ces revendications seront émises avec le format de nom suivant : `extn.<attributename>`.

Dans les jetons SAML, ces revendications seront émises avec le format d’URI suivant : `http://schemas.microsoft.com/identity/claims/extn.<attributename>`.

## <a name="configuring-group-optional-claims"></a>Configuration des revendications facultatives de groupe

   > [!NOTE]
   > La possibilité d’émettre des noms de groupe pour des utilisateurs et groupes synchronisés en local est en préversion publique.

Cette section couvre les options de configuration sous les revendications facultatives pour la modification des attributs de groupe utilisés dans les revendications de groupe, de l’ObjectId de groupe par défaut aux attributs synchronisés à partir du Windows Active Directory local.

> [!IMPORTANT]
> Pour plus de détails, dont des mises en garde importantes relatives à la préversion publique des revendications de groupe d’attributs locaux, voir [Configurer des revendications de groupe pour les applications avec Azure AD](../hybrid/how-to-connect-fed-group-claims.md).

1. Dans le portail -> Azure Active Directory -> Inscriptions d’applications, sélectionnez Application -> Manifeste

2. Activer les revendications d’appartenance de groupe en modifiant la revendication groupMembershipClaim

   Les valeurs valides sont les suivantes :

   - « Toutes »
   - « SecurityGroup »
   - « DistributionList »
   - « DirectoryRole »

   Par exemple :

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   Par défaut, les ObjectId de groupe sont émis dans la valeur de revendication de groupe.  Pour modifier la valeur de revendication afin qu’elle contienne des attributs de groupe local, ou pour modifier le type de revendication en rôle, utilisez une configuration OptionalClaims comme suit :

3. Définissez des revendications facultatives de configuration de nom de groupe.

   Si vous souhaitez que les groupes dans le jeton contiennent les attributs de groupe AD local, dans la section Revendications facultatives, spécifiez la revendication facultative de type de jeton à laquelle appliquer la configuration, le nom de la revendication facultative demandée et toutes les propriétés supplémentaires souhaitées.  Plusieurs types de jetons peuvent être répertoriés :

   - idToken pour le jeton d’ID d’OIDC ;
   - accessToken pour le jeton d’accès OAuth/OIDC ;
   - Saml2Token pour les jetons SAML.

   > [!NOTE]
   > Le type de jeton Saml2Token s’applique aux jetons de format SAML1.1 et SAML2.0

   Pour chaque type de jeton pertinent, modifiez la revendication de groupe pour utiliser la section OptionalClaims dans le manifeste. Le schéma d’OptionalClaims est le suivant :

   ```json
   {
   "name": "groups",
   "source": null,
   "essential": false,
   "additionalProperties": []
   }
   ```

   | Schéma de revendications facultatives | Valeur |
   |----------|-------------|
   | **name:** | Doit être « groupes » |
   | **source:** | Non utilisé. Omettez ou spécifiez la valeur null |
   | **essential:** | Non utilisé. Omettez ou spécifiez la valeur false |
   | **additionalProperties:** | Liste de propriétés supplémentaires.  Les options valides sont « sam_account_name », « dns_domain_and_sam_account_name », « netbios_domain_and_sam_account_name » et « emit_as_roles » |

   Dans additionalProperties une seule des options « sam_account_name », « dns_domain_and_sam_account_name » ou « netbios_domain_and_sam_account_name » est requise.  Si plusieurs options sont présentes, la première est utilisée et les autres ignorées.

   Certaines applications requièrent des informations de groupe sur l’utilisateur dans la revendication de rôle.  Pour modifier le type de revendication de revendication de groupe en revendication de rôle, ajoutez « emit_as_roles » aux propriétés supplémentaires.  Les valeurs de groupe sont émises dans la revendication de rôle.

   > [!NOTE]
   > Si l’option « emit_as_roles » est utilisée, les rôles d’application configurés auxquels l’utilisateur est affecté n’apparaissent pas dans la revendication de rôle

**Exemples :** Émettre des groupes en tant que noms de groupes dans des jetons d’accès OAuth au format dnsDomainName\sAMAccountName

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

Pour émettre des noms de groupes à retourner au format netbiosDomain\sAMAccountName en tant que revendication de rôles dans des jetons d’ID SAML et OIDC :

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }

 ```

## <a name="optional-claims-example"></a>Exemple de revendications facultatives

Dans cette section, vous allez suivre un scénario afin de voir comment utiliser la fonctionnalité de revendications facultatives pour votre application.
Plusieurs options sont disponibles pour mettre à jour les propriétés de configuration d’identité d’une application afin d’activer et de configurer des revendications facultatives :
-   Vous pouvez modifier le manifeste de l’application. L’exemple ci-dessous applique cette méthode pour effectuer la configuration. Pour obtenir une présentation du manifeste, lisez d’abord l’article [Manifeste de l’application Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest).
-   Il est également possible d’écrire une application qui utilise l’[API Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api) pour mettre à jour votre application. La [Référence des types complexes et des entités](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#optionalclaims-type) dans le guide de référence de l’API Graph peut vous aider à configurer les revendications facultatives.

**Exemple :** dans l’exemple ci-dessous, vous allez modifier le manifeste d’une application pour ajouter des revendications aux jetons d’accès, d’ID et SAML destinés à l’application.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Une fois que vous êtes authentifié, choisissez votre client Azure AD en le sélectionnant dans le coin supérieur droit de la page.
1. Sélectionnez **Inscription des applications** dans la partie gauche.
1. Recherchez dans la liste l’application pour laquelle vous souhaitez configurer des revendications facultatives, puis cliquez dessus.
1. Dans la page de l’application, cliquez sur **Manifeste** pour ouvrir l’éditeur de manifeste en ligne. 
1. Vous pouvez modifier directement le manifeste à l’aide de cet éditeur. Le manifeste respecte le schéma de l’[entité Application](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) et met automatiquement en forme le manifeste une fois enregistré. Les nouveaux éléments sont ajoutés à la propriété `OptionalClaims`.

    ```json
      "optionalClaims": 
      {
            "idToken": [ 
                  { 
                        "name": "upn", 
                        "essential": false, 
                        "additionalProperties": [ "include_externally_authenticated_upn"]  
                  }
            ],
            "accessToken": [ 
                  {
                        "name": "auth_time", 
                        "essential": false
                  }
            ],
            "saml2Token": [ 
                  { 
                        "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                        "source": "user", 
                        "essential": true
                  }
            ]
      }

    ```

    Ici, différentes revendications facultatives ont été ajoutées à chaque type de jeton que l’application peut recevoir. Les jetons d’ID contiendront désormais l’UPN pour les utilisateurs fédérés au format complet (`<upn>_<homedomain>#EXT#@<resourcedomain>`). Les jetons d’accès demandés par d’autres clients pour cette application incluront désormais la revendication auth_time. Les jetons SAML contiendront désormais l’extension de schéma d’annuaire skypeId (dans cet exemple, l’ID d’application pour cette application est ab603c56068041afb2f6832e2a17e237). Les jetons SAML exposeront l’ID Skype en tant que `extension_skypeId`.

1. Quand vous avez terminé de mettre à jour le manifeste, cliquez sur **Enregistrer** pour enregistrer le manifeste.

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en davantage sur les revendications standard fournies par Azure AD.

- [Jetons d’ID](id-tokens.md)
- [Jetons d’accès](access-tokens.md)
