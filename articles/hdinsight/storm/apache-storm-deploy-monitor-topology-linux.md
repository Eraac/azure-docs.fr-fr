---
title: Déploiement et gestion des topologies Apache Storm sur Azure HDInsight
description: Apprenez à déployer, surveiller et gérer des topologies Apache Storm à l’aide du tableau de bord Storm sur HDInsight basé sur Linux. Utilisez les outils Hadoop pour Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: ac1a4c77589f4ef88c9ee862cb871b376ca8a0fe
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483847"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Déploiement et gestion des topologies Apache Storm sur Azure HDInsight 

Ce document présente les principes fondamentaux de la gestion et du monitoring des topologies [Apache Storm](https://storm.apache.org/) qui s’exécutent sur des clusters Storm sur HDInsight.

## <a name="prerequisites"></a>Prérequis

* Un cluster Apache Storm sur HDInsight. Consultez la section [Création de clusters Apache Hadoop à l’aide du portail Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) et sélectionnez **Storm** dans le champ **Type de cluster**.


* (Facultatif) Des connaissances en SSH et SCP : Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Facultatif) Visual Studio : le SDK Azure 2.5.1 ou version ultérieure et Data Lake Tools pour Visual Studio. Pour plus d’informations, consultez [Get started using Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md) (Prise en main de Data Lake Tools pour Visual Studio).

    L’une des versions suivantes de Visual Studio :

  * Visual Studio 2012 avec Update 4

  * Visual Studio 2013 avec Update 4 ou [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (toute édition)

  * Visual Studio 2017 (toute édition) Data Lake Tools pour Visual Studio 2017 est installé avec la charge de travail Azure.

## <a name="submit-a-topology-visual-studio"></a>Soumettre une topologie : Visual Studio

Les outils HDInsight permettent de soumettre des topologies C# ou hybrides à votre cluster Storm. La procédure suivante utilise un exemple d’application. Pour plus d’informations sur la création à l’aide des outils HDInsight, consultez [Développement de topologies C# à l’aide des outils HDInsight pour Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Si vous n’avez pas encore installé la dernière version de Data Lake Tools pour Visual Studio, consultez [Get started using Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md) (Prise en main de Data Lake Tools pour Visual Studio).

    > [!NOTE]  
    > Data Lake Tools pour Visual Studio est le nouveau nom des outils HDInsight pour Visual Studio.
    >
    > Data Lake Tools pour Visual Studio est inclus dans la __charge de travail Azure__ pour Visual Studio 2017.

2. Ouvrez Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**.

3. Dans la boîte de dialogue **Nouveau projet**, développez **Installé** > **Modèles**, puis sélectionnez **HDInsight**. Dans la liste des modèles, sélectionnez **Exemple Storm**. En bas de la boîte de dialogue, entrez un nom pour l’application.

    ![image](./media/apache-storm-deploy-monitor-topology-linux/sample.png)

4. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Envoyer à Storm sur HDInsight**.

   > [!NOTE]  
   > Si vous y êtes invité, entrez les informations d’identification de connexion pour votre abonnement Azure. Si vous disposez de plusieurs abonnements, connectez-vous à celui qui contient votre cluster Storm dans HDInsight.

5. Sélectionnez votre Storm sur le cluster HDInsight dans la liste déroulante **Cluster Storm**, puis sélectionnez **Envoyer**. Vous pouvez contrôler si l’envoi a bien été effectué à l’aide de la fenêtre **Sortie** .

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Soumettre une topologie : SSH et la commande Storm

1. Utilisez SSH pour vous connecter au cluster HDInsight. Remplacez **USERNAME** par le nom de votre connexion SSH. Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Pour plus d’informations sur l’utilisation de SSH pour se connecter à votre cluster HDInsight, voir [Utilisation de SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilisez la commande suivante pour démarrer un exemple de topologie :

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Cette commande démarre l’exemple de topologie WordCount sur le cluster. Cette topologie va générer des phrases de manière aléatoire, puis compter les occurrences de chaque mot dans ces phrases.

   > [!NOTE]  
   > Pendant l’envoi de la topologie au cluster, vous devez d’abord copier le fichier jar contenant le cluster avant d’utiliser la commande `storm`. Vous pouvez utiliser la commande `scp` pour copier le fichier sur le cluster. Par exemple, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > L’exemple WordCount et d’autres exemples de Storm Starter sont déjà inclus dans votre cluster sous `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Soumettre une topologie : par programme

Vous pouvez déployer par programme une topologie à l’aide du service Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) fournit un exemple d’application Java qui montre comment déployer et démarrer une topologie via le service Nimbus.

## <a name="monitor-and-manage-visual-studio"></a>Superviser et gérer : Visual Studio

Lorsqu’une topologie est envoyée à l’aide de Visual Studio, la vue **Topologies Storm** s’affiche. Sélectionnez la topologie à partir de la liste pour afficher des informations sur la topologie en cours d’exécution.

![Visual Studio Monitor](./media/apache-storm-deploy-monitor-topology-linux/vsmonitor.png)

> [!NOTE]  
> Vous pouvez également afficher les **Topologies Storm** dans l’**Explorateur de serveurs** en développant **Azure** > **HDInsight**, puis en cliquant avec le bouton droit sur le cluster HDInsight et en sélectionnant **Affichage des topologies Storm**.

Sélectionnez la forme des spouts et bolts pour afficher des informations sur ces composants. Une nouvelle fenêtre s’ouvre pour chaque élément sélectionné.

### <a name="deactivate-and-reactivate"></a>Désactivation et réactivation

La désactivation d’une topologie la met en pause jusqu’à ce qu’elle soit arrêtée ou réactivée. Pour effectuer ces opérations, utilisez les boutons __Désactiver__ et __Réactiver__ en haut de la fenêtre __Résumé de la topologie__.

### <a name="rebalance"></a>Rééquilibrage

Le rééquilibrage d’une topologie permet au système de réviser le parallélisme de la topologie. Par exemple, si vous avez redimensionné le cluster pour ajouter plus de nœuds, le rééquilibrage permet à une topologie d’identifier les nouveaux nœuds.

Pour rééquilibrer une topologie, utilisez le bouton __Rééquilibrer__ en haut de la fenêtre __Résumé de la topologie__.

> [!WARNING]  
> Le rééquilibrage d’une topologie désactive d’abord la topologie, puis redistribue les workers uniformément sur le cluster avant de rétablir la topologie dans l’état où elle se trouvait avant le rééquilibrage. Par conséquent, si la topologie était active, elle redevient active. Si elle était désactivée, elle reste désactivée.

### <a name="kill-a-topology"></a>Supprimer une topologie

Les topologies Storm poursuivent leur exécution jusqu'à ce qu'elles soient arrêtées ou que le cluster soit supprimé. Pour supprimer une topologie, utilisez le bouton __Supprimer__ en haut de la fenêtre __Résumé de la topologie__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Superviser et gérer : SSH et la commande Storm

L’utilitaire `storm` vous permet d’utiliser des topologies en cours d’exécution à partir de la ligne de commande. Pour obtenir la liste complète des commandes, utilisez `storm -h` .

### <a name="list-topologies"></a>Liste de toutes les topologies

Utilisez la commande suivante pour répertorier toutes les topologies en cours d’exécution :

    storm list

Cette commande retourne des informations semblables au texte suivant :

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Désactivation et réactivation

La désactivation d’une topologie la met en pause jusqu’à ce qu’elle soit arrêtée ou réactivée. Utilisez la commande suivante pour désactiver et réactiver une topologie :

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Arrêt d’une topologie en cours d’exécution

Les topologies Storm, une fois démarrées, continuent leur exécution jusqu’à ce qu’elles soient arrêtées. Pour ce faire, utilisez la commande suivante :

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Rééquilibrage

Le rééquilibrage d’une topologie permet au système de réviser le parallélisme de la topologie. Par exemple, si vous avez redimensionné le cluster pour ajouter plus de nœuds, le rééquilibrage permet à une topologie d’identifier les nouveaux nœuds.

> [!WARNING]  
> Le rééquilibrage d’une topologie désactive d’abord la topologie, puis redistribue les workers uniformément sur le cluster avant de rétablir la topologie dans l’état où elle se trouvait avant le rééquilibrage. Par conséquent, si la topologie était active, elle redevient active. Si elle était désactivée, elle reste désactivée.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Superviser et gérer : Interface utilisateur de Storm

L’interface utilisateur Storm fournit une interface web incluse dans votre cluster HDInsight pour utiliser les topologies en cours d’exécution. Pour afficher l’interface utilisateur Storm, utilisez un navigateur web pour ouvrir **https://CLUSTERNAME.azurehdinsight.net/stormui** , où **CLUSTERNAME** est le nom de votre cluster.

> [!NOTE]  
> Si vous êtes invité à fournir un nom d’utilisateur et un mot de passe, entrez l’administrateur de cluster (admin) et le mot de passe que vous avez utilisé pour la création du cluster.

### <a name="main-page"></a>Page principale

La page principale de l’interface utilisateur de Storm fournit les informations suivantes :

* **Résumé du cluster** : informations de base sur le cluster Storm.
* **Résumé de la topologie** : liste des topologies en cours d’exécution. Utilisez les liens de cette section pour afficher plus d’informations sur les topologies spécifiques.
* **Résumé du superviseur** : informations sur le superviseur Storm.
* **Configuration Nimbus** : configuration Nimbus du cluster.

### <a name="topology-summary"></a>Résumé de la topologie

La sélection d’un lien de la section **Résumé de la topologie** affiche les informations suivantes sur la topologie :

* **Résumé de la topologie** : informations de base sur la topologie.
* **Actions de topologie** : actions de gestion que vous pouvez effectuer sur la topologie.

  * **Activer** : reprend le traitement d’une topologie désactivée.
  * **Désactiver** : interrompt une topologie en cours d’exécution.
  * **Rééquilibrer** : ajuste le parallélisme de la topologie. Il convient de rééquilibrer les topologies en cours d’exécution après avoir modifié le nombre de nœuds dans le cluster. Cette opération permet à la topologie d’ajuster le parallélisme pour compenser l’augmentation ou la diminution du nombre de nœuds du cluster.

    Pour plus d’informations, voir <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Comprendre le parallélisme d’une topologie Apache Storm</a>.
  * **Tuer** : met fin à une topologie Storm après expiration du délai spécifié.
* **État de la topologie** : statistiques relatives à la topologie. Utilisez les liens de la colonne **Fenêtre** pour définir l’intervalle de temps des entrées restantes sur la page.
* **Spouts** : spouts utilisés par la topologie. Utilisez les liens de cette section pour afficher plus d’informations sur des spouts spécifiques.
* **Bolts** : bolts utilisés par la topologie. Utilisez les liens de cette section pour afficher plus d’informations sur des bolts spécifiques.
* **Configuration de la topologie** : configuration de la topologie sélectionnée.

### <a name="spout-and-bolt-summary"></a>Résumé relatif aux spouts et aux bolts

La sélection d’un spout à partir de la section **Spouts** ou **Bolts** affiche les informations suivantes sur l’élément sélectionné :

* **Résumé du composant** : informations de base sur le spout ou le bolt.
* **Statistiques du spout/bolt** : statistiques relatives au spout ou au bolt. Utilisez les liens de la colonne **Fenêtre** pour définir l’intervalle de temps des entrées restantes sur la page.
* **Statistiques d’entrée** (bolt uniquement) : informations sur les flux d’entrée consommés par le bolt.
* **Statistiques de sortie** : informations sur les flux de données émis par le spout ou le bolt.
* **Exécuteurs** : informations sur les instances du spout ou du bolt. Sélectionnez l’entrée **Port** d’un exécuteur spécifique afin d’afficher le journal des informations de diagnostic généré pour cette instance.
* **Erreurs** : informations d’erreur pour le spout ou le bolt.

## <a name="monitor-and-manage-rest-api"></a>Superviser et gérer : API REST

L’interface utilisateur Storm repose sur l’API REST, ce qui vous permet de profiter de fonctionnalités de gestion et de surveillance similaires à l’aide de l’API REST. À l'aide de l'API REST, vous pouvez créer des outils personnalisés pour gérer et surveiller les topologies Storm.

Pour plus d’informations, voir [API REST de l’interface utilisateur Apache Storm](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Les informations suivantes sont spécifiques à l’utilisation de l’API REST avec Apache Storm sur HDInsight.

> [!IMPORTANT]  
> L’API REST Storm n’est pas disponible publiquement sur Internet et est accessible à l’aide d’un tunnel SSH vers le nœud principal du cluster HDInsight. Pour plus d’informations sur la création et l’utilisation d’un tunnel SSH, voir [Utiliser un tunnel SSH pour accéder à l’interface utilisateur web d’Ambari, ResourceManager, JobHistory, NameNode, Apache Oozie et d’autres interfaces utilisateur web](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>URI de base

L’URI de base pour l’API REST sur des clusters HDInsight sous Linux est disponible sur le nœud principal à l’adresse **https:\//HEADNODEFQDN:8744/api/v1/** . Le nom de domaine du nœud principal est généré lors de la création du cluster et n’est pas statique.

Vous trouverez le nom de domaine complet (FQDN) du nœud principal du cluster de plusieurs façons différentes :

* **À partir d’une session SSH** : utilisez la commande `headnode -f` à partir d’une session SSH vers le cluster.
* **À partir d’Ambari Web** : sélectionnez **Services** en haut de la page, puis sélectionnez **Storm**. Sous l’onglet **Résumé**, sélectionnez **Serveur de l’interface utilisateur de Storm**. Le nom de domaine complet du nœud qui héberge l’interface utilisateur de Storm et l’API REST figure en haut de la page.
* **À partir de l’API REST d’Ambari** : utilisez la commande `curl -u admin -G "https:\//CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` pour extraire des informations sur le nœud sur lequel l’interface utilisateur de Storm et l’API REST s’exécutent. Remplacez **CLUSTERNAME** par le nom du cluster. Quand vous y êtes invité, entrez le mot de passe du compte de connexion (administrateur). Dans la réponse, l’entrée « host_name » contient le nom de domaine complet du nœud.

### <a name="authentication"></a>Authentication

Les requêtes à l’API REST doivent utiliser l’ **authentification de base**avec le nom et le mot de passe d’administrateur de cluster HDInsight.

> [!NOTE]  
> Étant donné que l’authentification de base est envoyée en texte clair, vous devez **toujours** utiliser HTTPS pour sécuriser les communications avec le cluster.

### <a name="return-values"></a>Valeurs de retour

Les informations renvoyées depuis l’API REST peuvent être uniquement utilisables depuis le cluster. Par exemple, le nom de domaine complet (FQDN) retourné pour les serveurs [Apache ZooKeeper](https://zookeeper.apache.org/) n’est pas accessible sur Internet.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [Développer des topologies Java avec Apache Maven](apache-storm-develop-java-topology.md).

Pour d’autres exemples de topologies, voir [Exemples de topologies pour Apache Storm sur HDInsight](apache-storm-example-topology.md).
