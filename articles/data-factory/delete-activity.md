---
title: Activité Delete dans Azure Data Factory | Microsoft Docs
description: Découvrez comment supprimer des fichiers dans plusieurs magasins de fichiers avec l’activité Delete dans Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: 00658b650cdc0b1752bb9f2f205420018c1d6edd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61346341"
---
# <a name="delete-activity-in-azure-data-factory"></a>Activité Delete dans Azure Data Factory

L’activité Delete dans Azure Data Factory vous permet de supprimer des fichiers ou dossiers de magasins de stockage au niveau local ou dans le cloud. Utilisez cette activité pour nettoyer ou archiver les fichiers qui ne sont plus nécessaires.

> [!WARNING]
> Les fichiers ou dossiers supprimés ne peuvent pas être restaurés. Soyez donc prudent quand vous supprimez des fichiers ou dossiers à l’aide de l’activité Delete.

## <a name="best-practices"></a>Bonnes pratiques

Voici quelques recommandations relatives à l’utilisation de l’activité Delete :

-   Sauvegardez vos fichiers avant de les supprimer avec l’activité Delete au cas où vous souhaiteriez les restaurer par la suite.

-   Vérifiez que Data Factory dispose d’autorisations en écriture pour supprimer des dossiers ou fichiers du magasin de stockage.

-   Veillez à ne pas supprimer de fichiers en cours d’écriture. 

-   Si vous souhaitez supprimer des fichiers ou un dossier d’un système local, veillez à utiliser un runtime d’intégration auto-hébergé avec une version supérieure à 3.14.

## <a name="supported-data-stores"></a>Magasins de données pris en charge

-   [stockage d’objets blob Azure](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)

### <a name="file-system-data-stores"></a>Magasins de données de système de fichiers

