---
title: Copier des données depuis et vers Salesforce à l’aide d’Azure Data Factory | Microsoft Docs
description: Découvrez comment copier des données de Salesforce vers des banques de données réceptrices prises en charge ou comment copier des données de banques de données sources prises en charge vers Salesforce, à l’aide de l’activité de copie disponible dans le pipeline d’une fabrique de données.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: jingwang
ms.openlocfilehash: 6056df9aa9079887bfb06ca20ad564eb52baff38
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60546582"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Copier des données depuis et vers Salesforce à l’aide d’Azure Data Factory
> [!div class="op_single_selector" title1="Sélectionnez la version du service Data Factory que vous utilisez :"]
> * [Version 1](v1/data-factory-salesforce-connector.md)
> * [Version actuelle](connector-salesforce.md)

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données à partir de Salesforce et vers Salesforce. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Vous pouvez copier des données de Salesforce vers toute banque de données réceptrice prise en charge. Vous pouvez également copier des données de n’importe quel magasin de données source pris en charge vers Salesforce. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Ce connecteur Salesforce prend en charge :

- Développeur Salesforce, éditions professionnelle, d’entreprise ou illimitées.
- La copie de données depuis et vers le domaine de production, le bac à sable et le domaine personnalisé de Salesforce.

Le connecteur Salesforce s'appuie sur l'API REST/en bloc de Salesforce, avec [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) pour copier des données à partir d'un emplacement, et [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) pour copier des données vers un emplacement.

## <a name="prerequisites"></a>Prérequis

L’autorisation de l’API doit être activée dans Salesforce. Pour plus d’informations, consultez l’article [How do I enable API access in Salesforce by permission set?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) (Comment activer l’accès à l’API dans Salesforce par jeu d’autorisations ?)

## <a name="salesforce-request-limits"></a>Limites des requêtes Salesforce

Salesforce prend en charge un nombre limité de requêtes d’API totales et de requêtes d’API simultanées. Notez les points suivants :

- Si le nombre de requêtes simultanées dépasse la limite autorisée, les nouvelles requêtes sont bloquées avec un risque de défaillances aléatoires.
- Si le nombre total de requêtes dépasse la limite autorisée, le compte Salesforce est bloqué pendant 24 heures.

Vous pouvez également recevoir le message d’erreur « REQUEST_LIMIT_EXCEEDED » dans les deux scénarios. Pour plus d’informations, consultez la section « API Request Limits » (Limites de requête d’API) du document [Salesforce Developer Limits](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Limites des développeurs Salesforce).

## <a name="get-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir des entités Data Factory spécifiques au connecteur Salesforce.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés suivantes sont prises en charge pour le service lié Salesforce :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type |La propriété de type doit être définie sur **Salesforce**. |OUI |
| environmentUrl | Spécifiez l’URL de l’instance Salesforce. <br> - La valeur par défaut est `"https://login.salesforce.com"`. <br> - Pour copier des données du bac à sable, spécifiez `"https://test.salesforce.com"`. <br> - Pour copier les données du domaine personnalisé, spécifiez, par exemple, `"https://[domain].my.salesforce.com"`. |Non |
| username |Spécifiez un nom d’utilisateur pour le compte d’utilisateur. |OUI |
| password |Spécifiez le mot de passe du compte d’utilisateur.<br/><br/>Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| securityToken |Spécifiez le jeton de sécurité du compte d’utilisateur. Pour obtenir des instructions sur la réinitialisation et l’obtention d’un jeton de sécurité, consultez l’article [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Obtenir un jeton de sécurité). Pour en savoir plus sur les jetons de sécurité, consultez l’article [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)(Sécurité et API).<br/><br/>Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| connectVia | Le [runtime d’intégration](concepts-integration-runtime.md) à utiliser pour se connecter à la banque de données. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non pour la source, oui pour le récepteur si le service lié à la source n’a pas de runtime d’intégration |

>[!IMPORTANT]
>Lorsque vous copiez de données dans Salesforce, le runtime d’intégration Azure par défaut ne peut pas être utilisé pour exécuter la copie. En d’autres termes, si votre service lié à la source n’a pas de runtime d’intégration spécifié, [créez un Runtime d’intégration Azure](create-azure-integration-runtime.md#create-azure-ir) de manière explicite, avec un emplacement près de votre instance Salesforce. Associez le service lié de Salesforce comme dans l’exemple suivant.

**Exemple : Stocker les informations d’identification dans Data Factory**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemple : Stocker les informations d’identification dans Key Vault**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Jeux de données](concepts-datasets-linked-services.md). Cette section fournit la liste des propriétés prises en charge par le jeu de données Salesforce.

