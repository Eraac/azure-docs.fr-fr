---
title: Inscription en libre-service pour les comptes d’utilisateurs vérifiés par e-mail - Azure Active Directory | Microsoft Docs
description: Utilisez l’inscription en libre-service dans un locataire Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d949b746f05eb440f5ae28f683dfc838217ab47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956511"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Présentation de l’inscription en libre-service pour Azure Active Directory

Cet article explique comment utiliser l’inscription en libre-service pour renseigner une organisation dans Azure Active Directory (Azure AD). Si vous souhaitez prendre le contrôle d’un nom de domaine d’une organisation Azure AD non géré, consultez [Prendre le contrôle d’un annuaire non géré en tant qu’administrateur](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Pourquoi utiliser l’inscription libre-service ?
* Permettre aux clients de bénéficier plus rapidement des services dont ils ont besoin.
* Créer des offres envoyées par e-mail pour un service.
* Créer des flux d’inscription par e-mail permettant aux utilisateurs de créer rapidement des identités à l’aide de leurs alias de messagerie professionnelle, faciles à mémoriser
* Un répertoire Azure AD créé en libre-service peut être transformé en répertoire géré utilisable pour d’autres services.

## <a name="terms-and-definitions"></a>Termes et définitions
* **Inscription libre- service** : méthode selon laquelle un utilisateur s‘abonne à un service cloud et bénéficie automatiquement d’une identité créée pour lui dans Azure AD en fonction de son domaine de messagerie.
* **Annuaire Azure AD non managé** : annuaire dans lequel cette identité est créée. Un répertoire non géré est un répertoire qui ne possède aucun administrateur général.
* **Utilisateur vérifié par e-mail** : type de compte d’utilisateur dans Azure AD. Un utilisateur qui possède une identité créée automatiquement après s’être abonné à une offre libre-service est considéré comme un utilisateur vérifié par e-mail. Un utilisateur vérifié par e-mail est un membre ordinaire d'un répertoire marqué par la valeur creationmethod=EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Comment vérifier les paramètres libre-service ?
Les administrateurs peuvent désormais effectuer deux vérifications libre-service. Ils peuvent contrôler si :

* Les utilisateurs peuvent rejoindre l’annuaire par e-mail
* Les utilisateurs peuvent s’abonner eux-mêmes aux applications et services

### <a name="how-can-i-control-these-capabilities"></a>Comment puis-je vérifier ces fonctionnalités ?
Un administrateur peut configurer ces fonctionnalités à l’aide des paramètres Set-MsolCompanySettings d’applet de commande Azure AD suivants :

* **AllowEmailVerifiedUsers** vérifie si un utilisateur peut créer ou rejoindre un annuaire. Si vous définissez ce paramètre sur $false, aucun utilisateur vérifié par e-mail ne peut rejoindre l’annuaire.
* **AllowAdHocSubscriptions** vérifie la capacité des utilisateurs à réaliser une inscription libre-service. Si vous définissez ce paramètre sur $false, aucun utilisateur ne peut effectuer une inscription libre-service.
  
AllowEmailVerifiedUsers et AllowAdHocSubscriptions sont des paramètres de niveau annuaire qui peuvent être appliqués à un annuaire géré ou non géré. Voici un exemple où :

* Vous administrez un annuaire avec un domaine vérifié, par exemple, contoso.com
* Vous utilisez la collaboration B2B à partir d’un autre annuaire pour inviter un utilisateur qui n’existe pas encore (userdoesnotexist@contoso.com) dans l’annuaire local de contoso.com
* AllowEmailVerifiedUsers est activé pour l’annuaire local

Si les conditions précédentes sont remplies, un utilisateur membre est créé dans l’annuaire local, tandis qu’un utilisateur invité B2B est créé dans l’annuaire à l’origine de l’invitation.

Les inscriptions à la version d’évaluation de Flow et de PowerApps ne sont pas contrôlées par le paramètre **AllowAdHocSubscriptions**. Pour plus d’informations, consultez les articles suivants :

* [Comment faire pour empêcher les utilisateurs existants de commencer à utiliser Power BI ?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Questions et réponses sur Flow dans l’organisation](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Comment les vérifications parviennent-elles à fonctionner ensemble ?
Ces deux paramètres peuvent être utilisés conjointement pour définir une vérification plus précise de l’inscription libre-service. Par exemple, la commande suivante permettra aux utilisateurs de réaliser une inscription libre-service, mais uniquement si ces utilisateurs possèdent déjà un compte dans Azure AD (en d’autres termes, les utilisateurs qui ont besoin d’un compte vérifié par e-mail ne peuvent pas réaliser d’inscription libre-service) :

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

L‘organigramme suivant décrit les différentes combinaisons de paramètres et les conditions qui en résultent pour le répertoire et l’inscription en libre-service.

![Organigramme des contrôles de l’inscription en libre-service](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Pour en savoir plus et obtenir des exemples d'utilisation de ces paramètres, consultez [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter un nom de domaine personnalisé à Azure AD](../fundamentals/add-custom-domain.md)
* [Installation et configuration d’Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Guide de référence des applets de commande Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Fermer votre compte professionnel ou scolaire dans un annuaire Azure non managé](users-close-account.md)
