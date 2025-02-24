---
title: Transformer des données à l’aide d’une activité Hadoop Pig dans Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser l'activité pig d’une fabrique de données Azure pour exécuter des requêtes pig sur un cluster HDInsight à la demande/ou votre propre cluster.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 914bc37552a80886df16ed69fba4e31b3f22ac22
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61399520"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Transformer des données à l’aide d’une activité Hadoop Pig dans Azure Data Factory
> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-pig-activity.md)
> * [Version actuelle](transform-data-using-hadoop-pig.md)

L’activité HDInsight Pig d’un [pipeline](concepts-pipelines-activities.md) Data Factory exécute des requêtes Pig sur [votre propre](compute-linked-services.md#azure-hdinsight-linked-service) cluster ou cluster [à la demande](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight. Cet article s'appuie sur l'article [Activités de transformation des données](transform-data.md) qui présente une vue d'ensemble de la transformation des données et les activités de transformation prises en charge.

Si vous découvrez Azure Data Factory, lisez la [présentation d’Azure Data Factory](introduction.md) et suivez le [Didacticiel : Transformer des données](tutorial-transform-data-spark-powershell.md) avant de lire cet article. 

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }   
}
```
## <a name="syntax-details"></a>Détails de la syntaxe

| Propriété            | Description                              | Obligatoire |
| ------------------- | ---------------------------------------- | -------- |
| Nom                | Nom de l’activité                     | OUI      |
| description         | Texte décrivant la raison motivant l’activité. | Non       |
| Type                | Pour l’activité Hive, le type d’activité est HDinsightPig. | OUI      |
| linkedServiceName   | Référence au cluster HDInsight enregistré en tant que service lié dans Data Factory. Pour en savoir plus sur ce service lié, consultez l’article [Services liés de calcul](compute-linked-services.md). | OUI      |
| scriptLinkedService | Référence à un service lié de stockage Azure utilisé pour stocker le script Pig à exécuter. Si vous ne spécifiez pas ce service lié, le service lié Stockage Azure défini dans le service lié HDInsight est utilisé. | Non       |
| scriptPath          | Indiquez le chemin du fichier de script stocké dans le stockage Azure référencé par scriptLinkedService. Le nom de fichier respecte la casse. | Non       |
| getDebugInfo        | Spécifie quand les fichiers journaux sont copiés vers le stockage Azure utilisé par le cluster HDInsight (ou) spécifié par scriptLinkedService. Valeurs autorisées : None, Always ou Failure. Valeur par défaut : Aucune. | Non       |
| arguments           | Spécifie un tableau d’arguments pour un travail Hadoop. Les arguments sont passés sous la forme d’arguments de ligne de commande à chaque tâche. | Non       |
| defines             | Spécifier les paramètres sous forme de paires clé/valeur pour le référencement au sein du script Pig. | Non       |

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants qui expliquent comment transformer des données par d’autres moyens : 

* [Activité U-SQL](transform-data-using-data-lake-analytics.md)
* [Activité Hive](transform-data-using-hadoop-hive.md)
* [Activité MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Activité de diffusion en continu Hadoop](transform-data-using-hadoop-streaming.md)
* [Activité Spark](transform-data-using-spark.md)
* [Activité personnalisée .NET](transform-data-using-dotnet-custom-activity.md)
* [Activité d’exécution du lot Machine Learning](transform-data-using-machine-learning.md)
* [Activité de procédure stockée](transform-data-using-stored-procedure.md)