-   [Système de fichiers](connector-file-system.md)
-   [FTP](connector-ftp.md)
-   [SFTP](connector-sftp.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "DeleteActivity",
    "type": "Delete",
    "typeProperties": {
        "dataset": {
            "referenceName": "<dataset name>",
            "type": "DatasetReference"
        },
        "recursive": true/false,
        "maxConcurrentConnections": <number>,
        "enableLogging": true/false,
        "logStorageSettings": {
            "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference"
            },
            "path": "<path to save log file>"
        }
    }
}
```

## <a name="type-properties"></a>Propriétés type

| Propriété | Description | Obligatoire |
| --- | --- | --- |
| dataset | Fournit la référence au jeu de données pour déterminer les fichiers ou le dossier à supprimer. | OUI |
| recursive | Indique si les fichiers sont supprimés de manière récursive des sous-dossiers ou uniquement du dossier spécifié.  | Non. Par défaut, il s’agit de `false`. |
| maxConcurrentConnections | Nombre de connexions simultanées au magasin de stockage pour supprimer un dossier ou des fichiers.   |  Non. Par défaut, il s’agit de `1`. |
| enablelogging | Indique si vous devez enregistrer les noms des dossiers ou fichiers qui ont été supprimés. Si la valeur est true, vous devez fournir un compte de stockage pour enregistrer le fichier journal dans lequel vous pouvez suivre les comportements de l’activité Delete. | Non |
| logStorageSettings | S’applique seulement quand enablelogging = true.<br/><br/>Groupe de propriétés de stockage vous permettant de spécifier où enregistrer le fichier journal contenant les noms des dossiers ou fichiers supprimés par l’activité Delete. | Non |
| linkedServiceName | S’applique seulement quand enablelogging = true.<br/><br/>Service lié de [Stockage Azure](connector-azure-blob-storage.md#linked-service-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#linked-service-properties) ou [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) utilisé pour stocker le fichier journal qui contient les noms des dossiers ou fichiers supprimés par l’activité Delete. | Non |
| chemin d’accès | S’applique seulement quand enablelogging = true.<br/><br/>Chemin utilisé pour enregistrer le fichier journal dans votre compte de stockage. Si vous ne spécifiez pas le chemin d’accès, le service crée un conteneur à votre place. | Non |

## <a name="monitoring"></a>Surveillance

Vous pouvez voir et superviser les résultats de l’activité Delete dans deux emplacements : 
-   Sortie de l’activité Delete
-   Fichier journal

### <a name="sample-output-of-the-delete-activity"></a>Exemple de sortie de l’activité Delete

```json
{ 
  "datasetName": "AmazonS3",
  "type": "AmazonS3Object",
  "prefix": "test",
  "bucketName": "adf",
  "recursive": true,
  "isWildcardUsed": false,
  "maxConcurrentConnections": 2,  
  "filesDeleted": 4,
  "logPath": "https://sample.blob.core.windows.net/mycontainer/5c698705-a6e2-40bf-911e-e0a927de3f07",
  "effectiveIntegrationRuntime": "MyAzureIR (West Central US)",
  "executionDuration": 650
}
```

### <a name="sample-log-file-of-the-delete-activity"></a>Exemple de fichier journal de l’activité Delete

| Nom | Catégorie | Statut | Error |
|:--- |:--- |:--- |:--- |
| test1/yyy.json | Fichier | Deleted |  |
| test2/hello789.txt | Fichier | Deleted |  |
| test2/test3/hello000.txt | Fichier | Deleted |  |
| test2/test3/zzz.json | Fichier | Deleted |  |

## <a name="examples-of-using-the-delete-activity"></a>Exemples d’utilisation de l’activité Delete

### <a name="delete-specific-folders-or-files"></a>Supprimer des dossiers ou fichiers spécifiques

Le magasin a la structure de dossiers suivante :

Racine/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dossier_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dossier_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dossier_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dossier_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt

Utilisez à présent l’activité Delete pour supprimer un dossier ou des fichiers en combinant différentes valeurs de propriété du jeu de données et de l’activité Delete :

| folderPath (provenant du jeu de données) | fileName (provenant du jeu de données) | recursive (provenant de l’activité Delete) | Sortie |
|:--- |:--- |:--- |:--- |
| Racine/ Dossier_A_2 | NULL | False | Racine/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dossier_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dossier_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dossier_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dossier_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Racine/ Dossier_A_2 | NULL | True | Racine/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dossier_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;<strike>Dossier_A_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Dossier_B_1/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>7.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Dossier_B_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |
| Racine/ Dossier_A_2 | *.txt | False | Racine/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dossier_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dossier_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dossier_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dossier_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Racine/ Dossier_A_2 | *.txt | True | Racine/<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dossier_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dossier_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dossier_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dossier_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |

### <a name="periodically-clean-up-the-time-partitioned-folder-or-files"></a>Nettoyer périodiquement le dossier ou les fichiers partitionnés dans le temps

Vous pouvez créer un pipeline pour nettoyer périodiquement le dossier ou les fichiers partitionnés dans le temps.  Voici un exemple de structure de dossiers : `/mycontainer/2018/12/14/*.csv`.  Vous pouvez tirer parti de la variable système ADF du déclencheur de planification pour identifier le dossier ou les fichiers à supprimer dans chaque exécution du pipeline. 

#### <a name="sample-pipeline"></a>Exemple de pipeline

```json
{
    "name": "cleanup_time_partitioned_folder",
    "properties": {
        "activities": [
            {
                "name": "DeleteOneFolder",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "PartitionedFolder",
                        "type": "DatasetReference",
                        "parameters": {
                            "TriggerTime": {
                                "value": "@formatDateTime(pipeline().parameters.TriggerTime, 'yyyy/MM/dd')",
                                "type": "Expression"
                            }
                        }
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ],
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

#### <a name="sample-dataset"></a>Exemple de jeu de données

```json
{
    "name": "PartitionedFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@concat('mycontainer/',dataset().TriggerTime)",
                "type": "Expression"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}
```

#### <a name="sample-trigger"></a>Exemple de déclencheur

```json
{
    "name": "DailyTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "cleanup_time_partitioned_folder",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "TriggerTime": "@trigger().scheduledTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Day",
                "interval": 1,
                "startTime": "2018-12-13T00:00:00.000Z",
                "timeZone": "UTC",
                "schedule": {
                    "minutes": [
                        59
                    ],
                    "hours": [
                        23
                    ]
                }
            }
        }
    }
}
```

### <a name="clean-up-the-expired-files-that-were-last-modified-before-201811"></a>Nettoyer les fichiers ayant expiré dont la dernière modification est antérieure au 1/1/2018

Vous pouvez créer un pipeline pour nettoyer les fichiers anciens ou ayant expiré à l’aide du filtre d’attribut de fichier LastModified dans le jeu de données.  

#### <a name="sample-pipeline"></a>Exemple de pipeline

```json
{
    "name": "CleanupExpiredFiles",
    "properties": {
        "activities": [
            {
                "name": "DeleteFilebyLastModified",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "BlobFilesLastModifiedBefore201811",
                        "type": "DatasetReference"
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ]
    }
}
```

#### <a name="sample-dataset"></a>Exemple de jeu de données

```json
{
    "name": "BlobFilesLastModifiedBefore201811",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "*",
            "folderPath": "mycontainer",
            "modifiedDatetimeEnd": "2018-01-01T00:00:00.000Z"
        }
    }
}
```

### <a name="move-files-by-chaining-the-copy-activity-and-the-delete-activity"></a>Déplacer des fichiers par chaînage des activités Copy et Delete

Vous pouvez déplacer un fichier en utilisant une activité Copy pour copier un fichier, puis une activité Delete pour supprimer un fichier d’un pipeline.  Pour déplacer plusieurs fichiers, vous pouvez utiliser les activités GetMetadata, Filter, Foreach, Copy et Delete comme dans l’exemple suivant :

> [!NOTE]
> Soyez très prudent si, pour déplacer la totalité d’un dossier, vous définissez un jeu de données contenant un chemin de dossier uniquement, puis utilisez une activité Copy et une activité Delete pour faire référence au même jeu de données représentant un dossier. En effet, vous devez veiller à ce qu’aucun nouveau fichier n’arrive dans le dossier entre l’opération de copie et l’opération de suppression.  Si de nouveaux fichiers arrivent dans le dossier au moment où votre activité Copy vient juste d’achever la copie mais avant le démarrage de l’activité Delete, il est possible que cette dernière, en supprimant la totalité du dossier, supprime le nouveau fichier qui n’a pas encore été copié dans la destination. 

#### <a name="sample-pipeline"></a>Exemple de pipeline

```json
{
    "name": "MoveFiles",
    "properties": {
        "activities": [
            {
                "name": "GetFileList",
                "type": "GetMetadata",
                "typeProperties": {
                    "dataset": {
                        "referenceName": "OneSourceFolder",
                        "type": "DatasetReference"
                    },
                    "fieldList": [
                        "childItems"
                    ]
                }
            },
            {
                "name": "FilterFiles",
                "type": "Filter",
                "dependsOn": [
                    {
                        "activity": "GetFileList",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('GetFileList').output.childItems",
                        "type": "Expression"
                    },
                    "condition": {
                        "value": "@equals(item().type, 'File')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "ForEachFile",
                "type": "ForEach",
                "dependsOn": [
                    {
                        "activity": "FilterFiles",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('FilterFiles').output.value",
                        "type": "Expression"
                    },
                    "batchCount": 20,
                    "activities": [
                        {
                            "name": "CopyAFile",
                            "type": "Copy",
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": false
                                },
                                "sink": {
                                    "type": "BlobSink"
                                },
                                "enableStaging": false,
                                "dataIntegrationUnits": 0
                            },
                            "inputs": [
                                {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "OneDestinationFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "DestinationFileName": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ]
                        },
                        {
                            "name": "DeleteAFile",
                            "type": "Delete",
                            "dependsOn": [
                                {
                                    "activity": "CopyAFile",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                }
                            ],
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "dataset": {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                },
                                "logStorageSettings": {
                                    "linkedServiceName": {
                                        "referenceName": "BloblinkedService",
                                        "type": "LinkedServiceReference"
                                    },
                                    "path": "Container/log"
                                },
                                "enableLogging": true
                            }
                        }
                    ]
                }
            }
        ]
    }
}
```

#### <a name="sample-datasets"></a>Exemples de jeux de données

Jeu de données utilisé par l’activité GetMetadata pour énumérer la liste de fichiers.

```json
{
    "name": "OneSourceFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "",
            "folderPath": "myFolder"
        }
    }
}
```

Jeu de données utilisé comme source de données par les activités Copy et Delete.

```json
{
    "name": "OneSourceFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            },
            "filename": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().filename",
                "type": "Expression"
            },
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        }
    }
}
```

Jeu de données utilisé comme destination de données par l’activité Copy.

```json
{
    "name": "OneDestinationFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "DestinationFileName": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().DestinationFileName",
                "type": "Expression"
            },
            "folderPath": "mycontainer/dest"
        }
    }
}
```
## <a name="known-limitation"></a>Limitation connue

-   L’activité Delete ne prend pas en charge la suppression de la liste des dossiers décrite par le caractère générique.

-   Quand vous utilisez le filtre d’attribut de fichier : modifiedDatetimeStart et modifiedDatetimeEnd pour sélectionner les fichiers à supprimer, veillez à définir "fileName": "*" dans le jeu de données.

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur le déplacement de fichiers dans Azure Data Factory.

-   [Outil Copier des données dans Azure Data Factory](copy-data-tool.md)