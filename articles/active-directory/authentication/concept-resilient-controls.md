---
title: Créer une stratégie de gestion du contrôle d’accès résiliente - Azure Active Directory
description: Ce document fournit des conseils sur les stratégies à adopter par une organisation pour faire preuve de résilience et réduire le risque de verrouillage en cas d'interruption de service imprévue.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 12/19/2018
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 831ba47ea4e999219a6d8cf34cb5fb0fdcd1ead8
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594956"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Créer une stratégie de gestion du contrôle d'accès résiliente avec Azure Active Directory

>[!NOTE]
> Les informations contenues dans le présent document reflètent l'opinion de Microsoft Corporation sur les sujets abordés à la date de publication. Microsoft se doit de s'adapter aux conditions fluctuantes du marché, et cette opinion ne peut être considérée comme un engagement de sa part. Microsoft ne peut garantir la véracité de toute information présentée après la date de publication.

Les organisations qui s'appuient sur un seul type de contrôle d'accès, comme l'authentification multifacteur (MFA) ou un emplacement réseau unique, pour sécuriser leurs systèmes informatiques sont susceptibles de rencontrer des problèmes d'accès à leurs applications et ressources si ce type de contrôle d'accès est indisponible ou mal configuré. Par exemple, une catastrophe naturelle peut entraîner l'indisponibilité de pans entiers de l'infrastructure de télécommunications ou des réseaux d'entreprise. Une telle interruption peut empêcher les utilisateurs finaux et les administrateurs de se connecter.

Ce document fournit des conseils sur les stratégies à adopter par une organisation pour faire preuve de résilience et réduire le risque de verrouillage en cas d'interruption de service imprévue, selon les scénarios suivants :

 1. Les organisations peuvent accroître leur résilience afin de réduire le risque de verrouillage **avant une interruption** en implémentant des stratégies d'atténuation ou des plans d'urgence.
 2. Les organisations peuvent continuer à accéder aux applications et ressources de leur choix **lors d'une interruption** grâce à la mise en place de stratégies d'atténuation ou de plans d'urgence.
 3. Les organisations doivent veiller à préserver les informations, comme les journaux d’activité, **après une interruption** et avant d’appliquer les plans d’urgence qu’elles ont implémentés.
 4. Les organisations qui n'ont implémenté aucune stratégie de prévention ou aucun plan de substitution peuvent mettre en œuvre des **options d'urgence** pour faire face à l'interruption.

## <a name="key-guidance"></a>Orientations clés

Les quatre points à retenir dans ce document sont les suivants :

* Éviter le verrouillage de l'administrateur en utilisant des comptes d'accès d'urgence
* Implémenter une authentification multifacteur basée sur l’accès conditionnel plutôt qu’une authentification multifacteur par utilisateur.
* Atténuer le verrouillage des utilisateurs en utilisant plusieurs types de contrôle d’accès conditionnel.
* Atténuer le verrouillage des utilisateurs en fournissant plusieurs méthodes d'authentification ou des méthodes équivalentes pour chaque utilisateur

## <a name="before-a-disruption"></a>Avant une interruption

L'atténuation des interruptions doit être l'une des priorités de l'organisation pour faire face aux problèmes de contrôle d'accès potentiels. L'atténuation comprend la planification d'un événement réel et l'implémentation de stratégies pour veiller à ce que les contrôles d'accès et les opérations ne soient pas affectés en cas d'interruption.

### <a name="why-do-you-need-resilient-access-control"></a>Pourquoi avez-vous besoin d'un contrôle d'accès résilient ?

 Pour les utilisateurs, l'accès aux applications et ressources repose sur l'identité. Votre système d'identité détermine quels utilisateurs sont autorisés à accéder aux applications et dans quelles conditions (exigences d'authentification ou de contrôle d'accès, notamment). Lorsqu'une ou plusieurs exigences d'authentification ou de contrôle d'accès sont indisponibles pour authentifier les utilisateurs en raison de circonstances imprévues, les organisations peuvent rencontrer l'un des problèmes suivants (ou les deux) :

* **Verrouillage des administrateurs :** les administrateurs ne sont pas en mesure de gérer le locataire ou les services.
* **Verrouillage des utilisateurs :** les utilisateurs n'ont pas accès aux applications ou ressources.

