---
title: Copier en bloc à partir d’une base de données à l’aide d’une table de contrôle avec Azure Data Factory | Microsoft Docs
description: Découvrez comment utiliser un modèle de solution pour copier des données en bloc à partir d’une base de données à l’aide d’une table de contrôle externe pour stocker une liste de partitions de tables sources à l’aide d’Azure Data Factory.
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
ms.date: 12/14/2018
ms.openlocfilehash: c4224693642e8c9f76deedc0c8ad8586e122cc23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60635386"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Copier en bloc à partir d’une base de données avec une table de contrôle

Pour copier des données à partir d’un entrepôt de données dans Oracle Server, Netezza, Teradata ou SQL Server vers Azure SQL Data Warehouse, vous devez charger de grandes quantités de données à partir de plusieurs tables. Généralement, les données doivent être partitionnées dans chaque table pour que vous puissiez charger des lignes avec plusieurs threads en parallèle à partir d’une seule table. Cet article décrit un modèle à utiliser dans ces scénarios.

 >REMARQUE : Si vous souhaitez copier des données à partir d’un petit nombre de tables avec un volume de données relativement faible vers SQL Data Warehouse, il est plus efficace d’utiliser l’[outil Copier des données dans Azure Data Factory](copy-data-tool.md). Le modèle décrit dans cet article suffit amplement pour ce scénario.

## <a name="about-this-solution-template"></a>À propos de ce modèle de solution

Ce modèle récupère une liste de partitions de base de données source à copier à partir d’une table de contrôle externe. Ensuite, il effectue une itération au sein de chaque partition dans la base de données source et copie les données vers la destination.

Le modèle comporte trois activités :
- **Lookup** récupère la liste des partitions de base de données à partir d’une table de contrôle externe.
- **ForEach** obtient la liste de partitions à partir de l’activité Lookup et itère au sein de chaque partition vers l’activité Copy.
- **Copy** copie chaque partition du magasin de base de données source vers le magasin de destination.

Le modèle définit cinq paramètres :
- *Control_Table_Name* est votre table de contrôle externe, qui stocke la liste de partitions pour la base de données source.
- *Control_Table_Schema_PartitionID* est le nom de la colonne dans votre table de contrôle externe qui stocke chaque ID de partition. Veillez à ce que l’ID de partition soit unique pour chaque partition dans la base de données source.
- *Control_Table_Schema_SourceTableName* est votre table de contrôle externe qui stocke chaque nom de table de la base de données source.
- *Control_Table_Schema_FilterQuery* est le nom de la colonne dans votre table de contrôle externe qui stocke la requête de filtre servant à obtenir les données de chaque partition dans la base de données source. Par exemple, si vous avez partitionné les données par année, la requête stockée dans chaque ligne peut ressembler à ceci : 'select * from datasource where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''.
- *Data_Destination_Folder_Path* est le chemin où les données sont copiées dans votre magasin de destination. Ce paramètre est visible uniquement si la destination que vous choisissez est un stockage basé sur des fichiers. Si vous choisissez SQL Data Warehouse comme magasin de destination, ce paramètre n’est pas obligatoire. Cependant, les noms des tables et le schéma dans SQL Data Warehouse doivent être identiques à ceux de la base de données source.

## <a name="how-to-use-this-solution-template"></a>Utiliser ce modèle de solution

1. Créez une table de contrôle dans SQL Server ou Azure SQL Database afin de stocker la liste des partitions de la base de données source pour la copie en bloc. Dans l’exemple suivant, il existe cinq partitions dans la base de données source. Trois partitions concernent *datasource_table*, et deux autres concernent *project_table*. La colonne *LastModifytime* permet de partitionner les données de la table *datasource_table* de la base de données source. La requête utilisée pour lire la première partition est 'select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''. Vous pouvez également utiliser une requête similaire pour lire les données d’autres partitions.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Accédez au modèle **Copier en bloc à partir d’une base de données**. Créez une **nouvelle** connexion à la table de contrôle externe que vous avez créée à l’étape 1.

    ![Créer une connexion à la table de contrôle](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Créez une **nouvelle** connexion à la base de données source à partir de laquelle vous copiez des données.

     ![Créer une connexion à la base de données source](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Créez une **nouvelle** connexion au magasin de données de destination vers lequel vous copiez des données.

    ![Créer une connexion au magasin de destination](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Sélectionnez **Utiliser ce modèle**.

    ![Utiliser ce modèle](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Vous voyez le pipeline, comme indiqué dans l’exemple suivant :

    ![Passer en revue le pipeline](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Sélectionnez **Déboguer**, entrez les **Paramètres**, puis sélectionnez **Terminer**.

    ![Cliquez sur **Déboguer**](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Les résultats ressemblent à l’exemple qui suit :

    ![Vérifier le résultat](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Facultatif) Si vous avez choisi SQL Data Warehouse comme destination des données, vous devez entrer une connexion à un stockage d’objets blob Azure comme emplacement intermédiaire, conformément aux exigences de SQL Data Warehouse Polybase. Vérifiez que le conteneur a déjà été créé dans le stockage d’objets blob.
    
    ![Paramètre Polybase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Étapes suivantes

- [Présentation du service Azure Data Factory](introduction.md)
