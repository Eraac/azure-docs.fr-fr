---
title: Suivi des dépendances dans Azure Application Insights | Microsoft Docs
description: Supervisez les appels de dépendances entre Application Insights et votre application locale ou votre application web Microsoft Azure.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: mbullwin
ms.openlocfilehash: a233299b86100c7e43291ffb6448d576ef6072f0
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302560"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Suivi des dépendances dans Azure Application Insights 

Un *dépendance* est un composant externe qui est appelé par votre application. Il s’agit habituellement d’un service appelé à l’aide de HTTP, d’une base de données ou d’un système de fichiers. [Application Insights](../../azure-monitor/app/app-insights-overview.md) mesure la durée des appels de dépendances, indique si les appels ont réussi ou échoué et fournit diverses autres informations comme le nom des dépendances. Vous pouvez examiner des appels de dépendances spécifiques et les mettre en corrélation avec les requêtes et les exceptions.

## <a name="automatically-tracked-dependencies"></a>Dépendances suivies automatiquement

Les SDK Application Insights pour .NET et .NET Core sont fournis avec `DependencyTrackingTelemetryModule`, un module de télémétrie qui collecte automatiquement les dépendances. Cette collection des dépendances est activée automatiquement pour les applications [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) et [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), si celles-ci sont configurées conformément aux instructions décrites dans leur documentation officielle liée. `DependencyTrackingTelemetryModule` est fourni dans [ce](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) package NuGet et est activé automatiquement quand l’un des packages NuGet `Microsoft.ApplicationInsights.Web` ou `Microsoft.ApplicationInsights.AspNetCore` est utilisé.

 `DependencyTrackingTelemetryModule` suit automatiquement les dépendances suivantes :

