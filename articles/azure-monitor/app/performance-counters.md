---
title: Compteurs de performances dans Application Insights | Microsoft Docs
description: Surveillez les compteurs de performances système et .NET personnalisés dans Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mbullwin
ms.openlocfilehash: 0ec64a5ae412fb4a1900021fefcb7d9112b1b019
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66255338"
---
# <a name="system-performance-counters-in-application-insights"></a>Compteurs de performances système dans Application Insights

Windows offre un large éventail de [compteurs de performance](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters) tels que le niveau d’occupation du processeur, la mémoire, le disque et l’utilisation du réseau. Vous pouvez également définir vos propres compteurs de performances. La collection de compteurs de performance est prise en charge tant que votre application est exécutée sous IIS sur un hôte ou une machine virtuelle local sur lequel vous disposez d’un accès administratif. Bien que les applications exécutées en tant qu’applications web Azure n’aient pas d’accès direct aux compteurs de performance, un sous-ensemble des compteurs disponibles sont collectés par Application Insights.

## <a name="view-counters"></a>Afficher des compteurs

Le volet Métriques affiche un ensemble de compteurs de performances par défaut.

![Compteurs de performances signalés dans Application Insights](./media/performance-counters/performance-counters.png)

Les compteurs par défaut actuels qui sont configurés pour être collectés pour les applications Web ASP.NET/ASP.NET Core sont :

         - % Process\\Processor Time
         - % Process\\Processor Time Normalized
         - Memory\\Available Bytes
         - ASP.NET Requests/Sec
         - .NET CLR Exceptions Thrown / sec
         - ASP.NET ApplicationsRequest Execution Time
         - Process\\Private Bytes
         - Process\\IO Data Bytes/sec
         - ASP.NET Applications\\Requests In Application Queue
         - Processor(_Total)\\% Processor Time

## <a name="add-counters"></a>Ajouter des compteurs

Si le compteur de performances que vous souhaitez n’est pas inclus dans la liste des métriques, vous pouvez l’y ajouter.

1. Découvrez les compteurs disponibles sur votre serveur à l’aide de la commande PowerShell suivante sur le serveur local :

    `Get-Counter -ListSet *`

    (Voir [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).)
2. Ouvrez ApplicationInsights.config.

   * Si vous avez ajouté Application Insights à votre application pendant le développement, modifiez ApplicationInsights.config dans votre projet, puis redéployez-le sur vos serveurs.
3. Modifiez la directive du collecteur de performances :

```XML

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

> [!NOTE]
> Les applications ASP.NET Core n’ont pas `ApplicationInsights.config`. Par conséquent, la méthode ci-dessus n’est pas valable pour les applications ASP.NET Core.

Vous pouvez capturer les compteurs standard et ceux que vous avez implémentés vous-même. `\Objects\Processes` est un exemple de compteur standard disponible sur tous les systèmes Windows. `\Sales(photo)\# Items Sold` est un exemple de compteur personnalisé qui peut être implémenté dans un service web.

Le format est le suivant : `\Category(instance)\Counter"` ou, pour les catégories qui ne présentent aucune instance : `\Category\Counter`, tout simplement.

`ReportAs` est requis pour les noms de compteurs qui ne correspondent pas `[a-zA-Z()/-_ \.]+`, à savoir qu’ils contiennent des caractères ne se trouvant pas dans les ensembles suivants : lettres, crochets, barre oblique, trait d’union, trait de soulignement, espace, point.

Si vous spécifiez une instance, elle est collectée en tant que dimension « CounterInstanceName » de la mesure signalée.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Collecte des compteurs de performance en code pour les applications web ASP.NET ou les applications de console .NET/.NET Core
Pour collecter les compteurs de performances système et les envoyer à Application Insights, vous pouvez adapter l’extrait de code ci-dessous :


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Ou vous pouvez effectuer la même opération avec les mesures personnalisées que vous avez créées :

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Collecte des compteurs de performance en code pour les applications web ASP.NET Core

