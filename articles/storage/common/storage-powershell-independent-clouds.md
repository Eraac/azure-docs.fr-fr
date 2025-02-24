---
title: Gestion du stockage dans les clouds indépendants Azure avec Azure PowerShell | Microsoft Docs
description: Gestion du stockage dans le cloud chinois, le cloud Government et le cloud allemand avec Azure PowerShell
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/24/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 69707eec0ea1f2260ee50a48ce1dcb82dc9ddd8f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65145863"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Gestion du stockage dans les clouds indépendants Azure avec PowerShell

La plupart des gens utilisent le cloud public Azure pour leur déploiement Azure global. Pour des raisons de souveraineté, entre autres, il existe également des déploiements indépendants de Microsoft Azure. Ces déploiements indépendants sont appelés « environnements ». La liste suivante présente en détail les clouds indépendants actuellement disponibles.

* [Cloud Azure Government](https://azure.microsoft.com/features/gov/)
* [Cloud Azure de Chine géré par 21Vianet en Chine](http://www.windowsazure.cn/)
* [Cloud Azure allemand](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Utilisation d’un cloud indépendant 

Pour utiliser le stockage Azure dans l’un des clouds indépendants, vous vous connectez à ce cloud au lieu du cloud Azure public. Pour utiliser l’un des clouds indépendants plutôt que le cloud Azure public :

* Spécifiez *l’environnement* auquel se connecter.
* Déterminez et utilisez les régions disponibles.
* Utilisez le suffixe de point de terminaison approprié, qui est différent de celui du cloud Azure public.

Ces exemples nécessitent le module Az Azure PowerShell version 0.7 ou ultérieure. Dans une fenêtre PowerShell, exécutez `Get-Module -ListAvailable Az` pour trouver la version. Si aucune information ne s’affiche, ou si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps). 

## <a name="log-in-to-azure"></a>Connexion à Azure

Exécutez l’applet de commande [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) pour voir les environnements Azure disponibles :
   
```powershell
Get-AzEnvironment
```

Connectez-vous à votre compte qui a accès au cloud auquel vous souhaitez vous connecter, puis définissez l’environnement. Cet exemple montre comment se connecter à un compte qui utilise le cloud Azure Government.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Pour accéder au cloud de la Chine, utilisez l’environnement **AzureChinaCloud**. Pour accéder au cloud allemand, utilisez **AzureGermanCloud**.

À ce stade, si vous avez besoin de la liste des emplacements pour créer un compte de stockage ou une autre ressource, vous pouvez interroger les emplacements disponibles pour le cloud sélectionné à l’aide de [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location, DisplayName
```

Le tableau suivant présente les emplacements retournés pour le cloud allemand.

|Lieu | DisplayName |
|----|----|
| germanycentral | Centre de l’Allemagne|
| germanynortheast | Nord-Est de l’Allemagne | 


## <a name="endpoint-suffix"></a>Suffixe de point de terminaison

Le suffixe de point de terminaison pour chacun de ces environnements est différent de celui du point de terminaison du cloud Azure public. Par exemple, le suffixe de point de terminaison blob du cloud Azure public est **blob.core.windows.net**. Pour le cloud Government, le suffixe de point de terminaison blob est **blob.core.usgovcloudapi.net**. 

### <a name="get-endpoint-using-get-azenvironment"></a>Obtenir le point de terminaison avec Get-AzEnvironment 

Récupérez le suffixe de point de terminaison à l’aide de [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). Le point de terminaison est la propriété *StorageEndpointSuffix* de l’environnement. Les extraits de code suivants montrent comment effectuer cette opération. Toutes ces commandes retournent des valeurs similaires à « core.cloudapp.net» ou « core.cloudapi.de», etc. Ajoutez ce suffixe au service de stockage pour accéder à ce dernier. Par exemple, « queue.core.cloudapi.de » accède au service de file d’attente du cloud allemand.

L’extrait de code suivant récupère tous les environnements et le suffixe de point de terminaison pour chacun d’eux.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Cette commande retourne les résultats suivants.

| Name| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Pour récupérer toutes les propriétés de l’environnement spécifié, appelez **Get-AzEnvironment**, puis spécifiez le nom du cloud. L’extrait de code suivant retourne une liste de propriétés. Recherchez **StorageEndpointSuffix** dans cette liste. L’exemple suivant concerne le cloud allemand.

```powershell
Get-AzEnvironment -Name AzureGermanCloud 
```

Les résultats ressemblent à ce qui suit :

|Nom de la propriété|Valeur|
|----|----|
| Name | AzureGermanCloud |
| EnableAdfsAuthentication | False |
| ActiveDirectoryServiceEndpointResourceI | http://management.core.cloudapi.de/ |
| GalleryURL | https://gallery.cloudapi.de/ |
| ManagementPortalUrl | https://portal.microsoftazure.de/ | 
| ServiceManagementUrl | https://manage.core.cloudapi.de/ |
| PublishSettingsFileUrl| https://manage.microsoftazure.de/publishsettings/index |
| ResourceManagerUrl | http://management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix | .database.cloudapi.de |
| **StorageEndpointSuffix** | core.cloudapi.de |
| ... | ... | 

Pour récupérer uniquement la propriété de suffixe de point de terminaison de stockage, récupérez le cloud spécifique, puis demandez juste cette propriété.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

Cette opération retourne les informations suivantes.

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>Obtenir le point de terminaison à partir d’un compte de stockage

Vous pouvez également consulter les propriétés d’un compte de stockage pour récupérer les points de terminaison. Cela peut être utile si vous utilisez déjà un compte de stockage dans votre script PowerShell ; il vous suffit de récupérer le point de terminaison dont vous avez besoin. 

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

Pour un compte de stockage dans le cloud Government, ce code retourne les informations suivantes : 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Après la configuration de l’environnement

Pour poursuivre, vous pouvez utiliser le même code PowerShell que celui utilisé pour gérer vos comptes de stockage et accéder au plan de données comme indiqué dans l’article [Utilisation d’Azure PowerShell avec Stockage Azure](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous avez créé un groupe de ressources et un compte de stockage pour cet exercice, vous pouvez supprimer toutes les ressources en supprimant le groupe de ressources. Cette opération supprime également toutes les ressources contenues dans le groupe. Dans le cas présent, le compte de stockage créé et le groupe de ressources sont supprimés.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

* [Persistance des informations de connexion utilisateur d’une session PowerShell à l’autre](/powershell/azure/context-persistence)
* [Stockage Azure Government](../../azure-government/documentation-government-services-storage.md)
* [Guide du développeur Microsoft Azure Government](../../azure-government/documentation-government-developer-guide.md)
* [Remarques sur le développement d’applications pour Azure en Chine](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Documentation Azure Allemagne](../../germany/germany-welcome.md)
