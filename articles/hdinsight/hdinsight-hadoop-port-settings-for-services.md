---
title: Ports utilisés par les services Hadoop sur HDInsight - Azure
description: Liste des ports utilisés par les services Hadoop sur HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: hrasheed
ms.openlocfilehash: 77e7aec1797a4b33068430371ba0969d1737746e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508799"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Ports utilisés par les services Apache Hadoop sur HDInsight

Ce document fournit une liste des ports utilisés par les services Apache Hadoop exécutés sur des clusters HDInsight sous Linux. Il fournit également des informations sur les ports utilisés pour se connecter au cluster à l’aide de SSH.

## <a name="public-ports-vs-non-public-ports"></a>Ports publics et ports non publics

Les clusters HDInsight sous Linux exposent uniquement trois ports publiquement sur Internet, à savoir les ports 22, 23 et 443. Ces ports sont utilisés pour accéder en toute sécurité au cluster à l’aide de SSH et des services exposés sur le protocole HTTPS sécurisé.

En interne, HDInsight est implémenté par plusieurs machines virtuelles Azure (les nœuds dans le cluster) exécutées sur un réseau virtuel Azure. Depuis le réseau virtuel, vous pouvez accéder aux ports non exposés sur Internet. Par exemple, si vous vous connectez à l’un des nœuds principaux à l’aide de SSH, à partir du nœud principal, vous pouvez ensuite accéder directement aux services s’exécutant sur les nœuds du cluster.

> [!IMPORTANT]  
> Si vous ne spécifiez pas de réseau virtuel Azure comme une option de configuration pour HDInsight, un réseau virtuel Azure sera créé automatiquement. Toutefois, vous ne pouvez pas associer d’autres ordinateurs (comme les autres machines virtuelles Azure ou votre ordinateur de développement client) à ce réseau virtuel.

