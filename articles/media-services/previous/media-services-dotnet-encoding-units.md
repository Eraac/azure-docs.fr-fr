---
title: Mettre à l’échelle le traitement multimédia en ajoutant des unités d’encodage - Azure | Microsoft Docs
description: Découvrez comment ajouter des unités d’encodage avec .NET
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako;milangada;
ms.openlocfilehash: 65b647a375ecbe70f7f29af5b09827f1c34dcd6f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61217163"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Mise à l’échelle de l’encodage avec le Kit de développement logiciel (SDK) .NET
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Vue d'ensemble
> [!IMPORTANT]
> Pour obtenir plus d’informations sur la mise à l’échelle du traitement multimédia, consultez la rubrique de [présentation](media-services-scale-media-processing-overview.md).
> 
> 

Pour modifier le type d’unité réservée et le nombre d’unités réservées d’encodage à l’aide du Kit de développement logiciel (SDK) .NET, procédez comme suit :

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>Ouverture d'un ticket de support

Par défaut, chaque compte Media Services a une capacité maximale de 10 unités réservées de média S2 ou S3, ou de 25 unités réservées de média S1 et de 5 unités réservées de streaming à la demande. Vous pouvez demander une limite supérieure en ouvrant un ticket de support.

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

