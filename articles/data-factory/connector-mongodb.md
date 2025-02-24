---
title: Copier des données de MongoDB à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données de MongoDB vers des banques de données réceptrices prises en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: ca6040bb74839f30a2f1b13297f6037f05240c67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61400438"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Déplacer des données de MongoDB à l’aide d’Azure Data Factory

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données d’une base de données MongoDB. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

>[!IMPORTANT]
>Cette nouvelle version du connecteur MongoDB dans ADF fournit une meilleure prise en charge native de MongoDB. Si vous utilisez le connecteur MongoDB précédent, qui est pris en charge tel quel à des fins de compatibilité descendante, dans votre solution, consultez l’article [Connecteur MongoDB (hérité)](connector-mongodb-legacy.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données d’une base de données MongoDB vers toute banque de données réceptrice prise en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Plus précisément, ce connecteur MongoDB prend en charge les **versions jusqu’à 3.4**.

## <a name="prerequisites"></a>Prérequis

Pour copier des données depuis/vers une base de données MongoDB qui n’est pas accessible publiquement, vous devez configurer un runtime d’intégration auto-hébergé. Pour plus d’informations, voir l’article [Runtime d’intégration auto-hébergé](create-self-hosted-integration-runtime.md).

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur MongoDB.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié MongoDB sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type |La propriété type doit être définie sur : **MongoDbV2** |OUI |
| connectionString |Spécifiez la chaîne de connexion MongoDB, par exemple `mongodb://[username:password@]host[:port][/[database][?options]]`. Pour plus d’informations, consultez le [manuel MongoDB sur la chaîne de connexion](https://docs.mongodb.com/manual/reference/connection-string/). <br/><br />Vous pouvez marquer ce champ en tant que type **SecureString** pour le stocker de manière sécurisée dans Data Factory. Vous pouvez également [référencer un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| database | Nom de la base de données à laquelle vous souhaitez accéder. | OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser un runtime d’intégration auto-hébergé ou un runtime d’intégration Azure (si votre banque de données est accessible publiquement). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

**Exemple :**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://[username:password@]host[:port][/[database][?options]]"
            },
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez [Jeux de données et services liés](concepts-datasets-linked-services.md). Les propriétés suivantes sont prises en charge pour le jeu de données MongoDB :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur : **MongoDbV2Collection** | OUI |
| collectionName |Nom de la collection dans la base de données MongoDB. |OUI |

**Exemple :**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source MongoDB.

### <a name="mongodb-as-source"></a>MongoDB en tant que source

Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur : **MongoDbV2Source** | OUI |
| filter | Spécifie le filtre de sélection à l’aide d’opérateurs de requête. Pour retourner tous les documents dans une collection, omettez ce paramètre ou passez un document vide ({}). | Non |
| cursorMethods.project | Spécifie les champs à retourner dans les documents pour la projection. Pour retourner tous les champs dans les documents correspondants, omettez ce paramètre. | Non |
| cursorMethods.sort | Spécifie l’ordre dans lequel la requête retourne les documents correspondants. Voir [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Non |
| cursorMethods.limit | Spécifie le nombre maximal de documents retournés par le serveur. Voir [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Non |
| cursorMethods.skip | Spécifie le nombre de documents à ignorer, et à partir de quel endroit MongoDB commence à retourner des résultats. Voir [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Non |
| batchSize | Spécifie le nombre de documents à retourner dans chaque lot de la réponse renvoyée par l’instance MongoDB. Dans la plupart des cas, la modification de la taille de lot n’affectera pas l’utilisateur ou l’application. Cosmos DB limite la taille de chaque lot à 40 Mo, qui est la somme de la taille batchSize du nombre de documents. Par conséquent, diminuez cette valeur si la taille de votre document est trop grande. | Non<br/>(la valeur par défaut est **100**) |

>[!TIP]
>ADF prend en charge la consommation de document BSON en **mode Strict**. Vérifiez que votre requête de filtre est en mode Strict plutôt qu’en mode Shell. Vous trouverez une description plus détaillée dans le [manuel MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "MongoDbV2Source",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="export-json-documents-as-is"></a>exporter des documents JSON tels quels

Vous pouvez utiliser ce connecteur MongoDB pour exporter des documents JSON tels quels à partir d’une collection MongoDB vers différentes banques basées sur fichiers ou vers Azure Cosmos DB. Pour obtenir une telle copie indépendante du schéma, ignorez la section « structure » (également appelée *schéma*) dans le mappage de schéma et de jeu de données dans l’activité de copie.

## <a name="schema-mapping"></a>Mappage de schéma

Pour copier des données de MongoDB vers un récepteur tabulaire, consultez [Mappage de schéma](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
