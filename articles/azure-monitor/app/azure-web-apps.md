---
title: Analyser les performances d’Azure App Service | Microsoft Docs
description: Analyse des performances des applications pour les services d’application Azure. Analysez la charge, le temps de réponse et les dépendances dans des graphiques, et définissez des alertes sur les performances.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mbullwin
ms.openlocfilehash: 5594c1f3517bf3d3f74841493df3c683304fa3f5
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502092"
---
# <a name="monitor-azure-app-service-performance"></a>Analyser les performances d’Azure App Service

Activer la supervision de vos applications web .NET et .NET Core exécutées sur [Azure App Services](https://docs.microsoft.com/azure/app-service/) n’a jamais été aussi facile. Alors qu’auparavant vous deviez installer manuellement une extension de site, la dernière version de l’agent/extension est désormais intégrée à l’image App Service par défaut. Cet article explique pas à pas comment activer la supervision Application Insights et vous donne des conseils d’automatisation du processus pour les déploiements à grande échelle.

> [!NOTE]
> L’ajout manuel d’une extension de site Application Insights via **Outils de développement** > **Extensions** est une méthode dépréciée. Cette méthode d’installation de l’extension était dépendante des mises à jour manuelles pour chaque nouvelle version. La version stable la plus récente de l’extension est désormais [préinstallée](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) dans l’image App Service. Les fichiers se trouvent dans `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` et sont automatiquement mis à jour avec chaque version stable. Si vous suivez les instructions ci-dessous pour activer la supervision basée sur un agent, l’extension dépréciée sera automatiquement supprimée.

## <a name="enable-application-insights"></a>Activer Application Insights

Il existe deux façons d’activer la supervision des applications hébergées par Azure App Services :

* La **supervision basée sur un agent de l’application** (ApplicationInsightsAgent).  
    * Cette méthode est la plus facile à activer, car elle ne nécessite aucune configuration avancée. Elle est souvent appelée supervision « runtime ». Pour Azure App Services, nous vous conseillons d’activer ce niveau de supervision au minimum. Ensuite, selon votre scénario spécifique, vous pouvez évaluer si une supervision plus avancée par une instrumentation manuelle est nécessaire.

* L’**instrumentation manuelle de l’application avec le code** en installant le SDK Application Insights.

    * Cette approche est beaucoup plus personnalisable, mais elle nécessite d’[ajouter une dépendance sur les packages NuGet du SDK Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Cette méthode implique également de gérer vous-même l’installation des mises à jour vers la dernière version des packages.

    * Utilisez cette méthode si vous devez effectuer des appels d’API personnalisés pour suivre les événements/dépendances qui ne sont pas capturés par défaut avec la supervision basée sur un agent. Pour en savoir plus, consultez l’[article sur l’API Application Insights pour les événements et mesures personnalisés](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics).

> [!NOTE]
> Si les deux méthodes, la supervision basée sur un agent et l’instrumentation manuelle basée sur un SDK, sont détectées, seuls les paramètres de l’instrumentation manuelle sont appliqués. Cela évite que des données en double soient envoyées. Pour en savoir plus, consultez la [section de résolution des problèmes](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) ci-après.

## <a name="enable-agent-based-monitoring-net"></a>Activer la supervision basée sur un agent pour .NET

> [!NOTE]
> APPINSIGHTS_JAVASCRIPT_ENABLED et urlCompression ne peuvent pas être utilisés ensemble. Pour plus d’informations, consultez la [section de résolution des problèmes](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).


1. **Sélectionnez Application Insights** dans le panneau de configuration Azure pour votre service d’application.

    ![Sous Paramètres, choisissez Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Choisissez de créer une nouvelle ressource, sauf si vous avez déjà configuré une ressource Application Insights pour cette application. 

     > [!NOTE]
     > Quand vous cliquez sur **OK** pour créer la ressource, vous êtes invité à **Appliquer les paramètres de supervision**. Si vous sélectionnez **Continuer**, votre nouvelle ressource Application Insights est liée à votre service d’application et **déclenche un redémarrage de votre service d’application**. 

     ![Instrumenter votre application web](./media/azure-web-apps/create-resource-01.png)

2. Après avoir spécifié la ressource à utiliser, vous pouvez choisir la façon dont Application Insights doit collecter les données par plateforme pour votre application. La supervision des applications ASP.NET est activée par défaut avec deux niveaux de collecte différents.

    ![Choisir les options par plateforme](./media/azure-web-apps/choose-options-new.png)

   * Le niveau de **collecte De base** .NET fournit les fonctionnalités APM principales pour une instance unique.

   * Le niveau de **collecte Recommandé** .NET :
       * Ajoute les tendances d’utilisation de l’UC, de la mémoire et des E/S.
       * Met en corrélation les microservices entre les limites de requête/dépendance.
       * Collecte les tendances d’utilisation et permet la mise en corrélation des résultats de la disponibilité avec les transactions.
       * Collecte les exceptions non gérées par le processus hôte.
       * Améliore la précision des métriques APM avec charge, quand l’échantillonnage est utilisé.

3. Pour configurer des paramètres tels que l’échantillonnage, que vous pouviez auparavant contrôler au moyen du fichier applicationinsights.config, vous pouvez maintenant interagir avec ces mêmes paramètres en définissant les paramètres de l’application avec le préfixe correspondant. 

    * Par exemple, pour changer le pourcentage d’échantillonnage initial, créez un paramètre d’application `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` avec la valeur `100`.

    * Pour obtenir la liste des paramètres du processeur de télémétrie pris en charge pour l’échantillonnage adaptatif, consultez le [code](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) et la [documentation associée](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

## <a name="enable-agent-based-monitoring-net-core"></a>Activer la supervision basée sur un agent pour .NET Core

Les versions suivantes de .NET Core sont prises en charge : ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2

Le ciblage du framework complet à partir de .NET Core, le déploiement autonome et ASP.NET Core 3.0 **ne sont actuellement pas pris en charge** avec la supervision basée sur un agent/une extension. (L’[instrumentation manuelle](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) avec le code peut être utilisée dans tous les scénarios précédents.)

1. **Sélectionnez Application Insights** dans le panneau de configuration Azure pour votre service d’application.

    ![Sous Paramètres, choisissez Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Choisissez de créer une nouvelle ressource, sauf si vous avez déjà configuré une ressource Application Insights pour cette application. 

     > [!NOTE]
     > Quand vous cliquez sur **OK** pour créer la ressource, vous êtes invité à **Appliquer les paramètres de supervision**. Si vous sélectionnez **Continuer**, votre nouvelle ressource Application Insights est liée à votre service d’application et **déclenche un redémarrage de votre service d’application**. 

     ![Instrumenter votre application web](./media/azure-web-apps/create-resource-01.png)

2. Après avoir spécifié la ressource à utiliser, vous pouvez choisir la façon dont Application Insights doit collecter les données par plateforme pour votre application. .NET Core prend en charge le niveau de **collecte Recommandé** ou **Désactivé** pour .NET Core 2.0, 2.1 et 2.2.

    ![Choisir les options par plateforme](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-net"></a>Activer la supervision côté client pour .NET

La supervision côté client est activée pour ASP.NET. Pour activer la supervision côté client :

* Sélectionnez **Paramètres** >** **Paramètres de l’application****
   * Sous Paramètres de l’application, ajoutez un nouveau **nom de paramètre d’application** et une nouvelle **valeur** :

     Nom : `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valeur: `true`

   * **Enregistrez** les paramètres et **Redémarrez** votre application.

![Capture d’écran de l’interface utilisateur Paramètres de l’application](./media/azure-web-apps/appinsights-javascript-enabled.png)

Pour désactiver la supervision côté client, supprimez la paire clé-valeur associée dans les paramètres de l’application, ou définissez la valeur sur false.

## <a name="enable-client-side-monitoring-net-core"></a>Activer la supervision côté client pour .NET Core

La supervision côté client est **activée par défaut** pour les applications .NET Core configurées avec le niveau de **collecte Recommandé**, que le paramètre d’application « APPINSIGHTS_JAVASCRIPT_ENABLED » soit défini ou non.

Si vous avez besoin de désactiver la supervision côté client, effectuez les étapes suivantes :

* Sélectionnez **Paramètres** > **Paramètres de l’application**
   * Sous Paramètres de l’application, ajoutez un nouveau **nom de paramètre d’application** et la **valeur** associée :

     Nom : `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valeur: `false`

   * **Enregistrez** les paramètres et **Redémarrez** votre application.

![Capture d’écran de l’interface utilisateur Paramètres de l’application](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>Automatiser la supervision

Pour activer la collecte de données de télémétrie avec Application Insights, il vous suffit de définir les paramètres de l’application :

   ![Paramètres d’application App Service avec les paramètres Application Insights disponibles](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Définitions des paramètres d’application

|Nom du paramètre d’application |  Définition | Valeur |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extension principale, qui contrôle la supervision runtime. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Dans le mode par défaut uniquement, les fonctionnalités essentielles sont activées afin de garantir des performances optimales. | `default` ou `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Contrôle si le moteur de réécriture binaire `InstrumentationEngine` est activé. Ce paramètre impacte les performances ainsi que les temps de démarrage/démarrage à froid. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Contrôle si le texte de tables SQL et Azure est capturé, ainsi que les appels de dépendances. Avertissement sur les performances : ce paramètre nécessite `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Paramètres d’application App Service avec Azure Resource Manager

Les paramètres d’application pour App Services peuvent être gérés et configurés à l’aide de [modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Cette méthode s’utilise pour déployer de nouvelles ressources App Service avec Azure Resource Manager Automation, ou pour modifier les paramètres de ressources existantes.

La structure de base des paramètres d’application JSON pour un service d’application est celle-ci :

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

Pour obtenir un exemple de modèle Azure Resource Manager où les paramètres d’application sont configurés pour Application Insights, consultez ce [modèle](https://github.com/Andrew-MSFT/BasicImageGallery), en particulier la section débutant à la [ligne 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatisez la création d’une ressource Application Insights et créez un lien vers votre nouvelle application App Service.

Pour créer un modèle Azure Resource Manager configuré avec tous les paramètres Application Insights par défaut, commencez le même processus que si vous souhaitiez créer une application web avec Application Insights activé.

Sélectionnez **Options d’automatisation**

   ![Menu pour créer une application web App Service](./media/azure-web-apps/create-web-app.png)

Cette option génère le dernier modèle Azure Resource Manager configuré avec tous les paramètres nécessaires.

  ![Modèle d’application web App Service](./media/azure-web-apps/arm-template.png)

Dans l’exemple ci-dessous, remplacez toutes les instances de `AppMonitoredSite` par le nom de votre site :

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

> [!NOTE]
> Le modèle générera les paramètres de l’application en mode « par défaut ». Ce mode offre des performances optimisées, tout en vous laissant la possibilité de modifier le modèle pour activer les fonctionnalités de votre choix.

### <a name="enabling-through-powershell"></a>Activation par le biais de PowerShell

Pour activer la supervision de l’application avec PowerShell, modifiez uniquement les paramètres de l’application sous-jacents. L’exemple ci-dessous active la supervision de l’application pour un site web appelé « AppMonitoredSite » dans le groupe de ressources « AppMonitoredRG », et configure les données à envoyer à la clé d’instrumentation « 012345678-abcd-ef01-2345-6789abcd ».

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Mise à niveau de l’agent/extension de supervision

### <a name="upgrading-from-versions-289-and-up"></a>Mise à niveau à partir des versions 2.8.9 et ultérieures

La mise à niveau à partir de la version 2.8.9 s’effectue automatiquement, sans aucune action supplémentaire de votre part. Les nouveaux bits de supervision sont remis en arrière-plan au service d’application cible, et sont collectés au redémarrage de l’application.

Pour connaître la version de votre extension, consultez `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Capture d’écran du chemin d’URL http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Mise à niveau à partir des versions 1.0.0 - 2.6.5

À compter de la version 2.8.9, l’extension de site préinstallée est utilisée. Si vous avez une version antérieure, vous pouvez la mettre à jour de deux façons :

* [Mettre à niveau par activation dans le portail](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Même si vous avez installé l’extension Application Insights pour Azure App Service, l’interface utilisateur affiche uniquement le bouton **Activer**. En arrière-plan, l’ancienne extension de site privée sera supprimée.)

* [Mettre à niveau par le biais de PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell) :

    1. Définissez les paramètres de l’application pour activer l’extension de site préinstallée ApplicationInsightsAgent. Consultez [Activation par le biais de PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Supprimez manuellement l’extension de site privée nommée extension Application Insights pour Azure App Service.

Si vous réalisez la mise à niveau à partir d’une version antérieure à 2.5.1, vérifiez que les DLL ApplicationInsigths sont supprimées du dossier bin de l’application. Pour plus d’informations, [consultez les étapes de résolution des problèmes](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Résolution de problèmes

Vous trouverez ci-après les étapes à suivre pas à pas pour résoudre les problèmes rencontrés avec la supervision basée sur un agent/une extension pour les applications .NET et .NET Core exécutées sur Azure App Services.

> [!NOTE]
> Les applications Java et Node.js sont uniquement prises en charge sur Azure App Services avec l’instrumentation manuelle basée sur un SDK. Les étapes ci-dessous ne s’appliquent donc pas à ces scénarios.

1. Vérifiez que l’application est supervisée via `ApplicationInsightsAgent`.
    * Vérifiez que le paramètre d’application `ApplicationInsightsAgent_EXTENSION_VERSION` est défini à une valeur « ~2 ».
2. Assurez-vous que l’application remplit les prérequis de la supervision.
    * Accédez à `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Capture d’écran de la page de résultats https://yoursitename.scm.azurewebsites/applicationinsights](./media/azure-web-apps/app-insights-sdk-status.png)

    * Vérifiez que l’extension est en cours d’exécution (`Application Insights Extension Status` affiche `Pre-Installed Site Extension, version 2.8.12.1527, is running.`)
        * Si elle ne l’est pas, suivez les [instructions pour activer la supervision Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Vérifiez la présence d’une source d’état similaire à cette valeur : `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * S’il n’y en a pas, cela signifie que l’application n’est pas en cours d’exécution ou n’est pas prise en charge. Pour vous assurer que l’application est en cours d’exécution, essayez d’accéder manuellement à l’URL ou aux points de terminaison de l’application, afin d’exposer les informations d’exécution.

    * Vérifiez que `IKeyExists` a la valeur `true`
        * Si la valeur est false, ajoutez APPINSIGHTS_INSTRUMENTATIONKEY avec votre Guid ikey à vos paramètres d’application.

    * Vérifiez qu’il n’y a pas d’entrées `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly` et `AppContainsAspNetTelemetryCorrelationAssembly`.
        * S’il y en a, supprimez les packages suivants de votre application : `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource` et `Microsoft.AspNet.TelemetryCorrelation`.

Ce tableau explique plus en détail la signification de ces valeurs d’erreur, leurs causes sous-jacentes et les corrections conseillées :

|Valeur d’erreur|Explication|Correctif
|---- |----|---|
| `AppAlreadyInstrumented:true` | Cette valeur indique que l’extension a détecté que certains éléments du SDK sont déjà présents dans l’application et qu’ils seront mis en back-off. Cela peut être dû à la présence d’une référence à `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation` ou `Microsoft.ApplicationInsights`  | Supprimez la référence. Certaines de ces références sont ajoutées par défaut à partir de certains modèles Visual Studio, et les versions antérieures de Visual Studio ajoutent parfois des références à `Microsoft.ApplicationInsights`.
|`AppAlreadyInstrumented:true` | Si l’application cible .NET Core 2.1 ou 2.2 et fait référence au métapaquet [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All), elle utilise Application Insights, et l’extension sera mise en back-off. | Pour les clients sur .NET Core 2.1 ou 2.2, il est [conseillé](https://github.com/aspnet/Announcements/issues/287) d’utiliser le métapaquet Microsoft.AspNetCore.App à la place.|
|`AppAlreadyInstrumented:true` | Cette valeur peut également être due à la présence des DLL ci-dessus, d’un déploiement précédent, dans le dossier de l’application. | Supprimez ces DLL du dossier de l’application.|
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Cette valeur indique que l’extension a détecté la présence de références à `Microsoft.AspNet.TelemetryCorrelation` dans l’application et qu’elle sera mise en back-off. | Supprimez la référence.
|`AppContainsDiagnosticSourceAssembly**:true`|Cette valeur indique que l’extension a détecté la présence de références à `System.Diagnostics.DiagnosticSource` dans l’application et qu’elle sera mise en back-off.| Supprimez la référence.
|`IKeyExists:false`|Cette valeur indique que la clé d’instrumentation n’est pas présente dans le paramètre d’application `APPINSIGHTS_INSTRUMENTATIONKEY`. Causes possibles : Vous avez peut-être supprimé accidentellement les valeurs, oublié de définir les valeurs dans le script d’automatisation, etc. | Vérifiez que le paramètre est défini dans les paramètres d’application App Service.

### <a name="appinsightsjavascriptenabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED et urlCompression ne sont pas pris en charge

Si vous utilisez APPINSIGHTS_JAVASCRIPT_ENABLED = true là où du contenu est encodé, vous pouvez voir ce type d’erreur : 

- Erreur de réécriture d’URL 500
- Erreur du module de réécriture d’URL 500.53 avec un message indiquant que les règles de réécriture sortantes ne peuvent pas être appliquées quand le contenu de la réponse HTTP est encodé (« gzip »). 

Cela est dû au fait que le paramètre d’application APPINSIGHTS_JAVASCRIPT_ENABLED est défini sur true alors qu’il y a du contenu encodé en même temps. Ce scénario n’est pas pris en charge actuellement. La solution de contournement consiste à supprimer APPINSIGHTS_JAVASCRIPT_ENABLED de vos paramètres d’application. Malheureusement, cela signifie que si l’instrumentation JavaScript côté client/navigateur est toujours requise, des références manuelles au SDK sont nécessaires pour vos pages web. Suivez les [instructions](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) pour activer l’instrumentation manuelle avec le SDK JavaScript.

Pour avoir les toutes dernières informations sur l’extension/agent Application Insights, consultez les [notes de publication](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

## <a name="next-steps"></a>Étapes suivantes
* [Exécuter le profileur sur une application dynamique](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - analyse les fonctions Azure avec Application Insights
* [Autorisation de l’envoi de diagnostics Azure](../platform/diagnostics-extension-to-application-insights.md) vers Application Insights.
* [Analyse des mesures d’intégrité du service](../platform/data-platform.md) pour vous assurer que votre service est disponible et réactif.
* [Réceptions de notifications d’alerte](../platform/alerts-overview.md) lorsque des événements opérationnels se produisent ou que des mesures dépassent un seuil.
* Utilisation [d’Application Insights pour les pages Web et les applications JavaScript](javascript.md) pour obtenir les données de télémétrie du client à partir des navigateurs qui consultent une page web.
* [Configuration des tests de disponibilité web](monitor-web-app-availability.md) , pour recevoir des alertes en cas d’interruption de votre site.