Pour joindre des ordinateurs supplémentaires au réseau virtuel, vous devez d’abord créer le réseau virtuel, puis le spécifier lors de la création de votre cluster HDInsight. Pour plus d’informations, consultez [Étendre les capacités de HDInsight en utilisant un réseau virtuel Azure](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Ports publics

Tous les nœuds dans un cluster HDInsight se trouvent dans un réseau virtuel Azure et ne sont pas directement accessibles depuis Internet. Une passerelle publique fournit l’accès à Internet pour les ports suivants, qui sont communs à tous les types de cluster HDInsight.

| de diffusion en continu | Port | Protocole | Description |
| --- | --- | --- | --- |
| sshd |22 |SSH |Connecte les clients à sshd sur le nœud principal primaire. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |SSH |Connecte les clients à sshd sur le nœud de périmètre. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |SSH |Connecte les clients à sshd sur le nœud principal secondaire. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Interface utilisateur web d’Ambari. Consultez [Gérer des clusters HDInsight à l’aide de l’interface utilisateur web Apache Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |API Ambari REST. Consultez [Gérer des clusters HDInsight à l’aide de l’interface utilisateur Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat, |443 |HTTPS |API REST HCatalog. Voir [Utiliser MapReduce avec Curl](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Se connecte à Hive à l’aide de ODBC. Consultez la page [Connexion d’Excel à HDInsight à l’aide du pilote ODBC Microsoft](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Se connecte à Apache Hive à l’aide de JDBC. Consultez la page [Se connecter à Apache Hive sur HDInsight à l’aide du pilote JDBC Hive](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Les éléments suivants sont disponibles pour les types de clusters spécifiques :

| de diffusion en continu | Port | Protocole | Type de cluster | Description |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |hbase |API REST HBase. Voir [Bien démarrer avec Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |API REST Spark. Voir [Envoi de tâches Apache Spark à distance avec Apache Livy](spark/apache-spark-livy-rest-interface.md) |
| Serveur Spark Thrift |443 |HTTPS |Spark |Le serveur Spark Thrift utilisé pour envoyer des requêtes Hive. Voir [Utiliser Beeline avec Apache Hive sur HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Interface utilisateur web de Storm. Voir [Déploiement et gestion des topologies Apache Storm sur HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |

### <a name="authentication"></a>Authentication

Tous les services exposés publiquement sur Internet doivent être authentifiés :

| Port | Informations d'identification |
| --- | --- |
| 22 ou 23 |Les informations d’identification utilisateur SSH spécifiées lors de la création du cluster |
| 443 |Le nom de connexion (par défaut : admin) et le mot de passe qui ont été définis lors de la création du cluster |

## <a name="non-public-ports"></a>Ports non publics

> [!NOTE]  
> Certains services sont disponibles uniquement sur certains types de clusters. Par exemple, HBase est disponible uniquement sur les clusters de type HBase.

> [!IMPORTANT]  
> Certains services s’exécutent uniquement sur un nœud principal à la fois. Si vous tentez de vous connecter au service sur le nœud principal et que vous rencontrez une erreur, recommencez à l’aide du nœud secondaire.

### <a name="ambari"></a>Ambari

| de diffusion en continu | Nœuds | Port | Chemin d'accès de l'URL | Protocole | 
| --- | --- | --- | --- | --- |
| Interface utilisateur Web d'Ambari | Nœuds principaux | 8080 | / | HTTP |
| API Ambari REST | Nœuds principaux | 8080 | /api/v1 | HTTP |

Exemples :

* API Ambari REST : `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Ports HDFS

| de diffusion en continu | Nœuds | Port | Protocole | Description |
| --- | --- | --- | --- | --- |
| Interface utilisateur web de NameNode |Nœuds principaux |30070 |HTTPS |Interface utilisateur web pour afficher l’état |
| Service de métadonnées NameNode |Nœuds principaux |8020 |IPC |Métadonnées du système de fichiers |
| DataNode |Tous les nœuds de travail |30075 |HTTPS |Interface utilisateur web pour afficher l’état, les journaux d’activité, etc. |
| DataNode |Tous les nœuds de travail |30010 |&nbsp; |Transfert de données |
| DataNode |Tous les nœuds de travail |30020 |IPC |Opérations sur les métadonnées |
| NameNode secondaire |Nœuds principaux |50090 |HTTP |Point de contrôle pour les métadonnées NameNode |

### <a name="yarn-ports"></a>Ports YARN

| de diffusion en continu | Nœuds | Port | Protocole | Description |
| --- | --- | --- | --- | --- |
| Interface utilisateur web de Resource Manager |Nœuds principaux |8088 |HTTP |Interface utilisateur web pour Resource Manager |
| Interface utilisateur web de Resource Manager |Nœuds principaux |8090 |HTTPS |Interface utilisateur web pour Resource Manager |
| Interface d’administration de Resource Manager |Nœuds principaux |8141 |IPC |Pour les envois d’application (Hive, serveur Hive, Pig, etc.) |
| Scheduler Resource Manager |Nœuds principaux |8030 |HTTP |Interface d’administration |
| Interface d’application Resource Manager |Nœuds principaux |8050 |HTTP |Adresse de l’interface du gestionnaire d’applications |
| NodeManager |Tous les nœuds de travail |30050 |&nbsp; |L’adresse du gestionnaire de conteneurs |
| Interface utilisateur web de NodeManager |Tous les nœuds de travail |30060 |HTTP |Interface de Resource Manager |
| Adresse de Timeline |Nœuds principaux |10200 |RPC |Le service RPC du service de Timeline. |
| Interface utilisateur web de Timeline |Nœuds principaux |8181 |HTTP |L’interface utilisateur web du service Timeline |

### <a name="hive-ports"></a>Ports Hive

| de diffusion en continu | Nœuds | Port | Protocole | Description |
| --- | --- | --- | --- | --- |
| HiveServer2 |Nœuds principaux |10001 |Thrift |Service pour se connecter à Hive (Thrift/JDBC) |
| Metastore Hive |Nœuds principaux |9083 |Thrift |Service pour se connecter aux métadonnées Hive (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Ports WebHCat

| de diffusion en continu | Nœuds | Port | Protocole | Description |
| --- | --- | --- | --- | --- |
| Serveur WebHCat |Nœuds principaux |30111 |HTTP |API web sur HCatalog et d’autres services Hadoop |

### <a name="mapreduce-ports"></a>Ports MapReduce

| de diffusion en continu | Nœuds | Port | Protocole | Description |
| --- | --- | --- | --- | --- |
| JobHistory |Nœuds principaux |19888 |HTTP |Interface utilisateur web de MapReduce JobHistory |
| JobHistory |Nœuds principaux |10020 |&nbsp; |Serveur MapReduce JobHistory |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Transfère les sorties Map intermédiaires aux raccords de réduction qui le demandent |

### <a name="oozie"></a>Oozie

| de diffusion en continu | Nœuds | Port | Protocole | Description |
| --- | --- | --- | --- | --- |
| Serveur Oozie |Nœuds principaux |11000 |HTTP |URL du service Oozie |
| Serveur Oozie |Nœuds principaux |11001 |HTTP |Port pour l’administration Oozie |

### <a name="ambari-metrics"></a>Mesures d’Ambari

| de diffusion en continu | Nœuds | Port | Protocole | Description |
| --- | --- | --- | --- | --- |
| TimeLine (historique d’application) |Nœuds principaux |6188 |HTTP |L’interface utilisateur web du service Timeline |
| TimeLine (historique d’application) |Nœuds principaux |30200 |RPC |L’interface utilisateur web du service Timeline |

### <a name="hbase-ports"></a>Ports HBase

| de diffusion en continu | Nœuds | Port | Protocole | Description |
| --- | --- | --- | --- | --- |
| HMaster |Nœuds principaux |16000 |&nbsp; |&nbsp; |
| Interface utilisateur web d’informations sur HMaster |Nœuds principaux |16010 |HTTP |Le port de l’interface utilisateur web principale de HBase Master |
| Serveur de la région |Tous les nœuds de travail |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Le port que les clients utilisent pour se connecter à ZooKeeper |

### <a name="kafka-ports"></a>Ports Kafka

| de diffusion en continu | Nœuds | Port | Protocole | Description |
| --- | --- | --- | --- | --- |
| Service Broker |Nœuds de travail |9092 |[Protocole Kafka](https://kafka.apache.org/protocol.html) |Utilisé pour la communication client |
| &nbsp; |Nœuds Zookeeper |2181 |&nbsp; |Le port que les clients utilisent pour se connecter à ZooKeeper |

### <a name="spark-ports"></a>Ports Spark

| de diffusion en continu | Nœuds | Port | Protocole | Chemin d'accès de l'URL | Description |
| --- | --- | --- | --- | --- | --- |
| Serveurs Thrift Spark |Nœuds principaux |10002 |Thrift | &nbsp; | Service de connexion à Spark SQL (Thrift/JDBC) |
| Serveur Livy | Nœuds principaux | 8998 | HTTP | &nbsp; | Service d’exécution des instructions, des travaux et des applications |
| Jupyter Notebook | Nœuds principaux | 8001 | HTTP | &nbsp; | Site web du bloc-notes Jupyter |

Exemples :

* Livy : `curl -u admin -G "http://10.0.0.11:8998/"`. Dans cet exemple, `10.0.0.11` est l’adresse IP du nœud principal qui héberge le service Livy.
