---
title: Copier des données d’Oracle Responsys avec Azure Data Factory (préversion) | Microsoft Docs
description: Découvrez comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour copier des données d’Oracle Responsys vers des magasins de données récepteurs pris en charge.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: 7440a08bd8ceb85cc569e1bb6d7c4ee1e52178a4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60405900"
---
# <a name="copy-data-from-oracle-responsys-using-azure-data-factory-preview"></a>Copier des données d’Oracle Responsys avec Azure Data Factory (préversion)

Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour copier des données d’Oracle Responsys. Il s’appuie sur l’article [Vue d’ensemble de l’activité de copie](copy-activity-overview.md).

> [!IMPORTANT]
> Ce connecteur est actuellement en préversion. Essayez-le et envoyez-nous vos commentaires. Si vous souhaitez établir une dépendance sur les connecteurs en préversion dans votre solution, veuillez contacter le [support Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

Il est possible de copier des données d’Oracle Responsys vers n’importe quel magasin de données récepteur pris en charge. Pour obtenir la liste des banques de données prises en charge en tant que sources ou récepteurs par l’activité de copie, consultez le tableau [Banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fournit un pilote intégré qui permet la connexion. Vous n’avez donc pas besoin d’installer manuellement un pilote à l’aide de ce connecteur.

## <a name="getting-started"></a>Prise en main

Vous pouvez créer un pipeline avec l’activité de copie à l’aide du SDK .NET, du SDK Python, d’Azure PowerShell, de l’API REST ou du modèle Azure Resource Manager. Pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie, consultez le [didacticiel sur l’activité de copie](quickstart-create-data-factory-dot-net.md).

Les sections suivantes fournissent des informations sur les propriétés utilisées pour définir des entités Data Factory propres au connecteur Oracle Responsys.

## <a name="linked-service-properties"></a>Propriétés du service lié

Les propriétés prises en charge pour le service lié Oracle Responsys sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| type | La propriété type doit être définie sur : **Responsys** | OUI |
| endpoint | Point de terminaison du serveur Responsys.  | OUI |
| clientId | ID client associé à l’application Responsys.  | OUI |
| clientSecret | Clé secrète client associée à l’application Responsys. Vous pouvez choisir de marquer ce champ comme SecureString pour le stocker en toute sécurité dans le fichier de définition d'application, ou stocker le mot de passe dans Azure Key Vault et laisser l'activité de copie ADF en tirer (pull) les données lors de la copie. Pour plus d'informations, consultez la page [Stocker des informations d'identification dans Key Vault](store-credentials-in-key-vault.md). | OUI |
| useEncryptedEndpoints | Indique si les points de terminaison de la source de données sont chiffrés suivant le protocole HTTPS. La valeur par défaut est true.  | Non |
| useHostVerification | Indique si le nom d’hôte du certificat du serveur doit correspondre à celui du serveur en cas de connexion SSL. La valeur par défaut est true.  | Non |
| usePeerVerification | Indique s’il faut vérifier l’identité du serveur en cas de connexion SSL. La valeur par défaut est true.  | Non |

**Exemple :**

```json
{
    "name": "OracleResponsysLinkedService",
    "properties": {
        "type": "Responsys",
        "typeProperties": {
            "endpoint" : "<endpoint>",
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}

```

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir la liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article sur les [jeux de données](concepts-datasets-linked-services.md). Cette section donne la liste des propriétés prises en charge par le jeu de données Oracle Responsys.

Pour copier des données à partir d’Oracle Responsys, affectez la valeur **ResponsysObject** à la propriété type du jeu de données. Les propriétés prises en charge sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du jeu de données doit être définie sur : **ResponsysObject** | OUI |
| tableName | Nom de la table. | Non (si « query » dans la source de l’activité est spécifié) |

**Exemple**

```json
{
    "name": "OracleResponsysDataset",
    "properties": {
        "type": "ResponsysObject",
        "linkedServiceName": {
            "referenceName": "<Oracle Responsys linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}

```

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Pipelines](concepts-pipelines-activities.md). Cette section donne la liste des propriétés prises en charge par la source Oracle Responsys.

### <a name="oracle-responsys-as-source"></a>Oracle Responsys en tant que source

Pour copier des données à partir d’Oracle Responsys, affectez la valeur **ResponsysSource** au type de source dans l’activité de copie. Les propriétés prises en charge dans la section **source** de l’activité de copie sont les suivantes :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type de la source d’activité de copie doit être définie sur : **ResponsysSource** | OUI |
| query | Utiliser la requête SQL personnalisée pour lire les données. Par exemple : `"SELECT * FROM MyTable"`. | Non (si « tableName » est spécifié dans dataset) |

**Exemple :**

```json
"activities":[
    {
        "name": "CopyFromOracleResponsys",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle Responsys input dataset name>",
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
                "type": "ResponsysSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
