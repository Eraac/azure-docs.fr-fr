---
title: 'Démarrage rapide : Détecter des anomalies de données à l’aide de la bibliothèque de détecteurs d’anomalies et de Python'
titleSuffix: Azure Cognitive Services
description: Utilisez l’API Détecteur d’anomalies pour détecter les anomalies dans vos séries de données, soit en lot, soit en continu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: 9176ab84dd3f493604bd655e0498f5ad476776d0
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721517"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Démarrage rapide : Bibliothèque de client Détecteur d’anomalies pour Python

Commencez à utiliser la bibliothèque de client Détecteur d’anomalies pour .NET. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base. Le service Détecteur d’anomalies vous permet de rechercher des anomalies dans vos données de séries chronologiques en utilisant automatiquement les modèles les mieux adaptés sur celles-ci, quel que soit le secteur d’activité, le scénario ou le volume de données.

## <a name="key-concepts"></a>Concepts clés

Utilisez la bibliothèque de client Détecteur d’anomalies pour Python pour :

* Détecter les anomalies dans votre jeu de données de série chronologique, comme une requête de lots
* Détecter l’état d’anomalie du dernier point de données dans vos séries chronologiques

[Documentation de référence sur la bibliothèque](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [Package (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [Exemples](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* [Bibliothèque d’analyse de données Pandas](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Configuration

### <a name="create-an-anomaly-detector-resource"></a>Créer une ressource Détecteur d’anomalies

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Après avoir installé Python, vous pouvez installer la bibliothèque de client avec :

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Modèle objet

Le client Détecteur d’anomalies est un objet [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) qui s’authentifie auprès d’Azure à l’aide de votre clé. Le client propose deux méthodes de détection des anomalies : Sur un jeu de données entier à l’aide de [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) et sur les derniers points de données à l’aide de [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Les données de séries chronologiques sont envoyées en tant que série de [points](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) dans un objet [Requête](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python). L’objet `Request` contient des propriétés pour décrire les données ([Granularité](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) par exemple) et des paramètres pour la détection d’anomalies. 

La réponse de Détecteur d’anomalies est un objet [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) ou [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) selon la méthode utilisée. 

## <a name="getting-started"></a>Prise en main

Créez une application Python dans votre éditeur ou IDE favori. Ajoutez ensuite les déclarations d'importation suivantes à votre fichier. 

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

> [!NOTE]
> Ce démarrage rapide part du principe que vous avez [créé une variable d’environnement](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pour votre clé Détecteur d’anomalies, nommée `ANOMALY_DETECTOR_KEY`.

Créez des variables pour votre clé en tant que variable d’environnement, le chemin d’accès à un fichier de données de série chronologique et l’emplacement Azure de votre abonnement. Par exemple : `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

## <a name="code-examples"></a>Exemples de code 

Ces extraits de code vous montrent comment effectuer les opérations suivantes avec la bibliothèque de client Détecteur d’anomalies pour .NET :

* [Authentifier le client](#authenticate-the-client)
* [Charger un jeu de données de séries chronologiques à partir d’un fichier](#load-time-series-data-from-a-file)
* [Détecter des anomalies dans un jeu de données entier](#detect-anomalies-in-the-entire-data-set) 
* [Détecter l’état d’anomalie du dernier point de données](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="authenticate-the-client"></a>Authentifier le client

Ajoutez votre variable d’emplacement Azure au point de terminaison et authentifiez le client avec votre clé.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

### <a name="load-time-series-data-from-a-file"></a>Charger des données de séries chronologiques à partir d’un fichier

Téléchargez l’exemple de données pour ce démarrage rapide à partir de [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv) :
1. Dans votre navigateur, cliquez avec le bouton droit sur **Raw**.
2. Cliquez sur **Enregistrer le lien sous**.
3. Enregistrez le fichier dans le répertoire de votre application en tant que fichier .csv.

Ces données de séries chronologiques sont mises en forme dans un fichier .csv et envoyées à l’API Détecteur d’anomalies.

Chargez votre fichier de données avec la méthode `read_csv()` de la bibliothèque Pandas et créez une variable de liste vide pour stocker vos séries de données. Effectuez une itération dans le fichier et ajoutez les données en tant qu'objet [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python). Cet objet contient le timestamp et une valeur numérique issus des lignes de votre fichier de données .csv. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Créez un objet [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) avec vos séries chronologiques, et la [granularité](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (ou périodicité) de ses points de données. Par exemple : `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>Détecter des anomalies dans un jeu de données entier 

Appelez l’API pour détecter les anomalies dans l'ensemble des données de séries chronologiques à l'aide de la méthode [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) du client. Stockez l'objet [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) renvoyé. Itérez dans la liste `is_anomaly` de la réponse et imprimez l'index des valeurs `true`. Ces valeurs correspondent à l’indice des points de données anormaux, le cas échéant.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Détecter l’état d’anomalie du dernier point de données

Appelez l’API Détecteur d’anomalies pour déterminer si votre dernier point de données relève d'une anomalie à l’aide de la méthode [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) du client, et stockez l'objet [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) renvoyé. La valeur `is_anomaly` de la réponse correspond à une valeur booléenne qui spécifie l’état d'anomalie de ce point.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application dans votre IDE ou dans la ligne de commande avec la commande `python` et le nom de votre fichier.
 
## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources liées au groupe de ressources.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interface de ligne de commande Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Vous pouvez également exécuter la commande Cloud Shell suivante pour supprimer le groupe de ressources et les ressources qui lui sont associées. L’exécution de cette opération nécessite quelques minutes. 

```azurecli-interactive
az group delete --name example-anomaly-detector-resource-group
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Streaming de la détection d’anomalies avec Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* [Présentation de l’API Détecteur d’anomalies](../overview.md)
* [Bonnes pratiques](../concepts/anomaly-detection-best-practices.md) concernant l’utilisation de l’API Détecteur d’anomalies.
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
