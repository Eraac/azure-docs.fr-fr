---
title: Utiliser des lecteurs existants pour lire du contenu - Azure | Microsoft Docs
description: Cette rubrique répertorie les lecteurs existants que vous pouvez utiliser pour lire votre contenu.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c96710d6dcca9f5ef99b3a02a0bc875d433f814d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463386"
---
# <a name="playing-your-content-with-existing-players"></a>Lecture de votre contenu à l’aide des lecteurs existants
Azure Media Services prend en charge de nombreux formats de streaming populaires, tels que Smooth Streaming, le streaming HTTP et MPEG-Dash. Cette rubrique vous oriente vers les lecteurs existants que vous pouvez utiliser pour tester votre flux de données.

### <a name="the-azure-portal-media-services-content-player"></a>Le lecteur de contenu Media Services sur le portail Azure
Le **portail Azure** propose un lecteur de contenu que vous pouvez utiliser pour tester votre vidéo.

Cliquez sur la vidéo de votre choix (assurez-vous qu’elle a été [publiée](media-services-portal-publish.md)), puis sur le bouton **Lire** situé en bas du portail.

Certaines considérations s’appliquent :

* Le **lecteur de contenu de Media Services** assure la lecture depuis le point de terminaison de streaming par défaut. Si vous souhaitez lire à partir d’un autre point de terminaison de streaming que celui par défaut, utilisez un autre lecteur. Par exemple, [Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player
Utilisez [Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html) pour lire votre contenu (protégé ou non) dans l'un des formats suivants :

* Smooth Streaming
* MPEG DASH
* HLS
* MP4 progressif

### <a name="flash-player"></a>Flash Player
#### <a name="aes-encrypted-with-token"></a>Chiffrement AES avec jeton
[https://aestoken.azurewebsites.net](https://aestoken.azurewebsites.net)

### <a name="silverlight-players"></a>Lecteurs Silverlight

#### <a name="playready-with-token"></a>PlayReady avec jeton
[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Lecteurs DASH
[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Autres
Pour tester les URL HLS, vous pouvez également utiliser :

* **Safari** sur un appareil iOS ou
* **3ivx HLS Player** sous Windows.

## <a name="developing-video-players"></a>Développement de lecteurs vidéo
Pour plus d’informations sur le développement de vos propres lecteurs, consultez la page [Développement de lecteurs vidéo](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