Modifiez la méthode `ConfigureServices` dans votre classe `Startup.cs` comme suit :

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Compteurs de performances dans Analytics
Vous pouvez rechercher et afficher des rapports de compteur de performances dans [Analytics](../../azure-monitor/app/analytics.md).

Le schéma **compteur de performances** expose les noms `category`, `counter` et `instance` nom de chaque compteur de performance.  Dans les données de télémétrie de chaque application, vous voyez uniquement les compteurs de cette application. Par exemple, pour voir les compteurs disponibles : 

![Compteurs de performances dans Application Insights Analytics](./media/performance-counters/analytics-performance-counters.png)

(« Instance » fait ici référence à l’instance de compteur de performances, pas à l’instance de rôle ou de machine serveur. Le nom d’instance de compteur de performances segmente généralement les compteurs comme le temps processeur par le nom du processus ou de l’application.)

Pour obtenir un graphique présentant la mémoire disponible sur une période récente : 

![Graphique temporel dans Application Insights Analytics](./media/performance-counters/analytics-available-memory.png)

Comme les autres données de télémétrie, **performanceCounters** possède également une colonne `cloud_RoleInstance` qui indique l’identité de l’instance de serveur hôte sur lequel votre application est en cours d’exécution. Par exemple, pour comparer les performances de votre application sur des ordinateurs différents : 

![Performances segmentées par instances de rôle d’Application Insights Analytics](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Nombres ASP.NET et Application Insights

*Quelle est la différence entre le taux d’exceptions et les mesures d’exceptions ?*

* *taux d’exceptions* est un compteur de performances système. Le CLR compte l’ensemble des exceptions gérées et non gérées qui sont levées et divise le total d’un intervalle d'échantillonnage par la longueur de cet intervalle. Le Kit de développement logiciel (SDK) Application Insights collecte ce résultat et l’envoie au portail.

* *Exceptions* représente le nombre de rapports TrackException reçus par le portail au cours de l’intervalle d’échantillonnage du graphique. Il comprend uniquement les exceptions gérées pour lesquelles vous avez écrit des appels TrackException dans votre code et n’inclut pas toutes les [exceptions non gérées](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Compteurs de performance pour applications exécutés dans Azure Web Apps

Les applications ASP.NET et ASP.NET Core déployées sur les applications web d’Azure s’exécutent dans un environnement de bac à sable spécial. Cet environnement ne permet pas l’accès direct aux compteurs de performance du système. Cependant, un sous-ensemble limité de compteurs sont exposés en tant que variables d’environnement comme décrit [ici](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). Le Kit de développement logiciel (SDK) Application Insights pour ASP.NET et ASP.NET Core collecte les compteurs de performance des applications web Azure à partir de ces variables d’environnement spéciales. Seul un sous-ensemble de compteurs est disponible dans cet environnement. Sa liste complète se trouve [ici](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs).

## <a name="performance-counters-in-aspnet-core-applications"></a>Compteurs de performances dans les applications ASP.NET Core

* Le [Kit de développement logiciel (SDK) ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.4.1 et ultérieure collecte les compteurs de performance si l’application fonctionne dans Azure Web App (Windows)

* Le Kit de développement logiciel (SDK) version 2.7.0-beta3 et ultérieure collecte les compteurs de performance si l’application s’exécute sous Windows, et vise `NETSTANDARD2.0` ou plus.
* Pour les applications ciblant le.NET Framework, les compteurs de performance sont supportés dans toutes les versions du Kit de développement logiciel (SDK).
* Cet article sera mis à jour lors de l’ajout de la prise en charge des compteurs de performance non-Windows.

## <a name="alerts"></a>Alertes
Comme d’autres mesures, vous pouvez [définir une alerte](../../azure-monitor/app/alerts.md) pour vous avertir si un compteur de performances dépasse une limite que vous spécifiez. Ouvrez le volet Alertes et cliquez sur Ajouter une alerte.

## <a name="next"></a>Étapes suivantes

* [Suivi des dépendances](../../azure-monitor/app/asp-net-dependencies.md)
* [Suivi des exceptions](../../azure-monitor/app/asp-net-exceptions.md)

