---
title: Informations de référence sur l’API Synthèse vocale (REST) – Services Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser les API REST Synthèse vocale. Cet article vous présente les options d’autorisation, les options de requête, et vous explique comment structurer une demande et recevoir une réponse.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 684ddcd49d3cd3c3d4f0a8d6f4675e1e27bdb7b9
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604885"
---
# <a name="text-to-speech-rest-api"></a>API REST de synthèse vocale

Les services Speech vous permettent de [convertir du texte en voix de synthèse](#convert-text-to-speech) et d’[obtenir une liste de voix prises en charge](#get-a-list-of-voices) pour une région à partir d’un ensemble d’API REST. Chaque point de terminaison disponible est associé à une région. Une clé d’abonnement pour le point de terminaison ou la région que vous prévoyez d’utiliser est nécessaire.

L’API REST de synthèse vocale prend en charge les voix de synthèse vocale neuronales et standard qui, à leur tour, prennent chacune en charge une langue et un dialecte spécifiques, identifiés par les paramètres régionaux.

* Pour obtenir la liste complète des voix, consultez [prise en charge linguistique](language-support.md#text-to-speech).
* Pour obtenir des informations sur la disponibilité régionale, consultez [régions](regions.md#text-to-speech).

> [!IMPORTANT]
> Les prix varient pour les voix standard, personnalisées et neurales. Pour plus d’informations, voir la [tarification](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Avant d’utiliser cette API, tenez compte du point suivant :

* L’API REST de synthèse vocale nécessite un en-tête d’autorisation. Cela signifie que vous devez échanger un jeton pour accéder au service. Pour en savoir plus, consultez [Authentification](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Obtenir une liste de voix

Le point de terminaison `voices/list` vous permet d’obtenir une liste complète de voix pour une région/point de terminaison spécifique.

### <a name="regions-and-endpoints"></a>Régions et points de terminaison

| Région | Point de terminaison |
|--------|----------|
| Australie Est | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Brésil Sud | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Centre du Canada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA Centre | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Asie Est | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA Est | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA Est 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| France Centre | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Inde Centre | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Japon Est | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Centre de la Corée | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA Centre Nord | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europe Nord | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA Centre Sud | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Asie Sud-Est | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Sud du Royaume-Uni | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europe de l'Ouest | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA Ouest | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA Ouest 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>En-têtes de requête

Ce tableau liste les en-têtes obligatoires et facultatifs pour les demandes de synthèse vocale.

| En-tête | Description | Obligatoire/facultatif |
|--------|-------------|---------------------|
| `Authorization` | Un jeton d’autorisation précédé du mot `Bearer`. Pour en savoir plus, consultez [Authentification](#authentication). | Obligatoire |

### <a name="request-body"></a>Corps de la demande

Les demandes `GET` à ce point de terminaison ne nécessitent pas de corps.

### <a name="sample-request"></a>Exemple de requête

Chaque demande nécessite uniquement un en-tête d’autorisation.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Exemple de réponse

Cette réponse a été tronquée pour illustrer la structure d’une réponse.

> [!NOTE]
> La disponibilité des voix varie selon la région et/ou le point de terminaison.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)",
        "ShortName": "cs-CZ-Jakub",
        "Gender": "Male",
        "Locale": "cs-CZ"
    },

    ...

]
```

### <a name="http-status-codes"></a>Codes d’état HTTP

Le code d’état HTTP de chaque réponse indique la réussite ou des erreurs courantes.

| Code d'état HTTP | Description | Raison possible |
|------------------|-------------|-----------------|
| 200 | OK | La demande a abouti. |
| 400 | Demande incorrecte | Un paramètre obligatoire est manquant, vide ou présente une valeur Null. Il est également possible que la valeur transmise à un paramètre obligatoire ou facultatif ne soit pas valide. Ce problème est généralement dû à un en-tête trop long. |
| 401 | Non autorisé | La demande n’est pas autorisée. Vérifiez que votre clé d’abonnement ou votre jeton est valide et dans la région appropriée. |
| 429 | Trop de demandes | Vous avez dépassé le quota ou le taux de requêtes autorisé pour votre abonnement. |
| 502 | Passerelle incorrecte | Problème de réseau ou côté serveur. Cette erreur peut également signaler des en-têtes non valides. |


## <a name="convert-text-to-speech"></a>Convertir la synthèse vocale

Le point de terminaison `v1` vous permet de convertir du texte en voix à l’aide de [SSML (Speech Synthesis Markup Langage)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Régions et points de terminaison

Ces régions sont prises en charge pour la synthèse vocale à l’aide de l’API REST. Veillez à sélectionner le point de terminaison correspondant à votre région d’abonnement.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>En-têtes de requête

Ce tableau liste les en-têtes obligatoires et facultatifs pour les demandes de synthèse vocale.

| En-tête | Description | Obligatoire/facultatif |
|--------|-------------|---------------------|
| `Authorization` | Un jeton d’autorisation précédé du mot `Bearer`. Pour en savoir plus, consultez [Authentification](#authentication). | Obligatoire |
| `Content-Type` | Spécifie le type de contenu pour le texte fourni. Valeur acceptée : `application/ssml+xml`. | Obligatoire |
| `X-Microsoft-OutputFormat` | Spécifie le format de la sortie audio. Pour obtenir une liste complète des valeurs acceptées, consultez [Sorties audio](#audio-outputs). | Obligatoire |
| `User-Agent` | Nom de l'application. La valeur fournie doit être inférieure à 255 caractères. | Obligatoire |

### <a name="audio-outputs"></a>Sorties audio

Liste de formats audio pris en charge envoyés dans chaque demande en tant qu’en-tête `X-Microsoft-OutputFormat`. Chaque format indique la vitesse de transmission et le type d’encodage. Les services Speech prennent en charge les sorties audio de 24 kHz, 16 kHz et 8 kHz.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Si la voix que vous avez sélectionnée ne présente pas la même vitesse de transmission que le format de sortie spécifié, les données audio sont rééchantillonnées si nécessaire. Cependant, les voix de 24 kHz ne prennent pas en charge les formats de sortie `audio-16khz-16kbps-mono-siren` et `riff-16khz-16kbps-mono-siren`.

### <a name="request-body"></a>Corps de la demande

Le corps de chaque requête `POST` est envoyée en tant que [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). SSML vous permet de choisir la voix et la langue de la synthèse vocale renvoyée par le service de synthèse vocale. Pour obtenir la liste complète des voix prises en charge, consultez [prise en charge linguistique](language-support.md#text-to-speech).

> [!NOTE]
> Si vous utilisez une voix personnalisée, le corps d’une requête peut être envoyé sous forme de texte brut (ASCII ou UTF-8).

### <a name="sample-request"></a>Exemple de requête

Cette requête HTTP utilise SSML pour spécifier la voix et la langue. Le corps ne doit pas dépasser 1 000 caractères.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-JessaRUS'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Consultez nos guides de démarrage rapide pour obtenir des exemples propres aux langages :

* [.NET Core, C#](quickstart-dotnet-text-to-speech.md)
* [Python](quickstart-python-text-to-speech.md)
* [Node.JS](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>Codes d’état HTTP

Le code d’état HTTP de chaque réponse indique la réussite ou des erreurs courantes.

| Code d'état HTTP | Description | Raison possible |
|------------------|-------------|-----------------|
| 200 | OK | La requête a réussi ; le corps de réponse est un fichier audio. |
| 400 | Demande incorrecte | Un paramètre obligatoire est manquant, vide ou présente une valeur Null. Il est également possible que la valeur transmise à un paramètre obligatoire ou facultatif ne soit pas valide. Ce problème est généralement dû à un en-tête trop long. |
| 401 | Non autorisé | La demande n’est pas autorisée. Vérifiez que votre clé d’abonnement ou votre jeton est valide et dans la région appropriée. |
| 413 | Entité de requête trop volumineuse | L’entrée SSML comporte plus de 1 024 caractères. |
| 415 | Type de média non pris en charge | Il est possible que le `Content-Type` indiqué était incorrect. `Content-Type` doit être définie sur `application/ssml+xml`. |
| 429 | Trop de demandes | Vous avez dépassé le quota ou le taux de requêtes autorisé pour votre abonnement. |
| 502 | Passerelle incorrecte | Problème de réseau ou côté serveur. Cette erreur peut également signaler des en-têtes non valides. |

Si l’état HTTP est `200 OK`, le corps de la réponse contient un fichier audio au format demandé. Ce fichier peut être lu pendant son transfert ou pendant son enregistrement dans une mémoire tampon ou dans un fichier.

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
