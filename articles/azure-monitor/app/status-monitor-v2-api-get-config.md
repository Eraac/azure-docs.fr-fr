---
title: 'Référence sur l’API Azure Status Monitor v2 : Obtenir la configuration | Microsoft Docs'
description: Référence sur l’API Azure Status Monitor v2. Get-ApplicationInsightsMonitoringConfig. Surveillez les performances de site web sans avoir à redéployer le site web. Fonctionne avec les applications web ASP.NET hébergées en local, dans des machines virtuelles ou sur Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 1f7061c9823ddc8ff7f8f42976041f1c9ff68fc0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514400"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig-v021-alpha"></a>API Status Monitor v2 : Get-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Cet article décrit une cmdlet appartenant au [module PowerShell Az.ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou n’être pas prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Description

Obtient le fichier de configuration et imprime les valeurs dans la console.

> [!IMPORTANT] 
> Cette cmdlet requiert une session PowerShell avec des autorisations d’administrateur.

## <a name="examples"></a>Exemples

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>parameters

Aucun paramètre requis.

## <a name="output"></a>Sortie


#### <a name="example-output-from-reading-the-config-file"></a>Exemple de sortie à partir de la lecture du fichier de configuration

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Étapes suivantes

  Affichez vos données de télémétrie :
 - [Explorez les métriques](../../azure-monitor/app/metrics-explorer.md) pour surveiller les performances et l’utilisation.
- [Effectuez des recherches dans les événements et les journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes.
- Utilisez la fonctionnalité [Analytics](../../azure-monitor/app/analytics.md) pour des requêtes plus élaborées.
- [Créez des tableaux de bord](../../azure-monitor/app/overview-dashboard.md).
 
 Ajoutez des données de télémétrie :
 - [Créez des tests web](monitor-web-app-availability.md) pour vous assurer que votre site reste actif.
- [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de la page web et activer le suivi des appels.
- [Ajoutez le kit de développement logiciel (SDK) Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin de pouvoir insérer un suivi et journaliser les appels.
 
 Plus d’opérations avec Status Monitor v2 :
 - Utilisez notre guide pour [détecter un problème](status-monitor-v2-troubleshoot.md) dans Status Monitor v2.
 - Apportez des modifications à la configuration à l’aide de la cmdlet [Set config](status-monitor-v2-api-set-config.md).
