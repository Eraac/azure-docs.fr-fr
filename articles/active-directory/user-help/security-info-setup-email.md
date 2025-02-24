---
title: Configurer les informations de sécurité (préversion) pour utiliser votre adresse e-mail - Azure Active Directory | Microsoft Docs
description: Apprenez à configurer vos informations de sécurité pour vérifier votre identité au moyen de votre adresse e-mail.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 720aafac79a67f64b0974dba0dd60c6aa24a8c54
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60480591"
---
# <a name="set-up-security-info-preview-to-use-your-email-address"></a>Configurer les informations de sécurité (préversion) pour utiliser votre adresse e-mail
Procédez comme suit pour ajouter votre méthode de réinitialisation de mot de passe. Au terme de la configuration initiale, vous pourrez revenir à la page **Informations de sécurité** pour ajouter, mettre à jour ou supprimer vos informations de sécurité.

Après avoir configuré votre méthode de réinitialisation de mot de passe, vous devez également configurer votre méthode de vérification en deux étapes, à l'aide d'une [application d'authentification](security-info-setup-auth-app.md), de la [messagerie texte](security-info-setup-text-msg.md), ou d'un [appel téléphonique](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-email-address-from-the-security-info-page"></a>Configurer votre adresse e-mail à partir de la page Informations de sécurité
Selon les paramètres de votre organisation, vous pourrez peut-être utiliser votre adresse e-mail comme méthode d'informations de sécurité.

>[!Note]
>Nous vous recommandons d'utiliser une adresse e-mail dont l'accès n'est pas soumis à l'utilisation de votre mot de passe réseau. Si vous ne voyez pas l’option d’e-mail, il est possible que votre organisation ne vous autorise pas à utiliser un e-mail à des fins de vérification. Si tel est le cas, vous devez choisir une autre méthode, ou contacter votre administrateur pour obtenir de l’aide.

### <a name="to-set-up-your-email-address"></a>Pour configurer votre adresse e-mail

1. Connectez-vous à votre compte professionnel ou scolaire, puis accédez à votre page https://myprofile.microsoft.com/.

    ![Page Mon profil, avec les liens des informations de sécurité mis en évidence](media/security-info/securityinfo-myprofile-page.png)

2. Sélectionnez **Informations de sécurité** à partir du volet de navigation de gauche ou du lien du bloc **Informations de sécurité**, puis sélectionnez **Ajouter une méthode** dans la page **Informations de sécurité**.

    ![Page Informations de sécurité, avec l'option Ajouter une méthode en surbrillance](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Sur la page **Ajouter une méthode**, choisissez **E-mail** dans la liste déroulante, puis sélectionnez **Ajouter**.

    ![Zone Ajouter une méthode, avec l'option E-mail sélectionnée](media/security-info/securityinfo-myprofile-addemail.png)

4. Sur la page **E-mail**, entrez votre adresse e-mail (par exemple, alain@gmail.com), puis sélectionnez **Suivant**.

    ![Ajouter le numéro de téléphone et choisir la méthode de l'appel téléphonique](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >Cette adresse e-mail ne peut pas être votre e-mail professionnel ou scolaire.

5. Entrez le code envoyé à l'adresse e-mail spécifiée, puis sélectionnez **Suivant**.

    ![Ajouter le numéro de téléphone et choisir la méthode de l'envoi de SMS](media/security-info/securityinfo-myprofile-emailcode.png)

    Vos informations de sécurité sont mises à jour et vous pouvez utiliser votre adresse e-mail à des fins de contrôle de votre identité lors de la réinitialisation de mot de passe.

## <a name="delete-your-email-address-from-your-security-info-methods"></a>Supprimer votre adresse e-mail de vos méthodes d'informations de sécurité
Si vous ne souhaitez plus utiliser votre adresse e-mail comme méthode d'informations de sécurité, vous pouvez supprimer celle-ci de la page **Informations de sécurité**.

>[!Important]
>Si vous supprimez votre adresse e-mail par erreur, il n'existe aucun moyen d'annuler la suppression. Vous devrez à nouveau l'ajouter en suivant les étapes de la section [Configurer votre adresse e-mail](#set-up-your-email-address-from-the-security-info-page) de cet article.

### <a name="to-delete-your-email-address"></a>Pour supprimer votre adresse e-mail

1. Sur la page **Informations de sécurité**, sélectionnez le lien **Supprimer** en regard de l'option **E-mail**.

    ![Lien permettant de supprimer la méthode Téléphone sur la page Informations de sécurité](media/security-info/securityinfo-myprofile-emaildelete.png)

2. Sélectionnez **Oui** dans la zone de confirmation pour supprimer le compte **E-mail**. Une fois le compte e-mail supprimé, celui-ci disparaît de vos informations de sécurité et de la page **Informations de sécurité**.

## <a name="additional-security-info-methods"></a>Autres méthodes d'informations de sécurité
Vous avez la possibilité de choisir la façon d’être contacté par votre organisation pour la vérification de votre identité, en fonction de ce que vous essayez de faire. Ces options sont les suivantes :

- **Application d’authentification.** Téléchargez et utilisez une application d’authentification pour obtenir une notification d’approbation ou un code d’approbation généré de manière aléatoire pour la réinitialisation du mot de passe ou la vérification en deux étapes. Pour obtenir des instructions détaillées sur la configuration et l’utilisation de l’application Microsoft Authenticator, consultez [Configurer les informations de sécurité pour utiliser une application d’authentification](security-info-setup-auth-app.md).

- **SMS sur appareil mobile.** Entrez votre numéro de téléphone mobile et recevez un code par SMS, à utiliser pour la vérification en deux étapes ou la réinitialisation de mot de passe. Pour obtenir des instructions détaillées sur la vérification de votre identité par SMS, consultez [Configurer les informations de sécurité pour utiliser la messagerie texte (SMS)](security-info-setup-text-msg.md).

- **Appel sur téléphone mobile ou téléphone professionnel.** Entrez votre numéro de téléphone mobile et recevez un appel pour la réinitialisation de mot de passe ou la vérification en deux étapes. Pour obtenir des instructions détaillées sur la vérification de votre identité au moyen d’un numéro de téléphone, consultez [Configurer les informations de sécurité pour utiliser un appel téléphonique](security-info-setup-phone-number.md).

- **Questions de sécurité.** Répondez à certaines questions de sécurité créées par votre administrateur pour votre organisation. Cette option est uniquement disponible pour la réinitialisation du mot de passe et non pour la vérification en deux étapes. Pour des instructions pas à pas sur la façon de configurer vos questions de sécurité, consultez l’article [Configurer les informations de sécurité pour utiliser les questions de sécurité](security-info-setup-questions.md).
    
    >[!Note]
    >Si certaines de ces options ne sont pas disponibles, il est très probable que votre organisation n’autorise pas ces méthodes. Si tel est le cas, vous devez choisir une autre méthode ou contacter votre administrateur pour obtenir de l’aide.

## <a name="next-steps"></a>Étapes suivantes

- Si vous avez perdu ou oublié votre mot de passe, réinitialisez-le à partir du [portail de réinitialisation de mot de passe](https://passwordreset.microsoftonline.com/), ou suivez les étapes de l’article [Réinitialiser votre mot de passe professionnel ou scolaire](user-help-reset-password.md).

- Obtenez des conseils de dépannage et de l’aide pour les problèmes de connexion en consultant l’article [Quand vous ne pouvez pas vous connecter à votre compte Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
