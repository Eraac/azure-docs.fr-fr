---
title: Messages cloud-à-appareil avec Azure IoT Hub (Java) | Microsoft Docs
description: Envoi de messages cloud-à-appareil vers un appareil depuis un Azure IoT Hub à l’aide des kits de développement logiciel Azure IoT pour Java. Vous modifiez une application d’appareil simulé pour recevoir des messages cloud-à-appareil et modifiez une application principale pour envoyer des messages cloud-à-appareil.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: a74058c509c8031d0ac53c0d9cdf91e6f933ea1f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620144"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Envoi de messages cloud à appareil avec IoT Hub (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub est un service entièrement géré qui permet d’autoriser des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un serveur principal de solution. Le guide de démarrage rapide [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-java.md) explique comment créer un hub IoT, y provisionner une identité d’appareil et coder une application d’appareil simulé qui envoie des messages appareil-à-cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ce didacticiel est basé sur [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-java.md). Il vous montre comment effectuer les étapes suivantes :

* À partir du serveur principal de votre application, envoyez des messages cloud-à-appareil vers un appareil unique via IoT Hub.

* Recevez des messages cloud-à-appareil sur un appareil.

* À partir du serveur principal de votre application, demandez l’accusé de réception (*commentaires*) pour les messages envoyés à un appareil depuis IoT Hub.

Vous trouverez des informations supplémentaires sur les messages cloud-à-appareil dans le [Guide du développeur IoT Hub](iot-hub-devguide-messaging.md).

À la fin de ce didacticiel, vous exécutez deux applications de console Java :

* **simulated-device**, une version modifiée de l’application créée dans [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-java.md), qui se connecte à votre hub IoT et reçoit des messages cloud-à-appareil.

* **send-c2d-messages**, qui envoie un message cloud-à-appareil à l’application d’appareil simulé via IoT Hub, puis reçoit son accusé de réception.

> [!NOTE]
> IoT Hub offre la prise en charge de plusieurs plateformes d’appareils et plusieurs langages (notamment C, Java et Javascript) par le biais des Kits de développement logiciel (SDK) d’appareils Azure IoT. Pour obtenir des instructions détaillées sur la façon de connecter votre appareil au code de ce didacticiel, et à Azure IoT Hub de manière générale, consultez le [Centre de développement Azure IoT](https://azure.microsoft.com/develop/iot).

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Une version opérationnelle complète du guide de démarrage rapide [Envoyer des données de télémétrie d’un appareil vers un hub IoT](quickstart-send-telemetry-java.md) ou du didacticiel [Configurer le routage des messages avec IoT Hub](tutorial-routing.md).

* La version la plus récente de [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes.

## <a name="receive-messages-in-the-simulated-device-app"></a>Recevoir des messages dans l’application d’appareil simulé

Dans cette section, vous modifiez l’application d’appareil simulé créée dans [Envoyer des données de télémétrie d’un appareil à un IoT Hub](quickstart-send-telemetry-java.md) pour recevoir des messages cloud-à-appareil en provenance du hub IoT.

1. À l’aide d’un éditeur de texte, ouvrez le fichier simulated-device\src\main\java\com\mycompany\app\App.java.

2. Ajoutez la classe **MessageCallback** suivante comme classe imbriquée à l'intérieur de la classe **App**. La méthode **execute** est appelée lorsque l’appareil reçoit un message d’IoT Hub. Dans cet exemple, l’appareil notifie toujours l’IoT Hub qu’il a terminé le message :

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Modifiez la méthode **main** pour créer une instance **AppMessageCallback** et appelez la méthode **setMessageCallback** avant l’ouverture du client comme suit :

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > Si vous utilisez HTTPS au lieu de MQTT ou d’AMQP comme moyen de transport, l’instance **DeviceClient** vérifie les messages à partir d’IoT Hub peu fréquemment (moins de toutes les 25 minutes). Pour plus d’informations sur les différences entre la prise en charge de MQTT, d’AMQP et de HTTPS et la limitation d’IoT Hub, consultez la [section sur les messages du Guide du développeur IoT Hub](iot-hub-devguide-messaging.md).

4. Pour générer l’application **simulated-device** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier simulated-device :

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="send-a-cloud-to-device-message"></a>Envoi d’un message cloud vers appareil

Dans cette section, vous créez une application de console Java qui envoie des messages cloud-à-appareil à l’application de l’appareil simulé. Vous avez besoin de l’ID de l’appareil que vous avez ajouté dans le guide de démarrage rapide [Envoyer des données de télémétrie d’un appareil vers un hub IoT](quickstart-send-telemetry-java.md). Vous avez également besoin de la chaîne de connexion pour votre hub que vous trouverez dans le [Portail Azure](https://portal.azure.com).

1. Créez un projet Maven nommé **send-c2d-messages** à l’aide de la commande ci-dessous, à l’invite de commandes. Il s’agit d’une commande unique et longue :

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À l’invite de commandes, accédez au nouveau dossier send-c2d-messages.

3. Dans un éditeur de texte, ouvrez le fichier pom.xml dans le dossier send-c2d-messages et ajoutez la dépendance suivante au nœud **dependencies** . L’ajout de la dépendance vous permet d’utiliser le package **iothub-java-service-client** dans votre application pour communiquer avec votre service d’IoT Hub :

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Vous pouvez rechercher la dernière version de **iot-service-client** avec la [recherche Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Ajoutez les instructions **import** suivantes au fichier :

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Ajoutez les variables de niveau classe suivantes à la classe **App** en remplaçant **{yourhubconnectionstring}** et **{yourdeviceid}** par les valeurs que vous avez notées précédemment :

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Remplacez la méthode **main** par le code suivant. Ce code établit la connexion à votre IoT Hub, envoie un message à votre appareil, puis attend un accusé de réception indiquant que l’appareil a reçu le message et l’a traité :

    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article [Gestion des erreurs temporaires](/azure/architecture/best-practices/transient-faults).

9. Pour générer l’application **simulated-device** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier simulated-device :

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes du dossier simulated-device, exécutez la commande suivante pour commencer à envoyer la télémétrie à votre IoT Hub et écouter les messages cloud-à-appareil envoyés depuis ce dernier :

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Exécution de l’application de périphérique simulé](./media/iot-hub-java-java-c2d/receivec2d.png)

2. À l’invite de commandes dans le dossier send-c2d-messages, exécutez la commande suivante pour envoyer un message cloud-à-appareil, puis attendez de recevoir l’accusé de réception :

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Exécutez la commande pour envoyer le message cloud-à-appareil](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à envoyer et recevoir des messages cloud-à-appareil.

Pour voir des exemples de solutions de bout en bout qui utilisent IoT Hub, consultez [Accélérateurs de solution Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur d’IoT Hub](iot-hub-devguide.md).
