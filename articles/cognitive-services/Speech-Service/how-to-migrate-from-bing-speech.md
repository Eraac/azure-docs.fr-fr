---
title: Migrer de la Reconnaissance vocale Bing vers les services Speech Azure
titleSuffix: Azure Cognitive Services
description: Découvrez comment migrer d’un abonnement Reconnaissance vocale Bing existant vers les services Speech Azure.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: 33907437ab330278bdf7b023f6a93bd96e78cbad
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561338"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrer de la Reconnaissance vocale Bing vers le Service Speech

Référez-vous à cet article pour migrer vos applications de l’API Reconnaissance vocale Bing vers le Service Speech.

Cet article décrit les différences entre les API Reconnaissance vocale Bing et les services Speech, et suggère des stratégies pour migrer vos applications. Votre clé d’abonnement API Reconnaissance vocale Bing ne fonctionnera pas avec le service Speech, vous aurez besoin d’un nouvel abonnement aux services Speech.

Une clé unique d’abonnement aux services Speech permet d’accéder aux fonctions suivantes. Elles sont mesurées séparément pour que seules les fonctions que vous utilisez vous soient facturées.

* [Reconnaissance vocale](speech-to-text.md)
* [Reconnaissance vocale personnalisée](https://cris.ai)
* [Synthèse vocale](text-to-speech.md)
* [Voix de synthèse vocale personnalisées](how-to-customize-voice-font.md)
* [Traduction vocale](speech-translation.md) (n’inclut pas la [traduction de texte](../translator/translator-info-overview.md))

Le [Kit de développement logiciel (SDK) Speech](speech-sdk.md) constitue un remplacement fonctionnel des bibliothèques clientes de Reconnaissance vocale Bing, mais utilise une API différente.

## <a name="comparison-of-features"></a>Comparaison des fonctionnalités

Les services Speech sont très similaires à la Reconnaissance vocale Bing, à l’exception des différences suivantes.

Fonctionnalité | Reconnaissance vocale Bing | Services Speech | Détails
-|-|-|-
KIT DE DÉVELOPPEMENT LOGICIEL C++ | :heavy_minus_sign: | :heavy_check_mark: | Les services Speech prennent en charge Windows et Linux.
Kit de développement logiciel (SDK) Java | :heavy_check_mark: | :heavy_check_mark: | Les services Speech prennent en charge Android et Speech Devices.
Kit de développement logiciel (SDK) C# | :heavy_check_mark: | :heavy_check_mark: | Les services Speech prennent en charge Windows 10, la plateforme Windows universelle (UWP) et .NET Standard 2.0.
Reconnaissance vocale continue | 10 minutes | Illimitée (avec le Kit de développement logiciel) | Les protocoles WebSocket de la Reconnaissance vocale Bing et des services Speech prennent en charge jusqu’à 10 minutes par appel. Toutefois, le Kit de développement logiciel (SDK) Speech reconnecte ou déconnecte à l’issue du délai d’expiration.
Résultats partiels ou intermédiaires | :heavy_check_mark: | :heavy_check_mark: | Avec le protocole WebSockets ou le Kit de développement logiciel (SDK).
Modèles vocaux personnalisés | :heavy_check_mark: | :heavy_check_mark: | La Reconnaissance vocale Bing nécessite un abonnement Custom Speech séparé.
Polices de la voix personnalisées | :heavy_check_mark: | :heavy_check_mark: | La Reconnaissance vocale Bing nécessite un abonnement Custom Voice séparé.
Voix 24-KHz | :heavy_minus_sign: | :heavy_check_mark:
Reconnaissance de l’intention vocale | Nécessite un appel d’API LUIS séparé | Intégrée (avec Kit de développement logiciel (SDK)) |  Une clé LUIS peut être utilisée avec le service Speech Service.
Reconnaissance de l’intention simple | :heavy_minus_sign: | :heavy_check_mark:
Transcription par lots de fichiers audio longs | :heavy_minus_sign: | :heavy_check_mark:
Mode de reconnaissance | Manuel via URI de point de terminaison | Automatique | Le mode de reconnaissance n’est pas disponible dans Speech Service.
Lieu du point de terminaison | Globale | Zones géographiques | Les points de terminaison régionaux améliorent la latence.
API REST | :heavy_check_mark: | :heavy_check_mark: | L’API REST des services Speech est compatible avec la Reconnaissance vocale Bing (point de terminaison différent). Les API REST prennent en charge les fonctionnalités de synthèse vocale et de reconnaissance vocale limitée.
Protocoles WebSocket | :heavy_check_mark: | :heavy_check_mark: | L’API WebSocket des services Speech est compatible avec la Reconnaissance vocale Bing (point de terminaison différent). Migrez vers le Kit de développement logiciel (SDK) Speech si possible afin de simplifier votre code.
Appels d’API de service à service | :heavy_check_mark: | :heavy_minus_sign: | Fournis dans la Reconnaissance vocale Bing via la bibliothèque de services C#.
Kit de développement logiciel (SDK) open source | :heavy_check_mark: | :heavy_minus_sign: |

Les services Speech utilisent un modèle de tarification basé sur le temps (plutôt que sur les transactions). Pour plus de détails, consultez la [tarification des services Speech](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="migration-strategies"></a>Stratégies de migration

Si vous ou votre organisation disposez d’applications en développement ou en production qui utilisent l’API Reconnaissance vocale Bing, vous devez les mettre à jour pour utiliser les services Speech au plus tôt. Pour accéder aux kits de développement logiciel (SDK), aux exemples de code et aux didacticiels disponibles, vois la [documentation des services Speech](index.yml).

Les [API REST](rest-apis.md) des services Speech sont compatibles avec les API Reconnaissance vocale Bing. Si vous utilisez actuellement les API REST Reconnaissance vocale Bing, vous devez uniquement modifier le point de terminaison REST et basculer vers une clé d’abonnement Services Speech.

Les protocoles WebSocket des services Speech sont également compatibles avec ceux utilisés par la Reconnaissance vocale Bing. Pour tout nouveau développement, nous vous recommandons d’utiliser le kit de développement logiciel (SDK) Speech plutôt que WebSockets. Il est judicieux d’également migrer le code existant vers le kit de développement logiciel (SDK). Cependant, comme avec les API REST, le code existant utilisant la Reconnaissance vocale Bing via des WebSockets nécessite uniquement une modification du point de terminaison et une clé mise à jour.

Si vous utilisez une bibliothèque de client Reconnaissance vocale Bing pour un langage de programmation spécifique, la migration vers le [Kit de développement logiciel (SDK) Speech](speech-sdk.md) nécessite l’apport de modifications à votre application, car l’API est différente. Le kit de développement logiciel (SDK) Speech peut simplifier votre code tout en vous donnant accès à de nouvelles fonctionnalités.

Actuellement, le kit de développement logiciel (SDK) Speech prend en charge C# ([plus de détails ici](https://aka.ms/csspeech)), Java (Android et appareils personnalisés), Objective C (iOS), C++ (Windows et Linux) et JavaScript. Les API sont similaires sur toutes les plateformes, et facilitent le développement multi-plateforme.

Les services Speech n’offrent pas de point de terminaison global. Vous devez déterminer si votre application fonctionne efficacement lorsqu’elle utilise un point de terminaison régional unique pour tout son trafic. Autrement, utilisez la géolocalisation pour déterminer le point de terminaison plus efficace. Vous devez disposer d’un abonnement Service Services distinct dans chaque région que vous utilisez.

Si votre application utilise des connexions de longue durée et ne peut pas utiliser un kit SDK disponible, vous pouvez utiliser une connexion WebSockets. Gérez la limite de délai d’expiration de 10 minutes en vous reconnectant aux moments appropriés.

Pour prendre en main du kit de développement logiciel (SDK) Speech :

1. Téléchargez le [Kit de développement logiciel (SDK) Speech](speech-sdk.md).
1. Parcourez les [guides de démarrage rapide](quickstart-csharp-dotnet-windows.md) et les [didacticiels](how-to-recognize-intents-from-speech-csharp.md) des services Speech. Consultez également les [exemples de code](samples.md) pour vous familiariser avec les nouvelles API.
1. Mettez à jour votre application pour utiliser les services Speech.

## <a name="support"></a>Support

Les clients de Reconnaissance vocale Bing doivent contacter le service clientèle en ouvrant un [ticket de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Vous pouvez également nous contacter si vous avez besoin d’un [plan de support technique](https://azure.microsoft.com/support/plans/).

Pour la prise en charge du Service Speech, du Kit de développement logiciel (SDK) et de l’API, visitez la [page de support](support.md) des services Speech.

## <a name="next-steps"></a>Étapes suivantes

* [Essayer gratuitement les services Speech](get-started.md)
* [Démarrage rapide : Reconnaissance vocale dans une application UWP à l’aide du kit SDK Speech](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Voir aussi
* [Notes de publication des services Speech](releasenotes.md)
* [Qu’est-ce que le service Speech Service](overview.md)
* [Documentation du kit de développement logiciel (SDK) et des services Speech](speech-sdk.md#get-the-sdk)
