---
title: Explorer les journaux d’activité de traçage .NET dans Azure Application Insights avec ILogger
description: Exemples d’utilisation du fournisseur Azure Application Insights ILogger avec les applications console et ASP.NET Core.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: 0691c35661a6d185a6aa5ed3383ad600653359d3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058599"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider pour les journaux ILogger .NET Core

ASP.NET Core prend en charge une API de journalisation qui fonctionne avec différents types de fournisseurs de journalisation intégrés et tiers. La journalisation est activée en appelant **Log()** ou une variante de cette méthode sur les instances *ILogger*. Cet article explique comment utiliser *ApplicationInsightsLoggerProvider* pour capturer les journaux ILogger dans les applications console et ASP.NET Core. Cet article décrit également comment ApplicationInsightsLoggerProvider s’intègre avec d’autres applications de télémétrie Application Insights.
Pour plus d’informations, consultez [Journalisation dans ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Applications ASP.NET Core

ApplicationInsightsLoggerProvider est activé par défaut dans le [SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.0-beta3 (et versions ultérieures) lorsque vous activez la surveillance régulière d’Application Insights par le biais des méthodes standards suivantes :
- En appelant la méthode d’extension **UseApplicationInsights** sur IWebHostBuilder 
- En appelant la méthode d’extension **AddApplicationInsightsTelemetry** sur IServiceCollection

Les journaux ILogger que ApplicationInsightsLoggerProvider capture sont soumis à la même configuration que les autres données de télémétrie collectées. Ils possèdent le même ensemble de TelemetryInitializers et de TelemetryProcessors, utilisent le même TelemetryChannel, sont corrélées et échantillonnées de la même façon que les autres données de télémétrie. Si vous utilisez la version 2.7.0-beta3 ou ultérieure, aucune action n’est nécessaire pour capturer les journaux ILogger.

Seuls les journaux ILogger de niveau *Warning* ou supérieur (de toutes les catégories) sont envoyés à Application Insights par défaut. Cependant, vous pouvez [appliquer des filtres pour modifier ce comportement](#control-logging-level). Des étapes supplémentaires sont nécessaires pour capturer les journaux ILogger à partir de **Program.cs** ou de **Startup.cs**. (Consultez [Capturer les journaux ILogger à partir de Startup.cs et Program.cs dans les applications ASP.NET Core.](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Si vous utilisez une version antérieure du SDK Microsoft ApplicationInsights.AspNet ou si vous souhaitez simplement utiliser ApplicationInsightsLoggerProvider sans aucune autre surveillance Application Insights, utilisez la procédure suivante :

1. Installez le package NuGet :

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Modifiez **Program.cs** comme indiqué ici :

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

Le code de l’étape 2 configure `ApplicationInsightsLoggerProvider`. Le code suivant est un exemple de classe Controller, qui utilise `ILogger` pour envoyer les journaux. Les journaux sont capturés par Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Capturer les journaux ILogger à partir de Startup.cs et Program.cs dans les applications ASP.NET Core

> [!NOTE]
> Dans ASP.NET Core 3.0 et versions ultérieures, il n’est plus possible d’injecter `ILogger` dans Startup.cs et Program.cs. Consultez https://github.com/aspnet/Announcements/issues/353 pour plus d’informations.

Le nouveau ApplicationInsightsLoggerProvider peut capturer les journaux dès le début du pipeline de démarrage de l’application. Bien que ApplicationInsightsLoggerProvider soit automatiquement activé dans Application Insights (à partir de la version 2.7.0-beta3), sa clé d’instrumentation n’est configurée qu’à un stade ultérieur du pipeline. Ainsi, seuls les journaux de **Controller**/d’autres classes seront capturés. Pour capturer tous les journaux en commençant par **Program.cs** et **Startup.cs**, vous devez explicitement activer une clé d’instrumentation pour ApplicationInsightsLoggerProvider. En outre, *TelemetryConfiguration* n’est pas entièrement défini lorsque vous vous connectez à partir de **Program.cs** ou de **Startup.cs** lui-même. Ces journaux auront donc une configuration minimale qui utilise InMemoryChannel, aucun échantillonnage et aucun initialisateur ou processeur de télémétrie standard.

Les exemples suivants illustrent cette fonctionnalité avec **Program.cs** et **Startup.cs**.

#### <a name="example-programcs"></a>Exemple avec Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Exemple avec Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrer à partir de l’ancienne version de ApplicationInsightsLoggerProvider

Les versions du SDK Microsoft.ApplicationInsights.AspNet antérieures à la version 2.7.0-beta2 prenait en charge un fournisseur de journalisation qui est désormais obsolète. Ce fournisseur a été activé via la méthode d’extension **AddApplicationInsights()** de ILoggerFactory. Nous vous recommandons de migrer vers le nouveau fournisseur, ce qui implique deux opérations :

1. Supprimez l’appel *ILoggerFactory.AddApplicationInsights()* de la méthode **Startup.Configure()** pour éviter une journalisation en double.
2. Réappliquez les règles de filtrage dans le code, car elles ne seront pas suivies par le nouveau fournisseur. Les surcharges de *ILoggerFactory.AddApplicationInsights()* ont nécessité un minimum de fonctions LogLevel ou de filtrage. Avec le nouveau fournisseur, le filtrage fait partie de l’infrastructure de journalisation lui-même. Ce n’est pas le fournisseur Application Insights qui s’en charge. Ainsi tous les filtres qui sont fournis via les surcharges de *ILoggerFactory.AddApplicationInsights()* doivent être supprimés. De plus, il est souhaitable de définir des règles de filtrage conformément aux instructions de la section [Contrôler le niveau de journalisation](#control-logging-level). Si vous utilisez *appsettings.json* pour filtrer la journalisation, il continuera à fonctionner avec le nouveau fournisseur, car les deux utilisent le même alias, *ApplicationInsights*.

Vous pouvez toujours utiliser l’ancien fournisseur. (Il ne sera supprimé que lors d’un changement de version principale vers la version 3.*xxxx*.) Cependant, nous vous recommandons de migrer vers le nouveau fournisseur pour les raisons suivantes :

- Le fournisseur précédent ne prend pas en charge les [étendues de journal](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). Dans le nouveau fournisseur, les propriétés d’étendue sont automatiquement ajoutées en tant que propriétés personnalisées aux données de télémétrie collectées.
- Les journaux peuvent désormais être capturés beaucoup plus tôt dans le pipeline de démarrage de l’application. Les journaux des classes **Program** et **Startup** peuvent maintenant être capturés.
- Avec le nouveau fournisseur, le filtrage se fait au niveau de l’infrastructure elle-même. Vous pouvez filtrer les journaux vers le fournisseur Application Insights de la même manière que pour les autres fournisseurs, y compris les fournisseurs intégrés comme Console, Debug, etc. Vous pouvez également appliquer les mêmes filtres à plusieurs fournisseurs.
- Dans ASP.NET Core (2.0 et versions ultérieures), le moyen recommandé pour [activer les fournisseurs de journalisation](https://github.com/aspnet/Announcements/issues/255) est d’utiliser des méthodes d’extension sur ILoggingBuilder dans **Program.cs** lui-même.

> [!Note]
> Le nouveau fournisseur est disponible pour les applications ciblant NETSTANDARD2.0 ou une version ultérieure. Si votre application cible les anciennes versions de .NET Core, telles que .NET Core 1.1, ou si elle cible .NET Framework, continuez à utiliser l’ancien fournisseur.

## <a name="console-application"></a>Application de console

Voici un exemple de code d’application console configuré pour envoyer des traces ILogger à Application Insights.

Packages installés :

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

Cet exemple utilise le package autonome `Microsoft.Extensions.Logging.ApplicationInsights`. Par défaut, cette configuration utilise le strict minimum, TelemetryConfiguration, pour envoyer des données à Application Insights. Le strict minimum signifie que le canal utilisé est InMemoryChannel. Aucun échantillonnage n’est effectué et aucun TelemetryInitializers standard n’est utilisé. Ce comportement peut être substitué pour une application console comme indiqué dans l’exemple suivant.

Installez ce package supplémentaire :

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

La section suivante montre comment substituer la valeur par défaut TelemetryConfiguration à l’aide de la méthode **services.Configure<TelemetryConfiguration>()** . Cet exemple montre comment configurer `ServerTelemetryChannel` et l’échantillonnage. Il ajoute un ITelemetryInitializer personnalisé à TelemetryConfiguration.

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Contrôler le niveau de journalisation

L’infrastructure *ILogger* ASP.NET Core dispose d’un mécanisme intégré pour appliquer le [filtrage des journaux](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). Cela vous permet de contrôler les journaux qui sont envoyés à chaque fournisseur inscrit, y compris le fournisseur Application Insights. Le filtrage peut s’effectuer soit dans la configuration (généralement en utilisant un fichier *appsettings.json*), soit dans le code. Cette fonctionnalité est fournie par le framework lui-même. Elle n’est pas spécifique au fournisseur Application Insights.

Les exemples suivants appliquent les règles de filtrage à ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Créer des règles de filtrage dans la configuration avec appsettings.json

Pour ApplicationInsightsLoggerProvider, l’alias du fournisseur est `ApplicationInsights`. La section suivante du fichier *appsettings.json* configure le niveau *Avertissement* et les niveaux supérieurs de toutes les catégories dans les journaux, et le niveau *Erreur* et les niveaux supérieurs des catégories qui commencent par « Microsoft » à envoyer à `ApplicationInsightsLoggerProvider`.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>Créer des règles de filtre dans le code

L’extrait de code suivant configure le niveau *Avertissement* et les niveaux supérieurs de toutes les catégories dans les journaux, et le niveau *Erreur* et les niveaux supérieurs des catégories qui commencent par « Microsoft » à envoyer à `ApplicationInsightsLoggerProvider`. Cette configuration est la même que dans la section précédente du fichier *appsettings.json*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Quelles sont les versions anciennes et nouvelles de ApplicationInsightsLoggerProvider ?

Le [SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) inclut un fournisseur ApplicationInsightsLoggerProvider intégré (Microsoft.ApplicationInsights.AspNetCore.Logging.Logging.ApplicationInsightsLoggerProvider), qui était activé par les méthodes d’extension **ILoggerFactory**. Ce fournisseur est marqué obsolète à partir de la version 2.7.0-beta2. Il sera complètement supprimé lors de la prochaine mise à jour majeure de version. Le package [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) lui-même n’est pas obsolète. Il est nécessaire pour permettre la surveillance des requêtes, des dépendances, etc.

Il est également possible de préférer le nouveau package autonome [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), qui contient un fournisseur ApplicationInsightsLoggerProvider amélioré (Microsoft.Extensions.Logging.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) et des méthodes d’extension sur ILoggerBuilder pour son activation.

Le [SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.0-beta3 établit une dépendance avec le nouveau package et active automatiquement la fonctionnalité de capture ILogger.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Pourquoi certains journaux ILogger figurent-ils deux fois dans Application Insights ?

Si l’ancienne version (maintenant obsolète) de ApplicationInsightsLoggerProvider est activée dans votre environnement, la duplication est possible en appelant `AddApplicationInsights` sur `ILoggerFactory`. Vérifiez si votre méthode **Configure** possède les éléments suivants et supprimez-la :

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Si vous rencontrez une double journalisation lorsque vous déboguez depuis Visual Studio, définissez `EnableDebugLogger` sur *false* dans le code qui active Application Insights, comme suit. Cette duplication et ce correctif ne sont pertinents que lorsque vous déboguez l’application.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>J’ai effectué la mise à jour vers le [SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) version 2.7.0-beta3, et les journaux d’activité de ILogger sont capturés automatiquement. Comment désactiver complètement cette fonctionnalité ?

Consultez la section [Contrôler le niveau de journalisation](../../azure-monitor/app/ilogger.md#control-logging-level) pour découvrir comment filtrer les journaux en général. Pour désactiver ApplicationInsightsLoggerProvider, utilisez `LogLevel.None` :

**Dans le code :**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**Dans la configuration :**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Pourquoi certains journaux ILogger n’ont-ils pas les mêmes propriétés que d’autres ?

Application Insights capture et envoie les journaux ILogger à l’aide de la même TelemetryConfiguration qui est utilisée pour toutes les autres données de télémétrie. Il existe cependant une exception. Par défaut, TelemetryConfiguration n’est pas entièrement configuré lorsque vous vous connectez à partir de **Program.cs** ou de **Startup.cs**. Les journaux figurant à ces emplacements ne sont pas soumis à la configuration par défaut et n’exécuteront donc pas tous TelemetryInitializers et TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>J’utilise le package autonome Microsoft.Extensions.Logging.Logging.ApplicationInsights, et je veux enregistrer manuellement certaines télémesures personnalisées supplémentaires. Comment dois-je procéder ?

Lorsque vous utilisez le package autonome, `TelemetryClient` n’est pas injecté dans le conteneur DI, vous devez donc créer une nouvelle instance de `TelemetryClient` et utiliser la même configuration que celle du fournisseur d’enregistreur, comme le montre le code suivant. Cela garantit que la même configuration est utilisée pour toutes les données de télémétrie personnalisées ainsi que pour les données de télémétrie de la fonctionnalité ILogger.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Si vous utilisez le package Microsoft.ApplicationInsights.AspNetCore pour activer Application Insights, modifiez ce code pour obtenir `TelemetryClient` directement dans le constructeur. Pour obtenir un exemple, consultez [ce forum aux questions](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Quel type de télémétrie Application Insights est produit à partir des journaux d’activité ILogger ? Où les journaux d’activité ILogger figurent-ils dans Application Insights ?

ApplicationInsightsLoggerProvider capture les journaux ILogger et crée TraceTelemetry à partir de ceux-ci. Si un objet Exception est transmis à la méthode **Log()** sur ILogger, *ExceptionTelemetry* est créé à la place de TraceTelemetry. Ces éléments de télémétrie figurent aux mêmes emplacements que n’importe quel autre élément TraceTelemetry ou ExceptionTelemetry pour Application Insights, y compris le portail, les analyses ou le débogueur local Visual Studio.

Si vous préférez envoyer TraceTelemetry systématiquement, utilisez cet extrait de code : ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Le SDK n’est pas installé mais j’utilise l’extension Azure Web Apps pour activer Application Insights pour mes applications ASP.NET Core. Comment utiliser Personalizer ? 

L’extension Application Insights d’Azure Web Apps utilise l’ancien fournisseur. Vous pouvez modifier les règles de filtrage dans le fichier *appsettings.json* de votre application. Pour tirer parti du nouveau fournisseur, utilisez l’instrumentation au moment de la génération en établissant une dépendance NuGet sur le SDK. Cet article sera mis à jour lorsque l’extension utilisera le nouveau fournisseur.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>J’utilise le package autonome Microsoft.Extensions.Logging.Logging.ApplicationInsights, et j’active le fournisseur Application Insights en appelant **builder.AddApplicationInsights("ikey")** . Est-il possible d’obtenir une clé d’instrumentation à partir de la configuration ?


Modifiez Program.cs et appsettings.json comme suit :

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   Section appropriée de `appsettings.json` :

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

ce code n’est requis que lorsque vous utilisez un fournisseur de journalisation autonome. Pour la surveillance régulière d’Application Insights, la clé d’instrumentation est chargée automatiquement à partir du chemin de configuration *ApplicationInsights: Instrumentationkey*. Appsettings.json doit ressembler à ceci :

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :

* [Journalisation dans ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Journaux d’activité .NET dans Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
