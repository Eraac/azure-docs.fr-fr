---
title: Copier des données à partir de Google Cloud Storage avec Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser Azure Data Factory pour copier des données de Google Cloud Storage vers des banques de données réceptrices prises en charge.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 9c9a4b41dbb9b9acc2982ae2af1f3a611f9d3beb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65228280"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Copier des données à partir de Google Cloud Storage avec Azure Data Factory

Cet article explique comment copier des données à partir de Google Cloud Storage. Pour en savoir plus sur Azure Data Factory, lisez l’[article d’introduction](introduction.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Ce connecteur Google Cloud Storage est pris en charge pour les activités suivantes :

- [Activité de copie](copy-activity-overview.md) avec [prise en charge de la matrice source/du récepteur](copy-activity-overview.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité GetMetadata](control-flow-get-metadata-activity.md)

Plus spécifiquement, ce connecteur Google Cloud Storage prend en charge la copie de fichiers en l'état ou l’analyse de fichiers avec les [formats de fichier et codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Copier des données à partir de Google Cloud Storage exploite le [connecteur Amazon S3](connector-amazon-simple-storage-service.md) avec le point de terminaison personnalisé S3 correspondant, car Google Cloud Storage offre une interopérabilité compatible S3.

## <a name="required-permissions"></a>Autorisations requises

Pour copier des données à partir de Google Cloud Storage, assurez-vous que vous disposez des autorisations suivantes :

- **Pour l’exécution de l’activité de copie :** : `s3:GetObject` et `s3:GetObjectVersion` pour les opérations d’objet.
- **Lorsque vous utilisez l’interface utilisateur graphique de Data Factory pour la création** : les autorisations `s3:ListAllMyBuckets` et `s3:ListBucket`/`s3:GetBucketLocation` relatives aux opérations de compartiment sont également requises pour les opérations telles que « tester la connexion » et « parcourir/accéder à des chemins de fichiers ». Si vous ne souhaitez pas accorder ces autorisations, ignorez « tester la connexion » dans la page de création de service lié et spécifiez directement le chemin d’accès dans les paramètres du jeu de données.

## <a name="getting-started"></a>Prise en main

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Les sections suivantes fournissent des informations détaillées sur les propriétés utilisées pour définir les entités Data Factory spécifiques du connecteur Google Cloud Storage.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Google Cloud Storage sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété de type doit être définie sur **AmazonS3**. | OUI |
| accessKeyId | ID de la clé d’accès secrète. Pour trouver la clé d’accès et le secret, accédez à **Google Cloud Storage** > **Paramètres** > **Interopérabilité**. |OUI |
| secretAccessKey | La clé d’accès secrète elle-même. Marquez ce champ en tant que SecureString afin de le stocker en toute sécurité dans Data Factory, ou [référencez un secret stocké dans Azure Key Vault](store-credentials-in-key-vault.md). |OUI |
| serviceUrl | Spécifiez le point de terminaison personnalisé S3 en tant que **`https://storage.googleapis.com`** . | OUI |
| connectVia | [Runtime d’intégration](concepts-integration-runtime.md) à utiliser pour la connexion à la banque de données. Vous pouvez utiliser runtime d’intégration Azure ou un runtime d’intégration auto-hébergé (si votre banque de données se trouve dans un réseau privé). À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. |Non |

Voici un exemple :

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriétés du jeu de données

- Pour les **formats Parquet et de texte délimité**, reportez-vous à la section [Jeu de données aux formats Parquet et de texte délimité](#parquet-and-delimited-text-format-dataset).
- Pour les autres formats tels que les **formats ORC/Avro/JSON/binaire**, reportez-vous à la section [Autres formats de jeu de données](#other-format-dataset).

### <a name="parquet-and-delimited-text-format-dataset"></a>Jeu de données aux formats Parquet et de texte délimité

Pour copier des données à partir de Google Cloud Storage au [format Parquet ou de texte délimité](format-delimited-text.md), reportez-vous aux articles **Format Parquet** et [Format de texte délimité](format-parquet.md) sur le jeu de données basé sur le format et les paramètres pris en charge. Les propriétés suivantes sont prises en charge pour Google Cloud Storage sous les paramètres `location` dans le jeu de données basé sur le format :

| Propriété   | Description                                                  | Obligatoire |
| ---------- | ------------------------------------------------------------ | -------- |
| Type       | La propriété de type sous `location` dans le jeu de données doit être définie sur **AmazonS3Location**. | OUI      |
| bucketName | Le nom de compartiment S3.                                          | OUI      |
| folderPath | Le chemin d’accès au dossier sous le compartiment donné. Si vous souhaitez utiliser un caractère générique pour filtrer le dossier, ignorez ce paramètre et spécifiez-le dans les paramètres de la source de l’activité. | Non       |
| fileName   | Le nom de fichier sous le compartiment et le chemin d’accès donnés. Si vous souhaitez utiliser un caractère générique pour filtrer les fichiers, ignorez ce paramètre et spécifiez-le dans les paramètres de la source de l’activité. | Non       |

> [!NOTE]
> Le jeu de données de type **AmazonS3Object** au format Parquet/texte mentionné dans la section suivante est toujours pris en charge tel quel pour l’activité de copie/de recherche/GetMetadata pour la compatibilité descendante. Il est recommandé d’utiliser ce nouveau modèle à partir de maintenant. L’IU de création ADF génère désormais ces nouveaux types.

**Exemple :**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Autre format de jeu de données

Pour la copie de données à partir de Google Cloud Storage au **format ORC/Avro/JSON/binaire**, les propriétés suivantes sont prises en charge :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur : **AmazonS3Object** |OUI |
| bucketName | Le nom de compartiment S3. Le filtre de caractères génériques n'est pas pris en charge. |Oui pour la copie/Activité Lookup/Non pour l’activité GetMetadata |
| key | Le **filtre de nom ou de caractères génériques** de la clé d’objet S3 sous le compartiment spécifié. S’applique uniquement lorsque la propriété « prefix » n’est pas spécifiée. <br/><br/>Le filtre de caractères génériques est pris en charge pour la partie du dossier et la partie du nom de fichier. Les caractères génériques autorisés sont les suivants : `*` (correspond à zéro caractère ou plusieurs) et `?` (correspond à zéro ou un caractère).<br/>- Exemple 1 : `"key": "rootfolder/subfolder/*.csv"`<br/>- Exemple 2 : `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). Utilisez `^` comme caractère d’échappement si votre nom de dossier/fichier réel contient des caractères génériques ou ce caractère d’échappement. |Non |
| prefix | Préfixe de la clé d’objet S3. Les objets dont les clés commencent par ce préfixe sont sélectionnés. S’applique uniquement lorsque la propriété « key » n’est pas spécifiée. |Non |
| version | La version de l’objet S3 si le contrôle de version S3 est activé. |Non |
| modifiedDatetimeStart | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> Les propriétés peuvent être Null, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non |
| modifiedDatetimeEnd | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br/><br/> Les propriétés peuvent être Null, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés.| Non |
| format | Si vous souhaitez **copier des fichiers en l’état** entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie.<br/><br/>Si vous souhaitez analyser ou générer des fichiers dans un format spécifique, les types de format de fichier suivants sont pris en charge : **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** et **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour en savoir plus, consultez les sections relatives à [format Text](supported-file-formats-and-compression-codecs.md#text-format), [format Json](supported-file-formats-and-compression-codecs.md#json-format), [format Avro](supported-file-formats-and-compression-codecs.md#avro-format), [format Orc](supported-file-formats-and-compression-codecs.md#orc-format) et [format Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Non (uniquement pour un scénario de copie binaire) |
| compression | Spécifiez le type et le niveau de compression pour les données. Pour plus d’informations, voir [Formats de fichier et de codecs de compression pris en charge](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Types pris en charge : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**.<br/>Niveaux pris en charge : **Optimal** et **Fastest**. |Non |

>[!TIP]
>Pour copier tous les fichiers d’un dossier, spécifiez **bucketName** pour le compartiment et **prefix** pour la partie dossier.<br>Pour copier un seul fichier avec un nom donné, spécifiez **bucketName** pour le compartiment et **key** pour la partie dossier plus le nom de fichier.<br>Pour copier un sous-ensemble de fichiers d’un dossier, spécifiez **bucketName** pour le compartiment et **key** pour la partie dossier plus le filtre de caractères génériques.

**Exemple : utilisation de préfixe**

```json
{
    "name": "GoogleCloudStorageDataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section fournit la liste des propriétés prises en charge par la source Google Cloud Storage.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage comme source

- Pour copier à partir des **formats Parquet et de texte délimité**, reportez-vous à la section [Source aux formats Parquet et de texte délimité](#parquet-and-delimited-text-format-source).
- Pour copier à partir d’autres formats tels que les **formats ORC/Avro/JSON/binaire**, reportez-vous à la section [Autres formats de source](#other-format-source).

#### <a name="parquet-and-delimited-text-format-source"></a>Source aux formats Parquet et de texte délimité

Pour copier des données à partir de Google Cloud Storage au **format Parquet ou de texte délimité**, reportez-vous aux articles [Format Parquet](format-parquet.md) et [Format de texte délimité](format-delimited-text.md) sur la source de l’activité de copie basée sur le format et les paramètres pris en charge. Les propriétés suivantes sont prises en charge pour Google Cloud Storage sous les paramètres `storeSettings` dans la source de la copie basée sur le format :

| Propriété                 | Description                                                  | Obligatoire                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| Type                     | La propriété type sous `storeSettings` doit être définie sur **AmazonS3ReadSetting**. | OUI                                                         |
| recursive                | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. Notez que lorsque l’option « recursive » est définie sur true et que le récepteur est un magasin basé sur un fichier, un dossier vide ou un sous-dossier n’est pas copié ou créé sur le récepteur. Les valeurs autorisées sont **true** (par défaut) et **false**. | Non                                                          |
| prefix                   | Le préfixe pour la clé d’objet S3 sous le compartiment donné configuré dans le jeu de données pour filtrer les objets sources. Les objets dont les clés commencent par ce préfixe sont sélectionnés. S’applique uniquement lorsque les propriétés `wildcardFolderPath` et `wildcardFileName` ne sont pas spécifiées. |                                                             |
| wildcardFolderPath       | Le chemin d’accès du dossier avec des caractères génériques sous le compartiment donné configuré dans le jeu de données pour filtrer les dossiers sources. <br>Les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de dossier contient effectivement ce caractère d’échappement ou générique. <br>Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Non                                                          |
| wildcardFileName         | Le nom du fichier avec des caractères génériques situé sous le compartiment et le chemin d’accès folderPath/wildcardFolderPath donnés pour filtrer les fichiers sources. <br>Les caractères génériques autorisés sont : `*` (correspond à zéro ou plusieurs caractères) et `?` (correspond à zéro ou un caractère) ; utilisez `^` en guise d’échappement si votre nom de dossier contient effectivement ce caractère d’échappement ou générique.  Consultez d’autres exemples dans les [exemples de filtre de dossier et de fichier](#folder-and-file-filter-examples). | Oui, si `fileName` dans le jeu de données et `prefix` ne sont pas spécifiés |
| modifiedDatetimeStart    | Filtre de fichiers en fonction de l’attribut : Dernière modification. Les fichiers seront sélectionnés si leur heure de dernière modification se trouve dans l’intervalle de temps situé entre `modifiedDatetimeStart` et `modifiedDatetimeEnd`. L’heure est appliquée au fuseau horaire UTC au format « 2018-12-01T05:00:00Z ». <br> Les propriétés peuvent être Null, ce qui signifie qu’aucun filtre d’attribut de fichier n’est appliqué au jeu de données.  Lorsque `modifiedDatetimeStart` a une valeur DateHeure, mais que `modifiedDatetimeEnd` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est supérieur ou égal à la valeur DateHeure sont sélectionnés.  Lorsque `modifiedDatetimeEnd` a une valeur DateHeure, mais que `modifiedDatetimeStart` est NULL, cela signifie que les fichiers dont l’attribut de dernière modification est inférieur à la valeur DateHeure sont sélectionnés. | Non                                                          |
| modifiedDatetimeEnd      | Identique à ce qui précède.                                               | Non                                                          |
| maxConcurrentConnections | Le nombre de connexions simultanées au magasin de stockage. Spécifiez uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non                                                          |

> [!NOTE]
> Pour les formats Parquet et de texte délimité, la source de l’activité de copie de type **FileSystemSource** mentionnée dans la section suivante est toujours prise en charge telle quelle pour la compatibilité descendante. Il est recommandé d’utiliser ce nouveau modèle à partir de maintenant. L’IU de création ADF génère désormais ces nouveaux types.

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Autres formats de source

Pour copier des données à partir de Google Cloud Storage au **format ORC/Avro/JSON/binaire**, les propriétés suivantes sont prises en charge dans la section **source** de l’activité de copie :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur : **FileSystemSource** |OUI |
| recursive | Indique si les données sont lues de manière récursive dans les sous-dossiers ou uniquement dans le dossier spécifié. Remarque : Quand l’option récursive a la valeur true et que le récepteur est un magasin basé sur des fichiers, le dossier/sous-dossier vide n’est pas copié/créé dans le récepteur.<br/>Valeurs autorisées : **true** (par défaut) et **false** | Non |
| maxConcurrentConnections | Le nombre de connexions simultanées au magasin de stockage. Spécifiez uniquement lorsque vous souhaitez limiter les connexions simultanées au magasin de données. | Non |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exemples de filtres de dossier et de fichier

Cette section décrit le comportement résultant de l’utilisation de filtres de caractères génériques dans les noms de fichier et les chemins de dossier.

| Compartiment | key | recursive | Structure du dossier source et résultat du filtrage (les fichiers en gras sont récupérés)|
|:--- |:--- |:--- |:--- |
| Compartiment | `Folder*/*` | false | Compartiment<br/>&nbsp;&nbsp;&nbsp;&nbsp;DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| Compartiment | `Folder*/*` | true | Compartiment<br/>&nbsp;&nbsp;&nbsp;&nbsp;DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| Compartiment | `Folder*/*.csv` | false | Compartiment<br/>&nbsp;&nbsp;&nbsp;&nbsp;DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |
| Compartiment | `Folder*/*.csv` | true | Compartiment<br/>&nbsp;&nbsp;&nbsp;&nbsp;DossierA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Fichier5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AutreDossierB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier6.csv |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md##supported-data-stores-and-formats).
