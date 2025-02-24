---
title: Utiliser les points de terminaison Machine Learning dans Azure Stream Analytics
description: Cet article décrit comment utiliser des fonctions Machine Learning définies par l’utilisateur dans Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 650f8952e58046082768007295208f52113b5f81
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620902"
---
# <a name="azure-machine-learning-studio-integration-in-stream-analytics-preview"></a>Intégration d’Azure Machine Learning Studio dans Stream Analytics (préversion)
Stream Analytics prend en charge les fonctions définies par l’utilisateur qui appellent des points de terminaison Azure Machine Learning Studio. Prise en charge de l’API REST de cette fonctionnalité est détaillée dans la [bibliothèque de l’API REST Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx). Cet article fournit les informations supplémentaires nécessaires à la mise en œuvre réussie de cette fonctionnalité dans Stream Analytics. Un didacticiel a également été validé et est disponible [ici](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-studio-terminology"></a>Vue d’ensemble : terminologie d’Azure Machine Learning Studio
Microsoft Azure Machine Learning Studio offre un outil collaboratif fonctionnant par glisser-déplacer qui vous permet de générer, tester et déployer des solutions d’analyse prédictive à partir de vos données. Cet outil est appelé *Azure Machine Learning Studio*. Studio permet d’interagir avec les ressources d’apprentissage et de facilement générer, tester et affiner votre conception. Ces ressources et leurs définitions se trouvent ci-dessous.

* **Espace de travail** : L’*espace de travail* représente un conteneur regroupant toutes les autres ressources Machine Learning au sein d’un conteneur pour la gestion et le contrôle.
* **Expérience** : Les *expériences* sont créées par des scientifiques des données qui utilisent des jeux de données et effectuent l’apprentissage d’un modèle Machine Learning.
* **Point de terminaison** : Les *points de terminaison* représentent un objet Azure Machine Learning Studio servant à saisir des fonctionnalités en entrée, appliquer un modèle Machine Learning spécifié et renvoyer le résultat évalué.
* **Service Web d’évaluation** : Un *service web d’évaluation* représente une collection de points de terminaison, comme indiqué ci-dessus.

Chaque point de terminaison dispose d’API servant à l’exécution de lots et l’exécution synchronisée. Stream Analytics utilise l’exécution synchronisée. Le service spécifique est nommé [Service requête/réponse](../machine-learning/studio/consume-web-services.md) dans Azure Machine Learning Studio.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Ressources Machine Learning nécessaires aux tâches d’analyse Stream Analytics
Pour les besoins de l’analyse des travaux Stream Analytics, un point de terminaison demande/réponse, une clé [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md)et une définition swagger sont nécessaires pour une exécution réussie. Stream Analytics est doté d’un point de terminaison supplémentaire qui génère l’URL d’un point de terminaison swagger, fait des recherches dans l’interface et retourne une définition de fonction UDF par défaut à l’utilisateur.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Configuration d’une analyse Stream Analytics et d’une fonction définie par l’utilisateur Machine Learning via une API REST
À l’aide des API REST, vous pouvez configurer votre projet pour appeler des fonctions Azure Machine Language. La procédure comporte trois étapes :

1. Création d’un travail Stream Analytics
2. Définition d’une entrée
3. Définition d’une sortie
4. Création d’une fonction définie par l’utilisateur
5. Création d’une transformation Stream Analytics faisant appel à la fonction définie par l’utilisateur
6. Démarrage du travail

## <a name="creating-a-udf-with-basic-properties"></a>Création d’une fonction définie par l’utilisateur avec des propriétés de base
Par exemple, l’exemple de code suivant crée un fichier UDF scalaire nommé *newudf* associé à un point de terminaison Azure Machine Learning Studio. Notez que le *point de terminaison* (URI de service) se trouve sur la page d’aide API correspondant au service choisi et la clé *apiKey* se trouve sur la page principale des services.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Exemple de corps de requête :

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Appeler le point de terminaison RetrieveDefaultDefinition pour la fonction définie par l’utilisateur par défaut
Une fois la structure de la fonction définie par l’utilisateur créée, une définition complète de la fonction définie par l’utilisateur est nécessaire. Le point de terminaison RetrieveDefaultDefinition vous permet d’obtenir une définition par défaut d’une fonction scalaire liée à un point de terminaison Azure Machine Learning Studio. La charge de travail ci-dessous exige que vous obteniez la définition de fonction définie par l’utilisateur par défaut pour une fonction scalaire lié à un point de terminaison Azure Machine Learning. Il ne spécifie pas le point de terminaison réel tel qu’il a déjà été fourni pendant la requête PUT. Stream Analytics appelle le point de terminaison fourni dans la demande s’il est indiqué de façon explicite. Dans le cas contraire, il utilise celui qui a été référencé à l’origine. Ici, la fonction définie par l’utilisateur prend un paramètre à chaîne unique (une phrase) et retourne un seul résultat de type chaîne qui mentionne le libellé « sentiment » de cette phrase.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Exemple de corps de requête :

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Un exemple de sortie ressemble à ce qui suit.

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>Fonction définie par l’utilisateur correctif avec la réponse
Maintenant la fonction définie par l’utilisateur peut être corrigée à l’aide de la réponse précédente, comme indiqué ci-dessous.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Corps de la demande (sortie de RetrieveDefaultDefinition) :

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implémentation de la transformation Stream Analytics pour appeler la fonction définie par l’utilisateur
Maintenant, interrogez la fonction définie par l’utilisateur (nommée ici scoreTweet) pour chaque événement en entrée et rédiger une réponse pour cette sortie.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
