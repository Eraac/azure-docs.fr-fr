---
title: 'Démarrage rapide : Analyser une image distante - REST, PHP'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous analysez une image en utilisant l’API Vision par ordinateur avec PHP.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f9375d3ce7c5127ed80f3f7fad93f4db8aa58b76
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604312"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-and-php"></a>Démarrage rapide : Analyser une image distante à l’aide de l’API REST Vision par ordinateur et de PHP

Dans ce démarrage rapide, vous analysez une image stockée à distance pour extraire des caractéristiques visuelles à l’aide de l’API REST de Vision par ordinateur. Avec la [méthode d’analyse d’une image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), vous pouvez extraire des fonctionnalités visuelles en fonction du contenu de l’image.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- [PHP](https://secure.php.net/downloads.php) doit être installé.
- [Pear](https://pear.php.net) doit être installé.
- Vous devez disposer d’une clé d’abonnement pour la Vision par ordinateur. Vous pouvez obtenir une clé d’essai gratuit auprès de [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Vous pouvez également suivre les instructions mentionnées dans [Créer un compte Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pour vous abonner à Vision par ordinateur et obtenir votre clé.

## <a name="create-and-run-the-sample"></a>Création et exécution de l’exemple

Pour créer et exécuter l’exemple, effectuez les étapes suivantes :

1. Installez le package [`HTTP_Request2`](https://pear.php.net/package/HTTP_Request2) PHP5.
   1. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
   1. Exécutez la commande suivante :

      ```console
      pear install HTTP_Request2
      ```

   1. Une fois que vous avez installé le package, fermez la fenêtre d’invite de commandes.

1. Copiez le code ci-après dans un éditeur de texte.
1. Modifiez le code comme ci-dessous :
    1. Remplacez la valeur de `subscriptionKey` par votre clé d’abonnement.
    1. Si nécessaire, remplacez la valeur de `uriBase` par l’URL du point de terminaison de la méthode [Analyser l’image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) dans la région Azure où vous avez obtenu vos clés d’abonnement.
    1. Remplacez éventuellement la valeur de `imageUrl` par l’URL d’une autre image que vous voulez analyser.
    1. Si vous le souhaitez, remplacez la valeur du paramètre de requête `language` par une autre langue.
1. Enregistrez le code dans un fichier avec une extension `.php`. Par exemple : `analyze-image.php`.
1. Ouvrez une fenêtre de navigateur avec prise en charge de PHP.
1. Faites glisser et déposez le fichier dans la fenêtre du navigateur.

```php
<html>
<head>
    <title>Analyze Image Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl = 'https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'visualFeatures' => 'Categories,Description',
    'details' => '',
    'language' => 'en'
);
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
</body>
</html>
```

## <a name="examine-the-response"></a>Examiner la réponse

Une réponse correcte est retournée au format JSON. L’exemple de site web analyse et affiche une réponse réussie dans la fenêtre du navigateur, comme dans l’exemple suivant :

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "ebf5a1bc-3ba2-4c56-99b4-bbd20ba28705",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, supprimez le fichier, puis désinstallez le package `HTTP_Request2` PHP5. Pour désinstaller le package, effectuez les étapes suivantes :

1. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
2. Exécutez la commande suivante :

   ```console
   pear uninstall HTTP_Request2
   ```

3. Une fois que vous avez désinstallé le package, fermez la fenêtre d’invite de commandes.

## <a name="next-steps"></a>Étapes suivantes

Explorez l’API Vision par ordinateur utilisée pour analyser une image, détecter des célébrités et des monuments, créer une miniature et extraire le texte imprimé ou manuscrit. Pour tester rapidement l’API Vision par ordinateur, essayez la [console de test Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorer l’API Vision par ordinateur](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
