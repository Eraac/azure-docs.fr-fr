---
title: Création de filtres avec le Kit de développement logiciel (SDK) .NET Azure Media Services
description: Cette rubrique décrit comment créer des filtres pour que votre client puisse les utiliser pour diffuser des sections spécifiques d'un flux. Media Services crée des manifestes dynamiques pour obtenir cette diffusion sélective.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/03/2019
ms.author: juliako
ms.openlocfilehash: 2bcb8762b94347f4409507fb89a18cb6c9d0dacd
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494310"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Créer des filtres à l’aide du kit SDK .NET Media Services

Quand vous transmettez votre contenu à un client (événements de streaming en direct ou vidéo à la demande), le fichier manifeste de l’actif multimédia par défaut ne permet peut-être pas au client d’interagir avec le contenu comme il le voudrait. Avec Azure Media Services, vous pouvez définir des filtres de compte et d’élément multimédia à appliquer à votre contenu. 

Pour obtenir une description détaillée de cette fonctionnalité et les scénarios où il est utilisé, consultez [manifestes dynamiques](filters-dynamic-manifest-overview.md) et [filtres](filters-concept.md).

Cette rubrique explique comment utiliser le SDK .NET Media Services pour définir un filtre pour un actif multimédia Vidéo à la demande, et créer des [filtres de compte](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) et des [filtres d’actif multimédia](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

> [!NOTE]
> Veillez à consulter [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Conditions préalables 

- Lire [Filtres et manifestes dynamiques](filters-dynamic-manifest-overview.md).
- [Créer un compte Media Services](create-account-cli-how-to.md). Veillez à mémoriser le nom du groupe de ressources et le nom du compte Media Services. 
- Obtenir les informations nécessaires pour [accéder aux API](access-api-cli-how-to.md)
- Lire la rubrique [Charger, encoder et transmettre en continu des vidéos à l’aide d’Azure Media Services](stream-files-tutorial-with-api.md) pour voir comment [démarrer avec le SDK .NET](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Définir un filtre  

Dans .NET, vous configurez les sélections de pistes avec les classes [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) et [FilterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet). 

Le code suivant définit un filtre qui inclut toutes les pistes audio contenant EC-3 et toutes les pistes vidéo ayant une vitesse de transmission entre 0 et 1 000 000.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>Créer des filtres de compte

Le code suivant montre comment utiliser .NET pour créer un filtre de compte qui inclut toutes les sélections de pistes [définies ci-dessus](#define-a-filter). 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Créer des filtres d’actif multimédia

Le code suivant montre comment utiliser .NET pour créer un filtre d’actif multimédia qui inclut toutes les sélections de pistes [définies ci-dessus](#define-a-filter). 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Associer des filtres de localisateur de diffusion en continu

Vous pouvez spécifier une liste de filtres de compte d’actif ou, s’applique également à votre localisateur de diffusion en continu. Le [l’empaquetage dynamique (point de terminaison de diffusion en continu)](dynamic-packaging-overview.md) s’applique à cette liste de filtres avec ceux de votre client spécifie l’URL. Cette combinaison génère une [manifeste dynamique](filters-dynamic-manifest-overview.md), qui est basé sur les filtres dans l’URL + filtres que vous spécifiez dans le localisateur de diffusion en continu. Nous vous recommandons d’utiliser cette fonctionnalité si vous souhaitez appliquer des filtres, mais ne souhaitez pas exposer les noms de filtre dans l’URL.

Ce qui suit C# code montre comment créer un localisateur de diffusion en continu et spécifier `StreamingLocator.Filters`. Il s’agit d’une propriété facultative qui prend un `IList<string>` noms de filtre.

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>Stream à l’aide de filtres

Lorsque vous définissez des filtres, vos clients peuvent les utiliser dans l'URL de diffusion en continu. Il serait possible d’appliquer des filtres à des protocoles de streaming à débit adaptatif : HLS (HTTP Live Streaming) Apple, MPEG-DASH et Smooth Streaming.

Le tableau suivant présente des exemples d’URL utilisant des filtres :

|Protocol|Exemples|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Étapes suivantes

[Diffuser des vidéos en streaming](stream-files-tutorial-with-api.md) 


