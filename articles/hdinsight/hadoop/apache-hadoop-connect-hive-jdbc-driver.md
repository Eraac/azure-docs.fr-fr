---
title: Requête Apache Hive par le biais du pilote JDBC - Azure HDInsight
description: Utilisez le pilote JDBC depuis une application Java pour soumettre des requêtes Apache Hive à Hadoop sur HDInsight. Se connecter à partir du client SQL SQuirrel et par programme.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: hrasheed
ms.openlocfilehash: 56a2b89277cbf8866c1992a6738bd80106ef3313
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480012"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Requête Apache Hive par le biais du pilote JDBC dans HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Découvrez comment utiliser le pilote JDBC dans une application Java pour soumettre des requêtes Apache Hive à Apache Hadoop dans Azure HDInsight. Les informations contenues dans ce document montrent comment se connecter par programme à partir du client SQuirreL SQL.

Pour plus d’informations sur l’interface JDBC pour Hive, consultez [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Prérequis

* Un cluster HDInsight Hadoop. Pour en créer un, consultez [Prise en main d’Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Le [Kit de développeur Java (JDK) version 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) ou supérieure.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL est une application cliente JDBC.

## <a name="jdbc-connection-string"></a>Chaîne de connexion JDBC

Les connexions JDBC à un cluster HDInsight sur Azure sont établies sur le port 443, et le trafic est sécurisé à l’aide de SSL. La passerelle publique derrière laquelle se trouvent les clusters redirige le trafic vers le port d’écoute réel d’HiveServer2. La chaîne de connexion suivante montre le format à utiliser pour HDInsight :

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Remplacez `CLUSTERNAME` par le nom de votre cluster HDInsight :

## <a name="authentication"></a>Authentication

Lors de l’établissement de la connexion, vous devez utiliser les nom et mot de passe d’administrateur du cluster HDInsight pour vous authentifier auprès de la passerelle du cluster. Lors d’une connexion à partir de clients JDBC, tels que SQuirreL SQL, vous devez entrer les nom et mot de passe d’administrateur dans les paramètres du client.

À partir d’une application Java, vous devez utiliser les nom et mot de passe lors de l’établissement d’une connexion. Par exemple, le code Java suivant ouvre une nouvelle connexion à l’aide de la chaîne de connexion, du nom d’administrateur et du mot de passe :

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Connexion avec un client SQuirreL SQL

SQuirreL SQL est un client JDBC permettant d’exécuter à distance des requêtes Hive avec votre cluster HDInsight. Les étapes suivantes supposent que vous avez déjà installé SQuirreL SQL.

1. Créez un répertoire contenant certains fichiers à copier à partir de votre cluster.

2. Dans le script suivant, remplacez `sshuser` par le nom de compte d’utilisateur SSH pour le cluster.  Remplacez `CLUSTERNAME` par le nom du cluster HDInsight.  À partir d’une ligne de commande, passez de votre répertoire de travail à celui créé à l’étape précédente, puis entrez la commande suivante pour copier des fichiers depuis un cluster HDInsight :

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-1.2*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Démarrez l’application SQuirreL SQL. Dans la partie gauche de la fenêtre, sélectionnez **Pilotes**.

    ![Onglet Pilotes dans la partie gauche de la fenêtre](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. Parmi les icônes en haut de la boîte de dialogue **Pilotes**, sélectionnez l’icône **+** pour créer un pilote.

    ![Icônes des pilotes](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. Dans la boîte de dialogue Ajouter un pilote, ajoutez les informations suivantes :

    * **Nom** : Hive
    * **Exemple d’URL** : `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Chemin de la classe supplémentaire** : Utilisez le bouton **Ajouter** pour ajouter tous les fichiers jar téléchargés précédemment
    * **Nom de la classe**: org.apache.hive.jdbc.HiveDriver

   ![boîte de dialogue ajouter un pilote](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   Sélectionnez **OK** pour enregistrer ces paramètres.

6. Dans la partie gauche de la fenêtre SQL SQuirreL, sélectionnez **Alias**. Sélectionnez ensuite l’icône **+** pour créer un alias de connexion.

    ![ajouter un nouvel alias](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. Dans la boîte de dialogue **Ajouter un Alias** , utilisez les valeurs suivantes.

    * **Nom** : Hive sur HDInsight

    * **Pilote** : Sélectionnez le pilote **Hive** dans la liste déroulante

    * **URL**  : `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.

    * **Nom d’utilisateur** : Nom de compte de connexion de votre cluster HDInsight. Par défaut, il s’agit de `admin`.

    * **Mot de passe** : Mot de passe du compte de connexion du cluster.

   ![boîte de dialogue ajouter un alias](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > Utilisez le bouton **Test** pour vérifier que la connexion fonctionne. Quand la boîte de dialogue **Se connecter à : Hive sur HDInsight** s’affiche, sélectionnez **Connexion** pour effectuer le test. Si le test réussit, la boîte de dialogue **Connexion réussie** s’affiche. Si une erreur se produit, consultez [Résolution de problèmes](#troubleshooting).

    Pour enregistrer l’alias de connexion, utilisez le bouton **OK** au bas de la boîte de dialogue **Ajouter un alias**.

8. Dans la liste déroulante **Se connecter à** en haut de SQuirreL SQL, sélectionnez **Hive sur HDInsight**. Lorsque vous y êtes invité, sélectionnez **Connexion**.

    ![boîte de dialogue connexion](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. Une fois connecté, entrez la requête suivante dans la boîte de dialogue Requête SQL, puis sélectionnez l’icône **Exécuter** (représentant une personne qui court). La zone de résultats doit afficher les résultats de la requête.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![boîte de dialogue requête sql, incluant les résultats](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Connexion à partir d’un exemple d’application Java

Un exemple d’utilisation d’un client Java pour interroger Hive sur HDInsight est disponible sur [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Suivez les instructions indiquées dans le référentiel pour générer et exécuter l’exemple.

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Une erreur inattendue s'est produite lors de l'ouverture d'une connexion SQL

**Symptômes** : Quand vous vous connectez à un cluster HDInsight version 3.3 ou supérieure, vous pouvez recevoir un message indiquant qu’une erreur inattendue s’est produite. L’arborescence des appels de procédure pour cette erreur commence par les lignes suivantes :

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Cause** : Cette erreur est due à une version plus ancienne du fichier commons-codec.jar incluse avec SQuirreL.

**Résolution** : Pour corriger cette erreur, utilisez les étapes suivantes :

1. Quittez SQuirreL et accédez au répertoire où SQuirreL est installé sur votre système. Dans le répertoire SquirreL, sous le dossier `lib` , remplacez le fichier commons-codec.jar existant par le fichier téléchargé à partir du cluster HDInsight.

2. Redémarrez SQuirreL. L'erreur ne devrait plus apparaître lors de la connexion à Hive sur HDInsight.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez vu comment utiliser JDBC avec Hive, utilisez les liens suivants pour découvrir d’autres façons d’utiliser Azure HDInsight.

* [Visualiser des données Apache Hive à l’aide de Microsoft Power BI dans Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualiser des données Interactive Query Hive à l’aide de Power BI dans Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Utiliser Apache Zeppelin pour exécuter des requêtes Apache Hive dans Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Connecter Excel à HDInsight avec le pilote ODBC Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connecter Excel à Apache Hadoop à l’aide de Power Query](apache-hadoop-connect-excel-power-query.md).
* [Se connecter à Azure HDInsight et exécuter des requêtes Apache Hive avec Data Lake Tools pour Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Utiliser Azure HDInsight Tools pour Visual Studio Code](../hdinsight-for-vscode.md).
* [Téléchargement de données vers HDInsight](../hdinsight-upload-data.md)
* [Utilisation d’Apache Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation d’Apache Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation des tâches MapReduce avec HDInsight](hdinsight-use-mapreduce.md)
