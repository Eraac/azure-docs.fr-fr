---
title: Description des rôles et des autorisations des administrateurs - Azure Active Directory | Microsoft Docs
description: Le rôle de l’administrateur peut servir à ajouter des utilisateurs, attribuer des rôles d’administrateur, réinitialiser les mots de passe utilisateur, gérer les licences utilisateur et les domaines, etc.
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 06/04/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ca740a4dcca3bdbb1951e55df4061364a5c646
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083917"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Autorisations des rôles d’administrateur dans Azure Active Directory

À l’aide d’Azure Active Directory (Azure AD), vous pouvez désigner des administrateurs limités pour gérer des tâches liées aux identités dans des rôles moins privilégiés. Les administrateurs peuvent se voir attribuer des responsabilités comme l’ajout ou le changement d’utilisateurs, l’attribution de rôles d’administrateur, la réinitialisation des mots de passe utilisateur, la gestion des licences utilisateur et la gestion des noms de domaine. Les autorisations d’utilisateur par défaut ne peuvent être modifiées que dans les paramètres utilisateur dans Azure AD.

L’administrateur général a accès à toutes les fonctionnalités d’administration. Par défaut, le rôle d’administrateur général de l’annuaire est affecté à la personne qui souscrit un abonnement Azure. Seuls les administrateurs généraux et les administrateurs disposant d'un rôle privilégié peuvent déléguer des rôles d'administrateur. Pour limiter les risques, nous vous conseillons de réserver ce rôle à quelques membres de votre entreprise uniquement.


## <a name="assign-or-remove-administrator-roles"></a>Attribution ou suppression de rôles d’administrateur

