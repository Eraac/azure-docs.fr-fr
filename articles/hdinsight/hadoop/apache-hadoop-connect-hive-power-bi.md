---
title: Visualiser des données Apache Hive avec Power BI - Azure HDInsight
description: Découvrez comment utiliser Microsoft Power BI pour visualiser des données Hive traitées par Azure HDInsight.
keywords: hdinsight,hadoop,hive,interactive query,interactive hive,LLAP,odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: hrasheed
ms.openlocfilehash: 69353968f6b38f0d16b68c58b9b00c3e6d45850b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446869"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Visualiser des données Apache Hive à l’aide de Microsoft Power BI et ODBC dans Azure HDInsight

Découvrez comment connecter Microsoft Power BI Desktop à Azure HDInsight à l’aide d’ODBC et comment visualiser des données Apache Hive.

>[!IMPORTANT]
> Vous pouvez tirer parti du pilote ODBC Hive pour effectuer l’import par le biais du connecteur ODBC générique dans Power BI Desktop. Il n’est toutefois pas recommandé pour les charges de travail décisionnelles en raison de la nature non interactive du moteur d’interrogation Hive. Le [connecteur de requêtes interactives HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) et le [connecteur HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) sont des choix plus judicieux du fait de leurs performances.

Dans cet article, vous chargerez les données d’une table Hive `hivesampletable` dans Power BI. La table Hive contient des données sur l’utilisation des téléphones mobiles. Vous allez ensuite tracer ces données d’utilisation sur une carte du monde :

![rapport cartographique Power BI HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Les informations s’appliquent également au nouveau type de cluster [Interactive Query](../interactive-query/apache-interactive-query-get-started.md). Pour savoir comment se connecter à HDInsight Interactive Query à l’aide de Direct Query, consultez [Visualiser des données Interactive Query Hive avec Microsoft Power BI à l’aide de Direct Query dans Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Prérequis

Avant de poursuivre cet article, vérifiez que vous avez les éléments suivants :

* Un **cluster HDInsight**. Cela peut être un cluster HDInsight avec Hive ou un cluster du nouveau type Interactive Query. Pour plus d’informations sur la création de clusters, consultez [Créer un cluster](apache-hadoop-linux-tutorial-get-started.md#create-cluster).

* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . Vous pouvez télécharger une copie à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Création d’une source de données ODBC Hive

Consultez [Création d’une source de données ODBC Hive](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Chargement des données à partir de HDInsight

La table Hive hivesampletable est fournie avec tous les clusters HDInsight.

1. Démarrez Power BI Desktop.

2. À partir du menu du haut, accédez à **Accueil** > **Obtenir des données** > **Plus...** .

    ![données affichées Power BI HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

3. Dans la boîte de dialogue **Obtenir des données**, sélectionnez **Autre** à gauche, **ODBC** à droite, puis **Se connecter** en bas.

4. Dans la boîte de dialogue **À partir d’ODBC**, sélectionnez le nom de la source de données que vous avez créée dans la dernière section, puis sélectionnez **OK**.

5. Dans la boîte de dialogue **Navigateur**, développez **ODBC > HIVE > par défaut**, sélectionnez **hivesampletable**, puis sélectionnez **Charger**.

6. Dans la boîte de dialogue **Pilote ODBC**, sélectionnez **Par défaut ou personnalisé**, puis sélectionnez **Se connecter**.

## <a name="visualize-data"></a>Visualiser les données

Poursuivez la procédure précédente.

1. Dans le volet Visualisations, sélectionnez **Carte**  (icône représentant un globe).

    ![personnalisation de rapport Power BI HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. Dans le volet **Champs**, sélectionnez **country** et **devicemake**. Vous pouvez voir les données tracées sur la carte.
3. Développez la carte.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à visualiser des données de HDInsight à l’aide de Power BI.  Pour en savoir plus, consultez les articles suivants :

* [Utiliser Apache Zeppelin pour exécuter des requêtes Apache Hive dans Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Connecter Excel à HDInsight avec le pilote ODBC Microsoft Hive](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connecter Excel à Apache Hadoop à l’aide de Power Query](apache-hadoop-connect-excel-power-query.md).
* [Se connecter à Azure HDInsight et exécuter des requêtes Apache Hive avec Data Lake Tools pour Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Utiliser Azure HDInsight Tools pour Visual Studio Code](../hdinsight-for-vscode.md).
* [Charger des données dans HDInsight](./../hdinsight-upload-data.md).
