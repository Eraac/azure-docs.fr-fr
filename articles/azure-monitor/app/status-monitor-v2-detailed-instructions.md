---
title: Instructions détaillées pour Azure Status Monitor v2 | Microsoft Docs
description: Instructions détaillées pour la mise en route avec Status Monitor v2. Surveillez les performances de site web sans avoir à redéployer le site web. Fonctionne avec les applications web ASP.NET hébergées en local, dans des machines virtuelles ou sur Azure.
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
ms.openlocfilehash: c8199c960229f9cc53cf57f9da3e1f17ebd9f5c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074161"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Status Monitor v2 : Instructions détaillées

Cet article décrit comment intégrer à PowerShell Gallery et télécharger le module ApplicationMonitor.
Il décrit les paramètres les plus courants dont vous aurez besoin pour commencer.
Il inclut également les instructions du manuel au cas où vous n’avez pas accès à internet.

> [!IMPORTANT]
> Status Monitor v2 est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="get-an-instrumentation-key"></a>Obtenir une clé d’instrumentation

Pour commencer, vous avez besoin d’une clé d’instrumentation. Pour en savoir plus, consultez [Création d’une ressource Application Insights dans Azure](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Exécuter PowerShell en tant qu’administrateur avec une stratégie d’exécution avec élévation de privilèges

**Exécuter en tant qu’administrateur**

PowerShell a besoin d’autorisations de niveau administrateur pour apporter des modifications à votre ordinateur.

**Stratégie d’exécution**
- Description : Par défaut, l’exécution des scripts PowerShell est désactivée. Nous vous recommandons d’autoriser les scripts RemoteSigned (signés à distance) uniquement pour l’étendue Current (Actuelle).
- Référence : [À propos des stratégies d’exécution](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) et [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Commande : `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Paramètre facultatif :
    - `-Force`. Pour contourner l’invite de confirmation.

**Exemples d’erreurs**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Prérequis pour PowerShell

Effectuez l’audit de votre instance de PowerShell en exécutant la commande `$PSVersionTable`.
Cette commande génère la sortie suivante :


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Ces instructions ont été écrites et testées sur un ordinateur exécutant Windows 10 et les versions répertoriées ci-dessus.

## <a name="prerequisites-for-powershell-gallery"></a>Prérequis pour PowerShell Gallery

Ces étapes préparent votre serveur au téléchargement des modules à partir de PowerShell Gallery.

> [!NOTE] 
> PowerShell Gallery est pris en charge sur Windows 10, Windows Server 2016 et PowerShell 6.
> Pour plus d’informations sur les versions antérieures, consultez [Installation de PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).


1. Exécutez PowerShell en tant qu’administrateur avec une stratégie d’exécution avec élévation de privilèges.
2. Installez le fournisseur du package NuGet.
    - Description : Vous avez besoin de ce fournisseur pour interagir avec des référentiels NuGet tels que PowerShell Gallery.
    - Référence : [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Commande : `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Paramètres facultatifs :
        - `-Proxy`. Spécifie un serveur proxy pour la demande.
        - `-Force`. Pour contourner l’invite de confirmation.
    
    Vous recevez cette invite si NuGet n’est pas configuré :
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Configurez PowerShell Gallery comme un référentiel fiable.
    - Description : Par défaut, PowerShell Gallery n’est pas un référentiel fiable.
    - Référence : [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Commande : `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Paramètre facultatif :
        - `-Proxy`. Spécifie un serveur proxy pour la demande.

    Vous recevez cette invite si PowerShell Gallery n’est pas approuvé :

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Vous pouvez confirmer cette modification et effectuer l’audit de tous les PSRepositories en exécutant la commande `Get-PSRepository`.

4. Installez la nouvelle version de PowerShellGet.
    - Description : Ce module contient les outils permettant d’obtenir d’autres modules à partir de PowerShell Gallery. La version 1.0.0.1 est fournie avec Windows 10 et Windows Server. La version 1.6.0 ou supérieure est nécessaire. Pour déterminer la version installée, exécutez la commande `Get-Command -Module PowerShellGet`.
    - Référence : [Installation de PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).
    - Commande : `Install-Module -Name PowerShellGet`.
    - Paramètres facultatifs :
        - `-Proxy`. Spécifie un serveur proxy pour la demande.
        - `-Force`. Pour ignorer l’avertissement « Déjà installé » et installer la dernière version.

    Vous recevez cette erreur si vous n’utilisez pas la version la plus récente de PowerShellGet :
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Redémarrez PowerShell. Impossible de charger la nouvelle version dans la session active. Les nouvelles sessions PowerShell chargeront la dernière version de PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Télécharger et installer le module via PowerShell Gallery

Ces étapes téléchargent le module Az.ApplicationMonitor à partir de PowerShell Gallery.

1. Veillez à remplir tous les prérequis de PowerShell Gallery.
2. Exécutez PowerShell en tant qu’administrateur avec une stratégie d’exécution avec élévation de privilèges.
3. Installez le module Az.ApplicationMonitor.
    - Référence : [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Commande : `Install-Module -Name Az.ApplicationMonitor`.
    - Paramètres facultatifs :
        - `-Proxy`. Spécifie un serveur proxy pour la demande.
        - `-AllowPrerelease`. Pour installer les versions alpha et bêta.
        - `-AcceptLicense`. Pour contourner l’invite « Accepter la licence »
        - `-Force`. Pour ignorer l’avertissement « Référentiel non approuvé ».

## <a name="download-and-install-the-module-manually-offline-option"></a>Télécharger et installer manuellement le module (option hors connexion)

Si pour une raison quelconque vous ne pouvez pas vous connecter au module PowerShell, vous pouvez manuellement télécharger et installer le module Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Téléchargement manuel du dernier fichier nupkg

1. Accédez à https://www.powershellgallery.com/packages/Az.ApplicationMonitor
2. Sélectionnez la dernière version du fichier dans la table **Historique des versions**.
3. Sous **Options d’installation**, sélectionnez **Téléchargement manuel**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Option 1 : Installer dans un répertoire de modules PowerShell
Installez le module PowerShell téléchargé manuellement dans un répertoire PowerShell afin qu’il soit détectable par les sessions PowerShell.
Pour plus d’informations, consultez l’article [Installation d’un module PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Décompresser le fichier nupkg à l’aide de Expand-Archive (v1.0.1.0)

- Description : La version de base de Microsoft.PowerShell.Archive (v1.0.1.0) ne peut pas décompresser les fichiers nupkg. Renommez le fichier avec l’extension .zip.
- Référence : [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Commande :

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Décompresser le fichier nupkg à l’aide de Expand-Archive (v1.1.0.0)

- Description : Utilisez une version actuelle de Expand-Archive pour décompresser des fichiers nupkg sans avoir à modifier l’extension.
- Référence : [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) et [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Commande :

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Option 2 : Décompresser et importer le fichier nupkg manuellement
Installez le module PowerShell téléchargé manuellement dans un répertoire PowerShell afin qu’il soit détectable par les sessions PowerShell.
Pour plus d’informations, consultez l’article [Installation d’un module PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).

Si vous installez le module dans n’importe quel répertoire, importez-le manuellement à l’aide de [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> DLL installera via des chemins d’accès relatifs.
> Stockez le contenu du package dans le répertoire du runtime prévu et confirmez que les autorisations d’accès autorisent la lecture mais pas l’écriture.

1. Remplacez l’extension par « .zip » et extrayez le contenu du package dans le répertoire d’installation prévu.
2. Recherchez le chemin de fichier de Az.ApplicationMonitor.psd1.
3. Exécutez PowerShell en tant qu’administrateur avec une stratégie d’exécution avec élévation de privilèges.
4. Chargez le module à l’aide de la commande `Import-Module Az.ApplicationMonitor.psd1`.
    

## <a name="route-traffic-through-a-proxy"></a>Router le trafic via un proxy

Lorsque vous analysez un ordinateur sur votre intranet privé, vous devez acheminer le trafic HTTP via un proxy.

Les commandes PowerShell pour télécharger et installer Az.ApplicationMonitor à partir de PowerShell Gallery prennent en charge un paramètre `-Proxy`.
Lorsque vous écrivez vos scripts d’installation, passez en revue les instructions précédentes.

Le kit de développement logiciel (SDK) Application Insights doit envoyer les données de télémétrie de votre application à Microsoft. Nous vous recommandons de configurer les paramètres de proxy de votre application dans votre fichier web.config. Pour plus d’informations, consultez [FAQ Application Insights : Pass-through du proxy](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Activer la supervision

Utilisez la commande `Enable-ApplicationInsightsMonitoring` pour activer la supervision.

Consultez la [documentation de référence de l’API](status-monitor-v2-api-enable-monitoring.md) pour obtenir une description détaillée de l’utilisation de cette cmdlet.



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
