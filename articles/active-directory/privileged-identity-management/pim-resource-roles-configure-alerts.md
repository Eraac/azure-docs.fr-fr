---
title: Configurer des alertes de sécurité pour les rôles de ressources Azure dans PIM – Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer des alertes de sécurité pour les rôles de ressources Azure dans Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7ce8b79644a9ffc9481ba825ec5623a9268983
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476329"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Configurer des alertes de sécurité pour les rôles de ressources Azure dans PIM
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) génère des alertes en cas d’activité suspecte ou non fiable dans votre environnement. Lorsqu’une alerte est déclenchée, elle s’affiche sur la page Alertes. 

![Page des alertes des ressources Azure répertorie les alertes, le niveau de risque et leur nombre](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Consulter les alertes
Sélectionnez une alerte pour obtenir un rapport qui répertorie les utilisateurs ou les rôles à l’origine de son déclenchement et fournit des conseils pour sa correction.

![Rapport d’alerte montrant la dernière durée d’analyse, la description, les mesures d’atténuation, le type, la gravité, l’impact sur la sécurité et la manière d’éviter ce problème la prochaine fois](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alertes
| Alerte | Severity | Déclencheur | Recommandation |
| --- | --- | --- | --- |
| **Trop de propriétaires affectés à une ressource** |Moyenne |Un trop grand nombre d’utilisateurs possèdent le rôle de propriétaire. |Passez en revue les utilisateurs de la liste et réaffectez-en certains à des rôles moins privilégiés. |
| **Trop de propriétaires permanents affectés à une ressource** |Moyenne |Trop d’utilisateurs sont affectés de façon permanente à un rôle. |Passez en revue les utilisateurs sur la liste et réaffectez-en certains pour demander une activation pour l’utilisation du rôle. |
| **Rôle dupliqué créé** |Moyenne |Plusieurs rôles ont les mêmes critères. |Utilisez un seul de ces rôles. |


### <a name="severity"></a>Severity
* **Haute** : nécessite une action immédiate, car il s’agit d’une violation de stratégie. 
* **Moyenne** : ne nécessite pas d’action immédiate, mais signale une violation potentielle de la stratégie.
* **Faible** : ne nécessite pas d’action immédiate, mais suggère une modification de la stratégie.

## <a name="configure-security-alert-settings"></a>Configurez les paramètres d'alerte de sécurité
Dans la page Alertes, accédez aux **paramètres**.

![Page des alertes avec paramètres mis en surbrillance](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Personnalisez les paramètres des différentes alertes pour travailler avec votre environnement et les objectifs de sécurité.

![Page de configuration d’une alerte permettant d’activer et de configurer des paramètres](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les paramètres des rôles de ressources Azure dans PIM](pim-resource-roles-configure-role-settings.md)