|Les dépendances |Détails|
|---------------|-------|
|HTTP/HTTPS | Appels HTTP/HTTPS locaux ou distants |
|Appels WCF| Suivi automatique uniquement si les liaisons HTTP sont utilisées.|
|SQL | Appels effectués avec `SqlClient`. Consultez [cet article](#advanced-sql-tracking-to-get-full-sql-query) pour capturer une requête SQL.  |
|[Stockage Azure (Blob, Table, File d’attente)](https://www.nuget.org/packages/WindowsAzure.Storage/) | Appels effectués avec le client Stockage Azure. |
|[SDK client EventHub](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | Versions 1.1.0 et ultérieures. |
|[Kit de développement logiciel (SDK) client ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| Versions 3.0.0 et ultérieures. |
|Azure Cosmos DB | Suivi automatique uniquement si HTTP/HTTPS est utilisé. Le mode TCP ne sera pas capturé par Application Insights. |


## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>Configurer le suivi automatique des dépendances dans les applications console

Pour suivre automatiquement les dépendances des applications console .NET/.NET Core, installez le package Nuget `Microsoft.ApplicationInsights.DependencyCollector` et initialisez `DependencyTrackingTelemetryModule` de la façon suivante :

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>Comment fonctionne le suivi automatique des dépendances ?

Les dépendances sont automatiquement collectées avec l’une des techniques suivantes :

* Utilisation de l’instrumentation de code d’octet autour des méthodes sélectionnées. (InstrumentationEngine à partir de StatusMonitor ou de l’extension Azure Web App)
* Rappels d’EventSource
* Rappels de DiagnosticSource (dans la dernière version des SDK .NET/.NET Core)

## <a name="manually-tracking-dependencies"></a>Suivi manuel des dépendances

Voici des exemples de dépendances qui ne sont pas collectées automatiquement et nécessitent donc un suivi manuel.

* La surveillance d’Azure Cosmos DB est effectuée automatiquement uniquement si le protocole [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) est utilisé. Le mode TCP ne sera pas capturé par Application Insights.
* Redis

L’[API TrackDependency](api-custom-events-metrics.md#trackdependency), qui est utilisée par les modules de collecte automatique standard, vous permet de suivre manuellement les dépendances que le SDK ne collecte pas automatiquement.

Par exemple, si vous générez votre code avec un assembly que vous n’avez pas écrit vous-même, vous pouvez minuter tous les appels vers cet assembly afin de déterminer sa contribution dans votre temps de réponse. Pour afficher ces données dans les graphiques de dépendance d’Application Insights, envoyez-les en utilisant `TrackDependency`.

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

Vous pouvez aussi utiliser les méthodes d’extension `StartOperation` et `StopOperation` fournies par `TelemetryClient` pour suivre les dépendances manuellement, comme cela est expliqué [ici](custom-operations-tracking.md#outgoing-dependencies-tracking)

Si vous souhaitez désactiver le module de suivi des dépendances standard, supprimez la référence à DependencyTrackingTelemetryModule dans [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) pour les applications ASP.NET. Pour les applications ASP.NET Core, suivez les instructions données [ici](asp-net-core.md#configuring-or-removing-default-telemetrymodules).

## <a name="tracking-ajax-calls-from-web-pages"></a>Suivi des appels AJAX provenant de pages web

Pour les pages web, le SDK JavaScript Application Insights collecte automatiquement les appels AJAX en tant que dépendances, comme cela est décrit [ici](javascript.md#ajax-performance). Ce document se concentre sur les dépendances des composants serveur.

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>Suivi SQL avancé pour obtenir la requête SQL complète

Pour les appels SQL, le nom du serveur et de la base de données est toujours collecté et stocké comme le nom du `DependencyTelemetry` collecté. Il y a un champ supplémentaire appelé « data », qui peut contenir le texte de la requête SQL complète.

Pour les applications ASP.NET Core, il n’y a aucune étape supplémentaire à faire pour obtenir la requête SQL complète.

Pour les applications ASP.NET, la requête SQL complète est collectée par le biais de l’instrumentation de code d’octet, qui fait appel au moteur d’instrumentation. Vous devez donc effectuer des étapes supplémentaires spécifiques à la plateforme, comme décrit ci-dessous.

| Plateforme | Étape(s) nécessaire(s) pour obtenir la requête SQL complète |
| --- | --- |
| Application web Azure |Dans le Panneau de configuration de l’application web, [ouvrez le panneau Application Insights](../../azure-monitor/app/azure-web-apps.md) et activez les commandes SQL dans .NET |
| Serveur IIS (machine virtuelle Azure, locale, etc.) | [Installez Status Monitor sur le serveur où l’application est exécutée](../../azure-monitor/app/monitor-performance-live-website-now.md), puis redémarrez IIS.
| Service cloud Azure | Ajoutez une [tâche de démarrage pour installer StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> Votre application doit être intégrée au SDK ApplicationInsights au moment de la build en installant les packages NuGet pour les applications [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) ou [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) |
| IIS Express | Non pris en charge

Dans les cas ci-dessus, la bonne pratique pour vérifier que ce moteur d’instrumentation est correctement installé est de vous assurer que la version du SDK du `DependencyTelemetry` collecté est « rddp ». « rdddsd » ou « rddf » indique que les dépendances sont collectées par le biais de rappels de DiagnosticSource ou d’EventSource. La requête SQL complète n’est donc pas capturée.

## <a name="where-to-find-dependency-data"></a>Où trouver des données sur les dépendances

* [Mise en correspondance d’applications](app-map.md) visualise les dépendances entre votre application et les composants voisins.
* [Diagnostics de transaction](transaction-diagnostics.md) montre les données serveur mises en corrélation avec les composants unifiés.
* [Navigateurs](javascript.md#ajax-performance) montre les appels AJAX provenant des navigateurs des utilisateurs.
* Parcourez les requêtes lentes ou ayant échoué pour vérifier leurs appels de dépendance.
* Vous pouvez utiliser [Analytics](#logs-analytics) pour interroger des données de dépendances.

## <a name="diagnosis"></a> Diagnostiquer les demandes lentes

Chaque événement de demande est associé aux appels de dépendance, aux exceptions et aux autres événements qui sont suivis pendant que votre application traite la requête. Ainsi, si certaines demandes échouent, vous pouvez savoir si cela est dû à la lenteur des réponses d’une dépendance.

### <a name="tracing-from-requests-to-dependencies"></a>Traçage des demandes aux dépendances

Ouvrez l’onglet **Performances** et accédez à l’onglet **Dépendances** situé en haut à côté des opérations.

Cliquez sur un **nom de dépendance** sous Général. Après avoir sélectionné une dépendance, vous voyez s’afficher à droite un graphique de la distribution des durées de cette dépendance.

![Dans l’onglet Performances, cliquez sur l’onglet Dépendance en haut, puis sur un nom de dépendance dans le graphique](./media/asp-net-dependencies/2-perf-dependencies.png)

Cliquez sur le bouton bleu **Exemples** en bas à droite, puis sur un exemple pour afficher les détails de la transaction de bout en bout.

![Cliquer sur un exemple pour afficher les détails de la transaction de bout en bout](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>Profiler votre site en ligne

Vous voulez savoir à quoi tout ce temps a été consacré ? Le [profileur d’Application Insights](../../azure-monitor/app/profiler.md) effectue le suivi des appels HTTP vers votre site en production et vous montre les fonctions dans votre code qui ont pris le plus de temps.

## <a name="failed-requests"></a>Demandes ayant échoué

Les échecs de demandes peuvent également être associés à des échecs d’appels de dépendances.

Accédez maintenant à l’onglet **Échecs** sur la gauche, puis cliquez sur l’onglet **Dépendances** en haut.

![Cliquez sur le graphique des demandes ayant échoué](./media/asp-net-dependencies/4-fail.png)

Vous pouvez voir ici le nombre de demandes ayant échoué. Pour obtenir plus d’informations sur une occurrence ayant échoué, essayez de cliquer sur un nom de dépendance dans le tableau du bas. Vous pouvez cliquer sur le bouton bleu **Dépendances** en bas à droite pour obtenir les détails de la transaction de bout en bout.

## <a name="logs-analytics"></a>Logs (Analytics)

Vous pouvez suivre les dépendances dans le [langage de requête Kusto](/azure/kusto/query/). Voici quelques exemples.

* Rechercher les appels de dépendances ayant échoué :

``` Kusto

    dependencies | where success != "True" | take 10
```

* Rechercher les appels AJAX :

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* Rechercher les appels de dépendances associés aux demandes :

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* Rechercher les appels AJAX associés à des pages consultées :

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*Comment le collecteur automatique de dépendances signale-t-il les échecs d’appels des dépendances ?*

* Pour les appels de dépendances ayant échoué, le champ «success » est défini sur False. `DependencyTrackingTelemetryModule` ne signale pas les occurrences `ExceptionTelemetry`. Le modèle de données complet pour la dépendance est décrit [ici](data-model-dependency-telemetry.md).

## <a name="open-source-sdk"></a>Kit de développement logiciel (SDK) open source
Comme chaque SDK Application Insights, le module de collecte des dépendances est également open source. Lisez et contribuez au code, ou signalez les problèmes rencontrés dans le [dépôt GitHub officiel](https://github.com/Microsoft/ApplicationInsights-dotnet-server).

## <a name="next-steps"></a>Étapes suivantes

* [Exceptions](../../azure-monitor/app/asp-net-exceptions.md)
* [Données utilisateur et de page](../../azure-monitor/app/javascript.md)
* [Disponibilité](../../azure-monitor/app/monitor-web-app-availability.md)
