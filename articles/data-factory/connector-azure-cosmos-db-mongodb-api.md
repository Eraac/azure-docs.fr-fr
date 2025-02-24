---
title: Copier des données de ou vers l’API Azure Cosmos DB pour MongoDB à l’aide de Data Factory | Microsoft Docs
description: Découvrez comment utiliser Azure Data Factory pour copier des données de banques de données sources prises en charge de ou vers l’API Azure Cosmos DB pour MongoDB.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 82418c03039219adedf45828d769d278a14499ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61259716"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Copier des données de ou vers l’API Azure Cosmos DB pour MongoDB à l’aide d’Azure Data Factory

Cet article décrit comment utiliser l’activité de copie dans Azure Data Factory pour copier des données de et vers l’API Azure Cosmos DB pour MongoDB. Il s’appuie sur l’article [Activité de copie dans Azure Data Factory](copy-activity-overview.md), qui constitue une présentation de l’activité de copie.

>[!NOTE]
>Ce connecteur prend uniquement en charge la copie des données de/vers l’API Azure Cosmos DB pour MongoDB. Pour l’API SQL, voir [connecteur d’API SQL Cosmos DB](connector-azure-cosmos-db.md). Les autres types d’API ne sont pas pris en charge actuellement.

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données de l’API Azure Cosmos DB pour MongoDB vers toute banque de données réceptrice prise en charge, ou copier des données de toute banque de données source prise en charge vers l’API Azure Cosmos DB pour MongoDB. Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs pour l’activité de copie, consultez [Magasins de données et formats pris en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Vous pouvez utiliser le connecteur de l’API Azure Cosmos DB pour MongoDB pour :

- Copier des données de et vers l’[API Azure Cosmos DB pour MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Écrire dans Azure Cosmos DB comme **insert** ou **upsert**.
- Importer et exporter des documents JSON en l’état, ou copier des données depuis ou vers un jeu de données tabulaire. Les exemples incluent une base de données SQL et un fichier CSV. Pour copier des documents en l'état depuis ou vers des fichiers JSON ou une autre collection Azure Cosmos DB, consultez Importer ou exporter des documents JSON.

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir les entités Data Factory propres à l’API Azure Cosmos DB pour MongoDB.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié à l’API Azure Cosmos DB pour MongoDB sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** doit être définie sur **CosmosDbMongoDbApi**. | OUI |
| connectionString |Spécifiez la chaîne de connexion pour votre API Azure Cosmos DB pour MongoDB. Vous pouvez la trouver sur le portail Microsoft Azure -> votre panneau Cosmos DB -> chaîne de connexion primaire ou secondaire, avec le modèle de `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`. <br/><br />Vous pouvez marquer ce champ en tant que type **SecureString** pour le stocker de manière sécurisée dans Data Factory. Vous pouvez également [référencer un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| database | Nom de la base de données à laquelle vous souhaitez accéder. | OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion au magasin de données. Vous pouvez utiliser Azure Integration Runtime ou un runtime d’intégration auto-hébergé si votre banque de données se trouve sur un réseau privé. Si cette propriété n’est pas spécifiée, Azure Integration Runtime par défaut est utilisé. |Non |

**Exemple**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
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

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez [Jeux de données et services liés](concepts-datasets-linked-services.md). Les propriétés prises en charge pour le jeu de données de l’API Azure Cosmos DB pour MongoDB sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** du jeu de données doit être définie sur **CosmosDbMongoDbApiCollection**. |OUI |
| collectionName |Nom de la collection Azure Cosmos DB. |OUI |

**Exemple**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Cette section fournit la liste des propriétés prises en charge par l’API Azure Cosmos DB pour MongoDB en tant que source et récepteur.

Pour obtenir la liste complète des sections et des propriétés permettant de définir des activités, consultez [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>API Azure Cosmos DB pour MongoDB en tant que source

Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** de la source d’activité de copie doit être définie sur **CosmosDbMongoDbApiSource**. |OUI |
| filter | Spécifie le filtre de sélection à l’aide d’opérateurs de requête. Pour retourner tous les documents dans une collection, omettez ce paramètre ou passez un document vide ({}). | Non |
| cursorMethods.project | Spécifie les champs à retourner dans les documents pour la projection. Pour retourner tous les champs dans les documents correspondants, omettez ce paramètre. | Non |
| cursorMethods.sort | Spécifie l’ordre dans lequel la requête retourne les documents correspondants. Voir [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Non |
| cursorMethods.limit | Spécifie le nombre maximal de documents retournés par le serveur. Voir [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Non | 
| cursorMethods.skip | Spécifie le nombre de documents à ignorer, et à partir de quel endroit MongoDB commence à retourner des résultats. Voir [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Non |
| batchSize | Spécifie le nombre de documents à retourner dans chaque lot de la réponse renvoyée par l’instance MongoDB. Dans la plupart des cas, la modification de la taille de lot n’affectera pas l’utilisateur ou l’application. Cosmos DB limite la taille de chaque lot à 40 Mo, qui est la somme de la taille batchSize du nombre de documents. Par conséquent, diminuez cette valeur si la taille de votre document est trop grande. | Non<br/>(la valeur par défaut est **100**) |

>[!TIP]
>ADF prend en charge la consommation de document BSON en **mode Strict**. Vérifiez que votre requête de filtre est en mode Strict plutôt qu’en mode Shell. Vous trouverez une description plus détaillée dans le [manuel MongoDB](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Exemple**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
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
                "type": "CosmosDbMongoDbApiSource",
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

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>API Azure Cosmos DB pour MongoDB en tant que récepteur

Les propriétés suivantes sont prises en charge dans la section **sink** de l’activité de copie :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété **type** du récepteur d’activité de copie doit être définie sur **CosmosDbMongoDbApiSink**. |OUI |
| writeBehavior |Décrit comment écrire des données dans Azure Cosmos DB. Les valeurs autorisées sont **insert** et **upsert**.<br/><br/>Le comportement de la valeur **upsert** consiste à remplacer le document si un document portant le même identificateur existe déjà ; sinon, le document est inséré.<br /><br />**Remarque**: Azure Data Factory génère automatiquement un ID pour un document s’il n’est pas spécifié dans le document d’origine ni par le mappage de colonnes. Cela signifie que vous devez vérifier que votre document comporte un ID afin qu’**upsert** fonctionne comme prévu. |Non<br />(la valeur par défaut est **insert**) |
| writeBatchSize | La propriété **writeBatchSize** contrôle la taille des documents à écrire dans chaque lot. Vous pouvez essayer d’augmenter la valeur de **writeBatchSize** pour améliorer les performances et diminuer la valeur si la taille de votre document est grande. |Non<br />(la valeur par défaut est **10 000**) |
| writeBatchTimeout | Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer. La valeur autorisée est timespan. | Non<br/>(la valeur par défaut est **00:30:00** – 30 minutes) |

**Exemple**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

>[!TIP]
>Pour importer des documents JSON en l’état, voir la section [Importer ou exporter des documents JSON](#import-or-export-json-documents). Pour copier à partir de données sous forme tabulaire, voir [Mappage de schéma](#schema-mapping).

## <a name="import-or-export-json-documents"></a>Importer ou exporter des documents JSON

À l’aide de ce connecteur Azure Cosmos DB, vous pouvez facilement :

* Importer des documents JSON de différentes sources dans Azure Cosmos DB, notamment depuis le stockage Blob Azure, Azure Data Lake Store et d’autres banques de données basées sur des fichiers prises en charge par Azure Data Factory.
* Exporter des documents JSON d’une collection Azure Cosmos DB vers différentes banques basées sur des fichiers.
* Copier des documents entre deux collections Azure Cosmos DB en l’état.

Pour obtenir une telle copie indépendante du schéma, ignorez la section « structure » (également appelée *schéma*) dans le mappage de schéma et de jeu de données dans l’activité de copie.

## <a name="schema-mapping"></a>Mappage de schéma

Pour copier des données de l’API Azure Cosmos DB pour MongoDB vers un récepteur tabulaire ou inversement, consultez [Mappage de schéma](copy-activity-schema-and-type-mapping.md#schema-mapping).

Plus spécifiquement, pour l’écriture dans Cosmos DB, pour vous assurer de remplir Cosmos DB avec l’ID d’objet approprié à partir de vos données sources (par exemple, vous avez une colonne « id » dans la table de base de données SQL, et souhaitez utiliser la valeur de celle-ci en tant qu’ID de document dans MongoDB pour insérer/fusionner), vous devez définir le mappage de schéma approprié en fonction de la définition du mode strict MongoDB (`_id.$oid`) comme suit :

![ID de mappage dans le récepteur MongoDB](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Après exécution de l’activité de copie, sous BSON, ObjectId est généré dans un récepteur :

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez [Banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
