---
title: Paramétrer les services liés dans Azure Data Factory | Microsoft Docs
description: Découvrez comment paramétrer des services liés dans Azure Data Factory et transmettre des valeurs dynamiques au moment de l’exécution.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/18/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 0239c53f98fba201b6d70e1e2212eea36134e30d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60635526"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Paramétrer les services liés dans Azure Data Factory

Vous pouvez désormais paramétrer un service lié et transmettre des valeurs dynamiques au moment de l’exécution. Par exemple, si vous souhaitez vous connecter à différentes bases de données sur le même serveur Azure SQL Database, vous pouvez désormais paramétrer le nom de la base de données dans la définition du service lié. Cela vous évite d’avoir à créer un service lié pour chaque base de données sur le serveur Azure SQL Database. Vous pouvez également paramétrer les autres propriétés de la définition du service lié : par exemple, *Nom d’utilisateur.*

Vous pouvez utiliser l’interface utilisateur de Data Factory dans le portail Azure ou une interface de programmation pour paramétrer les services liés.

> [!TIP]
> Nous vous recommandons de ne pas paramétrer les mots de passe ou les secrets. Au lieu de cela, conservez toutes les chaînes de connexion dans Azure Key Vault et paramétrez le *Nom du secret*.

Pour voir une présentation de sept minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Magasins de données pris en charge

À ce stade, le paramétrage de service lié est pris en charge dans l’interface utilisateur de Data Factory dans le portail Azure pour les magasins de données suivants. Pour tous les autres magasins de données, vous pouvez paramétrer le service lié en sélectionnant l'icône **Code** dans l'onglet **Connexions** et à l'aide de l'éditeur JSON.
- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database pour MySQL

## <a name="data-factory-ui"></a>IU de la fabrique de données

![Ajouter du contenu dynamique à la définition du service lié](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Créer un paramètre](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "value": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
                "type": "SecureString"
            }
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
