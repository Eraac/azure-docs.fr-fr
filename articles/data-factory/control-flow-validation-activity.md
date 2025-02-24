---
title: Activité Validation dans Azure Data Factory | Microsoft Docs
description: L’activité Validation interrompt l’exécution du pipeline jusqu’à ce qu’elle valide le jeu de données attaché avec certains critères spécifiés par l’utilisateur.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 46447bdbea93d1f99c5682cf878c2035e6f49b78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60764320"
---
# <a name="validation-activity-in-azure-data-factory"></a>Activité Validation dans Azure Data Factory
Vous pouvez utiliser une Validation dans un pipeline pour garantir que l’exécution du pipeline se poursuit uniquement une fois qu’elle a confirmé que la référence du jeu de données attaché existe, qu’elle remplit les critères spécifiés ou que le délai d’attente a été atteint.


## <a name="syntax"></a>Syntaxe

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Propriétés type

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
Nom | Nom de l’activité « Validation » | Chaîne | OUI |
Type | Doit être défini sur **Validation**. | Chaîne | OUI |
dataset | L’activité bloquera l’exécution jusqu’à ce qu’elle ait confirmé que cette référence de jeu de données existe et qu’elle remplit les critères spécifiés, ou que le délai d’attente ait été atteint. Le jeu de données fourni doit prendre en charge la propriété « MinimumSize » ou « ChildItems ». | Référence de jeu de données | OUI |
timeout | Spécifie le délai d’expiration d’exécution de l’activité. Si aucune valeur n’est spécifiée, la valeur par défaut est sept jours (« 7:00:00:00 »). Le format est j.hh:mm:ss | Chaîne | Non |
sleep | Délai en secondes entre les tentatives de validation. Si aucune valeur n’est spécifiée, la valeur par défaut est 10 secondes. | Entier | Non |
childItems | Vérifie si le dossier a des éléments enfants. Peut être défini sur -true : vérifie que le dossier existe et qu’il a des éléments. Bloque jusqu’à ce qu’au moins un élément se trouve dans le dossier ou que la valeur de délai d’attente soit atteinte. -false : vérifie que le dossier existe et qu’il est vide. Bloque jusqu’à ce que le dossier soit vide ou que la valeur de délai d’attente soit atteinte. Si aucune valeur n’est spécifiée, l’activité bloquera jusqu’à ce que le dossier existe ou que le délai d’attente soit atteint. | Boolean | Non |
minimumSize | Taille minimale d’un fichier en octets. Si aucune valeur n’est spécifiée, la valeur par défaut est de 0 octet | Entier | Non |


## <a name="next-steps"></a>Étapes suivantes
Consultez les autres activités de flux de contrôle prises en charge par Data Factory :

- [Activité IfCondition](control-flow-if-condition-activity.md)
- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
- [Activité jusqu’à](control-flow-until-activity.md)