### <a name="administrator-lockout-contingency"></a>Plan d'urgence en cas de verrouillage des administrateurs

Pour déverrouiller l'accès administrateur à votre locataire, vous devez créer des comptes d'accès d'urgence. Ces comptes d'accès d'urgence (ou *break glass*) permettent de gérer la configuration d'Azure AD lorsque les procédures normales d'accès aux comptes à privilèges ne sont pas disponibles. Au moins deux comptes d'accès d'urgence doivent être créés, conformément aux [recommandations relatives aux comptes d'accès d'urgence]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

### <a name="mitigating-user-lockout"></a>Atténuer le risque de verrouillage des utilisateurs

 Pour atténuer le risque de verrouillage des utilisateurs, utilisez des stratégies d’accès conditionnel avec plusieurs contrôles pour permettre aux utilisateurs de choisir leur mode d’accès aux applications et ressources. Par exemple, en permettant à l'utilisateur de choisir de se connecter à l'aide de l'authentification multifacteur, **à** partir d'un appareil géré **ou** à partir du réseau d'entreprise, si l'un des contrôles d'accès est indisponible, l'utilisateur disposera d'autres options pour continuer à travailler.

#### <a name="microsoft-recommendations"></a>Recommandations de Microsoft

Intégrez les contrôles d’accès suivants aux stratégies d’accès conditionnel existantes de votre organisation :

