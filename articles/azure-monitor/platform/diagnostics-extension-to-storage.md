---
title: Stocker et afficher des données de diagnostic le stockage Azure
description: Obtenir des données de diagnostics Azure dans Stockage Azure et les afficher
services: azure-monitor
author: jpconnock
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: jeconnoc
ms.subservice: diagnostic-extension
ms.openlocfilehash: 23379e9d9bb29efb7fb026260e8245e8eb8a2d71
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60395065"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Stocker et afficher des données de diagnostic dans Azure Storage
Les données de diagnostic ne sont pas définitivement stockées, sauf si vous les transférez vers l’émulateur de stockage Microsoft Azure ou dans le stockage Azure. Une fois dans le stockage, elles peuvent être affichées avec un des outils disponibles.

## <a name="specify-a-storage-account"></a>Spécifiez un compte de stockage
Spécifiez le compte de stockage que vous souhaitez utiliser dans le fichier ServiceConfiguration.cscfg. Les informations de compte sont définies en tant que chaîne de connexion dans un paramètre de configuration. L’exemple suivant montre la chaîne de connexion par défaut créée pour un nouveau projet de service cloud dans Visual Studio :

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Vous pouvez modifier cette chaîne de connexion pour fournir des informations de compte pour un compte de stockage Azure.

Selon le type de données de diagnostic recueillies, Diagnostics Azure utilise le service Blob ou le service Table. Le tableau qui suit présente les sources de données permanentes et leur format.

| Source de données | Format de stockage |
| --- | --- |
| Journaux d’activité Azure |Table |
| Journaux d’activité IIS 7.0 |Blob |
| Journaux d’activité d’infrastructure de diagnostics Azure |Table |
| Journaux d’activité de suivi de requête ayant échoué |Blob |
| Journaux d’événements Windows |Table |
| Compteurs de performances |Table |
| Vidages sur incident |Blob |
| Journaux d’activité d’erreurs personnalisés |Blob |

## <a name="transfer-diagnostic-data"></a>Transférer les données de diagnostic
Pour le kit de développement logiciel 2.5 et versions ultérieures, la demande de transfert de données de diagnostic peut se produire dans le fichier de configuration. Vous pouvez transférer les données de diagnostic à des intervalles programmés, comme indiqué dans la configuration.

Pour le kit de développement logiciel 2.4 et versions antérieures, vous pouvez demander le transfert de données de diagnostic via le fichier de configuration et par programmation. L’approche par programmation vous permet également d’effectuer des transferts à la demande.

> [!IMPORTANT]
> Lorsque vous transférez des données de diagnostic vers un compte de stockage Microsoft Azure, vous encourez des frais pour les ressources de stockage qui utilise vos données de diagnostic.
> 
> 

## <a name="store-diagnostic-data"></a>Stockez les données de diagnostic
Les données du journal sont stockées dans le stockage Blob ou de Table avec les noms suivants :

**Tables**

* **WadLogsTable** : journaux d’activité rédigés dans le code à l’aide de l’écouteur de suivi.
* **WADDiagnosticInfrastructureLogsTable** : modifications de configuration et d’analyse de diagnostic.
* **WADDirectoriesTable** : répertoires que le moniteur de diagnostic surveille.  Cela inclut les journaux d’activité IIS, les journaux d’activité de requêtes ayant échoué et les répertoires personnalisés IIS.  L’emplacement du fichier journal blob est spécifié dans le champ de conteneur et le nom de l’objet blob se trouve dans le champ RelativePath.  Le champ AbsolutePath indique l’emplacement et le nom du fichier tel qu’il existait sur la machine virtuelle Azure.
* **WADPerformanceCountersTable** : les compteurs de performance.
* **WADWindowsEventLogsTable** : journaux des événements Windows.

**Objets blob**

* **wad-control-container** : (uniquement pour le kit de développement logiciel 2.4 et précédents) contient les fichiers de configuration XML qui contrôlent les diagnostics Azure.
* **wad-iis-failedreqlogfiles** : contient des informations tirées des journaux d’activité de requêtes de IIS ayant échoué.
* **wad-iis-logfiles** : contient des informations sur les journaux d’activité IIS.
* **« personnalisé »** – conteneur personnalisé basé sur la configuration des répertoires contrôlés par la surveillance de diagnostic.  Le nom de ce conteneur d’objets blobs est spécifié dans WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Outils permettant d’afficher les données de diagnostic
Plusieurs outils sont disponibles pour afficher les données après leur transfert vers le stockage. Par exemple :

* Explorateur de serveurs dans Visual Studio : si vous avez installé Microsoft Azure Tools pour Microsoft Visual Studio, vous pouvez utiliser le nœud de stockage Azure dans l’Explorateur de serveurs pour afficher des objets blobs en lecture seule et les données du tableau depuis vos comptes de stockage Azure. Vous pouvez afficher des données à partir de votre compte d’émulateur de stockage local et de comptes de stockage que vous avez créés pour Azure. Pour plus d’informations, consultez [Consultation et gestion des ressources de stockage avec l’Explorateur de serveurs](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [L’explorateur de stockage Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome qui vous permet d’utiliser facilement les données Azure Storage sur Windows, OSX et Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) inclut Diagnostics Azure Manager qui vous permet d’afficher, de télécharger et de gérer les données de diagnostic collectées par les applications s’exécutant dans Azure.

## <a name="next-steps"></a>Étapes suivantes
[Assurer le suivi du flux dans une application Cloud Services avec Diagnostics Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