Pour copier des données depuis et vers Salesforce, définissez la propriété de type du jeu de données sur **SalesforceObject**. Les propriétés suivantes sont prises en charge.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **SalesforceObject**.  | OUI |
| objectApiName | Nom d’objet Salesforce duquel extraire des données. | Non pour Source, Oui pour Récepteur |

> [!IMPORTANT]
> La partie « __c » du **nom de l’API** est requise pour tout objet personnalisé.

![Connexion Salesforce – Data Factory – Nom de l’API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Exemple :**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "objectApiName": "MyTable__c"
        }
    }
}
```

>[!NOTE]
>Pour la compatibilité descendante : Quand vous copiez des données depuis Salesforce, si vous utilisez le jeu de données de type « RelationalTable » précédent, il continue de fonctionner. Cependant, il vous est proposé de passer au nouveau type « SalesforceObject ».

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur **RelationalTable**. | OUI |
| tableName | Nom de la table dans Salesforce. | Non (si « query » est spécifié dans la source de l’activité) |

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source et le récepteur Salesforce.

### <a name="salesforce-as-a-source-type"></a>Salesforce en tant que type de source

Pour copier des données à partir de Salesforce, définissez le type de source sur **SalesforceSource** dans l’activité de copie. Les propriétés suivantes sont prises en charge dans la section **source** de l’activité de copie.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type de la source d’activité de copie doit être définie sur **SalesforceSource**. | OUI |
| query |Utilise la requête personnalisée pour lire des données. Vous pouvez utiliser une requête SQL-92 ou [SOQL (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Retrouvez d’autres conseils dans la section [Conseils de requête](#query-tips). Si la requête n’est pas spécifiée, toutes les données de l’objet Salesforce spécifié au niveau de la propriété « objectApiName » du jeu de données seront récupérées. | Non (si « objectApiName » est spécifié dans le jeu de données) |
| readBehavior | Indique si seuls les enregistrements existants doivent être interrogés ou si tous les enregistrements, y compris ceux qui ont été supprimés, doivent être interrogés. Si rien n’est spécifié, le comportement par défaut appliqué est le premier. <br>Valeurs autorisées : **query** (valeur par défaut), **queryAll**.  | Non |

> [!IMPORTANT]
> La partie « __c » du **nom de l’API** est requise pour tout objet personnalisé.

![Connexion Salesforce – Data Factory – Listes des noms d’API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Pour la compatibilité descendante : Quand vous copiez des données depuis Salesforce, si vous utilisez le type « RelationalSource » précédent, la source continue de fonctionner. Cependant, il vous est proposé de passer au nouveau type « SalesforceSource ».

### <a name="salesforce-as-a-sink-type"></a>Salesforce comme type de récepteur

Pour copier des données vers Salesforce, définissez le type de récepteur sur **SalesforceSink** dans l’activité de copie. Les propriétés suivantes sont prises en charge dans la section **récepteur** de l’activité de copie.

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type du récepteur d’activité de copie doit être définie sur **SalesforceSink**. | OUI |
| writeBehavior | Comportement d’écriture de l’opération.<br/>Les valeurs autorisées sont **Insert** et **Upsert**. | Non (la valeur par défaut est un point Insert) |
| externalIdFieldName | Nom du champ ID externe pour l’opération upsert. Le champ spécifié doit être défini en tant que « Champ Id externe » dans l’objet Salesforce. Il ne peut pas avoir de valeurs NULL dans les données d’entrée correspondantes. | Oui, pour « Upsert » |
| writeBatchSize | Nombre de lignes de données écrites dans Salesforce pour chaque lot. | Non (valeur par défaut : 5,000) |
| ignoreNullValues | Indique si les valeurs NULL des données d’entrée doivent être ignorées pendant une opération d’écriture.<br/>Les valeurs autorisées sont **true** et **false**.<br>- **True** : Conserver les données dans l’objet de destination quand vous effectuez une opération upsert ou de mise à jour. Insérer une valeur définie par défaut lorsque vous effectuez une opération insert.<br/>- **False** : Mettre à jour les données dans l’objet de destination avec la valeur NULL quand vous effectuez une opération upsert ou de mise à jour. Insérer une valeur NULL lorsque vous effectuez une opération insert. | Non (valeur par défaut : false) |

**Exemple : Récepteur Salesforce dans une activité de copie**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Conseils pour les requêtes

### <a name="retrieve-data-from-a-salesforce-report"></a>Récupérer des données à partir d’un rapport Salesforce

Vous pouvez récupérer des données à partir de rapports Salesforce en spécifiant une requête en tant que `{call "<report name>"}`. Par exemple `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Récupérer des enregistrements supprimés dans la Corbeille Salesforce