Pour découvrir comment attribuer des rôles d’administrateur à un utilisateur dans Azure Active Directory, consultez l’article [Afficher et attribuer des rôles d’administrateur dans Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Rôles disponibles

Les rôles d’administrateur disponibles sont les suivants :

* **[Administrateur d’application](#application-administrator)**  : Les utilisateurs dans ce rôle peuvent créer et gérer tous les aspects des applications d’entreprise, des inscriptions d’application et des paramètres de proxy d’application. Ce rôle permet également de donner son consentement pour des autorisations déléguées et des autorisations d’application, sauf pour Microsoft Graph et Azure AD Graph. Les utilisateurs affectés à ce rôle ne sont pas ajoutés en tant que propriétaires lorsque des inscriptions d’applications ou des applications d’entreprise sont créées.

  <b>Important !</b> Ce rôle permet de gérer des informations d’identification d’application. Les utilisateurs auxquels ce rôle a été attribué peuvent ajouter des informations d’identification à une application et les utiliser pour emprunter l’identité de l’application. Si l’identité de l’application a obtenu l’accès à Azure Active Directory, comme la possibilité de créer ou de mettre à jour un utilisateur ou d’autres objets, un utilisateur auquel ce rôle a été attribué peut effectuer ces actions tout en se faisant passer pour l’application. Cette capacité à emprunter l’identité de l’application peut correspondre une élévation de privilèges que l’utilisateur réalise dans ses attributions de rôle au sein d’Azure AD. Il est important de comprendre que l’affectation d’un utilisateur au rôle Administrateur d’applications lui donne la possibilité d’emprunter l’identité d’une application.

* **[Développeur d’applications](#application-developer)**  : Les utilisateurs dans ce rôle peuvent créer des inscriptions d’application quand le paramètre « Les utilisateurs peuvent inscrire des applications » est défini sur Non. Ce rôle octroie aussi l’autorisation de donner son consentement en son propre nom lorsque le paramètre « Les utilisateurs peuvent autoriser les applications à accéder aux données de l’entreprise en leur nom » est défini sur Non. Les utilisateurs affectés à ce rôle sont ajoutés en tant que propriétaires lorsque des inscriptions d’applications ou des applications d’entreprise sont créées.

* **[Administrateur d’authentification](#authentication-administrator)**  : Les utilisateurs disposant de ce rôle peuvent définir ou réinitialiser des informations d’identification sans mot de passe. Les administrateurs d’authentification peuvent obliger les utilisateurs à se réinscrire avec des informations d’identification sans mot de passe existantes (par exemple, MFA, FIDO) et à révoquer la **mémorisation de l’authentification multifacteur sur l’appareil**, ce qui permet de demander une authentification multifacteur lors de la prochaine connexion des utilisateurs non-administrateurs ou auxquels les rôles suivants uniquement sont attribués :
  * Administrateur d’authentification
  * Lecteurs de répertoires
  * Inviteur
  * Lecteur du Centre de messages
  * Lecteur de rapports

  Le rôle d’administrateur d’authentification est actuellement en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

  <b>Important !</b> Les utilisateurs auxquels ce rôle est assigné peuvent changer les informations d’identification des personnes susceptibles d’avoir accès à des informations sensibles ou privées ou à des configurations critiques à l’intérieur et à l’extérieur d’Azure Active Directory. Changer les informations d’identification d’un utilisateur peut signifier la capacité d’assumer l’identité et les autorisations de cet utilisateur. Par exemple :

  * Les propriétaires d’inscription d’application et d’application d’entreprise, qui peuvent gérer les informations d’identification des applications qu’ils possèdent. Ces applications peuvent disposer d’autorisations privilégiées dans Azure AD et ailleurs qui ne sont pas accordées aux administrateurs d’authentification. Par ce biais, un administrateur d’authentification peut être en mesure d’assumer l’identité d’un propriétaire d’application, puis d’assumer l’identité d’une application privilégiée en mettant à jour les identifiants de celle-ci.
  * Propriétaires d’abonnement Azure, qui peuvent avoir accès à des informations confidentielles ou privées ou à une configuration critique dans Azure.
  * Propriétaires de groupe de sécurité et de groupe Office 365, qui peuvent gérer l’appartenance à un groupe. Ces groupes peuvent accorder l’accès à des informations sensibles ou privées ou à une configuration critique dans Azure AD et ailleurs.
  * Administrateurs d’autres services en dehors d’Azure AD, tels qu’Exchange Online, le Centre de sécurité et de conformité Office et les systèmes de ressources humaines.
  * Les non-administrateurs comme les cadres supérieurs, les conseillers juridiques et les employés des ressources humaines qui peuvent avoir accès à des informations sensibles ou privées.

* **[Administrateur Azure Information Protection](#azure-information-protection-administrator)**  : Les utilisateurs avec ce rôle ont toutes les autorisations dans le service Azure Information Protection. Ce rôle permet de configurer les étiquettes pour la stratégie Azure Information Protection, de gérer les modèles de protection et d’activer la protection. Il n’octroie aucune autorisation dans Identity Protection Center, Privileged Identity Management, Monitor Office 365 Service Health ni dans le Centre de sécurité et conformité Office 365.

* **[Administrateur de flux d’utilisateurs B2C](#b2c-user-flow-administrator)**  : Les utilisateurs dotés de ce rôle peuvent créer et gérer des flux d’utilisateurs B2C (également appelés stratégies « intégrées ») dans le portail Azure. En créant ou en modifiant des flux d’utilisateurs, ces utilisateurs peuvent modifier le contenu html/CSS/javascript de l’expérience utilisateur, modifier les exigences MFA par flux d’utilisateurs, modifier les revendications dans le jeton et ajuster les paramètres de session de toutes les stratégies du locataire. En revanche, ce rôle n’inclut pas la possibilité de consulter les données utilisateur, ni d’apporter des modifications aux attributs inclus dans le schéma de locataire. Les modifications apportées aux stratégies Identity Experience Framework (autrement dit, personnalisées) sortent également de la portée de ce rôle.

* **[Administrateur d’attributs de flux d’utilisateurs B2C](#b2c-user-flow-attribute-administrator)**  : Les utilisateurs dotés de ce rôle peuvent ajouter ou supprimer des attributs personnalisés disponibles pour tous les flux d’utilisateurs dans le locataire. Ainsi, ils peuvent modifier ou ajouter de nouveaux éléments au schéma d’utilisateur final, influer sur le comportement de tous les flux d’utilisateurs et entraîner indirectement des modifications des données demandées aux utilisateurs finaux et envoyées sous forme de revendications aux applications. Ce rôle ne peut pas modifier des flux d’utilisateurs.

* **[Administrateur de jeux de clés B2C IEF](#b2c-ief-keyset-administrator)**  :    L’utilisateur peut créer et gérer des clés et secrets de stratégie pour le chiffrement des jetons, la signatures des jetons et le chiffrement/déchiffrement des revendications. En ajoutant de nouvelles clés aux conteneurs de clés existants, cet administrateur limité peut substituer des secrets en fonction des besoins sans affecter les applications existantes. Cet utilisateur peut voir le contenu complet de ces secrets et leurs dates d’expiration, même après leur création.
    
  <b>Important :</b> Il s’agit d’un rôle sensible. Le rôle d’administrateur de jeux de clés doit être soigneusement audité et attribué avec précaution pendant les périodes de préproduction et de production.

* **[Administrateur de stratégies B2C IEF](#b2c-ief-policy-administrator)**  : Les utilisateurs dotés de ce rôle ont la possibilité de créer, lire, mettre à jour et supprimer toutes les stratégies personnalisées dans Azure AD B2C et donc d’avoir un contrôle total sur la plateforme Identity Experience Framework du locataire Azure AD B2C pertinent. En modifiant les stratégies, cet utilisateur peut établir une fédération directe avec des fournisseurs d’identité externes, modifier le schéma d’annuaire, modifier tout le contenu visible par l’utilisateur (HTML, CSS et JavaScript), modifier les exigences liées à une authentification, créer des utilisateurs, envoyer des données utilisateur à des systèmes externes, notamment des migrations complètes, et modifier toutes les informations utilisateur, notamment des champs sensibles comme les mots de passe et les numéros de téléphone. À l’inverse, ce rôle ne peut pas modifier les clés de chiffrement ni modifier les secrets utilisés pour la fédération dans le locataire.

  <b>Important :</b> L’administrateur de stratégies B2C IEF est un rôle hautement sensible qui doit être attribué de manière très limitée pour les locataires en production. Les activités réalisées par ces utilisateurs doivent faire l’objet d’un audit précis, en particulier pour les locataires en production.

* **[Administrateur de facturation](#billing-administrator)**  : Effectue les achats, gère les abonnements, gère les tickets de support et supervise l’intégrité du service.

* **[Administrateur d’application cloud](#cloud-application-administrator)**  : Les utilisateurs dans ce rôle ont les mêmes autorisations que celles du rôle Administrateur d’application, sans la possibilité de gérer le proxy d’application. Ce rôle permet de créer et de gérer tous les aspects des applications d’entreprise et des inscriptions d’applications. Ce rôle permet également de donner son consentement pour des autorisations déléguées et des autorisations d’application, sauf pour Microsoft Graph et Azure AD Graph. Les utilisateurs affectés à ce rôle ne sont pas ajoutés en tant que propriétaires lorsque des inscriptions d’applications ou des applications d’entreprise sont créées.

  <b>Important !</b> Ce rôle permet de gérer des informations d’identification d’application. Les utilisateurs auxquels ce rôle a été attribué peuvent ajouter des informations d’identification à une application et les utiliser pour emprunter l’identité de l’application. Si l’identité de l’application a obtenu l’accès à Azure Active Directory, comme la possibilité de créer ou de mettre à jour un utilisateur ou d’autres objets, un utilisateur auquel ce rôle a été attribué peut effectuer ces actions tout en se faisant passer pour l’application. Cette capacité à emprunter l’identité de l’application peut correspondre une élévation de privilèges que l’utilisateur réalise dans ses attributions de rôle au sein d’Azure AD. Il est important de comprendre que l’affectation d’un utilisateur au rôle Administrateur d’applications cloud lui donne la possibilité d’emprunter l’identité d’une application.

* **[Administrateur d’appareil cloud](#cloud-device-administrator)**  : Les utilisateurs dans ce rôle peuvent activer, désactiver et supprimer des appareils dans Azure AD, et lire des clés BitLocker Windows 10 (le cas échéant) dans le portail Azure. Ce rôle ne permet pas de gérer d’autres propriétés sur l’appareil.

* **[Administrateur de conformité](#compliance-administrator)**  : Les utilisateurs avec ce rôle disposent des autorisations pour gérer les fonctionnalités liées à la conformité dans le centre de conformité Microsoft 365, le centre d'administration Microsoft 365, Azure et le Centre de sécurité et conformité Office 365. Les utilisateurs peuvent également gérer toutes les fonctionnalités dans le centre d’administration Exchange et le centre d’administration Teams et Skype Entreprise, et créer des tickets de support pour Azure et Microsoft 365. Pour plus d’informations, consultez [À propos des rôles d’administrateur Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  Dans | Peut
  ----- | ----------
  [Centre de conformité Microsoft 365](https://protection.office.com) | Protéger et gérer les données de votre organisation au sein des services Microsoft 365<br>Gérer les alertes de conformité
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Suivre, affecter et vérifier les activités de conformité réglementaire de votre organisation
  [Centre de sécurité et conformité Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gérer la gouvernance des données<br>Procéder à l'examen juridique des données<br>Gérer une requête d'objet de données
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Afficher toutes les données d’audit Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Dispose d'autorisations en lecture seule et peut gérer les alertes<br>Peut créer et modifier les stratégies de fichier et autoriser des actions de gouvernance de fichier<br> Peut afficher tous les rapports intégrés sous Gestion des données

* **[Administrateur des données de conformité](#compliance-data-administrator)**  : Les utilisateurs dotés de ce rôle sont autorisés à protéger et suivre les données dans le centre de conformité Microsoft 365, le centre d’administration Microsoft 365 et Azure. Les utilisateurs peuvent également gérer toutes les fonctionnalités dans le centre d’administration Exchange, le Gestionnaire de compatibilité, le centre d’administration Teams et Skype Entreprise, et créer des tickets de support pour Azure et Microsoft 365.

  Dans | Peut
  ----- | ----------
  [Centre de conformité Microsoft 365](https://protection.office.com) | Superviser les stratégies de conformité au sein des services Microsoft 365<br>Gérer les alertes de conformité
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Suivre, affecter et vérifier les activités de conformité réglementaire de votre organisation
  [Centre de sécurité et conformité Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gérer la gouvernance des données<br>Procéder à l'examen juridique des données<br>Gérer une requête d'objet de données
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Afficher toutes les données d’audit Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Dispose d'autorisations en lecture seule et peut gérer les alertes<br>Peut créer et modifier les stratégies de fichier et autoriser des actions de gouvernance de fichier<br> Peut afficher tous les rapports intégrés sous Gestion des données

* **[Administrateur de l’accès conditionnel](#conditional-access-administrator)** : Les utilisateurs avec ce rôle ont la possibilité de gérer les paramètres d’accès conditionnel Azure Active Directory.
  > [!NOTE]
  > Pour pouvoir déployer la stratégie d’accès conditionnel Exchange ActiveSync dans Azure, l’utilisateur doit également être administrateur général.
  
* **[Approbateur d’accès à Customer LockBox](#customer-lockbox-access-approver)**  : Gère les [requêtes Customer Lockbox](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) dans votre organisation. Il reçoit par e-mail des notifications ayant trait aux requêtes Customer Lockbox, et peut approuver ou refuser ces requêtes depuis le centre d'administration Microsoft 365. Il peut activer ou désactiver la fonctionnalité Customer Lockbox. Seuls les administrateurs généraux peuvent réinitialiser les mots de passe des personnes dotées de ce rôle.
  <!--  This was announced in August of 2018. https://techcommunity.microsoft.com/t5/Security-Privacy-and-Compliance/Customer-Lockbox-Approver-Role-Now-Available/ba-p/223393-->

* **[Administrateur Desktop Analytics](#desktop-analytics-administrator)** : Les utilisateurs avec ce rôle peuvent gérer Desktop Analytics et les services de stratégie et de personnalisation Office. Pour Desktop Analytics, cela inclut la possibilité d’afficher l’inventaire des biens, de créer des plans de déploiement, d’afficher l’état du déploiement et de l’intégrité. Pour le service de stratégie et personnalisation Office, ce rôle permet aux utilisateurs de gérer les stratégies Office.

* **[Administrateur de l’appareil](#device-administrators)** : Ce rôle peut uniquement être attribué comme administrateur local supplémentaire dans [Paramètres de l’appareil](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Les utilisateurs dotés de ce rôle deviennent des administrateurs de l'ordinateur local pour tous les appareils Windows 10 qui sont joints à Azure Active Directory. Ils ne peuvent pas gérer des objets appareil dans Azure Active Directory. 

* **[Lecteurs de répertoire](#directory-readers)**  : Il s’agit d’un rôle qui doit être attribué uniquement aux applications héritées ne prenant pas en charge le [framework de consentement](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ne l’attribuez pas à des utilisateurs.

* **[Comptes de synchronisation d’annuaires](#directory-synchronization-accounts)**  : Ne pas utiliser. Ce rôle est automatiquement attribué au service Azure AD Connect et n’est pas prévu ni pris en charge pour une autre utilisation.

* **[Enregistreurs de répertoire](#directory-writers)**  : Il s’agit d’un rôle hérité qui doit être attribué aux applications ne prenant pas en charge le [framework de consentement](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Vous ne devez pas l’affecter à tous les utilisateurs.

* **[Administrateur de Dynamics 365/Administrateur CRM](#crm-service-administrator)**  : Les utilisateurs auxquels ce rôle est assigné disposent d’autorisations générales dans Microsoft Dynamics 365 Online, lorsque le service est présent. Ils ont aussi la possibilité de gérer les tickets de support et de surveiller l’intégrité du service. Pour plus d’informations, consultez la page [Utiliser le rôle d’administrateur de service pour gérer votre locataire](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de service Dynamics 365 ». Il est appelé « Administrateur Dynamics 365 » dans le [portail Azure](https://portal.azure.com).

* **[Administrateur Exchange](#exchange-service-administrator)**  : Les utilisateurs avec ce rôle ont des autorisations générales dans Microsoft Exchange Online, quand le service est présent. Ils ont aussi la possibilité de créer et de gérer tous les groupes Office 365, de gérer les tickets de support et de surveiller l’état d’intégrité des services. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de services Exchange ». Il est appelé « Administrateur Exchange » dans le [portail Azure](https://portal.azure.com). Il est « Administrateur Exchange en ligne » dans le [centre d’administration Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144). 

* **[Administrateur de fournisseurs d’identité externes](#external-identity-provider-administrator)**  : Cet administrateur gère la fédération entre les locataires Azure Active Directory et les fournisseurs d’identité externes. Avec ce rôle, les utilisateurs peuvent ajouter de nouveaux fournisseurs d’identité et configurer tous les paramètres disponibles (par exemple, chemin d’authentification, ID de service, conteneurs de clés attribués). Cet utilisateur peut permettre au locataire d’approuver des authentifications de fournisseurs d’identité externes. L’impact produit sur les expériences utilisateur final varie selon le type de locataire :
  * Locataires Azure Active Directory pour les employés et partenaires : L’ajout d’une fédération (par exemple, avec Gmail) impacte immédiatement toutes les invitations d’invités qui ne sont pas encore utilisées. Consultez [Ajout de Google comme fournisseur d’identité pour les utilisateurs invités B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
  * Locataires Azure Active Directory B2C : L’ajout d’une fédération (par exemple, avec Facebook ou un autre service Azure Active Directory) n’impacte pas immédiatement les flux d’utilisateurs finaux tant que le fournisseur d’identité n’est pas ajouté comme option dans un flux d’utilisateur (autrement dit, une stratégie intégrée). Consultez [Configuration d’un compte Microsoft comme fournisseur d’identité](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) pour obtenir un exemple. Pour modifier des flux d’utilisateurs, le rôle limité d’« administrateur de flux d’utilisateurs B2C » est nécessaire.

* **[Administrateur général ou Administrateur d’entreprise](#company-administrator)**  : Les utilisateurs avec ce rôle ont accès à toutes les fonctionnalités d’administration dans Azure Active Directory, ainsi qu’aux services qui utilisent des identités Azure Active Directory comme le centre de sécurité Microsoft 365, le centre de conformité Microsoft 365, Exchange Online, SharePoint Online et Skype Entreprise Online. La personne qui s’inscrit pour le locataire Azure Active Directory devient administrateur général. Seuls les administrateurs généraux peuvent affecter d’autres rôles d’administrateur. Une entreprise peut comprendre plusieurs administrateurs généraux. Les administrateurs généraux peuvent réinitialiser le mot de passe des utilisateurs et de tous les autres administrateurs.

  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de l’entreprise ». Il est « Administrateur général » dans le [portail Azure](https://portal.azure.com).
  >
  >

* **[Inviteur d’invités](#guest-inviter)**  : les utilisateurs auxquels ce rôle est assigné peuvent gérer les invitations d’utilisateurs invités Azure Active Directory B2B lorsque le paramètre utilisateur **Les membres peuvent inviter** est défini sur Non. Pour plus d’informations sur B2B Collaboration, consultez la page [À propos d’Azure AD B2B Collaboration](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Il n’inclut pas d’autres autorisations.

* **[Administrateur Intune](#intune-service-administrator)**  : Les utilisateurs avec ce rôle ont des autorisations générales dans Microsoft Intune Online, quand le service est présent. Ce rôle donne aussi la possibilité de gérer les utilisateurs et les appareils afin d’associer la stratégie, ainsi que de créer et de gérer des groupes. Pour plus d’informations, consultez la page [Contrôle d’accès en fonction du rôle (RBAC) avec Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control).
  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de service Intune ». Il est « Administrateur Intune » dans le [portail Azure](https://portal.azure.com).
  
 * **[Administrateur Kaizala](#kaizala-administrator)**  : Les utilisateurs dotés de ce rôle ont des autorisations générales de gérer des paramètres au sein de Microsoft Kaizala, quand le service est présent, et ils peuvent gérer les tickets de support et superviser l’intégrité du service.
De plus, l’utilisateur peut accéder à des rapports liés à l’adoption et à l’utilisation de Kaizala par les membres de l’organisation, ainsi qu’à des rapports d’entreprise générés avec les actions Kaizala. 

* **[Administrateur de licence](#license-administrator)**  : Les utilisateurs dans ce rôle peuvent ajouter, supprimer et mettre à jour des attributions de licence pour les utilisateurs, les groupes (à l’aide de la gestion des licences par groupe) et gérer l’emplacement d’utilisation pour les utilisateurs. Le rôle ne permet pas d’acheter ou de gérer des abonnements, de créer ou de gérer des groupes, ni de créer ou de gérer les utilisateurs au-delà de leur emplacement d’utilisation. Ce rôle n’a pas d'accès pour afficher, créer ou gérer des tickets de support.

* **[Lecteur de confidentialité du Centre de messages](#message-center-privacy-reader)**  : Les utilisateurs dotés de ce rôle peuvent superviser toutes les notifications dans le Centre de messages, notamment les messages de confidentialité des données. Les lecteurs de confidentialité du Centre de messages reçoivent des notifications par e-mail, notamment celles liées à la confidentialité des données, et ils peuvent se désabonner à l’aide des préférences du Centre de messages. Seuls l’administrateur général et le lecteur de confidentialité du Centre de messages peuvent lire les messages de confidentialité des données. De plus, ce rôle permet de voir les groupes, les domaines et les abonnements. Il n’est pas autorisé à voir, créer ou gérer des demandes de service.

* **[Lecteur du Centre de messages](#message-center-reader)**  : Les utilisateurs dans ce rôle peuvent superviser les notifications et les mises à jour d’avis d’intégrité dans le [Centre de messages Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) pour leur organisation sur les services configurés comme Exchange, Intune et Microsoft Teams. Les lecteurs du Centre de messages reçoivent par e-mail des résumés hebdomadaires des publications, mises à jour, et peuvent partager les messages publiés sur le Centre de messages dans Office 365. Dans Azure AD, les utilisateurs affectés à ce rôle disposeront d’un accès en lecture seule aux services Azure AD comme les utilisateurs et les groupes. Ce rôle n’a pas d'accès pour afficher, créer ou gérer des tickets de support.

* **[Prise en charge de niveau 1 de partenaire](#partner-tier1-support)**  : Ne pas utiliser. Ce rôle est déconseillé et sera prochainement supprimé d’Azure AD. Il s’adresse à un petit nombre de partenaires revendeurs Microsoft et n’est pas destiné à une utilisation générale.

* **[Prise en charge de niveau 2 de partenaire](#partner-tier2-support)**  : Ne pas utiliser. Ce rôle est déconseillé et sera prochainement supprimé d’Azure AD. Il s’adresse à un petit nombre de partenaires revendeurs Microsoft et n’est pas destiné à une utilisation générale.

* **[Administrateur du support (mot de passe)](#helpdesk-administrator)**  : Les utilisateurs avec ce rôle peuvent changer des mots de passe, rendre non valides les jetons d’actualisation, gérer les demandes de service et superviser l’intégrité du service. L’invalidation d’un jeton d’actualisation oblige l’utilisateur à se reconnecter. Les administrateurs du support peuvent réinitialiser les mots de passe et invalider les jetons d’actualisation des autres utilisateurs qui ne sont pas administrateurs ni dotés des rôles suivants uniquement :
  * Lecteurs de répertoires
  * Inviteur
  * Administrateur du support technique
  * Lecteur du Centre de messages
  * Lecteur de rapports
  
  <b>Important !</b> Les utilisateurs auxquels ce rôle est assigné peuvent changer les mots de passe des personnes susceptibles d’avoir accès à des informations sensibles ou privées ou à des configurations critiques à l’intérieur et à l’extérieur d’Azure Active Directory. Changer le mot de passe d’un utilisateur peut signifier la capacité d’assumer l’identité et les autorisations de cet utilisateur. Par exemple :
  * Les propriétaires d’inscription d’application et d’application d’entreprise, qui peuvent gérer les informations d’identification des applications qu’ils possèdent. Ces applications peuvent disposer d’autorisations privilégiées dans Azure AD et ailleurs qui ne sont pas accordées aux administrateurs du support technique. Grâce à ce chemin d’accès, un administrateur de support technique peut être en mesure d’assumer l’identité d’un propriétaire d’application, puis d’assumer l’identité d’une application privilégiée en mettant à jour les identifiants de celle-ci.
  * Propriétaires d’abonnement Azure, qui peuvent avoir accès à des informations confidentielles ou privées ou à une configuration critique dans Azure.
  * Propriétaires de groupe de sécurité et de groupe Office 365, qui peuvent gérer l’appartenance à un groupe. Ces groupes peuvent accorder l’accès à des informations sensibles ou privées ou à une configuration critique dans Azure AD et ailleurs.
  * Administrateurs d’autres services en dehors d’Azure AD, tels qu’Exchange Online, le Centre de sécurité et de conformité Office et les systèmes de ressources humaines.
  * Les non-administrateurs comme les cadres supérieurs, les conseillers juridiques et les employés des ressources humaines qui peuvent avoir accès à des informations sensibles ou privées.


  > [!NOTE]
  > La délégation d’autorisations administratives à des sous-ensembles d’utilisateurs et l’application de stratégies à un sous-ensemble d’utilisateurs sont possibles avec des [unités administratives (préversion)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).


  > [!NOTE]
  > Ce rôle était auparavant appelé « administrateur de mot de passe » dans le [portail Azure](https://portal.azure.com/). Nous en changeons le nom par « administrateur du support » pour le faire correspondre à celui utilisé dans Azure AD PowerShell, l’API Azure AD Graph et l’API Microsoft Graph. Pendant une courte période, nous allons remplacer ce nom par « administrateur du support (mot de passe) » dans le portail Azure avant de le remplacer par « administrateur du support technique ».


* **[Administrateur Power BI](#power-bi-service-administrator)**  : Les utilisateurs avec ce rôle ont des autorisations générales dans Microsoft Power BI, quand le service est présent, et peuvent gérer les tickets de support et superviser l’intégrité du service. Pour plus d’informations, consultez la page [Présentation du rôle d’administrateur Power BI](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de service Power BI ». Il est appelé « Administrateur Power BI » dans le [portail Azure](https://portal.azure.com).

* **[Administrateur d’authentification privilégié](#privileged-authentication-administrator)**  : Les utilisateurs dotés de ce rôle peuvent définir ou réinitialiser des informations d’identification sans mot de passe pour tous les utilisateurs, y compris les administrateurs généraux. Les administrateurs d’authentification privilégiés peuvent forcer les utilisateurs à se réinscrire avec des informations d’identification sans mot de passe existantes (par exemple, MFA, FIDO) et à révoquer la mémorisation de l’authentification multifacteur sur l’appareil, ce qui permet de demander une authentification multifacteur lors de la prochaine connexion de tous les utilisateurs. Les administrateurs d’authentification privilégiés peuvent :
  * Forcer les utilisateurs à se réinscrire avec des informations d’identification sans mot de passe existantes (par exemple, MFA, FIDO)
  * Révoquer la mémorisation de l’authentification multifacteur sur l’appareil, ce qui permet de demander une authentification multifacteur lors de la prochaine connexion

* **[Administrateur de rôle privilégié](#privileged-role-administrator)**  : Les utilisateurs avec ce rôle peuvent gérer les attributions de rôles dans Azure Active Directory et Azure AD Privileged Identity Management. De plus, ce rôle permet de gérer tous les aspects de Privileged Identity Management et des unités administratives.

  <b>Important !</b> Ce rôle donne la possibilité de gérer les attributions de tous les rôles Azure AD, y compris le rôle d’administrateur général. Ce rôle n’inclut pas d’autres capacités privilégiées dans Azure AD, comme la création ou la mise à jour des utilisateurs. Toutefois, les utilisateurs affectés à ce rôle peuvent s’accorder ou accorder à d’autres des privilèges supplémentaires en leur attribuant des rôles supplémentaires.

* **[Lecteur de rapports](#reports-reader)**  : Les utilisateurs avec ce rôle peuvent voir les données de rapports sur l’utilisation et le tableau de bord des rapports dans le centre d’administration Microsoft 365 et le pack du contexte d’adoption dans Power BI. En outre, ce rôle donne accès aux rapports de connexion et aux activités dans Azure AD, ainsi qu’aux données renvoyées par l’API de création de rapports Microsoft Graph. Un utilisateur disposant du rôle Lecteur de rapports peut uniquement accéder au métriques d’utilisation et d’adoption pertinentes. Il n’a pas les autorisations administrateur permettant de configurer les paramètres ou d’accéder aux centres d’administration propres au produit comme Exchange. Ce rôle n’a pas d'accès pour afficher, créer ou gérer des tickets de support.

* **[Administrateur de recherche](#search-administrator)**  : Les utilisateurs dotés de ce rôle disposent d’un accès total à toutes les fonctionnalités de gestion de Recherche Microsoft disponibles dans le centre d’administration Microsoft 365. Les administrateurs de recherche peuvent déléguer les rôles Administrateur de recherche et Éditeur de recherche aux utilisateurs, créer et gérer du contenu, comme les signets, les questions et réponses, ainsi que les emplacements. De plus, ces utilisateurs peuvent voir le Centre de messages, superviser l’intégrité du service et créer des demandes de service.

* **[Éditeur de recherche](#search-editor)**  : Les utilisateurs dotés de ce rôle peuvent créer, gérer et supprimer du contenu pour Recherche Microsoft dans le centre d’administration Microsoft 365, notamment des signets, des questions et réponses, ainsi que des emplacements.

* **[Administrateur de la sécurité](#security-administrator)**  : Les utilisateurs disposant de ce rôle sont autorisés à gérer les fonctionnalités liées à la sécurité dans le Centre de sécurité Microsoft 365, Azure Active Directory Identity Protection, Azure Information Protection et le Centre de sécurité et conformité Office 365. Pour plus d’informations sur les autorisations Office 365, consultez [Autorisations dans le Centre de sécurité et conformité Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  Dans | Peut
  --- | ---
  [Centre de sécurité Microsoft 365](https://protection.office.com) | Surveiller les stratégies de sécurité au sein des services Microsoft 365<br>Gérer les menaces et les alertes liées à la sécurité<br>Afficher des rapports
  Identity Protection Center | Toutes les autorisations du rôle lecteur de sécurité<br>En outre, possibilité d’effectuer toutes les opérations du centre de protection de l'identité, à l’exception de la réinitialisation des mots de passe.
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Toutes les autorisations du rôle lecteur de sécurité<br>**Ne peut pas** gérer les paramètres ou les attributions de rôle Azure AD
  [Centre de sécurité et conformité Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gérer les stratégies de sécurité<br>Afficher, examiner et répondre aux menaces de sécurité<br>Afficher des rapports
  Azure Advanced Threat Protection | Surveiller et répondre aux activités de sécurité suspectes
  Windows Defender ATP et EDR | Attribuer des rôles<br>Gérer des groupes de machines<br>Configurer la détection des menaces liées aux points de terminaison et leur correction automatisée<br>Afficher, examiner et répondre aux alertes
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Affiche des informations relatives à l'utilisateur, l'appareil, l'inscription, la configuration et l'application<br>Ne peut apporter de modifications à Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Ajouter des administrateurs, des stratégies et des paramètres, charger des journaux d’activité et effectuer des actions de gouvernance
  [Centre de sécurité Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | peut afficher les stratégies de sécurité, les états de sécurité, les alertes et les recommandations, et ignorer les alertes et les recommandations
  [Office 365 Service Health](https://docs.microsoft.com/office365/enterprise/view-service-health) | Afficher l’intégrité des services Office 365

* **[Opérateur de sécurité](#security-operator)**  : Les utilisateurs dotés de ce rôle peuvent gérer les alertes et disposent d’un accès en lecture seule global à la fonctionnalité liée à la sécurité, notamment à toutes les informations disponibles dans le centre de sécurité Microsoft 365, Azure Active Directory, Identity Protection, Privileged Identity Management et le Centre de sécurité et de conformité Office 365. Pour plus d’informations sur les autorisations Office 365, consultez [Autorisations dans le Centre de sécurité et conformité Office 365](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

  Dans | Peut
  --- | ---
  [Centre de sécurité Microsoft 365](https://protection.office.com) | Toutes les autorisations du rôle lecteur de sécurité<br>Voir, examiner et répondre aux alertes sur les menaces de sécurité
  Identity Protection Center | Toutes les autorisations du rôle lecteur de sécurité<br>En outre, possibilité d’effectuer toutes les opérations du centre de protection de l'identité, à l’exception de la réinitialisation des mots de passe.
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Toutes les autorisations du rôle lecteur de sécurité
  [Centre de sécurité et conformité Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Toutes les autorisations du rôle lecteur de sécurité<br>Voir, examiner et répondre aux alertes de sécurité
  Windows Defender ATP et EDR | Toutes les autorisations du rôle lecteur de sécurité<br>Voir, examiner et répondre aux alertes de sécurité
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Toutes les autorisations du rôle lecteur de sécurité
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Toutes les autorisations du rôle lecteur de sécurité
  [Office 365 Service Health](https://docs.microsoft.com/office365/enterprise/view-service-health) | Afficher l’intégrité des services Office 365
<!--* **[Security Operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management.-->

* **[Lecteur Sécurité](#security-reader)**  : Les utilisateurs dotés de ce rôle ont un accès en lecture seule au niveau global à la fonctionnalité liée à la sécurité, notamment à toutes les informations dans le centre de sécurité Microsoft 365, Azure Active Directory, Identity Protection, Privileged Identity Management. Ils peuvent aussi lire les rapports sur les connexions Azure Active Directory et les journaux d’audit, ainsi que dans le centre de sécurité et de conformité Office 365. Pour plus d’informations sur les autorisations Office 365, consultez [Autorisations dans le Centre de sécurité et conformité Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  Dans | Peut
  --- | ---
  [Centre de sécurité Microsoft 365](https://protection.office.com) | Afficher les stratégies de sécurité au sein des services Microsoft 365<br>Afficher les menaces et les alertes liées à la sécurité<br>Afficher des rapports
  Identity Protection Center | Lire tous les rapports de sécurité et informations de paramètres pour les fonctionnalités de sécurité<br><ul><li>Anti-spam<li>Chiffrement<li>Prévention contre la perte de données<li>Anti-programme malveillant<li>Détection avancée des menaces<li>Anti-hameçonnage<li>Règles du flux de messagerie
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | A un accès en lecture seule à toutes les informations disponibles dans Azure AD PIM : Stratégies et rapports pour les attributions de rôle Azure AD, révisions de sécurité et prochainement accès en lecture aux données et rapports de stratégie pour les scénarios en plus de l’attribution de rôle Azure AD.<br>**Ne peut pas** s’inscrire auprès d’AD PIM ou y apporter des modifications. Dans le portail PIM ou par le biais de PowerShell, un membre ayant ce rôle peut activer des rôles supplémentaires (par exemple, un administrateur général ou un administrateur de rôle privilégié), s’il est éligible.
  [Centre de sécurité et conformité Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Afficher les stratégies de sécurité<br>Afficher et examiner les menaces de sécurité<br>Afficher des rapports
  Windows Defender ATP et EDR | Afficher et examiner les alertes
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Affiche des informations relatives à l'utilisateur, l'appareil, l'inscription, la configuration et l'application. Ne peut pas apporter de modifications à Intune.
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Dispose d'autorisations en lecture seule et peut gérer les alertes
  [Centre de sécurité Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | peut afficher les recommandations et les alertes, afficher les stratégies de sécurité, afficher les états de la sécurité, mais ne peut pas apporter des modifications
  [Office 365 Service Health](https://docs.microsoft.com/office365/enterprise/view-service-health) | Afficher l’intégrité des services Office 365

* **[Administrateur de support de service](#service-support-administrator)**  : Les utilisateurs dotés de ce rôle peuvent ouvrir des demandes de support auprès de Microsoft pour les services Azure et Office 365, ainsi que voir le tableau de bord des services et le Centre de messages dans le [portail Azure](https://portal.azure.com) et le [centre d’administration Microsoft 365](https://admin.microsoft.com). Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de support de service ». Il est « Administrateur de service » dans le [portail Azure](https://portal.azure.com), le [centre d’administration Microsoft 365](https://admin.microsoft.com) et le portail Intune.

* **[Administrateur SharePoint](#sharepoint-service-administrator)**  : Les utilisateurs avec ce rôle ont des autorisations générales dans Microsoft SharePoint Online, quand le service est présent. Ils peuvent également créer et gérer tous les groupes Office 365, gérer les tickets de support et superviser l’intégrité des services. Plus d’informations sur les [Rôles d’administrateur dans Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de services SharePoint ». Il est « Administrateur SharePoint » dans le [portail Azure](https://portal.azure.com).

* **[Administrateur Skype Entreprise ou Administrateur Lync](#lync-service-administrator)**  : Les utilisateurs avec ce rôle ont des autorisations générales dans Microsoft Skype Entreprise, quand le service est présent. Ils peuvent également gérer les attributs utilisateur propres à Skype dans Azure Active Directory. En outre, ce rôle permet de gérer les tickets de support et de surveiller l’intégrité des services, ainsi que d’accéder au Centre d’administration Teams et Skype Entreprise. Le compte doit également disposer d’une licence Teams ; dans le cas contraire, il ne pourra pas exécuter les applets de commande PowerShell Teams. Pour plus d’informations, consultez la page [About the Skype for Business admin role](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) (À propos du rôle d’administrateur Skype Entreprise) ; pour plus d’informations sur les licences Teams, consultez l’article [Licences de compléments pour Skype Entreprise et Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing).

  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de services Lync ». Il est appelé « Administrateur Skype Entreprise » dans le [portail Azure](https://portal.azure.com/).

* **[Administrateur Teams](#teams-service-administrator)**  : Les utilisateurs dans ce rôle peuvent gérer tous les aspects de la charge de travail Microsoft Teams via le centre d’administration Microsoft Teams et Skype Entreprise et les modules PowerShell respectifs. Cela inclut notamment tous les outils de gestion liés à la téléphonie, à la messagerie, aux réunions et aux équipes proprement dites. En outre, ce rôle permet de créer et de gérer tous les groupes Office 365, de gérer les tickets de support et de surveiller l’état d’intégrité des services.
  > [!NOTE]
  > Dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur du service Teams ». Il est « Administrateur Teams » dans le [portail Azure](https://portal.azure.com).

* **[Administrateur des communications Teams](#teams-communications-administrator)**  : Les utilisateurs dans ce rôle peuvent gérer les aspects de la charge de travail Microsoft Teams liés à la voix et à la téléphonie. Cela comprend les outils de gestion pour l’attribution des numéros de téléphone, les stratégies de voix et de réunion, ainsi que l’accès total à un ensemble d’outils d’analyse des appels.

* **[Ingénieur de support des communications Teams](#teams-communications-support-engineer)**  : Les utilisateurs dans ce rôle peuvent résoudre les problèmes de communication dans Microsoft Teams et Skype Entreprise à l’aide des outils de résolution de problèmes des appels utilisateur dans le centre d’administration Microsoft Teams et Skype Entreprise. Les utilisateurs disposant de ce rôle peuvent visualiser l’intégralité des informations d’enregistrement des appels pour l’ensemble des participants impliqués. Ce rôle n’a pas d'accès pour afficher, créer ou gérer des tickets de support.

* **[Spécialiste du support des communications Teams](#teams-communications-support-specialist)**  : Les utilisateurs dans ce rôle peuvent résoudre les problèmes de communication dans Microsoft Teams et Skype Entreprise à l’aide des outils de résolution de problèmes des appels utilisateur dans le centre d’administration Microsoft Teams et Skype Entreprise. Les utilisateurs disposant de ce rôle ne peuvent visualiser les détails utilisateur d’un appel que pour l’utilisateur qu’ils ont spécifiquement recherché. Ce rôle n’a pas d'accès pour afficher, créer ou gérer des tickets de support.

* **[Administrateur d’utilisateurs](#user-administrator)**  : Les utilisateurs dotés de ce rôle peuvent créer des utilisateurs, gérer tous les aspects des utilisateurs avec certaines restrictions (voir ci-dessous) et mettre à jour les stratégies d’expiration de mot de passe. De plus, les utilisateurs dotés de ce rôle peuvent créer et gérer tous les groupes. Ce rôle inclut également la possibilité de créer et de gérer des affichages utilisateur, de gérer les tickets de support et de surveiller l’état d’intégrité des services.

  | | |
  | --- | --- |
  |Autorisations générales|<p>Créer des utilisateurs et des groupes</p><p>Créer et gérer des vues utilisateur</p><p>Gérer les tickets de support Office<p>Mettre à jour les stratégies d’expiration de mot de passe|
  |<p>Tous les utilisateurs, notamment les administrateurs</p>|<p>Gérer les licences</p><p>Gérer toutes les propriétés de l’utilisateur, sauf le nom d’utilisateur principal</p>
  |Uniquement pour les utilisateurs qui ne sont pas administrateurs ou qui ont l’un des rôles d’administrateur limités suivants :<ul><li>Lecteurs de répertoires<li>Inviteur<li>Administrateur du support technique<li>Lecteur du Centre de messages<li>Lecteur de rapports<li>Administrateur d'utilisateurs|<p>Supprimer et restaurer</p><p>Désactiver et activer</p><p>Invalider les jetons d’actualisation</p><p>Gérer toutes les propriétés de l’utilisateur, y compris le nom d’utilisateur principal</p><p>Réinitialiser le mot de passe</p><p>Mettre à jour les clés d’appareil (FIDO)</p>
  
  <b>Important !</b> Les utilisateurs auxquels ce rôle est assigné peuvent changer les mots de passe des personnes susceptibles d’avoir accès à des informations sensibles ou privées ou à des configurations critiques à l’intérieur et à l’extérieur d’Azure Active Directory. Changer le mot de passe d’un utilisateur peut signifier la capacité d’assumer l’identité et les autorisations de cet utilisateur. Par exemple :
  * Les propriétaires d’inscription d’application et d’application d’entreprise, qui peuvent gérer les informations d’identification des applications qu’ils possèdent. Ces applications peuvent disposer d’autorisations privilégiées dans Azure AD et ailleurs qui ne sont pas accordées aux administrateurs utilisateurs. Grâce à ce chemin d’accès, un administrateur utilisateur peut être en mesure d’assumer l’identité d’un propriétaire d’application, puis d’assumer l’identité d’une application privilégiée en mettant à jour les identifiants de celle-ci.
  * Propriétaires d’abonnement Azure, qui peuvent avoir accès à des informations confidentielles ou privées ou à une configuration critique dans Azure.
  * Propriétaires de groupe de sécurité et de groupe Office 365, qui peuvent gérer l’appartenance à un groupe. Ces groupes peuvent accorder l’accès à des informations sensibles ou privées ou à une configuration critique dans Azure AD et ailleurs.
  * Administrateurs d’autres services en dehors d’Azure AD, tels qu’Exchange Online, le Centre de sécurité et de conformité Office et les systèmes de ressources humaines.
  * Les non-administrateurs comme les cadres supérieurs, les conseillers juridiques et les employés des ressources humaines qui peuvent avoir accès à des informations sensibles ou privées.

## <a name="role-permissions"></a>Autorisations des rôles
Les tableaux suivants décrivent les autorisations spécifiques à chaque rôle dans Azure Active Directory. Certains rôles peuvent disposer d’autorisations supplémentaires dans les services Microsoft en dehors d’Azure Active Directory.

### <a name="application-administrator"></a>Administrateur d’application
Peut créer et gérer tous les aspects des inscriptions d’applications et des applications d’entreprise.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Mettez à jour la propriété applications.audience dans Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Mettez à jour la propriété applications.authentication dans Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Mettez à jour des propriétés de base sur des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/create | Créez des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Mettez à jour la propriété applications.credentials dans Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Supprimez des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Mettez à jour la propriété applications.owners dans Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Mettez à jour la propriété applications.permissions dans Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Mettez à jour la propriété applications.policies dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Créez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Lisez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Mettez à jour des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Supprimez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Lisez toutes les propriétés (y compris les propriétés privilégiées) sur auditLogs dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Mettez à jour la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Créez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Supprimez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Mettez à jour la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Mettez à jour la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Mettez à jour la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Mettez à jour la propriété servicePrincipals.audience dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Mettez à jour la propriété servicePrincipals.authentication dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Mettez à jour des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Créez des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Mettez à jour la propriété servicePrincipals.credentials dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Supprimez des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Mettez à jour la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Mettez à jour la propriété servicePrincipals.permissions dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Mettez à jour la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Lisez toutes les propriétés (y compris les propriétés privilégiées) sur signInReports dans Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="application-developer"></a>Développeur d’applications
Peut créer des inscriptions d’applications indépendamment du paramètre « Les utilisateurs peuvent inscrire des applications ».

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Créez des applications dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Créez des attributions de rôles d’applications dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Créez des octrois d’autorisation OAuth2 dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Créez des principaux de service dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |

### <a name="authentication-administrator"></a>Administrateur d’authentification
Permet d’afficher, de définir et de réinitialiser les informations de méthode d’authentification pour tout utilisateur non administrateur.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Mettez à jour les propriétés d’authentification forte comme les informations d’identification MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="azure-information-protection-administrator"></a>Administrateur Azure Information Protection
Peut gérer tous les aspects du service Azure Information Protection.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Gérez tous les aspects d’Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="b2c-user-flow-administrator"></a>Administrateur de flux d’utilisateurs B2C
Créez et gérez tous les aspects des flux d’utilisateurs.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Lisez et configurez des flux d’utilisateurs dans Azure Active Directory B2C. |

### <a name="b2c-user-flow-attribute-administrator"></a>Administrateur d’attributs de flux d’utilisateurs B2C
Créez et gérez le schéma d’attribut disponible pour tous les flux d’utilisateurs.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Lisez et configurez les attributs utilisateur dans Azure Active Directory B2C. |

### <a name="b2c-ief-keyset-administrator"></a>Administrateur de jeux de clés B2C IEF
Gérez les secrets pour la fédération et le chiffrement dans Identity Experience Framework.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Lisez et configurez des jeux de clés dans Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator"></a>Administrateur de stratégies B2C IEF
Créez et gérez des stratégies d’infrastructure de confiance dans Identity Experience Framework.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Lisez et configurez des stratégies personnalisées dans Azure Active Directory B2C. |

### <a name="billing-administrator"></a>Administrateur de facturation
Peut effectuer des tâches de facturation courantes, comme la mise à jour des informations de paiement.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | Mettez à jour des propriétés de base sur une organisation dans Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gérez tous les aspects de la facturation Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |


### <a name="cloud-application-administrator"></a>Administrateur d'applications cloud
Peut créer et gérer tous les aspects des inscriptions d’applications et des applications d’entreprise, à l’exception du proxy d’application.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Mettez à jour la propriété applications.audience dans Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Mettez à jour la propriété applications.authentication dans Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Mettez à jour des propriétés de base sur des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/create | Créez des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Mettez à jour la propriété applications.credentials dans Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Supprimez des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Mettez à jour la propriété applications.owners dans Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Mettez à jour la propriété applications.permissions dans Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Mettez à jour la propriété applications.policies dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Créez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Mettez à jour des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Supprimez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Lisez toutes les propriétés (y compris les propriétés privilégiées) sur auditLogs dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Créez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Mettez à jour la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Supprimez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Mettez à jour la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Lisez la propriété policies.applicationConfiguration dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Mettez à jour la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Mettez à jour la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Mettez à jour la propriété servicePrincipals.audience dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Mettez à jour la propriété servicePrincipals.authentication dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Mettez à jour des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Créez des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Mettez à jour la propriété servicePrincipals.credentials dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Supprimez des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Mettez à jour la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Mettez à jour la propriété servicePrincipals.permissions dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Mettez à jour la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Lisez toutes les propriétés (y compris les propriétés privilégiées) sur signInReports dans Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="cloud-device-administrator"></a>Administrateur d’appareil cloud
Accès total pour gérer des appareils dans Azure AD.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Lisez toutes les propriétés (y compris les propriétés privilégiées) sur auditLogs dans Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Lisez la propriété devices.bitLockerRecoveryKeys dans Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Supprimez des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/disable | Désactivez des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/enable | Activez des appareils dans Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Lisez toutes les propriétés (y compris les propriétés privilégiées) sur signInReports dans Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |

### <a name="company-administrator"></a>Administrateur d’entreprise
Peut gérer tous les aspects d’Azure AD et des services Microsoft qui utilisent des identités Azure AD.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans microsoft.aad.cloudAppSecurity. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Créez et supprimez des unités administratives (administrativeUnits), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Créez et supprimez des applications, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Créez et supprimez des attributions de rôles d’applications (appRoleAssignments), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Lisez toutes les propriétés (y compris les propriétés privilégiées) sur auditLogs dans Azure Active Directory. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Créez et supprimez des contacts, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Créez et supprimez des contrats, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/devices/allProperties/allTasks | Créez et supprimez des appareils, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Créez et supprimez des rôles d’annuaire (directoryRoles), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Créez et supprimez des modèles de rôles d’annuaire (DirectoryRoleTemplates), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/domains/allProperties/allTasks | Créez et supprimez des domaines, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/groups/allProperties/allTasks | Créez et supprimez des groupes, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Créez et supprimez des paramètres de groupes (groupSettings), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Créez et supprimez des modèles de paramètres de groupes (groupSettingTemplates), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Créez et supprimez une personnalisations de client de connexion (loginTenantBranding), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Créez et supprimez des octrois d’autorisation OAuth2 (oAuth2PermissionGrants), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/organization/allProperties/allTasks | Créez et supprimez des organisations, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/policies/allProperties/allTasks | Créez et supprimez des stratégies, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Créez et supprimez des attributions de rôles (roleAssignments), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Créez et supprimez des définitions de rôles (roleDefinitions), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Créez et supprimez des appartenances à un rôle étendu (scopedRoleMemberships), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Peut effectuer l’action de service Activateservice dans Azure Active Directory |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Peut effectuer l’action de service Disabledirectoryfeature dans Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Peut effectuer l’action de service Enabledirectoryfeature dans Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Peut effectuer l’action de service Getavailableextentionproperties dans Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Créez et supprimez des principaux de service (servicePrincipals), et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Lisez toutes les propriétés (y compris les propriétés privilégiées) sur signInReports dans Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Créez et supprimez des références SKU souscrites, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Créez et supprimez des utilisateurs, et lisez et mettez à jour toutes les propriétés dans Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Exécutez toutes les actions dans Azure AD Connect. |
| microsoft.aad.directorySync/allEntities/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Lisez toutes les ressources dans microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Lisez toutes les ressources dans microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gérez tous les aspects d’Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gérez tous les aspects de la facturation Office 365. |
| microsoft.intune/allEntities/allTasks | Gérez tous les aspects d’Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gérez tous les aspects d’Office 365 Compliance Manager |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gérez tous les aspects de Desktop Analytics. |
| microsoft.office365.exchange/allEntities/allTasks | Gérez tous les aspects d’Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Gérez tous les aspects d’Office 365 Customer Lockbox. |
| microsoft.office365.messageCenter/messages/read | Lisez les messages dans microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Lisez les messages securityMessages dans microsoft.office365.messageCenter. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Gérez tous les aspects du Centre de protection Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gérez tous les aspects de Skype Entreprise Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |
| microsoft.office365.usageReports/allEntities/read | Lisez des rapports d’utilisation Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gérez tous les aspects de Dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gérez tous les aspects de Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Lisez toutes les ressources dans microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator"></a>Administrateur de conformité
Peut lire et gérer la configuration de la conformité et les rapports dans Azure AD et Office 365.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gérez tous les aspects d’Office 365 Compliance Manager |
| microsoft.office365.exchange/allEntities/allTasks | Gérez tous les aspects d’Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gérez tous les aspects de Skype Entreprise Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="compliance-data-administrator"></a>Administrateur des données de conformité
Crée et gère le contenu de conformité.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Lisez et configurez Microsoft Cloud App Security. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gérez tous les aspects d’Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gérez tous les aspects d’Office 365 Compliance Manager |
| microsoft.office365.exchange/allEntities/allTasks | Gérez tous les aspects d’Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gérez tous les aspects de Skype Entreprise Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="conditional-access-administrator"></a>Administrateur de l’accès conditionnel
Peut gérer les fonctionnalités d’accès conditionnel.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Lisez la propriété policies.conditionalAccess dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Mettez à jour la propriété policies.conditionalAccess dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Créez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Supprimez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Lisez la propriété policies.conditionalAccess dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Mettez à jour la propriété policies.conditionalAccess dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Lisez la propriété policies.conditionalAccess dans Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | Mettez à jour la propriété policies.conditionalAccess dans Azure Active Directory. |

### <a name="crm-service-administrator"></a>Administrateur de services CRM
Peut gérer tous les aspects du produit Dynamics 365.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gérez tous les aspects de Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="customer-lockbox-access-approver"></a>Approbateur d’accès à Customer LockBox
Peut approuver les demandes de support Microsoft pour accéder aux données organisationnelles du client.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.lockbox/allEntities/allTasks | Gérez tous les aspects d’Office 365 Customer Lockbox. |

### <a name="desktop-analytics-administrator"></a>Administrateur Desktop Analytics
Peut gérer Desktop Analytics et les services de stratégie et de personnalisation Office. Pour Desktop Analytics, cela inclut la possibilité d’afficher l’inventaire des biens, de créer des plans de déploiement, d’afficher l’état du déploiement et de l’intégrité. Pour le service de stratégie et personnalisation Office, ce rôle permet aux utilisateurs de gérer les stratégies Office.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gérez tous les aspects de Desktop Analytics. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="device-administrators"></a>Administrateurs d’appareils
Les utilisateurs dotés de ce rôle sont ajoutés au groupe d’administrateurs locaux sur des appareils joints à AD Azure.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Lisez des propriétés de base sur des paramètres de groupes dans Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Lisez des propriétés de base sur des modèles de paramètres de groupes dans Azure Active Directory. |

### <a name="directory-readers"></a>Lecteurs de répertoires
Peut lire les informations d’annuaire de base. Pour accorder l’accès aux applications, non destiné aux utilisateurs.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Lisez des propriétés de base sur des unités administratives dans Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Lisez la propriété administrativeUnits.members dans Azure Active Directory. |
| microsoft.aad.directory/applications/basic/read | Lisez des propriétés de base sur des applications dans Azure Active Directory. |
| microsoft.aad.directory/applications/owners/read | Lisez la propriété applications.owners dans Azure Active Directory. |
| microsoft.aad.directory/applications/policies/read | Lisez la propriété applications.policies dans Azure Active Directory. |
| microsoft.aad.directory/contacts/basic/read | Lisez des propriétés de base sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Lisez la propriété contacts.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/contracts/basic/read | Lisez des propriétés de base sur des contrats dans Azure Active Directory. |
| microsoft.aad.directory/devices/basic/read | Lisez des propriétés de base sur des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Lisez la propriété devices.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Lisez la propriété devices.registeredOwners dans Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Lisez la propriété devices.registeredUsers dans Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/basic/read | Lisez des propriétés de base sur des rôles d’annuaire dans Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Lisez la propriété directoryRoles.eligibleMembers dans Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Lisez la propriété directoryRoles.members dans Azure Active Directory. |
| microsoft.aad.directory/domains/basic/read | Lisez des propriétés de base sur des domaines dans Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Lisez la propriété groups.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/groups/basic/read | Lisez des propriétés de base sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Lisez la propriété groups.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/read | Lisez la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Lisez la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/groups/settings/read | Lisez la propriété groups.settings dans Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/read | Lisez des propriétés de base sur des paramètres de groupes dans Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Lisez des propriétés de base sur des modèles de paramètres de groupes dans Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Lisez des propriétés de base sur des octrois d’autorisation OAuth2 dans Azure Active Directory. |
| microsoft.aad.directory/organization/basic/read | Lisez des propriétés de base sur une organisation dans Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Lisez la propriété organization.trustedCAsForPasswordlessAuth dans Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/basic/read | Lisez des propriétés de base sur roleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/basic/read | Lisez des propriétés de base sur roleDefinitions dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Lisez la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Lisez la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Lisez des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Lisez la propriété servicePrincipals.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Lisez la propriété servicePrincipals.oAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Lisez la propriété servicePrincipals.ownedObjects dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Lisez la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Lisez la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/basic/read | Lisez des propriétés de base sur des références SKU souscrites dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Lisez la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Lisez des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Lisez la propriété users.directReports dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Lisez la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Lisez la propriété users.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Lisez la propriété users.oAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Lisez la propriété users.ownedDevices dans Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Lisez la propriété users.ownedObjects dans Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Lisez la propriété users.registeredDevices dans Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Comptes de synchronisation d’annuaires
Utilisés uniquement par le service Azure AD Connect.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Mettez à jour la propriété organization.dirSync dans Azure Active Directory. |
| microsoft.aad.directory/policies/create | Créez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Supprimez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/basic/read | Lisez des propriétés de base sur des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Mettez à jour des propriétés de base sur des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/owners/read | Lisez la propriété policies.owners dans Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Mettez à jour la propriété policies.owners dans Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Lisez la propriété policies.policiesAppliedTo dans Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Mettez à jour la propriété policies.tenantDefault dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Lisez la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Mettez à jour la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Lisez la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Mettez à jour la propriété servicePrincipals.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Mettez à jour la propriété servicePrincipals.audience dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Mettez à jour la propriété servicePrincipals.authentication dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Lisez des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Mettez à jour des propriétés de base sur des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Créez des principaux de service dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Mettez à jour la propriété servicePrincipals.credentials dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Lisez la propriété servicePrincipals.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Lisez la propriété servicePrincipals.oAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Lisez la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Mettez à jour la propriété servicePrincipals.owners dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Lisez la propriété servicePrincipals.ownedObjects dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Mettez à jour la propriété servicePrincipals.permissions dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Lisez la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Mettez à jour la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Exécutez toutes les actions dans Azure AD Connect. |

### <a name="directory-writers"></a>Enregistreurs de répertoire
Peut lire et écrire des informations d’annuaire de base. Pour accorder l’accès aux applications, non destiné aux utilisateurs.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/groups/create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Mettez à jour la propriété groups.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Mettez à jour des propriétés de base sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Mettez à jour la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Mettez à jour la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Mettez à jour la propriété groups.settings dans Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/update | Mettez à jour des propriétés de base sur des paramètres de groupe dans Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Créez des paramètres de groupe dans Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Supprimez des paramètres de groupe dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Mettez à jour la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Mettez à jour des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Mettez à jour la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Mettez à jour la propriété users.userPrincipalName dans Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Administrateur de services Exchange
Peut gérer tous les aspects du produit Exchange.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Mettez à jour la propriété groups.unified dans Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Mettez à jour les propriétés de base des groupes Office 365. |
| microsoft.aad.directory/groups/unified/create | Créez des groupes Office 365. |
| microsoft.aad.directory/groups/unified/delete | Supprimez des groupes Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Mettez à jour l’appartenance des groupes Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Mettez à jour la propriété des groupes Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.exchange/allEntities/allTasks | Gérez tous les aspects d’Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="external-identity-provider-administrator"></a>Administrateur de fournisseurs d’identité externes
Configurez des fournisseurs d’identité pour une utilisation dans une fédération directe.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Lisez et configurez les fournisseurs d’identité dans Azure Active Directory B2C. |

### <a name="guest-inviter"></a>Inviteur
Peut inviter des utilisateurs invités indépendamment du paramètre « Les membres peuvent inviter des invités ».

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Lisez la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Lisez des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Lisez la propriété users.directReports dans Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Invitez des utilisateurs invités dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Lisez la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Lisez la propriété users.memberOf dans Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Lisez la propriété users.oAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Lisez la propriété users.ownedDevices dans Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Lisez la propriété users.ownedObjects dans Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Lisez la propriété users.registeredDevices dans Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Administrateur du support technique
Peut réinitialiser des mots de passe pour les utilisateurs non-administrateurs et les administrateurs du support technique.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Lisez la propriété devices.bitLockerRecoveryKeys dans Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Mettez à jour des mots de passe pour tous les utilisateurs dans Azure Active Directory. Pour plus d’informations, consultez la documentation en ligne. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="intune-service-administrator"></a>Administrateur de services Intune
Peut gérer tous les aspects du produit Intune.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Mettez à jour des propriétés de base sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Créez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Supprimez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/devices/basic/update | Mettez à jour des propriétés de base sur des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Lisez la propriété devices.bitLockerRecoveryKeys dans Azure Active Directory. |
| microsoft.aad.directory/devices/create | Créez des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Supprimez des appareils dans Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Mettez à jour la propriété devices.registeredOwners dans Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Mettez à jour la propriété devices.registeredUsers dans Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Mettez à jour la propriété groups.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Mettez à jour des propriétés de base sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/groups/delete | Supprimez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Lisez la propriété groups.hiddenMembers dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Mettez à jour la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Mettez à jour la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Restaurez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Mettez à jour la propriété groups.settings dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Mettez à jour la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Mettez à jour des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Mettez à jour la propriété users.manager dans Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.intune/allEntities/allTasks | Gérez tous les aspects d’Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |

### <a name="kaizala-administrator"></a>Administrateur Kaizala
Peut gérer les paramètres de Microsoft Kaizala.  

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >  
  
| **Actions** | **Description** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez le Centre d’administration Office 365. |

### <a name="license-administrator"></a>Administrateur de licence
Peut gérer les licences de produit pour les utilisateurs et les groupes.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Mettez à jour la propriété users.usageLocation dans Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |

### <a name="lync-service-administrator"></a>Administrateur de services Lync
Peut gérer tous les aspects du produit Skype Entreprise.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gérez tous les aspects de Skype Entreprise Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="message-center-privacy-reader"></a>Lecteur de confidentialité du Centre de messages
Peut lire les billets du Centre de messages, les messages de confidentialité des données, les groupes, les domaines et les abonnements.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Lisez les messages dans microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Lisez les messages securityMessages dans microsoft.office365.messageCenter. |

### <a name="message-center-reader"></a>Lecteur du Centre de messages
Peut lire les messages et les mises à jour de son organisation dans le Centre de messages Office 365 uniquement. 

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Lisez les messages dans microsoft.office365.messageCenter. |

### <a name="partner-tier1-support"></a>Prise en charge de niveau 1 de partenaire
Ne pas utiliser - non destiné à une utilisation générale.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Mettez à jour des propriétés de base sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Créez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Supprimez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/groups/create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/groups/members/update | Mettez à jour la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Mettez à jour la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Mettez à jour la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Mettez à jour des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/delete | Supprimez des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Mettez à jour la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Mettez à jour des mots de passe pour tous les utilisateurs dans Azure Active Directory. Pour plus d’informations, consultez la documentation en ligne. |
| microsoft.aad.directory/users/restore | Restaurez des utilisateurs (Users) supprimés dans Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Mettez à jour la propriété users.userPrincipalName dans Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="partner-tier2-support"></a>Prise en charge de niveau 2 de partenaire
Ne pas utiliser - non destiné à une utilisation générale.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Mettez à jour des propriétés de base sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Créez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Supprimez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Créez et supprimez des domaines, et lisez et mettez à jour des propriétés standard dans Azure Active Directory. |
| microsoft.aad.directory/groups/create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/delete | Supprimez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Mettez à jour la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Restaurez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/organization/basic/update | Mettez à jour des propriétés de base sur une organisation dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Mettez à jour la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Mettez à jour des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/delete | Supprimez des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Mettez à jour la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Mettez à jour des mots de passe pour tous les utilisateurs dans Azure Active Directory. Pour plus d’informations, consultez la documentation en ligne. |
| microsoft.aad.directory/users/restore | Restaurez des utilisateurs (Users) supprimés dans Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Mettez à jour la propriété users.userPrincipalName dans Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="power-bi-service-administrator"></a>Administrateur du service Power BI
Peut gérer tous les aspects du produit Power BI.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gérez tous les aspects de Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="privileged-authentication-administrator"></a>Administrateur d’authentification privilégié
Autorisé à afficher, définir et réinitialiser des informations de méthode d’authentification pour tout utilisateur (administrateur ou non-administrateur).

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Mettez à jour les propriétés d’authentification forte comme les informations d’identification MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="privileged-role-administrator"></a>Administrateur de rôle privilégié
Peut gérer les attributions de rôles dans Azure AD et tous les aspects de Privileged Identity Management.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans microsoft.aad.privilegedIdentityManagement. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/allTasks | Lisez et configurez la propriété servicePrincipals.appRoleAssignedTo dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Lisez et configurez la propriété servicePrincipals.oAuth2PermissionGrants dans Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Créez et gérez des unités administratives (y compris les membres). |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Créez et gérez des attributions de rôle. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Créez et gérez des définitions de rôle. |

### <a name="reports-reader"></a>Lecteur de rapports
Peut lire les rapports d’audit et sur les connexions.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Lisez toutes les propriétés (y compris les propriétés privilégiées) sur auditLogs dans Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Lisez toutes les propriétés (y compris les propriétés privilégiées) sur signInReports dans Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.usageReports/allEntities/read | Lisez des rapports d’utilisation Office 365. |

### <a name="search-administrator"></a>Administrateur de recherche
Peut créer et gérer tous les aspects des paramètres de Recherche Microsoft.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Lisez les messages dans microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour toutes les propriétés dans microsoft.office365.search. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |
| microsoft.office365.usageReports/allEntities/read | Lisez des rapports d’utilisation Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |

### <a name="search-editor"></a>Éditeur de recherche
Peut créer et gérer le contenu éditorial comme les signets, les questions et réponses, les emplacements et les plans d’étage.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Lisez les messages dans microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Créez et supprimez du contenu, et lisez et mettez à jour toutes les propriétés dans microsoft.office365.search. |
| microsoft.office365.usageReports/allEntities/read | Lisez des rapports d’utilisation Office 365. |

### <a name="security-administrator"></a>Security Administrator
Peut lire des rapports et des informations de sécurité, ainsi que gérer la configuration dans Azure AD et Office 365.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Mettez à jour la propriété applications.policies dans Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Lisez toutes les propriétés (y compris les propriétés privilégiées) sur auditLogs dans Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Lisez la propriété devices.bitLockerRecoveryKeys dans Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Mettez à jour des propriétés de base sur des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/create | Créez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Supprimez des stratégies dans Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Mettez à jour la propriété policies.owners dans Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Mettez à jour la propriété policies.tenantDefault dans Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Mettez à jour la propriété servicePrincipals.policies dans Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Lisez toutes les propriétés (y compris les propriétés privilégiées) sur signInReports dans Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Lisez toutes les ressources dans microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Mettez à jour toutes les ressources dans microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Lisez toutes les ressources dans microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Lisez tous les aspects du Centre de protection Office 365. |
| microsoft.office365.protectionCenter/allEntities/update | Mettez à jour toutes les ressources dans microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |

### <a name="security-operator"></a>Opérateur de sécurité
Crée et gère les événements de sécurité.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Lisez et configurez Microsoft Cloud App Security. |
| microsoft.aad.identityProtection/allEntities/read | Lisez toutes les ressources dans microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Lisez toutes les ressources dans microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Lisez et configurez Azure AD Advanced Threat Protection. |
| microsoft.intune/allEntities/allTasks | Gérez tous les aspects d’Intune. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Lisez et configurez le Centre de sécurité et de conformité. |
| microsoft.office365.usageReports/allEntities/read | Lisez des rapports d’utilisation Office 365. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Lisez et configurez Windows Defender Advanced Threat Protection. |

### <a name="security-reader"></a>Lecteur de sécurité
Peut lire des rapports et des informations de sécurité dans Azure AD et Office 365.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Lisez toutes les propriétés (y compris les propriétés privilégiées) sur auditLogs dans Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Lisez la propriété devices.bitLockerRecoveryKeys dans Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Lisez toutes les propriétés (y compris les propriétés privilégiées) sur signInReports dans Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Lisez toutes les ressources dans microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Lisez toutes les ressources dans microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Lisez tous les aspects du Centre de protection Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |

### <a name="service-support-administrator"></a>Administrateur de support de service
Peut lire des informations sur l’intégrité du service et gérer les tickets de support.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="sharepoint-service-administrator"></a>Administrateur de services SharePoint
Peut gérer tous les aspects du service SharePoint.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Mettez à jour la propriété groups.unified dans Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Mettez à jour les propriétés de base des groupes Office 365. |
| microsoft.aad.directory/groups/unified/create | Créez des groupes Office 365. |
| microsoft.aad.directory/groups/unified/delete | Supprimez des groupes Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Mettez à jour l’appartenance des groupes Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Mettez à jour la propriété des groupes Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Créez et supprimez toutes les ressources, et lisez et mettez à jour des propriétés standard dans microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

### <a name="teams-communications-administrator"></a>Administrateur des communications Teams
Peut gérer les fonctionnalités d’appel et de réunion au sein du service Microsoft Teams.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |
| microsoft.office365.usageReports/allEntities/read | Lisez des rapports d’utilisation Office 365. |

### <a name="teams-communications-support-engineer"></a>Ingénieur de support des communications Teams
Peut résoudre les problèmes de communication au sein de Teams à l’aide d’outils avancés.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |

### <a name="teams-communications-support-specialist"></a>Spécialiste du support des communications Teams
Peut résoudre les problèmes de communication au sein de Teams à l’aide d’outils de base.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |

### <a name="teams-service-administrator"></a>Administrateur du service Teams
Peut gérer le service Microsoft Teams.

  > [!NOTE]
  > Ce rôle dispose d’autorisations supplémentaires en dehors d’Azure Active Directory. Pour plus d’informations, consultez la description des rôles ci-dessus.
  >
  >

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Lisez la propriété groups.hiddenMembers dans Azure Active Directory. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Mettez à jour la propriété groups.unified dans Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Mettez à jour les propriétés de base des groupes Office 365. |
| microsoft.aad.directory/groups/unified/create | Créez des groupes Office 365. |
| microsoft.aad.directory/groups/unified/delete | Supprimez des groupes Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Mettez à jour l’appartenance des groupes Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Mettez à jour la propriété des groupes Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |
| microsoft.office365.usageReports/allEntities/read | Lisez des rapports d’utilisation Office 365. |

### <a name="user-administrator"></a>Administrateur d'utilisateurs
Peut gérer tous les aspects des utilisateurs et groupes, notamment la réinitialisation des mots de passe pour les administrateurs limités.

| **Actions** | **Description** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Créez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Supprimez des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Mettez à jour des attributions de rôles d’applications dans Azure Active Directory. |
| microsoft.aad.directory/contacts/basic/update | Mettez à jour des propriétés de base sur des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Créez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Supprimez des contacts dans Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Mettez à jour la propriété groups.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Mettez à jour des propriétés de base sur des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/create | Créez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Créez des groupes dans Azure Active Directory. Le créateur est ajouté comme premier propriétaire, et l’objet créé compte dans le quota de 250 objets créés du créateur. |
| microsoft.aad.directory/groups/delete | Supprimez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Lisez la propriété groups.hiddenMembers dans Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Mettez à jour la propriété groups.members dans Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Mettez à jour la propriété groups.owners dans Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Restaurez des groupes dans Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Mettez à jour la propriété groups.settings dans Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Mettez à jour la propriété users.appRoleAssignments dans Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gérez les licences sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Mettez à jour des propriétés de base sur des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/create | Créez des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/delete | Supprimez des utilisateurs dans Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidez tous les jetons d’actualisation utilisateur dans Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Mettez à jour la propriété users.manager dans Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Mettez à jour des mots de passe pour tous les utilisateurs dans Azure Active Directory. Pour plus d’informations, consultez la documentation en ligne. |
| microsoft.aad.directory/users/restore | Restaurez des utilisateurs (Users) supprimés dans Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Mettez à jour la propriété users.userPrincipalName dans Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Lisez et configurez Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Créez et gérez les tickets de support Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Lisez les propriétés de base sur toutes les ressources dans microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Lisez et configurez Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Créez et gérez des tickets de support Office 365. |

## <a name="role-template-ids"></a>ID des modèles de rôle

Les ID des modèles de rôle sont principalement utilisés par les utilisateurs de l’API Graph ou de PowerShell.

DisplayName Graph | Nom d’affichage du portail Azure | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Administrateur d’application | Administrateur d’application | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Développeur d’applications | Développeur d’applications | CF1C38E5-3621-4004-A7CB-879624DCED7C
Administrateur d’authentification | Administrateur d’authentification | c4e39bd9-1100-46d3-8c65-fb160da0071f
Administrateur Azure Information Protection | Administrateur Azure Information Protection | 7495fdc4-34c4-4d15-a289-98788ce399fd
Administrateur de flux d’utilisateurs B2C | Administrateur de flux d’utilisateurs B2C | 6e591065-9bad-43ed-90f3-e9424366d2f0
Administrateur d’attributs de flux d’utilisateurs B2C | Administrateur d’attributs de flux d’utilisateurs B2C | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
Administrateur de jeux de clés B2C IEF | Administrateur de jeux de clés B2C IEF | aaf43236-0c0d-4d5f-883a-6955382ac081
Administrateur de stratégies B2C IEF | Administrateur de stratégies B2C IEF | 3edaf663-341e-4475-9f94-5c398ef6c070
Administrateur de facturation | Administrateur de facturation | b0f54661-2d74-4c50-afa3-1ec803f12efe
Administrateur d'applications cloud | Administrateur d’application cloud | 158c047a-c907-4556-b7ef-446551a6b5f7
Administrateur d’appareil cloud | Administrateur d’appareil cloud | 7698a772-787b-4ac8-901f-60d6b08affd2
Administrateur d’entreprise | Administrateur général | 62e90394-69f5-4237-9190-012177145e10
Administrateur de conformité | Administrateur de conformité | 17315797-102d-40b4-93e0-432062caca18
Administrateur des données de conformité | Administrateur des données de conformité | e6d1a23a-da11-4be4-9570-befc86d067a7
Administrateur de l’accès conditionnel | Administrateur de l’accès conditionnel | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Administrateur de services CRM | Administrateur Dynamics 365 | 44367163-eba1-44c3-98af-f5787879f96a
Approbateur d’accès à Customer LockBox | Approbateur d’accès à Customer LockBox | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Administrateur Desktop Analytics | Administrateur Desktop Analytics | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Administrateurs d’appareils | Administrateurs d’appareils | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Jonction d’appareils | Jonction d’appareils | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Gestionnaires d’appareils | Gestionnaires d’appareils | 2b499bcd-da44-4968-8aec-78e1674fa64d
Utilisateurs d’appareils | Utilisateurs d’appareils | d405c6df-0af8-4e3b-95e4-4d06e542189e
Lecteurs de répertoires | Lecteurs d’annuaires | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Comptes de synchronisation d’annuaires | Comptes de synchronisation d’annuaires | d29b2b05-8046-44ba-8758-1e26182fcf32
Enregistreurs de répertoire | Enregistreurs de répertoire | 9360feb5-f418-4baa-8175-e2a00bac4301
Administrateur de services Exchange | Administrateur Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Administrateur de fournisseurs d’identité externes | Administrateur de fournisseurs d’identité externes | be2f45a1-457d-42af-a067-6ec1fa63bc45
Inviteur | Inviteur d’invités | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Administrateur du support technique | Administrateur de mots de passe | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Administrateur de services Intune | Administrateur Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Administrateur Kaizala | Administrateur Kaizala | 74ef975b-6605-40af-a5d2-b9539d836353
Administrateur de licence | Administrateur de licence | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Administrateur de services Lync | Administrateur Skype Entreprise | 75941009-915a-4869-abe7-691bff18279e
Lecteur de confidentialité du Centre de messages | Lecteur de confidentialité du Centre de messages | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Lecteur du Centre de messages | Lecteur du Centre de messages | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Prise en charge de niveau 1 de partenaire | Prise en charge de niveau 1 de partenaire | 4ba39ca4-527c-499a-b93d-d9b492c50246
Prise en charge de niveau 2 de partenaire | Prise en charge de niveau 2 de partenaire | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Administrateur du service Power BI | Administrateur Power BI | a9ea8996-122f-4c74-9520-8edcd192826c
Administrateur d’authentification privilégié | Administrateur d’authentification privilégié | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Administrateur de rôle privilégié | Administrateur de rôle privilégié | e8611ab8-c189-46e8-94e1-60213ab1f814
Lecteur de rapports | Lecteur de rapports | 4a5d8f65-41da-4de4-8968-e035b65339cf
Administrateur de recherche | Administrateur de recherche | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Éditeur de recherche | Éditeur de recherche | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Security Administrator | Administrateur de sécurité | 194ae4cb-b126-40b2-bd5b-6091b380977d
Opérateur de sécurité | Opérateur de sécurité | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Lecteur de sécurité | Lecteur de sécurité | 5d6b6bb7-de71-4623-b4af-96380a352509
Administrateur de support de service | Administrateur de services fédérés | f023fd81-a637-4b56-95fd-791ac0226033
Administrateur de services SharePoint | Administrateur SharePoint | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Administrateur des communications Teams | Administrateur des communications Teams | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Ingénieur de support des communications Teams | Ingénieur de support des communications Teams | f70938a0-fc10-4177-9e90-2178f8765737
Spécialiste du support des communications Teams | Spécialiste du support des communications Teams | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Administrateur du service Teams | Administrateur du service Teams | 69091246-20e8-4a56-aa4d-066075b2a7a8
Utilisateur | Utilisateur | a0b1b346-4d3e-4e8b-98f8-753987be4970
Administrateur de compte utilisateur | Administrateur d’utilisateurs | fe930be7-5e62-47db-91af-98c3a49a38b1
Jonction d’appareils d’espace de travail | Jonction d’appareils d’espace de travail | c34f683f-4d5a-4403-affd-6615e00e3a7f


## <a name="deprecated-roles"></a>Rôles déconseillés

Les rôles suivants ne doivent pas être utilisés. Ils sont déconseillés et seront prochainement supprimés d’Azure AD.

* Administrateur de licences ad hoc
* Jonction d’appareils
* Gestionnaires d’appareils
* Utilisateurs d’appareils
* Créateur d’utilisateur vérifié par e-mail
* Administrateur de boîte aux lettres
* Jonction d’appareils d’espace de travail

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur l’affectation d’un utilisateur en tant qu’administrateur d’un abonnement Azure, consultez l’article [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../../role-based-access-control/role-assignments-portal.md)
* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Pour plus d’informations sur la façon dont le service Azure Active Directory est lié à votre abonnement Azure, consultez [Association des abonnements Azure avec Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
