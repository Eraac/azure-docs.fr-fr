---
title: 'Guide de dépannage : Application Insights pour .NET'
description: Vous ne voyez pas de données dans Azure Application Insights ? Essayez ici.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: e231569f-1b38-48f8-a744-6329f41d91d3
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2018
ms.author: mbullwin
ms.openlocfilehash: 2966f90dcb381e439c00a6540ef9a01bd24f8743
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561189"
---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Guide de dépannage : Application Insights pour .NET
## <a name="some-of-my-telemetry-is-missing"></a>Certaines de mes données télémétriques manquent
*Dans Application Insights, je vois seulement une fraction des événements qui sont générés par mon application.*

* Si vous voyez régulièrement la même fraction, cela est probablement causé par [l’échantillonnage](../../azure-monitor/app/sampling.md) adaptatif. Pour vérifier cela, ouvrez la recherche (dans le panneau de vue d’ensemble) et recherchez une instance d’une demande ou d’autres événements. En bas de la section Propriétés, cliquez sur «... » pour obtenir des détails de propriété complets. Si le nombre de requêtes est supérieur à 1, l’échantillonnage est en cours.
* Dans le cas contraire, il est possible que vous rencontriez une [limite de débit](../../azure-monitor/app/pricing.md#limits-summary) pour votre plan tarifaire. Ces limites sont appliquées par minute.

*Je perds des données de manière aléatoire.*

* Vérifiez si vous subissez des pertes de données dans le [canal de télémétrie](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost)

* Recherchez les problèmes connus dans le [référentiel Github](https://github.com/Microsoft/ApplicationInsights-dotnet/issues) du canal de télémétrie

*Je subis une perte de données dans une application console ou sur une application web lorsqu’elle est sur le point de s’arrêter.*

* Le canal du kit de développement logiciel (SDK) conserve les données de télémétrie dans la mémoire tampon et les envoie par lots. Si l’application est en cours d’arrêt, il peut être nécessaire d’appeler explicitement [Flush()](api-custom-events-metrics.md#flushing-data). Le comportement de `Flush()` dépend du véritable [canal](telemetry-channels.md#built-in-telemetry-channels) utilisé.

## <a name="no-data-from-my-server"></a>Aucune donnée de mon serveur
*J’ai installé l’application sur mon serveur web et maintenant je ne vois pas les données de télémétrie à partir de celui-ci. Cela a fonctionné correctement sur mon ordinateur de développement.*

* Probablement un problème de pare-feu. [Définissez des exceptions de pare-feu pour qu’Application Insights puisse envoyer des données](../../azure-monitor/app/ip-addresses.md).
* Il manque peut-être certains prérequis au serveur IIS : .NET Extensibility 4.5 et ASP.NET 4.5.

*J’ai [installé Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md) sur mon serveur web pour surveiller les applications existantes. Aucun résultat ne s’affiche.*

* Consultez [Résolution des problèmes liés à Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot).

## <a name="q01"></a>Aucune option « Ajouter Application Insights » dans Visual Studio
*Lorsque j’effectue un clic droit sur le projet existant dans l’Explorateur de solutions, je ne vois aucune option Application Insights.*

* Les outils ne prennent pas en charge tous les types de projets .NET. Les projets Web et WCF sont pris en charge. Pour les autres types de projets, notamment les applications de bureau ou de service, vous avez toujours la possibilité [d’ajouter un kit de développement logiciel Application Insights à votre projet manuellement](../../azure-monitor/app/windows-desktop.md).
* Vérifiez que vous disposez de [Visual Studio 2013 Update 3 ou version ultérieure](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs). Il est préinstallé avec les outils d’analyse développeur, qui fournissent le Kit de développement logiciel (SDK) Application Insights.
* Sélectionnez **Outils**, **Extensions et mises à jour**, puis vérifiez l’installation et l’activation des outils **Analyse développeur**. Dans ce cas, cliquez sur **Mises à jour** pour voir si une mise à jour est disponible.
* Ouvrez la boîte de dialogue Nouveau projet et sélectionnez l’application Web ASP.NET. Si vous voyez l’option Application Insights à cet endroit, les outils sont installés. Si ce n’est pas le cas, essayez de désinstaller, puis de réinstaller Developer Analytics Tools.

## <a name="q02"></a>Échec de l’ajout d’Application Insights
*Lorsque j’essaie d’ajouter Application Insights à un projet existant, je reçois un message d’erreur.*

Causes probables :

* la communication avec le portail Application Insights a échoué ;
* votre compte Azure présente un problème ;
* vous disposez uniquement d’un [accès en lecture à l’abonnement ou au groupe dans lequel vous avez essayé de créer la nouvelle ressource](../../azure-monitor/app/resources-roles-access-control.md).

Correctif :

* vérifiez que les informations de connexion que vous avez fournies correspondent au bon compte Azure.
* Dans votre navigateur, vérifiez que vous avez accès au [portail Azure](https://portal.azure.com). Ouvrez Paramètres et vérifiez s’il existe des restrictions.
* [Ajout d’Application Insights à votre projet existant](../../azure-monitor/app/asp-net.md) : Dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet, puis sélectionnez « Ajouter Application Insights ».

## <a name="emptykey"></a>J’obtiens une erreur « La clé d’instrumentation ne peut pas être vide ».
Il semble qu'une erreur est survenue durant l'installation d'Application Insights ou d'un enregistreur de données.

Dans l’explorateur de solutions, cliquez avec le bouton droit sur votre projet, puis sélectionnez **Application Insights > Configurer Application Insights**. Une boîte de dialogue vous invite à vous connecter à Azure et à créer une ressource Application Insights, ou à réutiliser une ressource existante.

## <a name="NuGetBuild"></a> « Packages NuGet manquants » sur mon serveur de builds
*Sur ma machine de développement, tout fonctionne correctement, mais j’obtiens une erreur NuGet sur le serveur de builds.*

Consultez les sections relatives à la [restauration des packages NuGet](https://docs.nuget.org/Consume/Package-Restore) et à la [restauration automatique des packages](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Commande de menu manquante pour ouvrir Application Insights à partir de Visual Studio
*Lorsque j’effectue un clic droit sur mon projet Explorateur de solutions, je ne vois pas toutes les commandes Application Insights, ou je ne vois pas de commande Ouvrir Application Insights.*

Causes probables :

* vous avez créé la ressource Application Insights manuellement, ou le type de projet n’est pas pris en charge par les outils Application Insights.
* Les outils Analyse développeur sont désactivés dans Visual Studio.
* Votre version de Visual Studio est antérieure à 2013 Mise à jour 3.

Correctif :

* assurez-vous que votre version de Visual Studio est bien 2013 Mise à jour 3 ou une version ultérieure.
* Sélectionnez **Outils**, **Extensions et mises à jour**, puis vérifiez l’installation et l’activation des outils **Analyse développeur**. Dans ce cas, cliquez sur **Mises à jour** pour voir si une mise à jour est disponible.
* Cliquez avec le bouton droit sur l’Explorateur de solutions. Si vous voyez la commande **Application Insights > Configurer Application Insights**, utilisez-la pour raccorder votre projet à la ressource dans le service Application Insights.

Dans le cas contraire, votre type de projet n’est pas directement pris en charge par les outils Developer Analytics Tools. Pour voir votre télémétrie, connectez-vous au [portail Azure](https://portal.azure.com), cliquez sur Application Insights dans la barre de navigation de gauche, puis sélectionnez votre application.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>« Accès refusé » lors de l’ouverture d’Application Insights dans Visual Studio
*La commande de menu « Ouvrir Application Insights » m’amène vers le portail Azure, mais j’obtiens une erreur « Accès refusé ».*

La connexion Microsoft que vous avez utilisée en dernier dans votre navigateur par défaut n’a pas accès à [la ressource créée au moment où Application Insights a été ajoutée à cette application](../../azure-monitor/app/asp-net.md). Il existe deux raisons possibles :

* Vous avez plusieurs comptes Microsoft, et peut-être un compte professionnel et un compte personnel Microsoft ? La connexion que vous avez utilisée en dernier dans votre navigateur par défaut était destinée à un compte qui diffère de celui disposant d’un accès pour [ajouter l’application Insights au projet](../../azure-monitor/app/asp-net.md).
  * Correctif : cliquez sur votre nom en haut à droite de la fenêtre du navigateur et déconnectez-vous. Connectez-vous ensuite avec le compte ayant l’accès. Dans la barre de navigation de gauche, cliquez sur Application Insights, puis sélectionnez votre application.
* Quelqu’un d’autre a ajouté Application Insights au projet et a oublié de vous octroyer [l’accès au groupe de ressources](../../azure-monitor/app/resources-roles-access-control.md) dans lequel elle a été créée.
  * Correctif : si c’est un compte professionnel qui a été utilisé, vous pouvez être ajouté à l’équipe ; ou vous pouvez vous voir accorder l’accès individuel au groupe de ressources individuel.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>« Actif introuvable » lors de l’ouverture d’Application Insights dans Visual Studio
*La commande de menu « Ouvrir Application Insights » m’amène vers le portail Azure, mais j’obtiens une erreur « Actif introuvable ».*

Causes probables :

* La ressource Application Insights pour votre application a été supprimée ; ou
* La clé d’instrumentation a été définie ou modifiée dans le fichier ApplicationInsights.config par une modification directe, sans mise à jour du fichier de projet.

La clé d’instrumentation dans ApplicationInsights.config détermine l’endroit où la télémétrie est envoyée. Une ligne dans le fichier projet détermine quelle ressource est ouverte lorsque vous utilisez la commande dans Visual Studio.

Correctif :

* Dans l’explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez Application Insights, Configurer Application Insights. Dans la boîte de dialogue, vous pouvez choisir d’envoyer la télémétrie à une ressource existante ou en créer une nouvelle. Ou :
* Ouvrez la ressource directement. Connectez-vous au [portail Azure](https://portal.azure.com), cliquez sur Application Insights dans la barre de navigation de gauche, puis sélectionnez votre application.

## <a name="where-do-i-find-my-telemetry"></a>Où puis-je trouver mes données de télémétrie ?
*Je me suis connecté au [portail Microsoft Azure](https://portal.azure.com) et je regarde le tableau de bord d’accueil Azure. Alors, où puis-je trouver mes données Application Insights ?*

* Dans la barre de navigation de gauche, cliquez sur Application Insights, puis sur le nom de votre application. Si aucun projet ne se trouve à cet endroit, vous devez [ajouter ou configurer Application Insights dans votre projet web](../../azure-monitor/app/asp-net.md).  
  Vous allez voir certains graphiques récapitulatifs. Vous pouvez cliquer dessus pour afficher d’autres détails.
* Dans Visual Studio, cliquez sur le bouton Application Insights, pendant le débogage de votre application.

## <a name="q03"></a> Aucune donnée sur serveur (ou aucune donnée)
*J’ai exécuté mon application, puis j’ai ouvert le service Application Insights dans Microsoft Azure, mais tous les graphiques affichent « Découvrez comment collecter... » ou « Non configuré ».* Ou *Mode page et données utilisateur uniquement, mais aucune donnée de serveur.*

* Exécutez votre application en mode Débogage dans Visual Studio (F5). Utilisez l’application afin de générer des données de télémétrie. Vérifiez que vous pouvez voir les événements consignés dans la fenêtre Sortie de Visual Studio.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* Dans Application Insights, ouvrez [Diagnostic Search](../../azure-monitor/app/diagnostic-search.md)(Recherche de diagnostic). En général, les données s’affichent d’abord ici.
* Cliquez sur le bouton Actualiser. Le panneau s’actualise à intervalles réguliers, mais vous pouvez également l’actualiser manuellement. Plus les intervalles de temps sur lesquels portent les graphiques sont étendus, plus l’intervalle d’actualisation est long.
* Consultez la correspondance des clés d’instrumentation Sur le panneau principal de votre application dans le portail Application Insights, dans la liste déroulante **Essentials**, examinez **Clé d’instrumentation**. Ensuite, dans votre projet dans Visual Studio, ouvrez le fichier ApplicationInsights.config et recherchez `<instrumentationkey>`. Vérifiez que les deux clés sont semblables. Si ce n’est pas le cas :  
  * Dans le portail, cliquez sur Application Insights et recherchez la ressource d’application avec la clé adéquate ; ou
  * Dans l’Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le projet, puis sélectionnez Application Insights, Configurer. Réinitialisez l’application pour envoyer des données de télémétrie à la ressource appropriée.
  * Si vous ne trouvez pas les clés correspondantes, vérifiez que vous utilisez pour Visual Studio les mêmes informations de connexion que pour le portail.
* Dans le [tableau de bord d’accueil de Microsoft Azure](https://portal.azure.com), examinez la carte État du service. Si des alertes sont indiquées, attendez qu'elles soient corrigées (OK), puis fermez et rouvrez le volet de votre application Application Insights.
* Vérifiez également [notre blog d'état](https://blogs.msdn.microsoft.com/servicemap-status/).
* Avez-vous écrit du code pour le [kit de développement logiciel côté serveur](../../azure-monitor/app/api-custom-events-metrics.md) susceptible de modifier la clé d’instrumentation dans les instances `TelemetryClient` ou dans `TelemetryContext` ? Ou avez-vous rédigé une [configuration de filtre ou d’échantillonnage](../../azure-monitor/app/api-filtering-sampling.md) trop exclusive ?
* Si vous avez modifié ApplicationInsights.config, vérifiez attentivement la configuration de [TelemetryInitializers et de TelemetryProcessors](../../azure-monitor/app/api-filtering-sampling.md). Un type ou un paramètre nommé de manière incorrecte peut empêcher le kit de développement logiciel d'envoyer des données.

## <a name="q04"></a>Aucune donnée sur l’affichage des pages, les navigateurs et l’utilisation
*Je vois des données dans les graphiques Heures de réponse de serveur et Demandes de serveur, mais aucune donnée Temps de chargement de la page consultée, ou dans les panneaux Navigateur ou Utilisation.*

Les données proviennent de scripts dans les pages web. 

* Si vous avez ajouté Application Insights à un projet existant, [vous devez ajouter manuellement les scripts](../../azure-monitor/app/javascript.md).
* Assurez-vous que Microsoft Internet Explorer n'affiche pas votre site en mode de compatibilité.
* Utilisez la fonctionnalité de débogage du navigateur (accessible via F12 dans certains navigateurs, puis Réseau) pour vérifier que les données sont envoyées à `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Aucune donnée de dépendance ou d’exception
Voir [télémétrie des dépendances](../../azure-monitor/app/asp-net-dependencies.md) et [télémétrie d’exception](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Aucune donnée de performances
Les données de performances (UC, taux d’E/S, etc.) sont disponibles pour les [services web Java](../../azure-monitor/app/java-collectd.md), les [applications de bureau Windows](../../azure-monitor/app/windows-desktop.md), les [services et applications web IIS si vous installez le moniteur d’état (Status monitor)](../../azure-monitor/app/monitor-performance-live-website-now.md) et [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md). Ces données figurent sous Paramètres, Serveurs.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Aucune donnée (serveur) n’apparaît depuis que j’ai publié l’application sur mon serveur
* Vérifiez que vous avez réellement copié toutes les DLL Microsoft ApplicationInsights sur le serveur, avec Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* Dans votre pare-feu, vous devrez peut-être [ouvrir certains ports TCP](../../azure-monitor/app/ip-addresses.md).
* Si vous devez utiliser un proxy pour l'envoi depuis votre réseau d'entreprise, définissez le paramètre [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) dans le fichier Web.config.
* Windows Server 2008 : vérifiez que vous avez installé les mises à jour suivantes : [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Je pouvais voir les données, mais plus maintenant
* Vérifiez le [blog d'état](https://blogs.msdn.com/b/applicationinsights-status/).
* Vous souhaitez savoir si vous avez atteint votre quota mensuel de points de données ? Ouvrez les champs Paramètres/Quota et Tarification pour le savoir. Le cas échéant, vous pouvez mettre à niveau votre forfait ou payer pour disposer d'une capacité supplémentaire. Consultez le [mécanisme de tarification](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Je ne vois pas toutes les données que j’attends
Si votre application envoie des données en grand nombre et si vous utilisez le kit de développement logiciel Application Insights pour ASP.NET version 2.0.0-beta3 ou ultérieure, la fonctionnalité [d’échantillonnage adaptatif](../../azure-monitor/app/sampling.md) peut fonctionner et transmettre uniquement un pourcentage de vos données de télémétrie.

Vous pouvez le désactiver, mais cela n’est pas recommandé. L’échantillonnage est conçu pour que la télémétrie associée soit correctement transmise pour faciliter le diagnostic.

## <a name="client-ip-address-is-0000"></a>L’adresse IP du client est 0.0.0.0

Le 5 février 2018, nous avons annoncé la suppression de la journalisation de l’adresse IP du client. Cela n’affecte pas l’emplacement géographique.

> [!NOTE]
> Si vous avez besoin des 3 premiers octets de l’adresse IP, vous pouvez utiliser un [initialiseur de télémétrie](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer) pour ajouter un attribut personnalisé.
> Cela n’affecte pas les données collectées avant le 5 février 2018.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Données géographiques erronées dans la télémétrie de l’utilisateur
Les dimensions de la ville, de la région et des pays proviennent des adresses IP et ne sont pas toujours précises. Ces adresses IP sont d’abord traitées pour la localisation, puis sont changées en 0.0.0.0 pour être stockées.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Exception « méthode introuvable » lors de l’exécution dans Services cloud Azure
Avez-vous effectué une génération pour .NET 4.6 ? 4.6 n’est pas automatiquement pris en charge dans les rôles Azure Cloud Services. [Installez 4.6 sur chaque rôle](../../cloud-services/cloud-services-dotnet-install-dotnet.md) avant d’exécuter votre application.

## <a name="troubleshooting-logs"></a>Journaux d’activité de dépannage

Suivez ces instructions pour capturer les journaux d’activité de dépannage de votre infrastructure.

### <a name="net-framework"></a>.NET Framework

1. Installez le package [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) à partir de NuGet. La version que vous installez doit correspondre à la version actuellement installée de `Microsoft.ApplicationInsighs`

2. Modifiez votre fichier applicationinsights.config pour inclure les éléments suivants :

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    Votre application doit disposer des autorisations d'écriture sur l'emplacement configuré

3. Redémarrez le processus pour que ces nouveaux paramètres soient récupérés par le kit de développement logiciel (SDK)

4. Dès que vous avez terminé, annulez ces modifications.

### <a name="net-core"></a>.NET Core

1. Installez le package [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) à partir de NuGet. La version que vous installez doit correspondre à la version actuellement installée de `Microsoft.ApplicationInsights`

Microsoft.ApplicationInsights.AspNetCore 2.7.1 est la version la plus récente et elle fait référence à Microsoft.ApplicationInsights 2.10. Par conséquent, vous devez installer Microsoft.AspNet.ApplicationInsights.HostingStartup 2.10.0.

2. Modifiez la méthode `ConfigureServices` dans votre classe `Startup.cs` :

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Votre application doit disposer des autorisations d'écriture sur l'emplacement configuré

3. Redémarrez le processus pour que ces nouveaux paramètres soient récupérés par le kit de développement logiciel (SDK)

4. Dès que vous avez terminé, annulez ces modifications.


## <a name="PerfView"></a> Collecter des journaux avec PerfView
[PerfView](https://github.com/Microsoft/perfview) est un outil d’analyse des performances et de diagnostic gratuit qui aide à isoler le processeur, la mémoire et d’autres problèmes en collectant et en visualisant des informations relatives aux diagnostics provenant de nombreuses sources.

Le kit de développement logiciel (SDK) Application Insights enregistre les journaux EventSource de résolution automatique des problèmes qui peuvent être capturés par PerfView.

Pour collecter des journaux, téléchargez PerfView et exécutez la commande suivante :
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

Vous pouvez modifier ces paramètres en fonction de vos besoins :
- **MaxCollectSec**. Définissez ce paramètre de manière à empêcher l’exécution permanente de PerfView, ce qui affecterait les performances de votre serveur.
- **OnlyProviders**. Définissez ce paramètre afin de collecter uniquement les journaux à partir du kit de développement logiciel (SDK). Vous pouvez personnaliser cette liste en fonction de vos enquêtes spécifiques. 
- **NoGui**. Définissez ce paramètre pour collecter les journaux sans l’interface utilisateur graphique.


Pour plus d’informations, consultez :
- [Enregistrement des traces de performances avec PerfView](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Sources de l’événement Application Insights](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/ETW)

## <a name="still-not-working"></a>Ne fonctionne toujours pas...
* [Forum Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