Pour interroger les enregistrements supprimés de manière réversible dans la Corbeille Salesforce, vous pouvez spécifier `readBehavior`comme `queryAll`. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Différence entre la syntaxe de requête SOQL et SQL

Lors de la copie des données à partir de Salesforce, vous pouvez utiliser une requête SOQL ou SQL. Notez que ces deux requêtes ont une syntaxe et une prise en charge des fonctionnalités différentes, ne les mélangez pas. Il est recommandé d’utiliser la requête SOQL, prise en charge nativement par Salesforce. Le tableau suivant répertorie les différences principales :

| Syntaxe | Mode SOQL | Mode SQL |
|:--- |:--- |:--- |
| Sélection de colonnes | Besoin d'énumérer les champs à copier dans la requête, par exemple `SELECT field1, filed2 FROM objectname` | `SELECT *` est pris en charge en plus de la sélection de colonnes. |
| Guillemets | Les noms de champ ou d’objet ne peuvent pas être mis entre guillemets. | Les noms de champ ou d’objet peuvent être mis entre guillemets, par exemple `SELECT "id" FROM "Account"` |
| Format de date/heure |  Reportez-vous aux détails [ici](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) et aux exemples fournis dans la section suivante. | Reportez-vous aux détails [ici](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) et aux exemples fournis dans la section suivante. |
| Valeurs booléennes | Représentées sous la forme de `False` et `True`, par exemple `SELECT … WHERE IsDeleted=True`. | Représenté sous la forme de 0 ou de 1, par exemple `SELECT … WHERE IsDeleted=1`. |
| Changement de nom de colonne | Non pris en charge. | Pris en charge, par exemple : `SELECT a AS b FROM …`. |
| Relation | Pris en charge, par exemple `Account_vod__r.nvs_Country__c`. | Non pris en charge. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Récupérer des données à l’aide d’une clause where sur la colonne DateTime

Lorsque vous spécifiez une requête SOQL ou SQL, faites attention à la différence de format DateTime. Par exemple :

* **Exemple SOQL** : `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Exemple SQL** : `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformedquerytruncated"></a>Erreur MALFORMED_QUERY:Truncated

L'erreur « MALFORMED_QUERY: Truncated » peut être rencontrée lorsqu'une colonne de type JunctionIdList est présente dans les données et que la prise en charge par Salesforce de données contenant un grand nombre de lignes est limitée. Pour atténuer ce problème, essayez d'exclure la colonne JunctionIdList ou de limiter le nombre de lignes à copier (vous pouvez partitionner le travail en plusieurs exécutions d'activité de copie).

## <a name="data-type-mapping-for-salesforce"></a>Mappage de type de données pour Salesforce

Lorsque vous copiez des données de Salesforce, les mappages suivants sont utilisés entre les types de données Salesforce et les types de données intermédiaires d’Azure Data Factory. Pour découvrir comment l’activité de copie mappe le schéma et le type de données la source au récepteur, consultez [Mappage de schéma dans l’activité de copie](copy-activity-schema-and-type-mapping.md).

| Type de données Salesforce | Type de données intermédiaires d’Azure Data Factory |
|:--- |:--- |
| Numérotation automatique |Chaîne |
| Case à cocher |Boolean |
| Devise |Decimal |
| Date |Datetime |
| Date/Heure |Datetime |
| Email |Chaîne |
| ID |Chaîne |
| Relation de recherche |Chaîne |
| Liste déroulante à sélection multiple |Chaîne |
| Number |Decimal |
| Pourcentage |Decimal |
| Téléphone |Chaîne |
| Liste déroulante |Chaîne |
| Texte |Chaîne |
| Zone de texte |Chaîne |
| Zone de texte (long) |Chaîne |
| Zone de texte (enrichi) |Chaîne |
| Texte (chiffré) |Chaîne |
| URL |Chaîne |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
