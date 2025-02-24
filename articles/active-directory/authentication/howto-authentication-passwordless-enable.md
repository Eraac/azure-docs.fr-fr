---
title: Configurer l’authentification sans mot de passe pour Azure Active Directory (préversion)
description: Activer l’authentification sans mot de passe pour Azure AD à l’aide de clés de sécurité FIDO2 ou de l’application Microsoft Authenticator (préversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ba2545467ebfbd032408aeee25b82b92a628f2a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711962"
---
# <a name="enable-passwordless-sign-in-for-azure-ad-preview"></a>Activer l’authentification sans mot de passe pour Azure AD (préversion)

## <a name="requirements"></a>Configuration requise

* Azure Multi-Factor Authentication
* Inscription combinée en préversion
* La préversion des clés de sécurité FIDO2 nécessite des clés de sécurité FIDO2 compatibles
* WebAuthN nécessite Microsoft Edge sur Windows 10 version 1809 ou une version ultérieure
* La connexion Windows basée sur FIDO2 requiert Azure AD sur Windows 10 version 1809 ou une version ultérieure

## <a name="prepare-devices-for-preview"></a>Préparer les appareils pour la préversion

Les appareils que vous utiliserez doivent exécuter Windows 10 version 1809 ou ultérieure. Vous bénéficierez de la meilleure expérience sur Windows 10 version 1903 ou ultérieure.

## <a name="enable-security-keys-for-windows-sign-in"></a>Activer les clés de sécurité pour la connexion Windows

Les organisations peuvent choisir d’utiliser une ou plusieurs des méthodes suivantes pour activer l’utilisation de clés de sécurité pour la connexion Windows.

### <a name="enable-credential-provider-via-intune"></a>Activer le fournisseur d’informations d’identification via Intune

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à **Microsoft Intune** > **Inscription d’appareils** > **Inscription Windows** > **Windows Hello Entreprise** > **Propriétés**.
1. Sous **Paramètres** définissez **Utiliser des clés de sécurité pour la connexion** sur **Activé**.

La configuration de clés de sécurité pour la connexion n’est pas dépendante de la configuration de Windows Hello Entreprise.

#### <a name="enable-targeted-intune-deployment"></a>Activer le déploiement Intune ciblé

Pour cibler des groupes d’appareils spécifiques pour activer le fournisseur d’informations d’identification, utilisez les paramètres personnalisés suivants via Intune. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Accédez à **Microsoft Intune** > **Configuration de l’appareil** > **Profils** > **Créer un profil**.
1. Configurez le nouveau profil avec les paramètres suivants
   1. Nom : Clés de sécurité pour la connexion Windows
   1. Description : Permet d’utiliser des clés de sécurité FIDO lors de la connexion à Windows
   1. Plateforme : Windows 10 et versions ultérieures
   1. Type de plateforme : Personnalisée
   1. Paramètres OMA-URI personnalisés :
      1. Nom : Activer les clés de sécurité FIDO pour la connexion à Windows
      1. OMA-URI : ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Type de données : Entier
      1. Valeur : 1 
1. Cette stratégie peut être attribuée à des utilisateurs, appareils ou groupes spécifiques. Vous trouverez plus d’informations dans l’article [Attribuer des profils d’utilisateur et d’appareil dans Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Création de stratégies de configuration d’appareil Intune personnalisées](./media/howto-authentication-passwordless-enable/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>Activer le fournisseur d’informations d’identification via un package d’approvisionnement

Pour les appareils non gérés par Intune, un package d’approvisionnement peut être installé pour activer la fonctionnalité. L’application Windows Configuration Designer peut être installée à partir du [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

1. Lancez Windows Configuration Designer.
1. Sélectionnez **Fichier** > **Nouveau projet**.
1. Donnez un nom à votre projet et notez le chemin d’accès auquel votre projet a été créé.
1. Sélectionnez **Suivant**.
1. Laissez **Package d’approvisionnement** sélectionné comme **Flux de travail de projet sélectionné** et sélectionnez **Suivant**.
1. Sélectionnez **Toutes les éditions de bureau de Windows** sous **Choisir les paramètres à afficher et configurer**, et sélectionnez **Suivant**.
1. Sélectionnez **Terminer**.
1. Dans votre projet nouvellement créé, accédez à **Paramètres d’exécution** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Définissez **UseSecurityKeyForSignIn** sur **Activé**.
1. Sélectionnez **Exporter** > **Package d’approvisionnement**
1. Laissez les valeurs par défaut dans la fenêtre **Générer** sous **Décrire le package d’approvisionnement**, et sélectionnez **Suivant**.
1. Laissez les valeurs par défaut dans la fenêtre **Générer** sous **Sélectionner les détails de sécurité pour le package d’approvisionnement**, et sélectionnez **Suivant**.
1. Prenez note du chemin d’accès (ou modifiez-le) dans la fenêtre **Générer** sous **Sélectionnez l’emplacement d’enregistrement du package d’approvisionnement**, et sélectionnez **Suivant**.
1. Sélectionnez **Générer** sur la page **Générer le package d’approvisionnement**.
1. Enregistrez les deux fichiers créés (ppkg et cat) dans un emplacement où vous pourrez les appliquer aux machines plus tard.
1. Suivez les instructions dans l’article [Appliquer un package d’approvisionnement](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package) pour appliquer le package d’approvisionnement que vous avez créé.

## <a name="obtain-fido2-security-keys"></a>Obtenir les clés de sécurité FIDO2

Consultez la section Clés de sécurité FIDO2 de l’article [Présentation de l’authentification sans mot de passe](concept-authentication-passwordless.md) pour plus d’informations sur les fabricants et clés pris en charge.

> [!NOTE]
> Si vous achetez et prévoyez d’utiliser des clés de sécurité NFC, vous aurez besoin d’un lecteur NFC pris en charge.

## <a name="enable-passwordless-authentication-methods"></a>Activer les méthodes d’authentification sans mot de passe

### <a name="enable-the-combined-registration-experience"></a>Activer l’expérience d’inscription combinée

Les fonctionnalités d’inscription pour les clés de sécurité FIDO2 s’appuient sur l’inscription combinée, en préversion. Suivez les étapes ci-dessous pour activer la préversion de l’inscription combinée.

1. Connectez-vous au [portail Azure](https://portal.azure.com)
1. Accédez à **Azure Active Directory** > **Paramètres utilisateur**
   1. Cliquez sur **Gérer les paramètres pour les fonctionnalités en préversion du panneau accès**
   1. Sous **Users can use preview features for registering and managing security info - enhanced** (les utilisateurs peuvent utiliser les fonctionnalités en préversion pour inscrire et gérer les informations de sécurité - amélioré).
      1. Choisissez **Sélectionné** et choisissez un groupe d’utilisateurs qui participe à la préversion.
      1. Ou choisissez **Tous** pour l’activer pour tous les membres de votre annuaire.
1. Cliquez sur **Enregistrer**.

### <a name="enable-new-passwordless-authentication-methods"></a>Activer de nouvelles méthodes d’authentification sans mot de passe

1. Connectez-vous au [portail Azure](https://portal.azure.com)
1. Accédez à **Azure Active Directory** > **Méthodes d’authentification** > **Stratégie de méthode d’authentification (préversion)**
1. Sous chaque **Méthode**, choisissez les options suivantes
   1. **Activer** - Oui ou Non
   1. **Cible** - Tous les utilisateurs ou les utilisateurs sélectionnés
1. **Enregistrez** chaque méthode

> [!WARNING]
> Les « stratégies de restriction de clé » FIDO2 ne fonctionnent pas encore. Cette fonctionnalité sera disponible avant la disponibilité générale ; ne modifiez pas ces stratégies par rapport à la valeur par défaut.

> [!NOTE]
> Vous n’avez pas besoin de vous abonner aux deux méthodes sans mot de passe (si vous souhaitez afficher un aperçu d’une seule méthode sans mot de passe, vous pouvez autoriser uniquement cette méthode). Nous vous invitons à essayer les deux méthodes, dans la mesure où elles ont toutes deux leurs propres avantages.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Inscription des utilisateurs et gestion des clés de sécurité FIDO2

1. Accédez à [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Connectez-vous si ce n’est pas déjà fait
1. Cliquez **Informations de sécurité**
   1. Si l’utilisateur possède dispose déjà d’au moins une méthode d’authentification multifacteur Azure inscrite, il peut inscrire immédiatement une clé de sécurité FIDO2.
   1. Sinon, il doit d’abord ajouter une méthode d'authentification multifacteur.
1. Ajoutez une clé de sécurité de FIDO2 en cliquant sur **Ajouter méthode** et en choisissant **Clé de sécurité**
1. Choisissez **Périphérique USB** ou **Appareil NFC**
1. Préparez votre clé et choisissez **Suivant**
1. Une fenêtre s’affichera et vous demandera de créer/saisir un code confidentiel pour votre clé de sécurité, puis d’effectuer le geste de requis pour votre clé biométrique ou tactile.
1. Vous revenez à l’expérience d’inscription combinée et êtes invité à fournir un nom explicite pour votre jeton, afin de pouvoir l’identifier si vous en avez plusieurs. Cliquez sur **Suivant**.
1. Cliquez sur **Terminé** pour terminer le processus

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Gérer la clé de sécurité biométrique, le code confidentiel, ou réinitialiser la clé de sécurité

* Windows 10 version 1809
   * Un logiciel d’accompagnement du fournisseur de clés de sécurité est requis
* Windows 10 version 1903 ou ultérieure
   * Les utilisateurs peuvent ouvrir les **Paramètres Windows** sur leur appareil > **Comptes** > **Clé de sécurité**
   * Les utilisateurs peuvent modifier leur code confidentiel, mettre à jour les informations biométriques ou réinitialiser leur clé de sécurité

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Inscription des utilisateurs et gestion de l’application Microsoft Authenticator

Pour configurer l’application Microsoft Authenticator pour la connexion par téléphone, suivez les instructions dans l’article [Vous connecter à vos comptes à l’aide de l’application Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

## <a name="sign-in-with-passwordless-credentials"></a>Se connecter avec les informations d’identification sans mot de passe

### <a name="sign-in-at-the-lock-screen"></a>Se connecter depuis l’écran de verrouillage

Dans l’exemple ci-dessous, un utilisateur Bala Sandhu a déjà approvisionné sa clé de sécurité FIDO2. Bala peut choisir le fournisseur d’informations d’identification de clé de sécurité à partir de l’écran de verrouillage Windows 10, et insérer la clé de sécurité pour se connecter à Windows.

![Connexion par clé de sécurité depuis l’écran de verrouillage Windows 10](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>Connexion en ligne

Dans l’exemple ci-dessous, un utilisateur a déjà approvisionné sa clé de sécurité FIDO2. L’utilisateur peut choisir de se connecter en ligne avec sa clé de sécurité FIDO2 depuis le navigateur Microsoft Edge sur Windows 10 version 1809 ou ultérieure.

![Connexion par clé de sécurité dans Microsoft Edge](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-edge-sign-in.png)

Pour plus d’informations sur la connexion à l’aide de l’application Microsoft Authenticator, consultez l’article [Vous connecter à vos comptes à l’aide de l’application Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

## <a name="known-issues"></a>Problèmes connus

### <a name="fido2-security-keys"></a>Clés de sécurité FIDO2

#### <a name="security-key-provisioning"></a>Approvisionnement de clé de sécurité

L’approvisionnement et le retrait de clés de sécurité par l’administrateur ne sont pas disponibles dans la préversion publique.

#### <a name="hybrid-azure-ad-join"></a>jointure Azure AD hybride ;

Les utilisateurs qui utilisent l’authentification unique WIA avec des informations d’identification gérées comme des clés de sécurité FIDO2 ou la connexion sans mot de passe avec l’application Microsoft Authenticator doivent utiliser une jonction hybride sur Windows 10 pour obtenir les avantages de l’authentification unique. Toutefois, les clés de sécurité fonctionnent uniquement pour les machines Azure Active Directory jointes pour l’instant. Nous vous recommandons de tester les clés de sécurité FIDO2 uniquement pour l’écran de verrouillage Windows sur des machines Azure Active Directory jointes. Cette limitation ne s’applique pas pour le web.

#### <a name="upn-changes"></a>Modifications de l’UPN

Nous travaillons sur la prise en charge d’une fonctionnalité qui permet la modification de l’UPN sur les appareils AADJ et AADJ hybrides. Si l’UPN de l’utilisateur change, vous ne pouvez plus modifier les clés de sécurité FIDO2 pour en prendre en compte. Par conséquent, la seule approche consiste à réinitialiser l’appareil, et l’utilisateur doit s’inscrire de nouveau.

### <a name="authenticator-app"></a>Application Authenticator

#### <a name="ad-fs-integration"></a>Intégration AD FS

Lorsqu’un utilisateur a activé l’information d’identification sans mot de passe Microsoft Authenticator, l’authentification de cet utilisateur implique toujours par défaut l’envoi d’une notification pour approbation. Cette logique empêche les utilisateurs d’un locataire hybride d’être dirigés vers ADFS pour la vérification de la connexion si ces utilisateurs ne suivent pas une étape supplémentaire consistant à cliquer sur « Utilisez votre mot de passe à la place ». Ce processus contourne également les stratégies d’accès conditionnel locales, ainsi que les flux d’authentification directe. Il existe une exception à ce processus : si un paramètre login_hint est spécifié, un utilisateur est automatiquement transféré vers AD FS et contourne la possibilité d’utiliser l’information d’identification sans mot de passe.

#### <a name="azure-mfa-server"></a>Serveur Azure MFA

Les utilisateurs finals qui sont activés pour MFA par le biais d’un serveur Azure MFA local d’une organisation peuvent toujours créer et utiliser une information d’identification de connexion par téléphone sans mot de passe. Si l’utilisateur tente de mettre à niveau plusieurs installations (supérieures à 5) de Microsoft Authenticator avec cette information d’identification, cette modification peut générer une erreur.  

#### <a name="device-registration"></a>Enregistrement de l’appareil

L’une des conditions requises pour la création de cette information d’identification forte est que l’appareil sur lequel elle réside soit inscrit dans le locataire Azure AD pour un utilisateur spécifique. Du fait des restrictions en matière d’inscription d’appareil, un appareil ne peut être inscrit que dans un seul locataire. Cette limite signifie qu’un seul compte professionnel ou scolaire de l’application Microsoft Authenticator peut être activé pour la connexion par téléphone.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur l’inscription des appareils](../devices/overview.md)

[En savoir plus sur Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
