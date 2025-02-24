---
title: Configurer des applications ASP.NET Core - Azure App Service | Microsoft Docs
description: Découvrir la configuration d’applications ASP.NET Core dans Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: f2781e3cc2433f73ba7ff33e5c452e29de746adf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956206"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Configurer une application ASP.NET Core pour Azure App Service

Les applications ASP.NET Core doivent être déployées en tant que fichiers binaires compilés. L’outil de publication de Visual Studio génère la solution, puis déploie directement les fichiers binaires compilés, alors que le moteur de déploiement d’App Service déploie en premier le référentiel de code, puis compile les fichiers binaires.

Ce guide fournit des concepts et des instructions clés aux développeurs ASP.NET Core qui utilisent un conteneur Linux intégré dans App Service. Si vous n’avez jamais utilisé Azure App Service, suivez au préalable le [Guide de démarrage rapide d’ASP.NET Core](quickstart-dotnetcore.md) et le [didacticiel sur ASP.NET Core avec SQL Database](tutorial-dotnetcore-sqldb-app.md).

## <a name="show-net-core-version"></a>Afficher la version de .NET Core

Pour afficher la version actuelle de .NET Core, exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Pour afficher toutes les versions de .NET Core prises en charge, exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) :

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Définir la version de .NET Core

Exécutez la commande suivante dans [Cloud Shell](https://shell.azure.com) pour définir la version 2.1 de .NET Core :

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Accéder aux variables d’environnement

Dans App Service, vous pouvez [définir les paramètres de l’application](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) en dehors de votre code d’application. Vous pouvez ensuite y accéder à l’aide du modèle ASP.NET standard :

```csharp
include Microsoft.Extensions.Configuration;
// retrieve App Service app setting
System.Configuration.ConfigurationManager.AppSettings["MySetting"]
// retrieve App Service connection string
Configuration.GetConnectionString("MyDbConnection")
```

Si vous configurez un paramètre d’application portant le même nom dans App Service et dans *Web.config*, la valeur d’App Service est prioritaire sur la valeur de Web.config. La valeur de Web.config vous permet de déboguer l’application localement, tandis que la valeur d’App Service vous permet d’exécuter l’application dans le produit avec les paramètres de production. Les chaînes de connexion fonctionnent de la même façon. De cette façon, vous pouvez conserver les secrets de votre application en dehors de votre référentiel de code et accéder aux valeurs appropriées sans modifier votre code.

## <a name="get-detailed-exceptions-page"></a>Accéder à la page d’exceptions détaillées

Lorsque votre application ASP.NET génère une exception dans le débogueur Visual Studio, le navigateur affiche une page d’exceptions détaillées, mais dans App Service, cette page est remplacée par une erreur **HTTP 500** générique : **Une erreur s’est produite lors du traitement de votre demande**. . Pour afficher la page d’exceptions détaillées dans App Service, ajoutez le paramètre d’application `ASPNETCORE_ENVIRONMENT` à l’application en exécutant la commande suivante dans <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Détecter une session HTTPS

Dans App Service, une [terminaison SSL](https://wikipedia.org/wiki/TLS_termination_proxy) se produit au niveau des équilibreurs de charge réseau. Toutes les requêtes HTTPS accèdent donc à votre application en tant que requêtes HTTP non chiffrées. Si votre logique d’application doit savoir si les demandes utilisateur sont chiffrées ou non, configurez l’intergiciel Forwarded Headers dans *Startup.cs* :

- Configurez l’intergiciel avec [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) pour transférer les en-têtes `X-Forwarded-For` et `X-Forwarded-Proto` dans `Startup.ConfigureServices`.
- Ajoutez des plages d’adresses IP privées aux réseaux connus afin que l’intergiciel puisse approuver l’équilibreur de charge d’App Service.
- Appelez la méthode [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) dans `Startup.Configure` avant les autres intergiciels.

Assemblez les trois éléments ; le code ressemble à l’exemple suivant :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Pour plus d’informations, consultez l’article [Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Déployer des solutions à projets multiples

Lorsque vous déployez un référentiel ASP.NET vers le moteur de déploiement avec un fichier *.csproj* contenu dans le répertoire racine, le moteur déploie le projet. Lorsque vous déployez un référentiel ASP.NET avec un fichier *.sln* contenu dans le répertoire racine, le moteur sélectionne le premier site web ou le premier projet d’application web qu’il trouve comme application App Service. Il se peut que le moteur ne sélectionne pas le projet de votre choix.

Pour déployer une solution à projets multiples, vous pouvez spécifier le projet à utiliser dans App Service de deux manières différentes :

### <a name="using-deployment-file"></a>Utilisation d’un fichier .deployment

Ajoutez un fichier *.deployment* à la racine du référentiel, puis ajoutez le code suivant :

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Utilisation des paramètres d’application

Dans <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>, ajoutez un paramètre d’application à l’application App Service en exécutant la commande CLI suivante. Remplacez *\<app-name>* , *\<resource-group-name>* et *\<project-name>* par les valeurs appropriées.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Ouvrir une session SSH dans un navigateur

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : application ASP.NET Core avec SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Questions fréquentes (FAQ) sur App Service sur Linux](app-service-linux-faq.md)