---
title: 'Référence sur l’API Azure Status Monitor v2 : Obtenir l’état | Microsoft Docs'
description: Référence sur l’API Azure Status Monitor v2. Get-ApplicationInsightsMonitoringStatus. Surveillez les performances de site web sans avoir à redéployer le site web. Fonctionne avec les applications web ASP.NET hébergées localement, dans des machines virtuelles ou sur Azure.
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
ms.openlocfilehash: 860226320fe1a546798cc462e4e5c06d4b9228cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514304"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringstatus-v022-alpha"></a>API Status Monitor v2 : Get-ApplicationInsightsMonitoringStatus (v0.2.2-alpha)

Cet article décrit une cmdlet appartenant au [module PowerShell Az.ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Description

Permet la résolution des problèmes du module PowerShell utilisé.
Cette applet de commande signale les informations de version et les informations sur les fichiers de clés nécessaires à la supervision.
Des paramètres supplémentaires fournissent d’autres rapports sur l’état de surveillance.

> [!IMPORTANT] 
> Cette cmdlet requiert une session PowerShell avec des autorisations d’administrateur.

## <a name="examples"></a>Exemples


### <a name="example-basic-information"></a>Exemple : Informations de base

Exécutez `Get-ApplicationInsightsMonitoringStatus` pour afficher des informations sur le module actuel :

```
PS C:\> Get-ApplicationInsightsMonitoringStatus

PowerShell Module version:
0.2.2-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content Exists: False
ConfigurationPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\applicationInsights.ikey.config Exists: True
ManagedHttpModuleHelperPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll Exists: True
RedfieldIISModulePath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll Exists: True
InstrumentationEngine86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll Exists: True
InstrumentationEngine64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll Exists: True
InstrumentationEngineExtensionHost86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll Exists: True
InstrumentationEngineExtensionHost64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll Exists: True
InstrumentationEngineExtensionConfig86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config Exists: True
InstrumentationEngineExtensionConfig64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config Exists: True
ApplicationInsightsSdkPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\Runtime\Microsoft.ApplicationInsights.dll Exists: True


Machine Identifier:
0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF0123456789ABCDEF
```

### <a name="example-runtime-status"></a>Exemple : État d’exécution

Vous pouvez inspecter le processus sur l’ordinateur instrumenté pour vérifier si toutes les DLL sont chargées. Si la surveillance fonctionne, au moins 12 DLL doivent être chargées.

Exécutez la commande suivante `Get-ApplicationInsightsMonitoringStatus -InspectProcess` :


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

## <a name="parameters"></a>parameters

### <a name="no-parameters"></a>(Aucun paramètre)

Par défaut, cette applet de commande signale les numéros de version et les chemins d’accès des DLL nécessaires à la supervision.

Utilisez cette option si vous avez besoin identifier la version de n’importe quelle DLL, y compris le SDK Application Insights.


### <a name="-inspectprocess"></a>-InspectProcess

**Facultative**. Utilisez ce paramètre pour indiquer si IIS est en cours d’exécution.
Il télécharge également des outils externes pour déterminer si les DLL sont chargées dans le runtime d’IIS.


Si ce processus échoue pour une raison quelconque, vous pouvez exécuter ces commandes manuellement :
- iisreset.exe /status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"


### <a name="-force"></a>-Force

**Facultative**. Utilisé uniquement avec InspectProcess. Utilisez ce commutateur pour ignorer l’invite utilisateur qui apparaît avant le téléchargement d’outils supplémentaires.


## <a name="next-steps"></a>Étapes suivantes

 Plus d’opérations avec Status Monitor v2 :
 - Utilisez notre guide pour [détecter un problème](status-monitor-v2-troubleshoot.md) dans Status Monitor v2.
