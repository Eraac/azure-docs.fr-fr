---
title: Traiter les événements Apache Kafka à l’aide de Stream Analytics - Azure Event Hubs | Microsoft Docs
description: Cet article décrit comment traiter les événements Kafka ingérés à travers des hubs d’événements à l’aide d’Azure Stream Analytics
services: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: 0c4beede2508104fc9af934d3f9a2bbcce791292
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626185"
---
# <a name="process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Processus Apache Kafka pour les événements Event Hubs à l’aide de Stream Analytics 
Cet article décrit comment diffuser des données dans des hubs d’événements Kafka et les traiter avec Azure Stream Analytics. Il vous guide dans les étapes suivantes : 

1. Créer un espace de noms Event Hubs prenant en charge Kafka.
2. Créer un client Kafka qui envoie des messages au hub d’événements.
3. Créer un travail Stream Analytics qui copie les données du hub d’événements dans un stockage blob Azure. 

Vous n’avez pas besoin de changer vos clients de protocole ou d’exécuter vos propres clusters quand vous utilisez le point de terminaison Kafka exposé par un hub d’événements. Azure Event Hubs prend en charge [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html) et versions ultérieures. 


## <a name="prerequisites"></a>Prérequis

Pour suivre ce démarrage rapide, vérifiez que vous avez :

* Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Téléchargez](https://maven.apache.org/download.cgi) et [installez](https://maven.apache.org/install.html) une archive binaire Maven.
* [Git](https://www.git-scm.com/)
* Un **compte de stockage Azure**. Si vous n’en avez pas, [créez-en un](../storage/common/storage-quickstart-create-account.md) avant de continuer. Le travail Stream Analytics dans cette procédure pas à pas stocke les données de sortie dans un stockage blob Azure. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Créer un espace de noms Event Hubs prenant en charge Kafka

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis cliquez sur **Créer une ressource** en haut à gauche de l’écran.
2. Recherchez **Event Hubs** et sélectionnez les options indiquées ici :
    
    ![Rechercher Event Hubs dans le portail](./media/event-hubs-kafka-stream-analytics/select-event-hubs.png) 
3. Dans la page **Event Hubs**, sélectionnez **Créer**.
4. Dans la page **Créer un espace de noms**, effectuez les actions suivantes : 
    1. Indiquez un **nom** unique pour l’espace de noms. 
    2. Sélectionnez un **niveau tarifaire**. 
    3. Sélectionnez **Activer Kafka**. Il s’agit d’une étape **importante**. 
    4. Sélectionnez **l’abonnement** dans lequel vous voulez créer l’espace de noms du hub d’événements. 
    5. Créez un **groupe de ressources** ou sélectionnez-en un. 
    6. Sélectionnez un **emplacement**. 
    7. Cliquez sur **Créer**.
    
        ![Créer un espace de noms](./media/event-hubs-kafka-stream-analytics/create-event-hub-namespace-page.png) 
4. Dans le **message de notification**, sélectionnez le **nom du groupe de ressources**. 

    ![Créer un espace de noms](./media/event-hubs-kafka-stream-analytics/creation-station-message.png)
1. Sélectionnez **l’espace de noms du hub d’événements** dans le groupe de ressources. 
2. Une fois l’espace de noms créé, sélectionnez **Stratégies d’accès partagé** sous **PARAMÈTRES**.

    ![Cliquer sur Stratégies d’accès partagé](./media/event-hubs-kafka-stream-analytics/shared-access-policies.png)
5. Vous pouvez choisir **RootManageSharedAccessKey** par défaut ou ajouter une nouvelle stratégie. Cliquez sur le nom de la stratégie et copiez la **chaîne de connexion**. Vous utilisez la chaîne de connexion pour configurer le client Kafka. 
    
    ![Sélectionner une stratégie](./media/event-hubs-kafka-stream-analytics/connection-string.png)  

Vous pouvez désormais diffuser des événements à partir de vos applications qui utilisent le protocole Kafka dans Event Hubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Envoyer des messages avec Kafka dans Event Hubs

1. Clonez le [dépôt Azure Event Hubs pour Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) sur votre machine.
2. Accédez au dossier `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Mettez à jour les détails de configuration du producteur dans `src/main/resources/producer.config`. Spécifiez le **nom** et la **chaîne de connexion** de **l’espace de noms du hub d’événements**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Accédez à `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/com/example/app`, puis ouvrez le fichier **TestDataReporter.java** dans un éditeur de votre choix. 
6. Commentez la ligne de code suivante :

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Ajoutez la ligne de code suivante à la place du code commenté : 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Ce code envoie les données d’événement au format **JSON**. Quand vous configurez une entrée pour un travail Stream Analytics, vous spécifiez un format JSON pour les données d’entrée. 
7. **Exécutez le producteur** et diffusez en continu dans Event Hubs prenant en charge Kafka. Sur une machine Windows, quand vous utilisez une **invite de commandes Node.js**, basculez sur le dossier `azure-event-hubs-for-kafka/quickstart/java/producer` avant d’exécuter ces commandes. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Vérifier que cet hub d’événements reçoit les données

1. Sélectionnez **Event Hubs** sous **ENTITÉS**. Vérifiez que vous voyez un hub d’événements nommé **test**. 

    ![Hub d’événements - test](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Vérifiez que des messages entrent dans le hub d’événements. 

    ![Hub d’événements - messages](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Traiter les données d’événement à l’aide d’un travail Stream Analytics
Dans cette section, vous créez un travail Azure Stream Analytics. Le client Kafka envoie des événements au hub d’événements. Vous créez un travail Stream Analytics qui accepte des données d’événement en entrée et les envoie dans un stockage blob Azure. Si vous n’avez pas de **compte de stockage Azure**, [créez-en un](../storage/common/storage-quickstart-create-account.md).

La requête dans le travail Stream Analytics parcourt les données sans effectuer d’analyse. Vous pouvez créer une requête qui transforme les données d’entrée en données de sortie dans un format différent ou avec des insights en plus.  

### <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics 

1. Sélectionnez **+ Créer une ressource** dans le [portail Azure](https://portal.azure.com).
2. Sélectionnez **Analytics** dans le menu **Place de marché Azure**, puis sélectionnez **Travail Stream Analytics**. 
3. Dans la page **Nouveau Stream Analytics**, effectuez les actions suivantes : 
    1. Entrez un **nom** pour le travail. 
    2. Sélectionnez votre **abonnement**.
    3. Sélectionnez **Créer** pour le **groupe de ressources** et entrez le nom. Vous pouvez aussi **utiliser un groupe de ressources existant**. 
    4. Sélectionnez un **emplacement** pour le travail.
    5. Sélectionnez **Créer** pour créer le travail. 

        ![Nouveau travail Stream Analytics](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Configurer les entrées du travail

1. Dans le message de notification, sélectionnez **Atteindre la ressource** pour voir la page **Travail Stream Analytics**. 
2. Sélectionnez **Entrées** dans la section **TOPOLOGIE DU TRAVAIL** dans le menu de gauche.
3. Sélectionnez **Ajouter une entrée de flux**, puis sélectionnez **Hub d’événements**. 

    ![Ajouter un hub d’événements comme entrée](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Dans la page de configuration **Entrée de hub d’événements**, effectuez les actions suivantes : 

    1. Spécifiez un **alias** pour l’entrée. 
    2. Sélectionnez votre **abonnement Azure**.
    3. Sélectionnez **l’espace de noms du hub d’événements** créé précédemment. 
    4. Sélectionnez **test** pour le **hub d’événements**. 
    5. Sélectionnez **Enregistrer**. 

        ![Configuration de l’entrée de hub d’événements](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Configurer la sortie du travail 

1. Sélectionnez **Sorties** dans la section **TOPOLOGIE DU TRAVAIL** du menu. 
2. Sélectionnez **+ Ajouter** dans la barre d’outils, puis **Stockage Blob**
3. Dans la page Paramètres de sortie du stockage Blob, effectuez les actions suivantes : 
    1. Spécifiez un **alias** pour la sortie. 
    2. Sélectionnez votre **abonnement**Azure. 
    3. Sélectionnez votre **compte de stockage Azure**. 
    4. Entrez un **nom pour le conteneur** qui stocke les données de sortie de la requête Stream Analytics.
    5. Sélectionnez **Enregistrer**.

        ![Configuration de la sortie de stockage Blob](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Définir une requête
Lorsque vous disposez d’un travail Stream Analytics configuré pour lire un flux de données entrantes, l’étape suivante consiste à créer une transformation qui analyse les données en temps réel. Vous définissez la requête de transformation à l’aide du [Langage de requête Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Dans cette procédure pas à pas, vous définissez une requête qui parcourt les données sans aucune transformation.

1. Sélectionnez **Requête**.
2. Dans la fenêtre de requête, remplacez `[YourOutputAlias]` par l’alias de sortie que vous avez créé précédemment.
3. Remplacez `[YourInputAlias]` par l’alias d’entrée que vous avez créé précédemment. 
4. Sélectionnez **Enregistrer** dans la barre d’outils. 

    ![Requête](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Exécuter la tâche Stream Analytics

1. Sélectionnez **Vue d’ensemble** dans le menu de gauche. 
2. Sélectionnez **Démarrer**. 

    ![Menu Démarrer](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Dans la page **Démarrer le travail**, sélectionnez **Démarrer**. 

    ![Page Démarrer le travail](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Attendez que l’état du travail passe de **Démarrage** à **En cours d’exécution**. 

    ![État du travail - en cours d'exécution](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Test du scénario
1. Réexécutez le **producteur Kafka** pour envoyer des événements au hub d’événements. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Vérifiez que vous voyez les **données de sortie** générées dans le **stockage blob Azure**. Vous voyez un fichier JSON dans le conteneur avec 100 lignes qui ressemblent aux exemples de ligne suivants : 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Dans ce scénario, le travail Azure Stream Analytics a reçu des données d’entrée du hub d’événements et les a stocké dans le stockage blob Azure. 



## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à échanger des données avec Event Hubs prenant en charge Kafka, sans changer vos protocoles clients ni exécuter vos propres clusters. Pour plus d’informations sur Event Hubs et sur Event Hubs pour Kafka, consultez les articles suivants :  

- [En savoir plus sur Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs pour Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Créer un espace de noms Event Hubs prenant en charge Kafka](event-hubs-create-kafka-enabled.md)
- [Diffuser dans Event Hubs à partir de vos applications Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Mettre en miroir un répartiteur Kafka dans un Event Hub prenant en charge Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connecter Apache Spark à un Event Hub prenant en charge Kafka](event-hubs-kafka-spark-tutorial.md)
- [Connecter Apache Flink à un Event Hub prenant en charge Kafka](event-hubs-kafka-flink-tutorial.md)
- [Intégrer Kafka Connect à un Event Hub prenant en charge Kafka](event-hubs-kafka-connect-tutorial.md)
- [Connecter Akka Streams à un Event Hub prenant en charge Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Explorer des exemples sur GitHub](https://github.com/Azure/azure-event-hubs-for-kafka) 
