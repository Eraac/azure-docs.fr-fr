---
title: Pendant combien de temps les données de rapport sont-elles conservées par Azure AD ? | Microsoft Docs
description: Découvrez la durée pendant laquelle Azure stocke les différents types de données de rapport.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41fa12c9d79d14a6602d995ed93b5d1a23be8a4d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65781040"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Pendant combien de temps les données de rapport sont-elles conservées par Azure AD ?

Dans cet article, vous allez en savoir plus sur les stratégies de rétention des données pour les différents rapports d’activité dans Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Quand Azure AD commence-t-il à collecter des données ?

| Édition d’Azure AD | Début de la collection |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Lorsque vous souscrivez un abonnement |
| Azure AD Gratuit <br /> Azure AD Standard | La première fois que vous ouvrez le [panneau Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou utilisez les [API de création de rapports](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Quand les données d’activité sont-elles disponibles dans le portail Azure ?

- **Immédiatement** si vous avez déjà travaillé avec des rapports dans le portail Azure.
- **Dans les 2 heures** si vous n’avez pas encore activé la création de rapports dans le portail Azure.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Au bout de combien de temps puis-je consulter les données d'activité après avoir obtenu une licence Premium ?

Si vous avez déjà des données d'activités avec votre licence gratuite, vous pouvez les voir immédiatement lors de la mise à niveau. Si vous n'avez pas encore de données, il faudra un ou deux jours pour que les données apparaissent dans les rapports après la mise à niveau vers une licence Premium.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Puis-je voir les données du mois dernier après avoir obtenu une licence Azure AD Premium ?

Si vous êtes récemment passé à une version Premium (y compris une version d’essai), vous pouvez voir les données jusqu’à 7 jours dans un premier temps. Lorsque les données s’accumulent, vous pouvez voir les données au cours des 30 derniers jours.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Quand Azure AD commence-t-il à collecter des données de signaux de sécurité ?  

Pour les signaux de sécurité, le processus de collection démarre lorsque vous choisissez d’utiliser **Identity Protection Center**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Pendant combien de temps les données sont-elles conservées par Azure AD ?

**Rapports d’activité**    

| Rapport                 | Azure AD Gratuit | Azure AD Standard | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--            | :--                 | :--                 |
| Journaux d’audit             | 7 jours        |  7 jours        | 30 jours             | 30 jours             |
| Connexions               | N/A           |  N/A           | 30 jours             | 30 jours             |
| Utilisation d’Azure MFA        | 30 jours       |  30 jours       | 30 jours             | 30 jours             |

Vous pouvez conserver les données d'activité d'audit et de connexion au-delà de la période de conservation par défaut décrite ci-dessus en les acheminant vers un compte de stockage Azure à l'aide d'Azure Monitor. Pour plus d’informations, consultez [Archiver des journaux d’activité Azure AD sur un compte de stockage Azure](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Signaux de sécurité**

| Rapport         | Azure AD Gratuit | Azure AD Standard | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--            | :--                 | :--                 |
| Les utilisateurs à risque  | 7 jours        | 7 jours         | 30 jours             | 90 jours             |
| Connexions risquées | 7 jours        | 7 jours         |  30 jours            | 90 jours             |

---
