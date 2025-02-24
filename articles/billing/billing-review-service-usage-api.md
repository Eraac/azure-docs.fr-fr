---
title: Passer en revue l’utilisation des ressources des services Azure avec l’API REST | Microsoft Docs
description: Découvrez comment utiliser les API REST Azure pour passer en revue l’utilisation des ressources des services Azure.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: banders
ms.openlocfilehash: 47e19fae26d6e3bc465799980c587d7bb7ed5e92
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443060"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Passer en revue l’utilisation des ressources Azure avec l’API REST

Les API Azure Cost Management vous permettent de consulter et de gérer la consommation de vos ressources Azure.

Dans cet article, vous allez apprendre à créer un rapport quotidien qui génère un document de valeurs séparées par des virgules avec vos informations d’utilisation horaire, puis à utiliser des filtres pour personnaliser le rapport afin de pouvoir interroger l’utilisation des machines virtuelles, des bases de données et des ressources avec mot clé dans un groupe de ressources Azure.

>[!NOTE]
> L’API Azure Cost Management est en préversion privée.

## <a name="create-a-basic-cost-management-report"></a>Créer un rapport de gestion des coûts de base

Utilisez l’opération `reports` dans l’API Cost Management pour définir la façon dont les rapports de coûts sont générés et où ils doivent être publiés.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

Le paramètre `{subscriptionGuid}` est obligatoire et doit contenir un ID d'abonnement qui peut être lu à l'aide des informations d'identification fournies dans le jeton d'API. Le `{reportName}`

Les en-têtes suivants sont requis : 

|En-tête de requête|Description|  
|--------------------|-----------------|  
|*Content-Type :*| Requis. Défini sur `application/json`. |  
|*Authorization :*| Requis. Défini sur un jeton `Bearer` valide. |

Configurez les paramètres du rapport dans le corps de la requête HTTP. Dans l’exemple ci-dessous, le rapport est défini pour être généré tous les jours quand il est actif, est un fichier CSV écrit dans un conteneur d’objets blob Stockage Azure et contient des informations sur les coûts horaires pour toutes les ressources dans le groupe de ressources `westus`.

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

L’attribut

## <a name="filtering-reports"></a>Filtrage des rapports

Quand vous créez un rapport, les sections `filter` et `dimensions` du corps de la demande vous permettent de vous concentrer sur les coûts de types de ressource spécifiques. Le corps de la demande précédent montre comment filtrer sur toutes les ressources d’une région. 

### <a name="get-all-compute-usage"></a>Obtenir toutes les utilisations de calcul

Utilisez la dimension `ResourceType` pour créer un rapport sur les coûts des machines virtuelles Azure de votre abonnement dans toutes les régions.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines", 
                "Microsoft.Compute/virtualMachines"
        ] 
    }
}
```

### <a name="get-all-database-usage"></a>Obtenir toutes les utilisations de la base de données

Utilisez la dimension `ResourceType` pour créer un rapport sur les coûts Azure SQL Database de votre abonnement dans toutes les régions.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ] 
    }
}
```

### <a name="report-on-specific-instances"></a>Créer des rapports sur des instances spécifiques

La dimension `Resource` vous permet de créer un rapport sur les coûts de ressources spécifiques.

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>Modification des plages de temps

Attribuez à la définition `timeframe` la valeur `Custom` pour définir une plage de temps en dehors des options intégrées de la semaine en cours et du mois en cours.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Étapes suivantes
- [Bien démarrer avec l’API REST Azure](https://docs.microsoft.com/rest/api/azure/)   
