---
title: Approuver ou refuser des demandes d’accès pour la gestion des droits d’utilisation Azure AD (préversion) - Azure Active Directory
description: Découvrez comment utiliser le portail Mon Accès pour approuver ou refuser les demandes à un package d’accès pour la gestion des droits d’utilisation Azure Active Directory (préversion).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/18/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78243f02cbbe3d06b94ee52d6193865dbfa27121
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489003"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>Approuver ou refuser des demandes d’accès pour la gestion des droits d’utilisation Azure AD (préversion)

> [!IMPORTANT]
> La gestion des droits d’utilisation Azure Active Directory (Azure AD) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Avec la gestion des droits d’utilisation Azure AD, vous pouvez configurer des stratégies pour exiger l’approbation des packages d’accès et choisir un ou plusieurs approbateurs. Cet article décrit comment les approbateurs désignés peuvent approuver ou refuser les demandes pour les packages d’accès.

## <a name="open-request"></a>Ouvrir la demande

La première étape pour approuver ou refuser les demandes d’accès consiste à rechercher et ouvrir la demande d’accès en attente d’approbation. Il existe deux manières d’ouvrir la demande d’accès.

**Rôle prérequis :** Approbateur

1. Recherchez un e-mail de Microsoft Azure vous demandant d’approuver ou de refuser une demande. Voici un exemple d’e-mail :

    ![E-mail concernant l’approbation de la demande pour un package d’accès](./media/entitlement-management-shared/email-approve-request.png)

1. Cliquez sur le lien **Approuver ou refuser la demande** pour ouvrir la demande d’accès.

1. Connectez-vous au portail Mon Accès.

Si vous n’avez pas reçu l’e-mail, vous pouvez localiser les demandes d’accès en attente d’approbation comme suit.

1. Connectez-vous au portail Mon Accès sur [https://myaccess.microsoft.com](https://myaccess.microsoft.com).

1. Dans le menu de gauche, cliquez sur **Approbations** pour afficher la liste des demandes d’accès en attente d’approbation.

1. Dans l’onglet **En attente**, recherchez la demande.

## <a name="approve-or-deny-request"></a>Approuver ou refuser la demande

Une fois que vous avez ouvert une demande d’accès en attente d’approbation, vous pouvez consulter les détails qui vous aideront à prendre la décision.

**Rôle prérequis :** Approbateur

1. Cliquez sur le lien **Afficher** pour ouvrir le volet Demande d’accès.

1. Cliquez sur **Détails** pour afficher les détails de la demande d’accès.

    Les détails incluent le nom d’utilisateur, l’organisation, la date de début et de fin le cas échéant, la justification professionnelle, la date de soumission de la demande et la date d’expiration de la demande.

1. Cliquez sur **Approuver** ou **Refuser**.

1. Si nécessaire, indiquez un motif.

    ![Portail Mon Accès - Demande d’accès](./media/entitlement-management-shared/my-access-approve-request.png)

1. Cliquez sur **Soumettre** pour soumettre votre décision.

    Si une stratégie est configurée avec plusieurs approbateurs, un seul approbateur doit prendre une décision concernant l’approbation en attente. Une fois qu’un approbateur a rendu sa décision concernant la demande d’accès, celle-ci est terminée et n’est plus disponible pour les autres approbateurs. Les autres approbateurs peuvent voir la décision et l’approbateur qui est intervenu dans leur portail Mon Accès. À ce stade, seule l’approbation en une étape est prise en charge.

    Si aucun des approbateurs configurés ne peut approuver ou refuser la demande d’accès, celle-ci expire après la période définie. L’utilisateur est averti que sa demande d’accès a expiré et qu’il doit la soumettre à nouveau.

## <a name="next-steps"></a>Étapes suivantes

- [Demander l’accès à un package d’accès](entitlement-management-request-access.md)
- [Processus de demande et notifications par e-mail](entitlement-management-process.md)
