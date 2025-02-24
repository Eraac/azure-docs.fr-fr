---
title: Inscription combinée pour Azure AD SSPR et l'authentification multifacteur (préversion) - Azure Active Directory
description: Inscription à l’authentification multifacteur et à la réinitialisation de mot de passe en libre-service Azure AD (préversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc0e80ec7a378d44000565034ce47d95c78b777e
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537013"
---
# <a name="combined-security-information-registration-preview"></a>Inscription d’informations de sécurité combinée (préversion)

Avant l’inscription combinée, les utilisateurs s’inscrivaient séparément à des méthodes d’authentification pour Azure Multi-Factor Authentication et la réinitialisation de mot de passe en libre-service (SSPR). Certains étaient troublés par le fait que des méthodes similaires servaient pour l’authentification multifacteur et la réinitialisation SSPR, mais qu’ils devaient s’inscrire aux deux fonctionnalités. Désormais, avec l’inscription combinée, les utilisateurs peuvent s’inscrire une seule fois et bénéficier des avantages de l’authentification multifacteur et de la réinitialisation SSPR.

![Page Mon profil affichant les informations de sécurité d’un utilisateur inscrit](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Avant d’activer la nouvelle expérience, passez en revue cette documentation axée sur l’administrateur et la documentation orientée utilisateur pour vous assurer de bien comprendre les fonctions et l’effet de cette fonctionnalité. Basez votre formation sur la [documentation pour les utilisateurs](../user-help/user-help-security-info-overview.md) afin de préparer vos utilisateurs à cette nouvelle expérience et de favoriser un déploiement réussi.

L’inscription combinée d’informations de sécurité Azure AD n’est pas disponible actuellement sur les clouds nationaux tels qu’Azure US Government, Azure Allemagne et Azure China 21Vianet.

|     |
| --- |
| L’inscription combinée d’informations de sécurité pour la réinitialisation de mot de passe en libre-service Azure Active Directory (Azure AD) et l’authentification multifacteur est une fonctionnalité d’évaluation publique d’Azure AD. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Les utilisateurs activés pour la préversion d’origine et l’expérience d’inscription combinée améliorée voient le nouveau comportement. Les utilisateurs activés pour les deux expériences voient uniquement la nouvelle expérience de la page Mon profil. La nouvelle page Mon profil s’aligne sur l’apparence de l’inscription combinée et offre une expérience transparente pour les utilisateurs. Les utilisateurs peuvent voir la page Mon profil en accédant à [https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

Les pages Mon profil sont localisées sur la base des paramètres de langue de l’ordinateur qui accède à ces pages. Microsoft stocke la dernière langue utilisée dans le cache du navigateur, afin que les tentatives suivantes d’accès aux pages continuent de s’afficher dans la dernière langue utilisée. Si vous effacez le cache, les pages seront réaffichées. Pour imposer l’utilisation d’une langue spécifique, vous pouvez ajouter `?lng=<language>` à la fin de l’URL, où `<language>` est le code de la langue que vous souhaitez utiliser.

![Configurer la réinitialisation SSPR ou d’autres méthodes de vérification de sécurité](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Méthodes disponibles dans l’inscription combinée

L’inscription combinée prend en charge les méthodes et les actions d’authentification suivantes :

|   | Register | Modifier | Supprimer |
| --- | --- | --- | --- |
| Microsoft Authenticator | Oui (jusqu'à 5) | Non | OUI |
| Autre application d’authentification | Oui (jusqu'à 5) | Non | OUI |
| Jeton matériel | Non | Non | OUI |
| Téléphone | OUI | OUI | OUI |
| Autre téléphone | OUI | OUI | OUI |
| Téléphone de bureau | Non | Non | Non |
| Email | OUI | OUI | OUI |
| Questions de sécurité | OUI | Non | OUI |
| Mots de passe d'application | OUI | Non | OUI |

> [!NOTE]
> Les mots de passe d’application sont disponibles uniquement pour les utilisateurs qui ont été paramétrés pour l’authentification multifacteur. Les mots de passe d’application ne sont pas disponibles pour les utilisateurs activés pour l’authentification multifacteur via une stratégie d’accès conditionnel.

Les utilisateurs peuvent définir l’une des options suivantes comme méthode d’authentification multifacteur par défaut :

- Microsoft Authenticator – notification.
- Application d’authentification ou jeton matériel – code.
- Appel téléphonique.
- SMS.

Au fur et à mesure que nous ajouterons de nouvelles méthodes d’authentification à Azure AD, ces méthodes seront disponibles dans le cadre d’une inscription combinée.

## <a name="combined-registration-modes"></a>Modes d’inscription combinée

Il existe deux modes d’inscription combinée : le mode d’interruption et le mode de gestion.

- Le **mode d’interruption** est une expérience de type Assistant, présentée aux utilisateurs lorsqu’ils s’inscrivent ou actualisent les informations de sécurité à la connexion.

- Le **mode de gestion** fait partie du profil utilisateur et permet aux utilisateurs de gérer leurs informations de sécurité.

Pour ces deux modes, les utilisateurs qui se sont inscrits précédemment à une méthode pouvant être utilisée pour l’authentification multifacteur doivent effectuer une authentification multifacteur avant de pouvoir accéder à leurs informations de sécurité.

### <a name="interrupt-mode"></a>Mode d’interruption

L’inscription combinée respecte les stratégies d’authentification multifacteur et de réinitialisation SSPR, si les deux sont activées pour votre locataire. Ces stratégies contrôlent le fait que l’inscription d’un utilisateur est interrompue ou non pendant la connexion, ainsi que les méthodes d’inscription disponibles.

Voici plusieurs scénarios dans lesquels les utilisateurs peuvent être invités à s’inscrire à leurs informations de sécurité, ou à les actualiser :

- Inscription à l’authentification multifacteur appliquée via Identity Protection : les utilisateurs sont invités à s’inscrire lors de la connexion. Ils s’inscrivent à des méthodes d’authentification multifacteur et de réinitialisation SSPR (si l’utilisateur est activé pour SSPR).
- Inscription à l’authentification multifacteur appliquée via l’authentification multifacteur par utilisateur : les utilisateurs sont invités à s’inscrire lors de la connexion. Ils s’inscrivent à des méthodes d’authentification multifacteur et de réinitialisation SSPR (si l’utilisateur est activé pour SSPR).
- Inscription à l’authentification multifacteur appliquée via l’accès conditionnel ou d’autres stratégies : les utilisateurs sont invités à s’inscrire lorsqu’ils utilisent une ressource qui nécessite l’authentification multifacteur. Ils s’inscrivent à des méthodes d’authentification multifacteur et de réinitialisation SSPR (si l’utilisateur est activé pour SSPR).
- Inscription à la réinitialisation SSPR appliquée : les utilisateurs sont invités à s’inscrire lors de la connexion. Ils s’inscrivent uniquement aux méthodes SSPR.
- Actualisation SSPR appliquée : les utilisateurs doivent vérifier leurs informations de sécurité à la fréquence définie par l’administrateur. Les informations sont montrées aux utilisateurs et ces derniers peuvent confirmer les informations actuelles ou apporter des modifications si nécessaire.

Lorsque l’inscription est appliquée, les utilisateurs peuvent voir le nombre minimal de méthodes nécessaires pour être conformes aux stratégies d’authentification multifacteur et SSPR, de la plus à la moins sécurisée.

Par exemple :

- Un utilisateur est activé pour la réinitialisation SSPR. La stratégie SSPR nécessitait deux méthodes pour réinitialiser et activait le code de l’application mobile, le courrier et le téléphone.
   - Cet utilisateur est tenu de s’inscrire à deux méthodes.
      - L’utilisateur voit l’application d’authentification et le téléphone par défaut.
      - L’utilisateur peut choisir de s’inscrire par e-mail à la place de l’application d’authentification ou du téléphone.

Cet organigramme décrit les méthodes qui sont montrées à un utilisateur lorsque son inscription est interrompue pendant la connexion :

![Organigramme des informations de sécurité combinée](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Si vous avez activé l’authentification multifacteur et la réinitialisation SSPR, nous vous recommandons d’appliquer l’inscription à l’authentification multifacteur.

Si la stratégie SSPR nécessite que les utilisateurs vérifient leurs informations de sécurité à intervalles réguliers, les utilisateurs sont interrompus pendant la connexion et toutes leurs méthodes inscrites leur sont montrées. Ils peuvent confirmer les informations actuelles si elle sont à jour, ou apporter des modifications si nécessaire.

### <a name="manage-mode"></a>Mode de gestion

Les utilisateurs peuvent accéder au mode de gestion en accédant à [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) ou en sélectionnant **Informations de sécurité** dans Mon profil. Là, les utilisateurs peuvent ajouter des méthodes, supprimer ou modifier les méthodes existantes, modifier la méthode par défaut et bien plus encore.

## <a name="key-usage-scenarios"></a>Principaux scénarios d’utilisation

### <a name="set-up-security-info-during-sign-in"></a>Configurer les informations de sécurité pendant la connexion

Un administrateur a appliqué l’inscription.

Un utilisateur n’a pas configuré toutes les informations de sécurité requises et accède au portail Azure. Après avoir entré le nom d’utilisateur et le mot de passe, l’utilisateur est invité à configurer les informations de sécurité. Ensuite, l’utilisateur suit les étapes indiquées dans l’Assistant pour configurer les informations de sécurité requises. Si vos paramètres le permettent, l’utilisateur peut choisir de configurer d’autres méthodes que celles affichées par défaut. À l’issue de l’Assistant, les utilisateurs passent en revue les méthodes qu'ils configurent et leur méthode par défaut pour l’authentification multifacteur. Pour terminer le processus de configuration, l’utilisateur confirme les informations et continue vers le portail Azure.

### <a name="set-up-security-info-from-my-profile"></a>Configurer les informations de sécurité à partir de Mon profil

Un administrateur n’a pas appliqué l’inscription.

Un utilisateur qui n’a pas encore configuré toutes les informations de sécurité requises accède à [https://myprofile.microsoft.com](https://myprofile.microsoft.com). L’utilisateur sélectionne **Informations de sécurité** dans le volet gauche. Là, l’utilisateur choisit d’ajouter une méthode, sélectionne une des méthodes disponibles et suit la procédure requise pour configurer cette méthode. Une fois terminé, l’utilisateur voit la méthode qui vient d’être configurée sur la page Informations de sécurité.

### <a name="delete-security-info-from-my-profile"></a>Supprimer les informations de sécurité à partir de Mon profil

Un utilisateur qui a configuré précédemment au moins une méthode accède à [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). L’utilisateur choisit de supprimer une des méthodes précédemment inscrites. Une fois terminé, l’utilisateur ne voit plus cette méthode sur la page Informations de sécurité.

### <a name="change-the-default-method-from-my-profile"></a>Modifier la méthode par défaut à partir de Mon profil

Un utilisateur qui a configuré précédemment au moins une méthode pouvant être utilisée pour l’authentification multifacteur accède à [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). L’utilisateur remplace la méthode par défaut actuelle par une autre méthode par défaut. Une fois terminé, l’utilisateur voit la nouvelle méthode par défaut sur la page Informations de sécurité.

## <a name="next-steps"></a>Étapes suivantes

[Activer une inscription combinée dans votre locataire](howto-registration-mfa-sspr-combined.md)

[Rapport d’utilisation et d’insights SSPR et MFA](howto-authentication-methods-usage-insights.md)

[Méthodes disponibles pour l'authentification multifacteur et la réinitialisation de mot de passe en libre-service](concept-authentication-methods.md)

[Configurer la réinitialisation du mot de passe en libre-service](howto-sspr-deployment.md)

[Configurer Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
