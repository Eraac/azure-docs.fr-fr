---
title: Connexion sans mot de passe avec l’application Microsoft Authenticator (préversion) - Azure Active Directory
description: Connexion à Azure AD à l’aide de l’application Microsoft Authenticator sans utiliser votre mot de passe (préversion publique)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3125de0d1fd784b30c000bb287b457397c0fbebb
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703036"
---
# <a name="passwordless-phone-sign-in-with-the-microsoft-authenticator-app-public-preview"></a>Connexion par téléphone sans mot de passe avec l’application Microsoft Authenticator (préversion publique)

L’application Microsoft Authenticator vous permet de vous connecter à n’importe quel compte Azure AD sans utiliser de mot de passe. À l’instar de la technologie de [Windows Hello Entreprise](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator a recours à l’authentification par clé pour activer une information d’identification utilisateur qui est liée à un appareil et utilise un code biométrique ou confidentiel.

![Exemple de connexion dans un navigateur demandant à l’utilisateur d’approuver la connexion](./media/howto-authentication-phone-sign-in/phone-sign-in-microsoft-authenticator-app.png)

Au lieu d’obtenir une invite de mot de passe après avoir entré un nom d’utilisateur, une personne qui a activé la connexion par téléphone dans l’application Microsoft Authenticator voit s’afficher un message lui demandant d’entrer un nombre dans son application. Dans l’application, l’utilisateur doit alors sélectionner le nombre correspondant, choisir Approuver, puis fournir son code confidentiel ou biométrique, ce qui achève l’authentification.

## <a name="enable-my-users"></a>Activer mes utilisateurs

Pour la préversion publique, un administrateur doit commencer par ajouter une stratégie par le biais de PowerShell pour autoriser l’utilisation de l’information d’identification dans le locataire. Avant de suivre cette étape, consultez la section « Problèmes connus ».

### <a name="tenant-prerequisites"></a>Prérequis du locataire

* Azure Active Directory
* Utilisateurs finals activés pour Azure Multi-Factor Authentication
* Possibilité pour les utilisateurs d’inscrire leurs appareils

### <a name="steps-to-enable"></a>Procédure d’activation

Suivez les étapes de l’article [Activer l’authentification sans mot de passe pour Azure AD](howto-authentication-passwordless-enable.md#enable-new-passwordless-authentication-methods) pour activer les méthodes d’authentification sans mot de passe dans votre annuaire.

## <a name="how-do-my-end-users-enable-phone-sign-in"></a>Comment mes utilisateurs finals activent-ils la connexion par téléphone ?

Dans le cadre de la préversion publique, il n’existe aucun moyen d’imposer aux utilisateurs de créer ou d’utiliser cette nouvelle information d’identification. La connexion sans mot de passe n’est proposée à un utilisateur final qu’une fois qu’un administrateur a activé son locataire et que l’utilisateur a mis à jour son application Microsoft Authenticator pour activer la connexion par téléphone.

> [!NOTE]
> L’application intègre cette fonctionnalité depuis mars 2017 ; par conséquent, lorsque la stratégie est activée pour un locataire, il est possible que les utilisateurs rencontrent immédiatement ce flux. Gardez ce point à l’esprit et préparez vos utilisateurs à cette modification.
>

1. Inscription dans Azure Multi-Factor Authentication.
1. Installation de la dernière version de Microsoft Authenticator sur des appareils exécutant iOS 8.0 ou une version ultérieure, ou Android 6.0 ou une version ultérieure.
1. Ajout d’un compte professionnel ou scolaire avec notifications Push à l’application. La documentation pour utilisateurs finals est accessible à l’adresse [https://aka.ms/authappstart](https://aka.ms/authappstart).

Une fois que le compte MFA avec notifications Push est configuré pour l’utilisateur dans l’application Microsoft Authenticator, l’utilisateur peut suivre la procédure décrite dans l’article [Connectez-vous avec votre téléphone, et non votre mot de passe](../user-help/microsoft-authenticator-app-phone-signin-faq.md) pour achever l’inscription à la fonctionnalité de connexion par téléphone.

## <a name="known-issues"></a>Problèmes connus

### <a name="ad-fs-integration"></a>Intégration AD FS

Lorsqu’un utilisateur a activé l’information d’identification sans mot de passe Microsoft Authenticator, l’authentification de cet utilisateur implique toujours par défaut l’envoi d’une notification pour approbation. Cette logique empêche les utilisateurs d’un locataire hybride d’être dirigés vers ADFS pour la vérification de la connexion si ces utilisateurs ne suivent pas une étape supplémentaire consistant à cliquer sur « Utilisez votre mot de passe à la place ». Ce processus contourne également les stratégies d’accès conditionnel locales, ainsi que les flux d’authentification directe. Il existe une exception à ce processus : si un paramètre login_hint est spécifié, un utilisateur est automatiquement transféré vers AD FS et contourne la possibilité d’utiliser l’information d’identification sans mot de passe.

### <a name="azure-mfa-server"></a>Serveur Azure MFA

Les utilisateurs finals qui sont activés pour MFA par le biais d’un serveur Azure MFA local d’une organisation peuvent toujours créer et utiliser une information d’identification de connexion par téléphone sans mot de passe. Si l’utilisateur tente de mettre à niveau plusieurs installations (supérieures à 5) de Microsoft Authenticator avec cette information d’identification, cette modification peut générer une erreur.  

### <a name="device-registration"></a>Inscription des appareils

L’une des conditions requises pour la création de cette information d’identification forte est que l’appareil sur lequel elle réside soit inscrit dans le locataire Azure AD pour un utilisateur spécifique. Du fait des restrictions en matière d’inscription d’appareil, un appareil ne peut être inscrit que dans un seul locataire. Cette limite signifie qu’un seul compte professionnel ou scolaire de l’application Microsoft Authenticator peut être activé pour la connexion par téléphone.

## <a name="next-steps"></a>Étapes suivantes

[Présentation de l’authentification sans mot de passe](concept-authentication-passwordless.md)

[En savoir plus sur l’inscription des appareils](../devices/overview.md#getting-devices-in-azure-ad)

[En savoir plus sur Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
