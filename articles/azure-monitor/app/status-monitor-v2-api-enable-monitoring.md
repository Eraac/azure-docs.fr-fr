---
title: 'Référence sur l’API Azure Status Monitor v2 : Activer la surveillance | Microsoft Docs'
description: Référence sur l’API Azure Status Monitor v2. Enable-ApplicationInsightsMonitoring. Surveillez les performances de site web sans avoir à redéployer le site web. Fonctionne avec les applications web ASP.NET hébergées en local, dans des machines virtuelles ou sur Azure.
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
ms.openlocfilehash: e0d5363e253e89b32b5eca14366504f0ace39043
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479645"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v031-alpha"></a>API Status Monitor v2 : Enable-ApplicationInsightsMonitoring (v0.3.1-alpha)

Cet article décrit une cmdlet appartenant au [module PowerShell Az.ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Description

Permet la surveillance sans code des applications IIS sur un ordinateur cible.

Cette cmdlet modifie l’élément applicationHost.config de l’application IIS et définit des clés de Registre.
Il crée également un fichier applicationinsights.ikey.config, qui définit la clé d’instrumentation utilisée par chaque application.
IIS charge RedfieldModule au démarrage, qui injecte le Kit de développement logiciel (SDK) Application Insights dans les applications à mesure qu’elles démarrent.
Redémarrez IIS pour que vos modifications prennent effet.

Après avoir activé la surveillance, nous vous recommandons d’utiliser [Métriques en temps réel](live-stream.md) pour vérifier rapidement si votre application nous envoie des données de télémétrie.


> [!NOTE] 
> - Pour commencer, vous avez besoin d’une clé d’instrumentation. Pour plus d’informations, consultez [Create an Application Insights resource](create-new-resource.md#copy-the-instrumentation-key) (Création d’une ressource Application Insights).
> - Cette cmdlet nécessite que vous lisiez et acceptiez notre licence et déclaration de confidentialité.

> [!IMPORTANT] 
> Cette cmdlet requiert une session PowerShell avec des autorisations d’administrateur, ainsi qu’une stratégie d’exécution avec élévation des privilèges. Pour plus d’informations, consultez [Run PowerShell as Admin with an elevated execution policy](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy) (Exécuter PowerShell en tant qu’administrateur avec une stratégie d’exécution avec élévation de privilèges).

## <a name="examples"></a>Exemples

### <a name="example-with-a-single-instrumentation-key"></a>Exemple avec une clé d’instrumentation unique
Dans cet exemple, toutes les applications sur l’ordinateur actuel reçoivent une clé d’instrumentation unique.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Exemple avec un mappage de clé d’instrumentation
Dans cet exemple :
- `MachineFilter` effectue la correspondance avec l’ordinateur actuel en utilisant le caractère générique `'.*'`.
- `AppFilter='WebAppExclude'` fournit une clé d’instrumentation `null`. L’application spécifiée ne sera pas instrumentée.
- `AppFilter='WebAppOne'` attribue à l’application spécifiée une clé d’instrumentation unique.
- `AppFilter='WebAppTwo'` attribue à l’application spécifiée une clé d’instrumentation unique.
- Enfin, `AppFilter` utilise également le caractère générique `'.*'` pour établir les correspondances de toutes les applications web pour lesquelles les règles précédentes n’ont effectué aucune correspondance, et attribuer une clé d’instrumentation par défaut.
- Des espaces ont été ajoutées pour faciliter la lisibilité.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>parameters

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Obligatoire.** Utilisez ce paramètre pour fournir une clé d’instrumentation unique que l’ensemble des applications utiliseront sur l’ordinateur cible.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Obligatoire.** Utilisez ce paramètre pour fournir plusieurs clés d’instrumentation et un mappage des clés d’instrumentation utilisées par chaque application.
Vous pouvez créer un script d’installation unique pour plusieurs ordinateurs en définissant le paramètre `MachineFilter`.

> [!IMPORTANT]
> Les applications effectueront le mappage avec les règles dans l’ordre d’apparition de ces dernières. Par conséquent, vous devez d’abord spécifier les règles les plus spécifiques et finir par les règles les plus génériques.

#### <a name="schema"></a>Schéma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** est une expression régulière C# obligatoire associée au nom de la machine virtuelle ou de l’ordinateur.
    - ’.*’ effectue la correspondance avec tous les éléments
    - ’ComputerName’ effectue la correspondance avec les ordinateurs présentant le nom exact spécifié uniquement.
- **AppFilter** est une expression régulière C# obligatoire associée au nom de la machine virtuelle ou de l’ordinateur.
    - ’.*’ effectue la correspondance avec tous les éléments
    - ’ComputerName’ effectue la correspondance avec les applications IIS présentant le nom exact spécifié uniquement.
- **InstrumentationKey** est requis pour activer la surveillance des applications qui correspondent aux deux filtres précédents.
    - Conservez la valeur null si vous souhaitez définir des règles pour exclure la surveillance.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Facultatif.** Utilisez cette instruction switch pour permettre au moteur d’instrumentation de collecter des événements et des messages sur ce qui se passe pendant l’exécution d’un processus managé. Ces événements et les messages incluent les codes de résultat de dépendance, les verbes HTTP et le texte de commande SQL.

Le moteur d’instrumentation ajoute une surcharge et est désactivé par défaut.

### <a name="-acceptlicense"></a>-AcceptLicense
**Facultatif.** Utilisez cette instruction switch pour accepter la licence et la déclaration de confidentialité dans les installations sans affichage.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Lorsque vous avez un cluster de serveurs web, il se peut que vous utilisiez une [configuration partagée](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
HttpModule ne peut pas être injecté dans cette configuration partagée.
Ce script échoue avec le message indiquant que des étapes d’installation supplémentaires sont requises.
Utilisez ce commutateur pour ignorer cette vérification et poursuivre l’installation des prérequis. Pour plus d’informations, consultez [known conflict-with-iis-shared-configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Verbose
**Paramètre commun.** Utilisez cette instruction switch pour afficher les journaux détaillés.

### <a name="-whatif"></a>-WhatIf 
**Paramètre commun.** Utilisez cette instruction switch pour tester et valider vos paramètres d’entrée sans réellement activer la surveillance.

## <a name="output"></a>Output


#### <a name="example-output-from-a-successful-enablement"></a>Exemple de sortie à partir d’une activation réussie

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="next-steps"></a>Étapes suivantes

  Affichez vos données de télémétrie :
 - [Explorez les métriques](../../azure-monitor/app/metrics-explorer.md) pour surveiller les performances et l’utilisation.
- [Effectuez des recherches dans les événements et les journaux](../../azure-monitor/app/diagnostic-search.md) pour diagnostiquer les problèmes.
- [Utilisez la fonctionnalité Analytics](../../azure-monitor/app/analytics.md) pour des requêtes plus élaborées.
- [Créez des tableaux de bord](../../azure-monitor/app/overview-dashboard.md).
 
 Ajoutez des données de télémétrie :
 - [Créez des tests web](monitor-web-app-availability.md) pour vous assurer que votre site reste actif.
- [Ajoutez la télémétrie de client web](../../azure-monitor/app/javascript.md) pour afficher les exceptions à partir du code de la page web et activer le suivi des appels.
- [Ajoutez le Kit de développement logiciel (SDK) Application Insights à votre code](../../azure-monitor/app/asp-net.md) afin de pouvoir insérer un suivi et journaliser les appels.
 
 Plus d’opérations avec Status Monitor v2 :
 - Utilisez notre guide pour [détecter un problème](status-monitor-v2-troubleshoot.md) dans Status Monitor v2.
 - [Obtenez la configuration](status-monitor-v2-api-get-config.md) pour confirmer que vos paramètres ont été enregistrés correctement.
 - [Obtenez l’état](status-monitor-v2-api-get-status.md) pour inspecter la surveillance.
