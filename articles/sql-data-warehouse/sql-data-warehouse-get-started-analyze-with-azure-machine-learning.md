---
title: Analyse de données avec Azure Machine Learning | Microsoft Docs
description: Utilisez Azure Machine Learning pour générer un modèle Machine Learning prédictif basé sur les données stockées dans Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: cae2acf98f39030f4ff340d32f1911bb2b5763ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65860842"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Analyse des données avec Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Ce didacticiel utilise Azure Machine Learning pour générer un modèle Machine Learning prédictif basé sur les données stockées dans Azure SQL Data Warehouse. Plus précisément, il crée une campagne marketing ciblée pour Adventure Works, le magasin de vélos, en prévoyant si un client est susceptible d’acheter ou non un vélo.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce didacticiel, vous avez besoin des éléments suivants :

* un entrepôt SQL Data Warehouse préchargé avec les exemples de données AdventureWorksDW. Pour le configurer, consultez [Créer un Azure SQL Data Warehouse][Create a SQL Data Warehouse] et chargez les données d’exemple. Si vous disposez déjà d’un entrepôt de données, mais sans disposer d’exemples de données, vous pouvez [charger manuellement des exemples de données][load sample data manually].

## <a name="1-get-the-data"></a>1. Obtenir les données
Les données sont indiquées dans la vue dbo.vTargetMail de la base de données AdventureWorksDW. Pour lire ces données :

1. Connectez-vous à [Azure Machine Learning Studio][Azure Machine Learning studio], puis cliquez sur Mes expériences.
2. Cliquez sur **+NOUVEAU** dans le coin inférieur gauche de l’écran et sélectionnez **Expérience vide**.
3. Entrez un nom pour votre expérience : marketing ciblé.
4. Faites glisser le module **Importer des données** sous **Entrée et sortie de données** dans le volet de modules du canevas.
5. Spécifiez les détails de votre base de données SQL Data Warehouse dans le volet Propriétés.
6. Spécifiez la **requête** de base de données pour lire les données intéressantes.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Démarrez l’expérience en cliquant sur l’option **Démarrer** sous la zone de dessin de l’expérience.
![Exécuter l’expérience][1]

Une fois que l’expérience s’est terminée avec succès, cliquez sur le port de sortie au bas du module Reader et sélectionnez **Visualiser** pour voir les données importées.
![Afficher les données importées][3]

## <a name="2-clean-the-data"></a>2. Nettoyer les données
Pour nettoyer les données, supprimez certaines colonnes qui sont inutiles pour le modèle. Pour ce faire :

1. Faites glisser le module **Sélectionner des colonnes dans le jeu de données** sous **Transformation des données < Manipulation** dans le canevas. Connectez ce module au module **Importer des données**.
2. Cliquez sur **Lancer le sélecteur de colonne** dans le volet Propriétés pour spécifier les colonnes que vous souhaitez supprimer.
   ![Colonnes de projet][4]
3. Excluez deux colonnes : CustomerAlternateKey et GeographyKey.
   ![Supprimer les colonnes inutiles][5]

## <a name="3-build-the-model"></a>3. Générer le modèle
Nous allons fractionner les données dans la proportion 80 et 20 : 80 % pour l’apprentissage d’un modèle Machine Learning et 20 % pour tester le modèle. Nous nous engageons à utiliser des algorithmes « À deux classes » pour ce problème de classification binaire.

1. Faites glisser le module **Fractionner** dans la zone de dessin.
2. Sur le volet Propriétés, entrez 0,8 comme Fraction de lignes dans le premier jeu de données.
   ![Fractionner les données en jeu d’apprentissage et de test][6]
3. Faites glisser le module **Arbre de décision optimisé à deux classes** dans la zone de dessin.
4. Faites glisser le module **Entraîner le modèle** dans le canevas et spécifiez des entrées en le connectant aux modules **Arbre de décision optimisé à deux classes** (algorithme ML) et **Fractionnement** (données sur lesquelles entraîner l’algorithme). 
     ![Connecter le module Former le modèle][7]
5. Cliquez sur l’option **Lancer le sélecteur de colonne** figurant dans le volet Propriétés. Sélectionnez la colonne **BikeBuyer** comme colonne à prédire.
   ![Sélectionner la colonne à prédire][8]

## <a name="4-score-the-model"></a>4. Notation du modèle
Maintenant, nous allons voir comment le modèle s’exécute sur les données de test. Nous allons comparer l’algorithme de notre choix avec un autre algorithme et voir celui qui fonctionne le mieux.

1. Faites glisser le module **Noter un modèle** dans le canevas et connectez-le aux modules **Entraîner le modèle** et **Fractionner les données**.
   ![Noter le modèle][9]
2. Faites glisser **Machines de points Bayes à deux classes** dans la zone de dessin de l’expérience. Nous allons comparer comment cet algorithme fonctionne par rapport à l’arbre de décision optimisé à deux classes.
3. Copiez et collez les modules de Former le modèle et le modèle Noter le modèle dans la zone de dessin.
4. Faites glisser le module **Évaluer le modèle** module dans la zone de dessin pour comparer les deux algorithmes.
5. **Exécutez** l’expérience.
   ![Exécuter l’expérience][10]
6. Cliquez sur le port de sortie situé au bas du module Évaluer le modèle, puis sélectionnez Visualiser.
   ![Visualiser les résultats de l’évaluation][11]

Les mesures fournies sont la courbe ROC (caractéristiques du fonctionnement du récepteur), le diagramme de rappel de précision et la courbe d’élévation. En examinant ces mesures, nous pouvons voir que le premier modèle fonctionne mieux que le second. Pour regarder les prévisions du premier modèle, cliquez sur le port de sortie du modèle de notation, puis cliquez sur Visualiser.
![Visualiser les résultats de la notation][12]

Vous verrez deux colonnes supplémentaires ajoutées à votre groupe de données de test.

* Probabilités évaluées : probabilité qu’un client soit un acheteur potentiel de vélo.
* Étiquette de marquage : classification effectuée par le modèle – acheteur de vélo (1) ou non (0). Ce seuil de probabilité pour l’étiquetage est défini à 50 % et peut être ajusté.

En comparant la colonne BikeBuyer (réelle) avec les étiquettes de marquage (prévision), vous pouvez voir comment le modèle a fonctionné. Au cours des opérations suivantes, vous pouvez utiliser ce modèle pour élaborer des prévisions pour les nouveaux clients et publier ce modèle en tant que service web ou écrire les résultats dans SQL Data Warehouse.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la création de modèles Machine Learning prédictifs, reportez-vous à [Introduction à Machine Learning sur Azure][Introduction to Machine Learning on Azure].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2-visualize-new.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduction to Machine Learning on Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
