---
title: Autorisations dans Azure Security Center | Microsoft Docs
description: Cet article explique comment Azure Security Center utilise le contrôle d’accès en fonction du rôle pour affecter des autorisations aux utilisateurs et identifie les actions autorisées pour chaque rôle.
services: security-center
cloud: na
documentationcenter: na
author: rkarlin
manager: barbkess
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: aa6c154e14fbf5291e2493f4f27b9d4501ba18f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60905617"
---
# <a name="permissions-in-azure-security-center"></a>Autorisations dans Azure Security Center

Azure Security Center utilise le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/role-assignments-portal.md) qui fournit des [rôles intégrés](../role-based-access-control/built-in-roles.md) susceptibles d’être affectés à des utilisateurs, des groupes et des services dans Azure.

Security Center évalue la configuration de vos ressources pour identifier les vulnérabilités et les problèmes de sécurité. Dans Security Center, vous ne voyez les informations relatives à une ressource que lorsque vous avez reçu le rôle de propriétaire, de collaborateur ou de lecteur pour l’abonnement ou le groupe de ressources auquel appartient la ressource.

Outre ces rôles, il existe deux rôles propres à Security Center :

* **Lecteur Sécurité** : l’utilisateur ayant ce rôle dispose de droits d’affichage dans Security Center. Il peut afficher les recommandations, les alertes, la stratégie de sécurité actuelle et les états de sécurité, mais ne peut pas apporter de modifications.
* **Administrateur de la sécurité** : l’utilisateur ayant ce rôle dispose des mêmes droits que le Lecteur Sécurité. Il peut en outre modifier la stratégie de sécurité et ignorer les alertes et les recommandations.

> [!NOTE]
> Les rôles de sécurité que sont le lecteur Sécurité et l’administrateur de la sécurité ont uniquement accès à Security Center. Les rôles de sécurité n’ont pas accès aux autres services d’Azure (par exemple, Stockage, Web et mobile ou Internet des objets).
>
>

## <a name="roles-and-allowed-actions"></a>Rôles et actions autorisées

Le tableau suivant affiche les rôles et les actions autorisées dans Security Center. Un X indique que l’action est autorisée pour ce rôle.

| Rôle | Modifier une stratégie de sécurité | Appliquer des recommandations de sécurité à une ressource | Ignorer les alertes et les recommandations | Afficher les alertes et les recommandations |
|:--- |:---:|:---:|:---:|:---:|
| Propriétaire de l’abonnement | X | X | X | X |
| Collaborateur de l’abonnement | -- | X | X | X |
| Propriétaire du groupe de ressources | -- | X | -- | X |
| Collaborateur du groupe de ressources | -- | X | -- | X |
| Lecteur | -- | -- | -- | X |
| Security Administrator | X | -- | X | X |
| Lecteur de sécurité | -- | -- | -- | X |

> [!NOTE]
> Nous vous recommandons d’attribuer le rôle le moins permissif permettant aux utilisateurs d’effectuer leurs tâches. Par exemple, affectez le rôle Lecteur aux utilisateurs qui n’ont besoin que de consulter des informations sur l’intégrité de la sécurité d’une ressource sans effectuer aucune action, telles que l’application des recommandations ou la modification des stratégies.
>
>

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a expliqué comment Security Center utilise le contrôle d’accès en fonction du rôle pour affecter des autorisations aux utilisateurs et a identifié les actions autorisées pour chaque rôle. Maintenant que vous êtes familiarisé avec les affectations de rôles nécessaires pour surveiller l’état de sécurité de votre abonnement, modifier les stratégies de sécurité et appliquer les recommandations, découvrez comment :

- [Définir des stratégies de sécurité dans Security Center](tutorial-security-policy.md)
- [Gérer les recommandations de sécurité dans Security Center](security-center-recommendations.md)
- [Surveiller l’intégrité de la sécurité de vos ressources Azure](security-center-monitoring.md)
- [Gérer et répondre aux alertes de sécurité dans Security Center](security-center-managing-and-responding-alerts.md)
- [Surveiller les solutions de sécurité des partenaires](security-center-partner-solutions.md)
