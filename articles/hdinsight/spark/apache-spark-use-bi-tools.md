---
title: 'Didacticiel : Analyser les données d’Apache Spark à l’aide de Power BI dans Azure HDInsight '
description: Didacticiel - Utiliser Microsoft Power BI pour visualiser les données Apache Spark stockées sur les clusters HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/16/2019
ms.openlocfilehash: d5296fe19cef9e8881d39bd9e59eb4c40d049959
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296187"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Didacticiel : Analyser les données d’Apache Spark à l’aide de Power BI dans HDInsight

Dans ce didacticiel, vous allez apprendre à utiliser [Microsoft Power BI](https://powerbi.microsoft.com/) pour visualiser des données sur un cluster Apache Spark dans [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Visualiser les données Spark à l’aide de Power BI

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Suivre l'article [Didacticiel : Charger des données et exécuter des requêtes sur un cluster Apache Spark dans Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Facultatif : [Abonnement d'essai à Power BI](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Vérifier les données

Le bloc-notes [Jupyter Notebook](https://jupyter.org/) que vous avez créé dans le [tutoriel précédent](apache-spark-load-data-run-query.md) comprend du code pour créer une table `hvac`. Cette table est basée sur le fichier CSV disponible sur tous les clusters HDInsight Spark à l'emplacement `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Utilisez la procédure suivante pour vérifier les données.

1. Dans le bloc-notes Jupyter, collez le code suivant, puis appuyez sur **MAJ + ENTRÉE**. Le code vérifie l’existence des tables.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Le résultat se présente ainsi :

    ![Afficher les tables dans Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Si vous avez fermé le bloc-notes avant de commencer ce didacticiel, `hvactemptable` est nettoyée et n’est donc pas incluse dans la sortie.  Seules les tables Hive qui sont stockées dans le metastore (colonne **isTemporary** définie sur **False**) sont accessibles à partir des outils décisionnels. Dans ce didacticiel, vous vous connectez à la table **hvac** que vous avez créée.

2. Collez le code suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE**. Le code vérifie les données de la table.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Le résultat se présente ainsi :

    ![Afficher les lignes de la table hvac dans Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. Dans le menu **Fichier** du Notebook, sélectionnez **Fermer et interrompre**. Arrêtez le bloc-notes pour libérer les ressources.

## <a name="visualize-the-data"></a>Visualiser les données

Dans cette section, vous utilisez Power BI pour créer des visualisations, des rapports et tableaux de bord à partir des données de cluster Spark.

### <a name="create-a-report-in-power-bi-desktop"></a>Création d’un rapport dans Power BI Desktop

Les premières étapes de l’utilisation de Spark consistent à se connecter au cluster dans Power BI Desktop, à charger des données à partir du cluster et à créer une visualisation de base reposant sur ces données.

> [!NOTE]  
> Le connecteur indiqué dans cet article est actuellement en préversion. Fournir vos commentaires via le site de la [Communauté Power BI](https://community.powerbi.com/) ou [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Ouvrez Power BI Desktop. Si l'écran de démarrage s'ouvre, fermez-le.

2. Sous l'onglet **Accueil**, accédez à **Obtenir les données** > **Autres...**

    ![Obtenir des données dans Power BI Desktop à partir d’HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Obtenir des données dans Power BI à partir d’Apache Spark BI")

3. Entrez `Spark` dans la zone de recherche, sélectionnez **Azure HDInsight Spark**, puis choisissez **Se connecter**.

    ![Obtenir des données dans Power BI à partir d’Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Obtenir des données dans Power BI à partir Apache Spark BI")

4. Entrez l'URL de votre cluster (sous la forme `mysparkcluster.azurehdinsight.net`) dans la zone de texte **Serveur**.

5. Sous **Mode de connectivité des données**, sélectionnez **DirectQuery**. Sélectionnez ensuite **OK**.

    Vous pouvez utiliser l’un ou l’autre des deux modes de connectivité des données avec Spark. Si vous utilisez DirectQuery, les modifications sont répercutées dans les rapports sans que la totalité du jeu de données soit actualisée. Si vous importez des données, vous devez actualiser le jeu de données pour voir les modifications. Pour plus d’informations sur la façon d’utiliser DirectQuery et sur quand y recourir, consultez [Utilisation de DirectQuery dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Entrez les informations du compte de connexion HDInsight, puis sélectionnez **Se connecter**. Le nom du compte par défaut est *administrateur*.

7. Sélectionnez la table `hvac`, attendez que l'aperçu des données s'affiche, puis sélectionnez **Charger**.

    ![Nom d’utilisateur et mot de passe du cluster Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nom d’utilisateur et mot de passe du cluster Spark")

    Power BI Desktop dispose de toutes les informations nécessaires pour se connecter au cluster Spark et charger des données à partir de la `hvac` table. La table et ses colonnes sont affichées dans le volet **Champ**.

8. Visualisez l’écart entre la température cible et la température réelle de chaque bâtiment :

    1. Dans le volet **VISUALISATIONS**, sélectionnez **Graphique en aires**.

    2. Faites glisser le champ **BuildingID** vers **Axe** et les champs **ActualTemp** et **TargetTemp** vers **Valeurs**.

        ![Créer des visualisations de données Spark à l’aide d’Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Créer des visualisations de données Spark à l’aide d’Apache Spark BI")

        Le diagramme a l’aspect suivant :

        ![Créer des visualisations de données Spark à l’aide d’Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Créer des visualisations de données Spark à l’aide d’Apache Spark BI")

        Par défaut, la visualisation affiche la somme des valeurs des champs **ActualTemp** et **TargetTemp**. Sélectionnez la flèche bas en regard d'**ActualTemp** et **TragetTemp** dans le volet Visualisations ; vous pouvez voir que **Somme** est sélectionné.

    3. Cliquez sur la flèche bas en regard d'**ActualTemp** et **TragetTemp** dans le volet Visualisations ; sélectionnez **Moyenne** pour obtenir une moyenne des températures réelle et cible de chaque bâtiment.

        ![Créer des visualisations de données Spark à l’aide d’Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Créer des visualisations de données Spark à l’aide d’Apache Spark BI")

        La visualisation de vos données sera semblable à celle illustrée dans la capture d’écran. Déplacez le curseur sur la visualisation pour obtenir des info-bulles contenant des données pertinentes.

        ![Créer des visualisations de données Spark à l’aide d’Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Créer des visualisations de données Spark à l’aide d’Apache Spark BI")

9. Accédez à **Fichier** > **Enregistrer**, entrez le nom `BuildingTemperature` du fichier, puis sélectionnez **Enregistrer**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publier le rapport sur le service Power BI (facultatif)

Le service Power BI vous permet de partager des rapports et tableaux de bord dans toute votre organisation. Dans cette section, vous publiez tout d’abord le jeu de données et le rapport. Ensuite, vous épinglez le rapport à un tableau de bord. Les tableaux de bord sont généralement utilisés pour se concentrer sur un sous-ensemble de données dans un rapport ; vous avez une seule visualisation dans votre rapport, mais elle est quand même utile pour exécuter la procédure.

1. Ouvrez Power BI Desktop.
2. À partir de l’ongle **Accueil**, cliquez sur **Publier**.

    ![Publier à partir de Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publier à partir de Power BI Desktop")

2. Sélectionnez l’espace de travail sur lequel vous souhaiter publier vos jeu de données et rapport, puis cliquez sur **Sélectionnez**. Dans l’image suivante, l’option par défaut **Mon espace de travail** est sélectionnée.

    ![Sélectionner l’espace de travail sur lequel publier le jeu de données et le rapport](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Sélectionner l’espace de travail sur lequel publier le jeu de données et le rapport") 

3. Une fois la publication réussie, cliquez sur **Ouvrir 'BuildingTemperature.pbix' dans Power BI**.

    ![Publication réussie, cliquer pour entrer les informations d’identification](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publication réussie, cliquer pour entrer les informations d’identification") 

4. Dans le service Power BI, cliquez sur **Entrer les informations d’identification**.

    ![Entrer les informations d’identification dans le service Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Entrer les informations d’identification dans le service Power BI")

5. Cliquez sur **Modifier les informations d’identification**.

    ![Modifier les informations d’identification dans le service Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Modifier les informations d’identification dans le service Power BI")

6. Entrez les informations du compte de connexion HDInsight, puis cliquez sur **Se connecter**. Le nom du compte par défaut est *administrateur*.

    ![Se connecter au cluster Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Se connecter au cluster Spark")

7. Dans le volet gauche, accédez à **Espace de travail** > **Mon espace de travail** > **RAPPORTS**, puis cliquez sur **BuildingTemperature**.

    ![Rapport répertorié sous Rapports dans le volet gauche](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Rapport répertorié sous Rapports dans le volet gauche")

    Vous devez également voir **BuildingTemperature** sous **JEUX DE DONNÉES** dans le volet gauche.

    Le visuel que vous avez créé dans Power BI Desktop est désormais disponible dans le service Power BI. 

8. Pointez votre curseur sur la visualisation, puis cliquez sur l’icône d’épingle dans le coin supérieur droit.

    ![Rapport dans le service Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Rapport dans le service Power BI")

9. Sélectionnez « Nouveau tableau de bord », entrez le nom `Building temperature`, puis cliquez sur **Épingler**.

    ![Épingler au nouveau tableau de bord](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Épingler au nouveau tableau de bord")

10. Dans le rapport, cliquez sur **Accéder au tableau de bord**. 

Votre visuel est épinglé au tableau de bord ; vous pouvez ajouter d’autres visuels au rapport et les épingler au même tableau de bord. Pour plus d’informations sur les rapports et les tableaux de bord, consultez [Rapports dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) et [Tableaux de bord dans le service Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Supprimer des ressources

Après avoir terminé ce didacticiel, vous souhaiterez peut-être supprimer le cluster. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés.

Pour supprimer un cluster, consultez [Supprimer un cluster HDInsight à l’aide de votre navigateur, de PowerShell ou d’Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avis appris à utiliser [Microsoft Power BI](https://powerbi.microsoft.com/) pour visualiser des données sur un cluster Apache Spark dans [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). Passez à l’article suivant pour découvrir comment les données que vous avez enregistrées dans Spark peuvent être placées dans un outil analytique décisionnel tel que Power BI.

> [!div class="nextstepaction"]
> [Exécuter une tâche de streaming Apache Spark](apache-spark-eventhub-streaming.md)