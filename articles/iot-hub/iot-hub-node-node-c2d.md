---
title: Messages cloud-à-appareil avec Azure IoT Hub (Node) | Microsoft Docs
description: Envoi de messages cloud-à-appareil vers un appareil depuis un Azure IoT Hub à l’aide des kits de développement logiciel Azure IoT pour Node.js. Vous modifiez une application d’appareil simulé pour recevoir des messages cloud-à-appareil et modifiez une application principale pour envoyer des messages cloud-à-appareil.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: 35acc1448b662a9b0c08e9d1f91886903444bcb8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620055"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Envoi de messages cloud à appareil avec IoT Hub (Node)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduction

Azure IoT Hub est un service entièrement géré qui permet d’autoriser des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un serveur principal de solution. Le guide de démarrage rapide [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-node.md) explique comment créer un hub IoT, y provisionner une identité d’appareil et coder une application d’appareil simulé qui envoie des messages appareil-à-cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ce tutoriel est basé sur [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-node.md). Cette rubrique vous explique les procédures suivantes :

* À partir du serveur principal de votre application, envoyez des messages cloud-à-appareil vers un appareil unique via IoT Hub.
* Recevez des messages cloud-à-appareil sur un appareil.
* À partir du serveur principal de votre application, demandez l’accusé de réception (*commentaires*) pour les messages envoyés à un appareil depuis IoT Hub.

Vous trouverez des informations supplémentaires sur les messages cloud-à-appareil dans le [Guide du développeur IoT Hub](iot-hub-devguide-messaging.md).

À la fin de ce didacticiel, vous exécuterez deux applications de console Node.js :

* **SimulatedDevice**, une version modifiée de l’application créée dans [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-node.md), qui se connecte à votre hub IoT et reçoit des messages cloud-à-appareil.

* **SendCloudToDeviceMessage**, qui envoie un message cloud-à-appareil à l’application pour appareil simulée par le biais d’IoT Hub, puis reçoit son accusé de réception.

> [!NOTE]
> IoT Hub offre la prise en charge de plusieurs plateformes d’appareils et plusieurs langages (notamment C, Java et Javascript) par le biais des Kits de développement logiciel (SDK) d’appareils Azure IoT. Pour obtenir des instructions détaillées sur la façon de connecter votre appareil au code de ce didacticiel, et à Azure IoT Hub de manière générale, consultez le [Centre de développement Azure IoT](https://azure.microsoft.com/develop/iot).
>

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Node.js version 10.0.x ou ultérieure.
* Un compte Azure actif. (Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial) en quelques minutes seulement.)

## <a name="receive-messages-in-the-simulated-device-app"></a>Recevoir des messages dans l’application d’appareil simulé

Dans cette section, vous modifiez l’application d’appareil simulé créée dans [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-node.md) pour recevoir des messages cloud-à-appareil en provenance du hub IoT.

1. À l’aide d’un éditeur de texte, ouvrez le fichier SimulatedDevice.js.

2. Modifiez la fonction **connectCallback** pour gérer les messages envoyés à partir d’IoT Hub. Dans cet exemple, l’appareil appelle toujours la fonction **complete** afin de notifier IoT Hub qu’il a traité le message. La nouvelle version de la fonction **connectCallback** ressemble à l’extrait de code suivant :

    ```javascript
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
  
   > [!NOTE]
   > Si vous utilisez HTTPS au lieu de MQTT ou d’AMQP comme moyen de transport, l’instance **DeviceClient** vérifie les messages à partir d’IoT Hub peu fréquemment (moins de toutes les 25 minutes). Pour plus d’informations sur les différences entre la prise en charge de MQTT, d’AMQP et de HTTPS ainsi que la limitation d’IoT Hub, consultez le [Guide du développeur IoT Hub](iot-hub-devguide-messaging.md).
   >

## <a name="send-a-cloud-to-device-message"></a>Envoi d’un message cloud vers appareil

Dans cette section, vous allez créer une application de console Node.js qui envoie des messages cloud-à-appareil à l’application de l’appareil simulé. Vous avez besoin de l’ID de l’appareil que vous avez ajouté dans le guide de démarrage rapide [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-node.md). Vous avez également besoin de la chaîne de connexion pour votre hub que vous trouverez dans le [Portail Azure](https://portal.azure.com).

1. Créez un dossier vide appelé **sendcloudtodevicemessage**. Dans le dossier **sendcloudtodevicemessage** , créez un fichier package.json à l’aide de la commande ci-dessous, à l’invite de commandes. Acceptez toutes les valeurs par défaut :

    ```shell
    npm init
    ```

2. À l’invite de commandes, dans le dossier **sendcloudtodevicemessage**, exécutez la commande suivante pour installer le package **azure-iothub** :

    ```shell
    npm install azure-iothub --save
    ```

3. À l’aide d’un éditeur de texte, créez un fichier **SendCloudToDeviceMessage.js** dans le dossier **sendcloudtodevicemessage**.

4. Ajoutez les instructions `require` ci-dessous au début du fichier **SendCloudToDeviceMessage.js** :

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Ajoutez le code suivant au fichier **SendCloudToDeviceMessage.js** . Remplacez la valeur de l’espace réservé « {iot hub connection string} » par la chaîne de connexion IoT Hub du hub que vous avez créé dans le guide de démarrage rapide [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-node.md). Remplacez l’espace réservé « {device id} » par l’ID de l’appareil que vous avez ajouté dans le guide de démarrage rapide [Envoyer des données de télémétrie d’un appareil à un hub IoT](quickstart-send-telemetry-node.md) :

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Ajoutez la fonction suivante pour imprimer les résultats de l’opération sur la console :

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Ajoutez la fonction suivante pour imprimer les messages d’accusé de réception sur la console :

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Ajoutez le code suivant pour envoyer un message à votre appareil et gérer le message de commentaires lorsque l’appareil accuse réception d’un message cloud-à-appareil :

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. Enregistrez et fermez le fichier **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes du dossier **simulateddevice** , exécutez la commande suivante pour envoyer la télémétrie à IoT hub et écouter les messages cloud-à-appareil :

    ```shell
    node SimulatedDevice.js
    ```

    ![Exécution de l’application de périphérique simulé](./media/iot-hub-node-node-c2d/receivec2d.png)

2. À l’invite de commandes dans le dossier **sendcloudtodevicemessage**, exécutez la commande suivante pour envoyer un message cloud-à-appareil, puis attendez de recevoir l’accusé de réception :

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Exécutez l’application pour envoyer la commande cloud-à-appareil](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Par souci de simplicité, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article [Gestion des erreurs temporaires](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à envoyer et recevoir des messages cloud-à-appareil.

Pour voir des exemples de solutions de bout en bout qui utilisent IoT Hub, consultez [Accélérateur de solution de supervision à distance Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur d’IoT Hub](iot-hub-devguide.md).
