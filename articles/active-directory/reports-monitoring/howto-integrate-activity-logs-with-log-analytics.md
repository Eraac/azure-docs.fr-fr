---
title: Diffuser en continu les journaux d’activité Azure Active Directory vers les journaux d’activité Azure Monitor | Microsoft Docs
description: Découvrez comment intégrer des journaux d’activité Azure Active Directory aux journaux d’activité Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 213fb6e73ae2fc4314320d0e3e593632d8eb7f85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60437307"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Intégrer des journaux d’activité Azure AD aux journaux d’activité Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Les journaux d’activité Azure Monitor vous permettent d’interroger les données pour trouver des événements particuliers, analyser les tendances et effectuer une corrélation entre différentes sources de données. Avec l’intégration des journaux d’activité d’Azure AD aux journaux d’activité Azure Monitor, vous pouvez maintenant effectuer les tâches suivantes :

 * Comparer vos journaux d’activité de connexion Azure AD aux journaux d’activité de sécurité publiés par le Centre de sécurité Azure.

 * Résoudre les goulots d’étranglement de performances sur la page de connexion de votre application en mettant en corrélation les données de performances d’application à partir d’Azure Application Insights.  

La vidéo suivante issue d’une session Ignite montre les avantages de l’utilisation de journaux d’activité Azure Monitor pour Azure AD dans des scénarios utilisateur pratique.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

Cet article explique comment intégrer des journaux d’activité Azure Active Directory (Azure AD) à Azure Monitor.

## <a name="supported-reports"></a>Rapports pris en charge

Vous pouvez acheminer les journaux d’activité d’audit et authentifier des journaux d’activité vers les journaux d’activité Azure Monitor en vue d’une analyse ultérieure. 

* **Journaux d’audit**: Le [rapport d’activité des journaux d’audit](concept-audit-logs.md) vous permet de consulter l’historique de toutes les tâches effectuées dans votre locataire.
* **Journaux d’activité de connexion** : Le [rapport d’activité de connexion](concept-sign-ins.md) vous permet d’identifier qui a effectué les tâches consignées dans les journaux d’audit.

> [!NOTE]
> Les journaux d’activité d’audit et de connexions associés à B2C ne sont pas pris en charge pour le moment.
>

## <a name="prerequisites"></a>Prérequis 

Pour utiliser cette fonctionnalité, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’en avez pas, vous pouvez demander un [essai gratuit](https://azure.microsoft.com/free/).
* Un locataire Azure AD.
* Utilisateur considéré comme *administrateur général* ou *administrateur de la sécurité* pour un locataire Azure AD.
* Espace de travail Log Analytics dans votre abonnement Azure. Découvrez comment [créer un espace de travail Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="send-logs-to-azure-monitor"></a>Envoyer des journaux à Azure Monitor

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 

2. Sélectionnez **Azure Active Directory** > **Paramètres de diagnostic** -> **Ajouter le paramètre de diagnostic**. Vous pouvez également sélectionner **Exporter les paramètres** à partir de la page **Journaux d’audit** ou **Connexions** pour accéder à la page de configuration des paramètres de diagnostic.  
    
3. Dans le menu **Paramètres de diagnostic**, cochez la case **Envoyer vers l’espace de travail Log Analytics**, puis sélectionnez **Configurer**.

4. Sélectionnez l’espace de travail Log Analytics vers lequel vous souhaitez envoyer les journaux d’activité ou créez un nouvel espace de travail dans la boîte de dialogue fournie.  

5. Effectuez une ou plusieurs des actions suivantes :
    * Cochez la case **AuditLogs** pour envoyer les journaux d’audit vers l’espace de travail Log Analytics. 
    * Cochez la case **SignInLogs** pour envoyer les journaux d’activité de connexion vers l’espace de travail Log Analytics.

6. Sélectionnez **Enregistrer** pour enregistrer le paramètre.

    ![Paramètres de diagnostic](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Au bout de 15 minutes, vérifiez que les événements sont diffusés dans votre espace de travail Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

* [Analyser les journaux d’activité Azure AD avec les journaux d’activité Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Installer et utiliser les vues Log Analytics pour Azure Active Directory](howto-install-use-log-analytics-views.md)