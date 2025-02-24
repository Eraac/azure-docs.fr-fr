---
title: Mise en correspondance d’applications dans Azure Application Insights | Microsoft Docs
description: Surveiller des topologies d’applications complexes avec la mise en correspondance d’applications
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 70d1f54aed5e83801b1d1e249d7a412dd6d9a49a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65964040"
---
# <a name="application-map-triage-distributed-applications"></a>Cartographie d’application : trier des applications distribuées

La mise en correspondance d’applications vous permet d’identifier les baisses de performances ou les défaillances sur l’ensemble des composants de votre application distribuée. Chaque nœud de cette mise en correspondance représente un composant d’application ou ses dépendances, avec des indicateurs de performance clés d’intégrité et l’état des alertes. Vous pouvez cliquer sur n’importe quel composant pour obtenir des diagnostics plus détaillés, par exemple des événements Application Insights. Si votre application utilise les services Azure, vous pouvez également accéder aux diagnostics Azure, et notamment aux recommandations de SQL Database Advisor.

## <a name="what-is-a-component"></a>Qu’est un composant ?

Les composants sont des parties pouvant être déployées de manière indépendante de votre application distribuée/de microservices. Les développeurs et équipes d’opérations disposent d’une visibilité au niveau du code ou d’un accès à la télémétrie générée par ces composants d’application. 

* Les composants sont différents des dépendances externes « observées » telles que SQL, EventHub, etc., auxquelles votre équipe/organisation peut ne pas avoir accès (code ou télémétrie).
* Les composants s’exécutent sur un nombre quelconque d’instances de serveur/rôle/conteneur.
* Les composants peuvent être des clés d’instrumentation Application Insights distinctes (même si les abonnements sont différents) ou des rôles différents rapportant à une clé d’instrumentation Application Insights unique. L’aperçu de mise en correspondance montre les composants, quelle que soit leur configuration.

## <a name="composite-application-map"></a>Cartographie d’application composite

Vous pouvez afficher la topologie complète des applications sur plusieurs niveaux des composants d’application associés. Les composants peuvent représenter différentes ressources Application Insights, ou différents rôles d’une seule ressource. La mise en correspondance d’applications trouve les composants en suivant les appels de dépendance HTTP effectués entre les serveurs sur lesquels le kit SDK Application Insights est installé. 

Cette expérience démarre la découverte progressive des composants. Lorsque vous chargez la cartographie d’application pour la première fois, un ensemble de requêtes est déclenché pour découvrir les composants liés à ce composant. Un bouton dans le coin supérieur gauche permet de mettre à jour le nombre de composants de votre application dès qu’ils sont détectés. 

Lorsque vous cliquez sur « Update map components » (Mettre à jour les composants de cartographie), la carte est actualisée avec tous les composants détectés. Selon la complexité de votre application, le chargement peut prendre une minute.

Si tous les composants sont des rôles au sein d’une seule ressource Application Insights, cette étape de découverte n’est pas requise. La charge initiale pour une telle application aura tous ses composants.

![Capture d’écran de la cartographie d’application](media/app-map/app-map-001.png)

L’un des principaux objectifs de cette expérience est de permettre de visualiser des topologies complexes incluant des centaines de composants.

Cliquez sur n’importe quel composant pour afficher des informations connexes ainsi que les performances et l’expérience de triage de défaillance de ce composant.

