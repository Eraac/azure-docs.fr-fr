---
title: Vue d’ensemble du streaming en direct avec Azure Media Services v3 | Microsoft Docs
description: Cet article offre une vue d’ensemble du streaming en direct à l’aide d’Azure Media Services v3.
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
ms.date: 06/16/2019
ms.author: juliako
ms.openlocfilehash: 0abc3eec380cccae2672d0e9aa4a3a4c7199362f
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295660"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Streaming en direct avec Azure Media Services v3

Azure Media Services vous permet de transmettre des événements en direct auprès de vos clients dans le cloud Azure. Pour cela, vous avez besoin des éléments suivants :  

- Une caméra, pour capturer l’événement en direct.<br/>Pour obtenir des idées de configuration, consultez [Simple and portable event video gear setup]( https://link.medium.com/KNTtiN6IeT).

    Si vous n’avez pas accès à une caméra, des outils tels que [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) peuvent être utilisés pour générer un flux en direct à partir d’un fichier vidéo.
- Un encodeur vidéo live, pour convertir les signaux de la caméra (ou d’un autre appareil, comme un ordinateur portable) en flux de contribution qui sera ensuite envoyé à Media Services. Le flux de contribution peut inclure des signaux de publicité, tels que les marqueurs SCTE-35.<br/>Pour obtenir une liste d’encodeurs de streaming live recommandés, consultez [Encodeurs de streaming live](recommended-on-premises-live-encoders.md). Consultez également ce billet de blog : [Live streaming production with OBS](https://link.medium.com/ttuwHpaJeT).
- Des composants dans Media Services, pour ingérer, prévisualiser, empaqueter, enregistrer, chiffrer et diffuser l’événement en direct auprès de vos clients, ou dans un CDN en vue d’une diffusion ultérieure.

Cet article offre une vue d’ensemble et des conseils relatifs au streaming en direct avec Media Services ainsi que des liens vers d’autres articles pertinents.
 
> [!NOTE]
> Actuellement, vous ne pouvez pas utiliser le portail Azure pour gérer des ressources v3. Utilisez l’[API REST](https://aka.ms/ams-v3-rest-ref), l’interface [CLI](https://aka.ms/ams-v3-cli-ref) ou l’un des kits [SDK](media-services-apis-overview.md#sdks) pris en charge.

## <a name="dynamic-packaging"></a>Empaquetage dynamique

Avec Media Services, vous pouvez utiliser [l’empaquetage dynamique](dynamic-packaging-overview.md), qui vous permet de prévisualiser et de diffuser vos streams en direct aux [formats MPEG DASH, HLS et Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) à partir du flux de contribution envoyé au service. Vos clients peuvent alors lire le flux en direct au moyen de n’importe quel lecteur compatible avec HLS, DASH ou Smooth Streaming. Vous pouvez utiliser le lecteur multimédia [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) dans vos applications web ou mobiles afin de transmettre votre flux dans un de ces protocoles.

## <a name="dynamic-encryption"></a>Chiffrement dynamique

Le chiffrement dynamique permet de chiffrer dynamiquement votre contenu en direct ou à la demande avec AES-128 ou un des trois systèmes principaux de gestion des droits numériques (DRM) : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et licences (PlayReady, Widevine et FairPlay) DRM aux clients autorisés. Pour plus d’informations, consultez [Empaquetage dynamique](content-protection-overview.md).

## <a name="dynamic-manifest"></a>Manifeste dynamique

Le filtrage dynamique permet de contrôler le nombre de pistes, de formats, de vitesses de transmission et de fenêtres de temps de présentation envoyés aux lecteurs. Pour plus d’informations, consultez [Filtres et manifestes dynamiques](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Types d’événements en direct

Les [événements en direct](https://docs.microsoft.com/rest/api/media/liveevents) sont chargés de la réception et du traitement des flux vidéo en direct. Un événement en direct peut être de deux types : en transmission en direct ou en encodage en direct. Pour plus d’informations sur le streaming en direct dans Media Services v3, consultez [Événements en direct et sorties en direct](live-events-outputs-concept.md).

### <a name="pass-through"></a>Requête directe

![transmission directe](./media/live-streaming/pass-through.svg)

Quand vous utilisez l’**événement en direct** de type pass-through, vous chargez l’encodeur live local de générer un flux vidéo à vitesse de transmission multiple et d’envoyer ce flux comme flux de contribution à l’événement en direct (à l’aide du protocole RTMP ou MP4 fragmenté d’entrée). L’événement en direct envoie ensuite les flux vidéo entrants au packager dynamique (point de terminaison de streaming) sans transcodage supplémentaire. Une transmission LiveEvent est optimisée pour les événements en direct de longue durée ou le streaming en direct linéaire sans interruption (24 heures sur 24, 365 jours par an). 

### <a name="live-encoding"></a>Encodage en direct  

![encodage en temps réel](./media/live-streaming/live-encoding.svg)

Quand vous utilisez l’encodage cloud avec Media Services, vous configurez votre encodeur live local pour qu’il envoie un flux vidéo à une seule vitesse de transmission comme flux de contribution (jusqu’à 32 Mbit/s au total) à l’événement en direct (à l’aide du protocole RTMP ou MP4 fragmenté). L’événement en direct transcode le flux de débit unique entrant en [flux vidéo à différents débits](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) avec différentes résolutions pour améliorer la diffusion et la rendre disponible pour les appareils de lecture par le biais de protocoles standard du secteur tels que MPEG-DASH, Apple HTTP Live Streaming (HLS) et Microsoft Smooth Streaming. 

## <a name="live-streaming-workflow"></a>Workflow de streaming en direct

Pour comprendre le flux de travail de streaming en direct dans Media Services v3, vous devez commencer par évaluer et comprendre les concepts suivants : 

- [Points de terminaison de streaming](streaming-endpoint-concept.md)
- [Événements en direct et sorties en direct](live-events-outputs-concept.md)
- [Localisateurs de diffusion en continu](streaming-locators-concept.md)

### <a name="general-steps"></a>Étapes générales

1. Dans votre compte Media Services, vérifiez que le **point de terminaison de streaming** (Origin) est en cours d’exécution. 
2. Créez un [événement en temps réel](live-events-outputs-concept.md). <br/>Lors de la création de l’événement, vous pouvez spécifier qu’il démarre automatiquement. Sinon, lancez-le dès que vous souhaitez commencer le streaming.<br/> Lorsque le démarrage automatique est défini sur true, l’événement en direct démarre juste après sa création. La facturation commence donc dès que son exécution démarre. Vous devez appeler explicitement la commande Stop sur la ressource de l’événement en direct pour arrêter toute facturation supplémentaire. Pour plus d’informations, consultez [États et facturation des événements en direct](live-event-states-billing.md).
3. Récupérez la ou les URL ingérées et configurez votre encodeur local afin qu’il utilise cette URL pour envoyer le flux de contribution.<br/>Voir [Encodeurs live recommandés](recommended-on-premises-live-encoders.md).
4. Récupérez l’URL d’aperçu et utilisez-la pour vérifier que l’entrée de l’encodeur est bien reçue.
5. Créez un objet **Asset**.
6. Créez un objet **LiveOutput** et utilisez le nom de l’objet Asset que vous venez de créer.<br/>La **sortie en direct** archive le flux dans l’**actif multimédia**.
7. Créer un **localisateur de streaming**  avec les types intégrés de la [stratégie de streaming](streaming-policy-concept.md)
8. Listez les chemins dans le **Localisateur de streaming** pour récupérer les URL à utiliser (elles sont déterministes).
9. Récupérez le nom d’hôte du **point de terminaison de streaming** (Origin) à partir duquel vous souhaitez effectuer le streaming.
10. Combinez l’URL de l’étape 8 avec le nom d’hôte de l’étape 9 pour obtenir l’URL complète.
11. Si vous ne souhaitez plus afficher votre **événement en direct**, arrêtez le streaming de l’événement et supprimez le **localisateur de streaming**.

## <a name="other-important-articles"></a>Autres articles importants

- [Encodeurs live recommandés](recommended-on-premises-live-encoders.md)
- [Utiliser un magnétoscope numérique cloud](live-event-cloud-dvr.md)
- [Comparaison des fonctionnalités des types d'événements en direct](live-event-types-comparison.md)
- [États et facturation](live-event-states-billing.md)
- [Latence](live-event-latency.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

* [Didacticiel sur le streaming en direct](stream-live-tutorial-with-api.md)
* [Conseils de migration pour le passage de Media Services v2 à Media Services v3](migrate-from-v2-to-v3.md)