1. Fournissez à chaque utilisateur plusieurs méthodes d'authentification basées sur différents canaux de communication, par exemple l'application Microsoft Authenticator (basée sur Internet), le jeton OATH (généré sur l'appareil) et les SMS (téléphoniques).
2. Déployez Windows Hello Entreprise sur les appareils Windows 10 pour répondre aux exigences d'authentification multifacteur dès la connexion de l'appareil.
3. Utilisez des appareils approuvés via la [jonction Azure AD hybride](https://docs.microsoft.com/azure/active-directory/devices/overview) ou des [appareils gérés par Microsoft Intune](https://docs.microsoft.com/intune/planning-guide). Un appareil approuvé améliorera l'expérience utilisateur, car l'appareil proprement dit répondra aux exigences d'authentification forte de la stratégie sans que l'utilisateur ne soit confronté au défi de l'authentification multifacteur. L'authentification multifacteur sera alors requise lors de l'inscription d'un nouvel appareil et lors de l'accès à des applications ou ressources à partir d'appareils non approuvés.
4. Utilisez des stratégies de protection de l'identité Azure AD basées sur les risques qui bloquent l'accès lorsque l'utilisateur ou la connexion risquent de se substituer aux stratégies d'authentification multifacteur définies.

>[!NOTE]
> Pour implémenter des stratégies basées sur les risques, des licences [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) sont requises.

L'exemple suivant décrit les stratégies que vous devez créer pour fournir un contrôle d'accès résilient permettant à l'utilisateur d'accéder à ses applications et ressources. Dans cet exemple, vous aurez besoin d'un groupe de sécurité **AppUsers** pour les utilisateurs cibles auxquels vous souhaitez octroyer l'accès, d'un groupe **CoreAdmins** pour les administrateurs principaux et d'un groupe **EmergencyAccess** pour les comptes d'accès d'urgence.
Cet exemple de jeu de stratégies permettra aux utilisateurs sélectionnés du groupe **AppUsers** d'accéder aux applications sélectionnées s'ils se connectent à partir d'un appareil approuvé OU fournira une authentification forte, telle que l'authentification multifacteur. Il exclut les comptes d'urgence et les administrateurs principaux.

**Jeu de stratégies d'atténuation de l'accès conditionnel :**

* Stratégie 1 : bloquer l'accès aux personnes extérieures aux groupes cibles
  * Utilisateurs et groupes : inclure tous les utilisateurs. exclure AppUsers, CoreAdmins et EmergencyAccess
  * Applications cloud : inclure toutes les applications
  * Conditions : (aucune)
  * Contrôle d'octroi : Block
* Stratégie 2 : octroyer l'accès aux utilisateurs qui ont besoin d'une authentification multifacteur OU d'un appareil approuvé.
  * Utilisateurs et groupes : inclure AppUsers. Exclure CoreAdmins et EmergencyAccess
  * Applications cloud : inclure toutes les applications
  * Conditions : (aucune)
  * Contrôle d'octroi : octroyer l'accès, exiger une authentification multifacteur, exiger que l'appareil soit conforme. Pour plusieurs contrôles : exiger un des contrôles sélectionnés.

### <a name="contingencies-for-user-lockout"></a>Plans d'urgence en cas de verrouillage des utilisateurs

Votre organisation peut également créer des stratégies d'urgence. Pour créer des stratégies d'urgence, vous devez définir des critères de compromis entre la continuité des opérations, les coûts opérationnels, les coûts financiers et les risques liés à la sécurité. Par exemple, vous pouvez activer une stratégie d'urgence pour un sous-ensemble d'utilisateurs, d'applications ou de clients, ou à partir d'un sous-ensemble d'emplacements. En cas d'interruption, les stratégies d'urgence permettront aux administrateurs et aux utilisateurs finaux d'accéder aux applications et ressources si aucune méthode d'atténuation n'a été implémentée.
Le fait de savoir à quoi vous vous exposez en cas d'interruption vous aidera à réduire les risques et constituera un élément essentiel de votre processus de planification. Pour créer votre plan d'urgence, commencez par déterminer les besoins de votre organisation :

1. Identifiez à l'avance vos applications stratégiques : Quelles sont les applications qui doivent impérativement être accessibles, même avec un risque accru ou un niveau de sécurité plus faible ? Dressez la liste de ces applications et assurez-vous que les autres parties prenantes (commerciaux, sécurité, service juridique, dirigeants) s'accordent toutes à dire que si tous les contrôles d'accès disparaissent, ces applications doivent continuer à fonctionner. Les applications se décomposeront probablement en trois catégories :
   * **Applications stratégiques de catégorie 1** qui ne peuvent pas être indisponibles pendant plus de quelques minutes, par exemple les applications dont dépendent directement les revenus de l'organisation.
   * **Applications importantes de catégorie 2** qui doivent être accessibles en quelques heures.
   * **Applications à faible priorité de catégorie 3** qui peuvent supporter une interruption de quelques jours.
2. Pour les applications des catégories 1 et 2, Microsoft vous recommande de planifier à l'avance le type de niveau d'accès que vous souhaitez autoriser :
   * Voulez-vous autoriser un accès total ou restreint, en limitant les téléchargements par exemple ?
   * Voulez-vous autoriser l'accès à une partie de l'application, mais pas à l'ensemble de celle-ci ?
   * Voulez-vous autoriser l'accès aux professionnels de l'information et bloquer l'accès aux administrateurs jusqu'à la restauration du contrôle d'accès ?
3. Pour ces applications, Microsoft vous recommande également de planifier les voies d'accès que vous souhaitez ouvrir et fermer :
   * Voulez-vous autoriser l'accès par navigateur uniquement et bloquer les clients capables d'enregistrer des données hors connexion ?
   * Voulez-vous autoriser l'accès aux utilisateurs du réseau d'entreprise uniquement et bloquer les utilisateurs extérieurs ?
   * Voulez-vous autoriser l'accès à partir de certains pays ou régions uniquement pendant l'interruption ?
   * Voulez-vous que les stratégies associées aux stratégies d'urgence, en particulier pour les applications stratégiques, échouent ou aboutissent si aucun autre contrôle d'accès n'est disponible ?

#### <a name="microsoft-recommendations"></a>Recommandations de Microsoft

Une stratégie d’accès conditionnel d’urgence est une **stratégie désactivée** qui ignore toute authentification multifacteur Azure ou tierce ainsi que les contrôles basés sur les risques ou sur l’appareil. Ensuite, lorsque votre organisation décide d'activer son plan d'urgence, les administrateurs peuvent activer la stratégie et désactiver les stratégies standard basées sur les contrôles.

>[!IMPORTANT]
> La désactivation des stratégies de protection de vos utilisateurs, même temporairement, réduira votre niveau de sécurité tant que le plan d'urgence sera en place.

* Configurez un jeu de stratégies de secours si une interruption au niveau d'un type d'informations d'identification ou d'un mécanisme de contrôle d'accès affecte l'accès à vos applications. Configurez une stratégie désactivée exigeant la jonction de domaine pour le contrôle, comme une sauvegarde pour une stratégie active exigeant un fournisseur d'authentification multifacteur tiers.
* Protégez vos mots de passe des personnes malintentionnées, lorsque l'authentification multifacteur n'est pas exigée, en suivant les pratiques décrites dans le livre blanc [Conseils sur les mots de passe](https://aka.ms/passwordguidance).
* Déployez la [Réinitialisation du mot de passe libre-service (SSPR) Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) et la [Protection par mot de passe Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) pour veiller à ce que les utilisateurs n'utilisent pas les mêmes mots de passe ni les termes que vous choisissez d'interdire.
* Si un certain niveau d'authentification n'est pas atteint, utilisez des stratégies limitant l'accès au sein des applications plutôt que de revenir à un accès total. Par exemple :
  * Configurez une stratégie de sauvegarde qui envoie la demande de session restreinte à Exchange et SharePoint.
  * Si votre organisation utilise Microsoft Cloud App Security, n'hésitez pas à avoir recours à une stratégie qui tire parti de MCAS. MCAS autorisera un accès en lecture seule, mais pas les chargements.
* Nommez vos stratégies pour être certain qu’il soit facile de les trouver pendant une interruption. Incluez les éléments suivants dans le nom de la stratégie :
  * Un *numéro de libellé* pour la stratégie.
  * Un texte à afficher, cette stratégie n’étant prévue qu’en cas d’urgence. Par exemple :  **ACTIVER EN CAS D’URGENCE**
  * L’*interruption* à laquelle elle s’applique. Par exemple :  **Pendant une interruption MFA**
  * Un *numéro de séquence* pour montrer l’ordre dans lequel les stratégies doivent être activées.
  * Les *applications* auxquelles elle s’applique.
  * Les *contrôles* auxquels elle s’appliquera.
  * Les *conditions* qui sont exigées.
  
Ce standard de nommage pour les stratégies d’urgence doit se présenter de cette façon : 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

L’exemple suivant permet : **Exemple A - Stratégie d'accès conditionnel d'urgence pour restaurer l'accès aux applications de collaboration stratégiques** constitue une urgence d'entreprise typique. Dans ce scénario, l'organisation exige généralement l'authentification multifacteur pour tous les accès Exchange Online et SharePoint Online, et dans ce cas, l'interruption est due à une panne du fournisseur d'authentification multifacteur du client (il peut s'agir de l'authentification multifacteur Azure, d'un fournisseur d'authentification multifacteur local ou d'une authentification multifacteur tierce). Cette stratégie atténue cette panne en permettant aux utilisateurs ciblés d'accéder à ces applications à partir d'appareils Windows approuvés et de leur réseau d'entreprise approuvé. Elle exclut également les comptes d'urgence et les administrateurs principaux de ces restrictions. Les utilisateurs ciblés accéderont alors à Exchange Online et SharePoint Online, tandis que les autres utilisateurs n’auront toujours pas accès aux applications en raison de la panne. Cet exemple nécessite un emplacement réseau **CorpNetwork** et un groupe de sécurité **ContingencyAccess** pour les utilisateurs cibles, un groupe **CoreAdmins** pour les administrateurs principaux et un groupe **EmergencyAccess** pour les comptes d'accès d'urgence. Le plan d'urgence requiert quatre stratégies pour fournir l'accès souhaité. 

**Exemple A - Stratégies d'accès conditionnel d'urgence pour restaurer l'accès aux applications de collaboration stratégiques :**

* Stratégie 1 : exiger des appareils joints au domaine pour Exchange et SharePoint
  * Nom : EM001 - ACTIVER EN CAS D’URGENCE : Interruption MFA [1/4] - Exchange SharePoint - Exiger la jonction Azure AD Hybride
  * Utilisateurs et groupes : inclure ContingencyAccess. Exclure CoreAdmins et EmergencyAccess
  * Applications cloud : Exchange Online et SharePoint Online
  * Conditions : Quelconque
  * Contrôle d'octroi : exiger un appareil joint au domaine
  * État : Désactivé
* Stratégie 2 : bloquer les plateformes autres que Windows
  * Nom : EM002 - ACTIVER EN CAS D’URGENCE : Interruption MFA [2/4] - Exchange SharePoint - Bloquer l’accès à l’exception de Windows
  * Utilisateurs et groupes : inclure tous les utilisateurs. Exclure CoreAdmins et EmergencyAccess
  * Applications cloud : Exchange Online et SharePoint Online
  * Conditions : plateforme d'appareils - Inclure toutes les plateformes, exclure Windows
  * Contrôle d'octroi : Block
  * État : Désactivé
* Stratégie 3 : bloquer les réseaux autres que CorpNetwork
  * Nom : EM003 - ACTIVER EN CAS D’URGENCE : Interruption MFA [3/4] - Exchange SharePoint - Bloquer l’accès à l’exception du réseau d’entreprise
  * Utilisateurs et groupes : inclure tous les utilisateurs. Exclure CoreAdmins et EmergencyAccess
  * Applications cloud : Exchange Online et SharePoint Online
  * Conditions : emplacements - Inclure n'importe quel emplacement, exclure CorpNetwork
  * Contrôle d'octroi : Block
  * État : Désactivé
* Stratégie 4 : bloquer explicitement EAS
  * Nom : EM004 - ACTIVER EN CAS D’URGENCE : Interruption MFA [4/4] - Exchange - Bloquer EAS pour tous les utilisateurs
  * Utilisateurs et groupes : inclure tous les utilisateurs
  * Applications cloud : Inclure Exchange Online
  * Conditions : Applications clientes : Exchange Active Sync
  * Contrôle d'octroi : Block
  * État : Désactivé

Ordre d'activation :

1. exclure ContingencyAccess, CoreAdmins et EmergencyAccess de la stratégie d'authentification multifacteur existante. Vérifier qu'un utilisateur de ContingencyAccess a accès à SharePoint Online et Exchange Online.
2. Activer la stratégie 1 : vérifier que les utilisateurs des appareils joints au domaine qui ne font pas partie des groupes d'exclusion ont accès à Exchange Online et SharePoint Online. Vérifier que les utilisateurs du groupe d'exclusion ont accès à SharePoint Online et Exchange à partir de n'importe quel appareil.
3. Activer la stratégie 2 : vérifier que les utilisateurs qui ne font pas partie du groupe d'exclusion n'ont pas accès à SharePoint Online et Exchange Online à partir de leurs appareils mobiles. Vérifier que les utilisateurs du groupe d'exclusion ont accès à SharePoint et Exchange à partir de n'importe quel appareil (Windows/iOS/Android).
4. Activer la stratégie 3 : vérifier que les utilisateurs qui ne font pas partie des groupes d'exclusion n'ont pas accès à SharePoint et Exchange en dehors du réseau d'entreprise, même avec un appareil joint à un domaine. Vérifier que les utilisateurs du groupe d'exclusion ont accès à SharePoint et Exchange à partir de n'importe quel réseau.
5. Activer la stratégie 4 : vérifier qu'aucun utilisateur n'a accès à Exchange Online à partir des applications de messagerie natives sur les appareils mobiles.
6. Désactiver la stratégie d'authentification multifacteur existante pour SharePoint Online et Exchange Online.

Dans l'exemple suivant, **Exemple B - Stratégies d'accès conditionnel d'urgence pour autoriser l'accès mobile à Salesforce**, l'accès d'une application métier est restauré. Dans ce scénario, le client a généralement besoin que ses commerciaux accèdent à Salesforce (configuré pour l'authentification unique auprès d'Azure AD) à partir d'appareils mobiles conformes uniquement. Dans ce cas, l'interruption est liée à un problème d'évaluation de la conformité des appareils et au fait que la panne survient à un moment délicat où l'équipe commerciale doit avoir accès à Salesforce pour conclure des contrats. Ces stratégies d'urgence accorderont aux utilisateurs critiques l'accès à Salesforce à partir d'un appareil mobile afin qu'ils puissent continuer à conclure des contrats et ne pas perturber l'activité. Dans cet exemple, **SalesforceContingency** contient tous les commerciaux qui doivent conserver leur accès tandis que **SalesAdmins** contient les administrateurs nécessaires à Salesforce.

**Exemple B - Stratégies d'accès conditionnel d'urgence :**

* Stratégie 1 : bloquer tous les utilisateurs extérieurs à l'équipe SalesContingency
  * Nom : EM001 - ACTIVER EN CAS D’URGENCE : Interruption pour conformité d’appareil [1/2] - Salesforce - Bloquer tous les utilisateurs à l’exception de SalesforceContingency
  * Utilisateurs et groupes : inclure tous les utilisateurs. Exclure SalesAdmins et SalesforceContingency
  * Applications cloud : Salesforce.
  * Conditions : Aucun
  * Contrôle d'octroi : Block
  * État : Désactivé
* Stratégie 2 : bloquer l'équipe commerciale sur toutes les plateformes autres que la plateforme mobile (pour réduire la surface d'attaque)
  * Nom : EM002 - ACTIVER EN CAS D’URGENCE : Interruption pour conformité d’appareil [2/2] - Salesforce - Bloquer toutes les plateformes à l’exception de iOS et Android
  * Utilisateurs et groupes : inclure SalesforceContingency. Exclure SalesAdmins
  * Applications cloud : Salesforce
  * Conditions : plateforme d'appareils - Inclure toutes les plateformes, exclure iOS et Android
  * Contrôle d'octroi : Block
  * État : Désactivé

Ordre d'activation :

1. exclure SalesAdmins et SalesforceContingency de la stratégie de conformité des appareils existante pour Salesforce. Vérifier qu'un utilisateur du groupe SalesforceContingency a accès à Salesforce.
2. Activer la stratégie 1 : vérifier que les utilisateurs extérieurs à SalesContingency n'ont pas accès à Salesforce. Vérifier que les utilisateurs des groupes SalesAdmins et SalesforceContingency ont accès à Salesforce.
3. Activer la stratégie 2 : vérifier que les utilisateurs du groupe SalesContingency n’ont pas accès à Salesforce à partir de leurs ordinateurs portables Windows/Mac, mais qu’ils y ont toujours accès à partir de leurs appareils mobiles. Vérifier que SalesAdmin a toujours accès à Salesforce à partir de n'importe quel appareil.
4. Désactiver la stratégie de conformité des appareils existante pour Salesforce.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Déployer la synchronisation du hachage de mot de passe même si vous êtes fédéré ou utilisez l'authentification directe

Le verrouillage des utilisateurs peut également se produire si les conditions suivantes sont réunies :

- Votre organisation utilise une solution d'identité hybride avec authentification directe ou fédération.
- Vos systèmes d'identité locaux (tels qu'Active Directory, AD FS ou un composant dépendant) ne sont pas disponibles. 
 
Pour être plus résiliente, votre organisation doit [activer la synchronisation du hachage de mot de passe](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), car celle-ci permet d'[utiliser la synchronisation du hachage de mot de passe](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) si les systèmes d'identité locaux sont en panne.

#### <a name="microsoft-recommendations"></a>Recommandations de Microsoft
 Que votre organisation utilise la fédération ou l'authentification directe, activez la synchronisation du hachage de mot de passe à l'aide de l'Assistant Azure AD Connect.

>[!IMPORTANT]
> Il n'est pas nécessaire de faire passer les utilisateurs de l'authentification fédérée à l'authentification gérée pour utiliser la synchronisation du hachage de mot de passe.

## <a name="during-a-disruption"></a>Pendant une interruption

Si vous avez opté pour l'implémentation d'un plan d'atténuation, vous surmonterez automatiquement à une interruption du contrôle d'accès. Toutefois, si vous avez choisi de créer un plan d'urgence, vous pourrez activer vos stratégies d'urgence pendant l'interruption du contrôle d'accès :

1. Activez vos stratégies d'urgence pour permettre aux utilisateurs ciblés d'accéder à des applications spécifiques à partir de réseaux spécifiques.
2. Désactivez vos stratégies standard basées sur le contrôle.

### <a name="microsoft-recommendations"></a>Recommandations de Microsoft

En fonction des mesures d'atténuation ou d'urgence prises lors d'une interruption, votre organisation peut octroyer l'accès au moyen de mots de passe uniquement. L'absence de protection représente un risque considérable en termes de sécurité et celui-ci doit être soigneusement évalué. Les organisations doivent :

1. Dans le cadre de votre stratégie de contrôle des modifications, documentez chaque modification et l'état précédent pour pouvoir annuler les mesures d'urgence mises en place dès que les contrôles d'accès seront pleinement opérationnels.
2. Supposons que des personnes malveillantes tentent de collecter des mots de passe en lançant des attaques par pulvérisation de mot de passe ou par hameçonnage pendant que l'authentification multifacteur est désactivée. Ces personnes malveillantes disposent également peut-être déjà de mots de passe qui, jusque-là, ne permettaient pas d'accéder à certaines ressources exposées pendant ce laps de temps. Pour les utilisateurs critiques tels que les cadres, vous pouvez partiellement atténuer ce risque en réinitialisant leurs mots de passe avant de désactiver l'authentification multifacteur.
3. Archivez toutes les activités de connexion pour identifier qui a accès à quoi au moment de la désactivation de l'authentification multifacteur.
4. [Triez tous les événements à risque signalés](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) au cours de ce laps de temps.

## <a name="after-a-disruption"></a>Après une interruption

Une fois le service responsable de l’interruption restauré, annulez les modifications que vous avez apportées pendant l’activation du plan d’urgence. 

1. Activez les stratégies standard.
2. Désactivez vos stratégies d'urgence. 
3. Le cas échéant, restaurez les autres modifications que vous avez apportées et documentées pendant l'interruption.
4. Si vous avez utilisé un compte d'accès d'urgence, n'oubliez pas de régénérer les informations d'identification et de sécuriser physiquement les nouvelles informations d'identification dans le cadre des procédures liées aux comptes d'accès d'urgence.
5. Continuez à [trier tous les événements à risque signalés](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) après l'interruption pour activité suspecte.
6. Révoquez tous les jetons d'actualisation émis [à l'aide de PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) pour cibler un ensemble d'utilisateurs. La révocation de tous les jetons d’actualisation est importante pour les comptes à privilèges utilisés lors de l’interruption, et elle les obligera à se réauthentifier et à se conformer au contrôle des stratégies restaurées.

## <a name="emergency-options"></a>Options d'urgence

 En cas d’urgence et si votre organisation n’a pas encore implémenté de plan d’atténuation ou d’urgence, suivez les recommandations de la section [Plan d’urgence en cas de verrouillage des utilisateurs](#contingencies-for-user-lockout) si des stratégies d’accès conditionnel sont déjà utilisées pour appliquer l’authentification multifacteur.
Si votre organisation utilise des stratégies d'authentification multifacteur héritées par utilisateur, vous pouvez envisager l'alternative suivante :

1. Si vous disposez de l'adresse IP sortante du réseau d'entreprise, vous pouvez l'ajouter en tant qu'adresse IP approuvée pour activer l'authentification sur le réseau d'entreprise uniquement.
   1. Si vous ne disposez pas de l'inventaire des adresses IP sortantes ou si vous devez activer l'accès à l'intérieur et à l'extérieur du réseau d'entreprise, vous pouvez ajouter tout l'espace d'adressage IPv4 en tant qu'adresses IP approuvées en spécifiant 0.0.0.0/1 et 128.0.0.0/1.

>[!IMPORTANT]
 > Si vous élargissez le champ des adresses IP approuvées pour débloquer l'accès, les événements à risque associés aux adresses IP (par exemple, un voyage impossible ou un emplacement inconnu) ne seront pas générés.

>[!NOTE]
 > La configuration des [adresses IP approuvées](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) pour l'authentification multifacteur Azure n'est disponible qu'avec les [licences Azure AD Premium](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing).

## <a name="learn-more"></a>En savoir plus

* [Documentation Azure AD Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Gérer les comptes d’administration de l’accès d’urgence dans Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Configurer des emplacements nommés dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Comment configurer des appareils hybrides joints à Azure Active Directory](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Guide de déploiement de Windows Hello Entreprise](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Aide sur les mots de passe - Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Que sont les conditions dans l’accès conditionnel Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Que sont les contrôles d’accès dans l’accès conditionnel Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
