---
title: 'Démarrage rapide : Détecter des visages sur une image avec l’API REST et PHP'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez détecter les visages d’une image à l’aide de l’API Visage avec PHP.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 2ed1a20bf47771d927c0d58730fa0eb4471572af
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603366"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-php"></a>Démarrage rapide : Détecter des visages sur une image avec l’API REST et PHP

Dans ce guide de démarrage rapide, vous allez utiliser l’API REST Visage Azure avec PHP pour détecter des visages humains dans une image.

## <a name="prerequisites"></a>Prérequis

- Clé d’abonnement à l’API Visage. Vous pouvez obtenir une clé d’abonnement d’essai gratuit à partir de la page [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Vous pouvez également suivre les instructions dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner au service API Visage et obtenir votre clé.
- Un éditeur de code tel que [Visual Studio Code](https://code.visualstudio.com/download).
- Package PHP [HTTP_Request2](https://pear.php.net/package/HTTP_Request2).
- Un navigateur web compatible PHP. Si vous n'avez pas configuré cela, vous pouvez le faire en installant et en configurant [XAMPP](https://www.apachefriends.org/) sur votre ordinateur.

## <a name="initialize-the-html-file"></a>Initialiser le fichier HTML

Créez un fichier HTML, *detectFaces.html*, et ajoutez le code suivant.

```html
<html>
    <head>
        <title>Face Detect Sample</title>
    </head>
    <body></body>
</html>
```

## <a name="write-the-php-script"></a>Écrire le script PHP

Ajoutez le code suivant à l’intérieur de l’élément `body` du document. Ce code définit une interface utilisateur de base avec un champ URL, un bouton **Analyze face**, un volet de réponse et un volet d’affichage de l’image.

```php
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/face/v1.0/';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg';

// This sample uses the PHP5 HTTP_Request2 package
// (https://pear.php.net/package/HTTP_Request2).
require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . '/detect');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'returnFaceId' => 'true',
    'returnFaceLandmarks' => 'false',
    'returnFaceAttributes' => 'age,gender,headPose,smile,facialHair,glasses,' .
        'emotion,hair,makeup,occlusion,accessories,blur,exposure,noise');
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
```

Vous devez mettre à jour le champ `subscriptionKey` avec la valeur de votre clé d’abonnement, et éventuellement changer la chaîne `uriBase` pour qu’elle contienne l’identificateur de région approprié. (Pour connaître la liste des points de terminaison de toutes les régions, consultez les [documents API Visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)). Le champ `returnFaceAttributes` spécifie les attributs du visage à récupérer. Vous pouvez changer cette chaîne selon l’utilisation prévue.

## <a name="run-the-script"></a>Exécutez le script

Ouvrez le fichier dans un navigateur web prenant en charge PHP. Vous devez obtenir une chaîne JSON de données de visage semblable à la suivante.

```json
[
    {
        "faceId": "e93e0db1-036e-4819-b5b6-4f39e0f73509",
        "faceRectangle": {
            "top": 621,
            "left": 616,
            "width": 195,
            "height": 195
        },
        "faceAttributes": {
            "smile": 0,
            "headPose": {
                "pitch": 0,
                "roll": 6.8,
                "yaw": 3.7
            },
            "gender": "male",
            "age": 37,
            "facialHair": {
                "moustache": 0.4,
                "beard": 0.4,
                "sideburns": 0.1
            },
            "glasses": "NoGlasses",
            "emotion": {
                "anger": 0,
                "contempt": 0,
                "disgust": 0,
                "fear": 0,
                "happiness": 0,
                "neutral": 0.999,
                "sadness": 0.001,
                "surprise": 0
            },
            "blur": {
                "blurLevel": "high",
                "value": 0.89
            },
            "exposure": {
                "exposureLevel": "goodExposure",
                "value": 0.51
            },
            "noise": {
                "noiseLevel": "medium",
                "value": 0.59
            },
            "makeup": {
                "eyeMakeup": true,
                "lipMakeup": false
            },
            "accessories": [],
            "occlusion": {
                "foreheadOccluded": false,
                "eyeOccluded": false,
                "mouthOccluded": false
            },
            "hair": {
                "bald": 0.04,
                "invisible": false,
                "hairColor": [
                    {
                        "color": "black",
                        "confidence": 0.98
                    },
                    {
                        "color": "brown",
                        "confidence": 0.87
                    },
                    {
                        "color": "gray",
                        "confidence": 0.85
                    },
                    {
                        "color": "other",
                        "confidence": 0.25
                    },
                    {
                        "color": "blond",
                        "confidence": 0.07
                    },
                    {
                        "color": "red",
                        "confidence": 0.02
                    }
                ]
            }
        }
    },
    {
        "faceId": "37c7c4bc-fda3-4d8d-94e8-b85b8deaf878",
        "faceRectangle": {
            "top": 693,
            "left": 1503,
            "width": 180,
            "height": 180
        },
        "faceAttributes": {
            "smile": 0.003,
            "headPose": {
                "pitch": 0,
                "roll": 2,
                "yaw": -2.2
            },
            "gender": "female",
            "age": 56,
            "facialHair": {
                "moustache": 0,
                "beard": 0,
                "sideburns": 0
            },
            "glasses": "NoGlasses",
            "emotion": {
                "anger": 0,
                "contempt": 0.001,
                "disgust": 0,
                "fear": 0,
                "happiness": 0.003,
                "neutral": 0.984,
                "sadness": 0.011,
                "surprise": 0
            },
            "blur": {
                "blurLevel": "high",
                "value": 0.83
            },
            "exposure": {
                "exposureLevel": "goodExposure",
                "value": 0.41
            },
            "noise": {
                "noiseLevel": "high",
                "value": 0.76
            },
            "makeup": {
                "eyeMakeup": false,
                "lipMakeup": false
            },
            "accessories": [],
            "occlusion": {
                "foreheadOccluded": false,
                "eyeOccluded": false,
                "mouthOccluded": false
            },
            "hair": {
                "bald": 0.06,
                "invisible": false,
                "hairColor": [
                    {
                        "color": "black",
                        "confidence": 0.99
                    },
                    {
                        "color": "gray",
                        "confidence": 0.89
                    },
                    {
                        "color": "other",
                        "confidence": 0.64
                    },
                    {
                        "color": "brown",
                        "confidence": 0.34
                    },
                    {
                        "color": "blond",
                        "confidence": 0.07
                    },
                    {
                        "color": "red",
                        "confidence": 0.03
                    }
                ]
            }
        }
    }
]
```

## <a name="next-steps"></a>Étapes suivantes

Explorez les API Visage utilisées pour détecter les visages humains dans une image, délimiter les visages avec des rectangles et retourner des attributs tels que l’âge et le sexe.

> [!div class="nextstepaction"]
> [API Visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