![Menu volant](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Examiner les échecs

Pour lancer le panneau Échecs, sélectionnez **Examiner les échecs**.

![Capture d’écran du bouton Examiner les échecs](media/app-map/investigate-failures.png)

![Capture d’écran du panneau Échecs](media/app-map/failures.png)

### <a name="investigate-performance"></a>Examiner les performances

Pour résoudre les problèmes de performances, sélectionnez **Examiner les performances**.

![Capture d’écran du bouton Examiner les performances](media/app-map/investigate-performance.png)

![Capture d’écran du panneau Performances](media/app-map/performance.png)

### <a name="go-to-details"></a>Accéder aux détails

Pour explorer l’expérience de transaction de bout en bout pouvant présenter des vues générées au niveau de la pile des appels, sélectionnez **Accéder aux détails**.

![Capture d’écran du bouton Accéder aux détails](media/app-map/go-to-details.png)

![Capture d’écran des détails de transaction de bout en bout](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Afficher dans Analytics

Pour interroger et analyser vos données d’application de manière plus approfondie, cliquez sur **Afficher dans Analytics**.

![Capture d’écran du bouton Afficher dans Analytics](media/app-map/view-in-analytics.png)

![Capture d’écran de l’expérience d’analyse](media/app-map/analytics.png)

### <a name="alerts"></a>Alertes

Pour visualiser les alertes actives et les règles sous-jacentes qui entraînent le déclenchement d’alertes, sélectionnez **Alertes**.

![Capture d’écran du bouton Alertes](media/app-map/alerts.png)

![Capture d’écran de l’expérience d’analyse](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Définir le nom du rôle cloud

La cartographie d’application utilise la propriété **nom du rôle cloud** pour identifier les composants sur la carte. Le SDK Application Insights ajoute automatiquement la propriété nom du rôle cloud aux données de télémétrie émises par les composants. Par exemple, le kit de développement logiciel (SDK) ajoute un nom de site web ou un nom de rôle de service à la propriété nom du rôle cloud. Toutefois, vous pouvez être amené à remplacer la valeur par défaut. Pour remplacer nom du rôle cloud et modifier ce qui s’affiche sur la cartographie d’application :

### <a name="netnet-core"></a>.NET/.NET Core

**Écrire un initialiseur TelemetryInitializer personnalisé comme ci-dessous.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**Charger l'initialiseur dans la configuration TelemetryConfiguration active**

Dans ApplicationInsights.config :

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

> [!NOTE]
> Ajouter l’initialiseur en utilisant `ApplicationInsights.config` n’est pas valide pour les applications ASP.NET Core.

Une autre méthode pour les applications web ASP.NET consiste à instancier l’initialiseur dans le code, par exemple dans Global.aspx.cs :

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

Pour les applications [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers), l’ajout d’un nouveau `TelemetryInitializer` se fait en l’ajoutant au conteneur d’injection de dépendance, comme indiqué ci-dessous. Cela se fait dans la méthode `ConfigureServices` de votre classe `Startup.cs`.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Autre méthode pour Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

### <a name="java"></a>Java

Si vous utilisez Spring Boot avec le starter SpringBoot Application Insights, le seul changement nécessaire consiste à définir votre nom personnalisé pour l’application dans le fichier application.properties.

`spring.application.name=<name-of-app>`

Le starter SpringBoot attribue automatiquement nom du rôle cloud à la valeur que vous entrez pour la propriété spring.application.name.

Pour plus d’informations sur la corrélation Java et sur la façon de configurer nom du rôle cloud pour les applications non-SpringBoot, consultez cette [section](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) sur la corrélation.

### <a name="clientbrowser-side-javascript"></a>JavaScript côté client/navigateur

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Comprendre le nom du rôle cloud dans le contexte de la cartographie d'application

En matière de **nom de rôle cloud**, il peut être utile d’examiner une cartographie d’application présentant plusieurs noms de rôles cloud :

![Capture d’écran de la cartographie d’application](media/app-map/cloud-rolename.png)

Dans la cartographie d'application ci-dessus, les noms présents dans les zones vertes correspondent à des valeurs de nom de rôle cloud pour différents aspects de cette application distribuée. Ainsi, pour cette application, les rôles sont les suivantes : `Authentication`, `acmefrontend`, `Inventory Management` et `Payment Processing Worker Role`. 

Dans le cas de cette application, chaque nom de rôle cloud représente également une ressource Application Insights unique avec ses propres clés d’instrumentation. Le propriétaire de cette application ayant accès aux quatre ressources Application Insights distinctes, la cartographie d’application peut constituer une représentation des relations sous-jacentes.

Pour les [définitions officielles](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93) :

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

L'**instance de rôle cloud** peut être utile pour les scénarios dans lesquels le **nom de rôle cloud** vous indique que le problème se situe au niveau de votre serveur web frontal, mais vous pouvez exécuter votre serveur web frontal sur plusieurs serveurs à charge équilibrée et dès lors, explorer de manière plus approfondie via les requêtes Kusto pour savoir si le problème a une incidence sur tous les serveurs frontaux ou un seul peut s'avérer particulièrement important.

Vous pouvez vouloir remplacer la valeur de l'instance de rôle cloud si votre application est en cours d’exécution dans un environnement conteneurisé où le simple fait de connaître le serveur en question peut ne pas suffire à localiser un problème donné.

Pour plus d’informations sur la substitution de la propriété nom du rôle cloud avec des initialiseurs de télémétrie, consultez [Ajouter des propriétés : ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Résolution de problèmes

Si la cartographie d’application ne fonctionne pas comme prévu, essayez ces étapes :

### <a name="general"></a>Généralités

1. Veillez à utiliser un SDK officiellement pris en charge. Les SDK non pris en charge ou de la communauté ne prennent pas forcément en charge la corrélation.

    Reportez-vous à cet [article](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) pour obtenir la liste des SDK pris en charge.

2. Mettez à niveau tous les composants avec la dernière version du SDK.

3. Si vous utilisez Azure Functions avec C#, effectuez une mise à niveau vers [Functions V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Vérifiez que [nom du rôle cloud](#set-cloud-role-name) est correctement configuré.

5. S’il vous manque une dépendance, vérifiez qu’elle figure dans la liste des [dépendances collectées automatiquement](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Sinon, vous pouvez toujours la suivre manuellement avec un [suivi d’appel de dépendance](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>Trop grand nombre de nœuds sur la carte

La cartographie d’application construit un nœud d’application pour chaque nom de rôle cloud unique présent dans la télémétrie de vos requêtes et un nœud de dépendance pour chaque combinaison unique de type, cible et nom de rôle de cloud dans la télémétrie de vos dépendances. En présence de plus de 10 000 nœuds dans vos données de télémétrie, la cartographie d’application ne sera pas en mesure d’extraire tous les nœuds et liens, et votre carte sera incomplète. Si cela se produit, un message d’avertissement apparaît lors de l’affichage de la carte.

En outre, la cartographie d’application ne peut prendre en charge que 1 000 nœuds non groupés distincts affichés simultanément. La cartographie d’application réduit la complexité visuelle en regroupant les dépendances présentant les mêmes types et appelants, mais si vos données de télémétrie contiennent trop de noms de rôles de cloud uniques ou de types de dépendances, ce regroupement ne sera pas suffisant et la carte ne pourra pas s'afficher.

Pour y remédier, il vous faudra modifier votre instrumentation afin de bien définir le nom de rôle cloud, le type de dépendance, ainsi que les champs cibles de dépendance.

* La cible de dépendance doit représenter le nom logique d’une dépendance. En général, celui-ci correspond au nom de serveur ou de ressource de la dépendance. Par exemple, dans le cas de dépendances HTTP, il est défini sur le nom d’hôte. Il ne doit pas contenir d'ID uniques ou de paramètres qui changent d'une requête à une autre.

* Le type de dépendance doit représenter le type logique d’une dépendance. Par exemple, HTTP, SQL ou Blob Azure sont des types de dépendances classiques. Il ne doit pas contenir d'ID uniques.

* Le but du nom de rôle cloud est décrit dans la [section ci-dessus](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Commentaires du portail

Pour envoyer des commentaires, utilisez l’option de commentaires.

![Image MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation de la corrélation](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
