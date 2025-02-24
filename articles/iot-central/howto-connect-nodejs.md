---
title: Connecter une application cliente Node.js générique à Azure IoT Central | Microsoft Docs
description: En tant que développeur d’appareils, comment connecter un appareil Node.js générique à votre application Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 06/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 90e4a061e38fdd3a13a640363069fae3a18e0b49
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444245"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Connecter une application cliente Node.js générique à votre application Azure IoT Central (Node.js)

Cet article vous explique comment, en tant que développeur d’appareils, connecter une application Node.js générique représentant un appareil réel à votre application Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

1. Une application Azure IoT Central. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
1. Une machine de développement où [Node.js](https://nodejs.org/) version 4.0.0 ou ultérieure est installé. Vous pouvez exécuter `node --version` sur la ligne de commande pour vérifier la version. Node.js est disponible pour un large éventail de systèmes d’exploitation.

## <a name="create-a-device-template"></a>Créer un modèle d’appareil

Dans votre application Azure IoT Central, vous avez besoin d’un modèle d’appareil avec les mesures, les propriétés d’appareil, les paramètres et les commandes suivantes :

### <a name="telemetry-measurements"></a>Mesures de télémétrie

Ajoutez la télémétrie suivante dans la page **Mesures** :

| Nom d’affichage | Nom du champ  | Units | Min | max | Nombre de décimales |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Température  | température | F     | 60  | 110 | 0              |
| Humidité     | humidité    | %     | 0   | 100 | 0              |
| Pression     | pression    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> La mesure de télémétrie est représentée par un nombre à virgule flottante.

Entrez les noms des champs dans le modèle d’appareil exactement comme ils figurent dans le tableau. Si les noms des champs ne correspondent pas aux noms des propriétés dans le code de l’appareil, les données de télémétrie ne peuvent pas être affichées dans l’application.

### <a name="state-measurements"></a>Mesures d’état

Ajoutez l’état suivant dans la page **Mesures** :

| Nom d’affichage | Nom du champ  | Valeur 1 | Nom d’affichage | Valeur 2 | Nom d’affichage |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Mode du ventilateur     | fanmode     | 1       | Exécution      | 0       | Arrêté      |

> [!NOTE]
> Le type de données de la mesure État est « chaîne ».

Entrez les noms des champs dans le modèle d’appareil exactement comme ils figurent dans le tableau. Si les noms des champs ne correspondent pas aux noms des propriétés dans le code de l’appareil, l’état ne peut pas être affiché dans l’application.

### <a name="event-measurements"></a>Mesures d’événement

Ajoutez l’événement suivant dans la page **Mesures** :

| Nom d’affichage | Nom du champ  | Severity |
| ------------ | ----------- | -------- |
| Surchauffe  | overheat    | Error    |

> [!NOTE]
> Le type de données de la mesure Événement est « chaîne ».

### <a name="location-measurements"></a>Mesures d’emplacement

Ajoutez la mesure d’emplacement suivante à la page **Mesures** :

| Nom d’affichage | Nom du champ  |
| ------------ | ----------- |
| Location     | location    |

Le type de données de la mesure d’emplacement comporte deux nombres à virgule flottante pour la latitude et la longitude et un nombre à virgule flottante facultatif pour l’altitude.

Entrez les noms des champs dans le modèle d’appareil exactement comme ils figurent dans le tableau. Si les noms des champs ne correspondent pas aux noms des propriétés dans le code de l’appareil correspondant, l’emplacement ne peut pas être affiché dans l’application.

### <a name="device-properties"></a>Propriétés de l’appareil

Ajoutez les propriétés d’appareil suivantes dans la page **Propriétés** :

| Nom d’affichage        | Nom du champ        | Type de données |
| ------------------- | ----------------- | --------- |
| Numéro de série       | serialNumber      | text      |
| Fabricant de l’appareil | manufacturer      | text      |

Entrez les noms des champs dans le modèle d’appareil exactement comme ils figurent dans le tableau. Si les noms des champs ne correspondent pas aux noms des propriétés dans le code de l’appareil, les propriétés ne peuvent pas être affichées dans l’application.

### <a name="settings"></a>Paramètres

Ajoutez les paramètres **numériques** suivants dans la page **Paramètres** :

| Nom d’affichage    | Nom du champ     | Units | Décimales | Min | max  | Initial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Vitesse du ventilateur       | fanSpeed       | tr/min   | 0        | 0   | 3000 | 0       |
| Température définie | setTemperature | F     | 0        | 20  | 200  | 80      |

Entrez les noms des champs dans le modèle d’appareil exactement comme ils figurent dans le tableau. Si les noms des champs ne correspondent pas aux noms des propriétés dans le code de l’appareil, l’appareil ne peut pas recevoir la valeur du paramètre.

### <a name="commands"></a>Commandes

Ajoutez la commande suivante dans la page **Commandes** :

| Nom d’affichage    | Nom du champ     | Délai d’expiration par défaut | Type de données |
| --------------- | -------------- | --------------- | --------- |
| Compte à rebours       | countdown      | 30              | number    |

Ajoutez le champ d’entrée suivant à la commande Compte à rebours :

| Nom d’affichage    | Nom du champ     | Type de données | Valeur |
| --------------- | -------------- | --------- | ----- |
| Compter à partir de      | countFrom      | number    | 10    |

Entrez les noms des champs dans le modèle d’appareil exactement comme ils figurent dans les tableaux. Si les noms des champs ne correspondent pas aux noms des propriétés dans le code de l’appareil, l’appareil ne peut pas traiter la commande.

## <a name="add-a-real-device"></a>Ajouter un appareil réel

Dans votre application Azure IoT Central, ajoutez un appareil réel au modèle d’appareil que vous avez créé dans la section précédente.

Ensuite, suivez les instructions du tutoriel « Ajouter un appareil » pour [générer une chaîne de connexion pour l’appareil réel](tutorial-add-device.md#generate-connection-string). Vous utilisez cette chaîne de connexion dans la section suivante :

### <a name="create-a-nodejs-application"></a>Création d’une application Node.js

Les étapes suivantes montrent comment créer une application cliente qui implémente l’appareil réel que vous avez ajouté à l’application. Ici, l’application Node.js représente l’appareil réel. 

1. Sur votre machine, créez un dossier nommé `connected-air-conditioner-adv`. Accédez à ce dossier dans votre environnement de ligne de commande.

1. Pour initialiser votre projet Node.js, exécutez les commandes suivantes :

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Créez un fichier nommé **connectedAirConditionerAdv.js** dans le dossier `connected-air-conditioner-adv`.

1. Ajoutez les instructions `require` suivantes au début du fichier **connectedAirConditionerAdv.js** :

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Ajoutez les déclarations de variable ci-après au fichier :

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var locLong = -122.1215;
    var locLat = 47.6740;
    var client = clientFromConnectionString(connectionString);
    ```

    Remplacez l’espace réservé `{your device connection string}` par la [chaîne de connexion de l’appareil](tutorial-add-device.md#generate-connection-string). Dans cet exemple, vous initialisez `targetTemperature` à zéro, mais vous pourriez utiliser la valeur actuelle lue sur l’appareil ou une valeur du jumeau d’appareil.

1. Pour envoyer des mesures de télémétrie, d’état, d’événement et d’emplacement à votre application Azure IoT Central, ajoutez la fonction suivante au fichier :

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var locationLong = locLong - (Math.random() / 100);
      var locationLat = locLat - (Math.random() / 100);
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined,
        location: {
            lon: locationLong,
            lat: locationLat }
        });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. Pour envoyer des propriétés d’appareil à votre application Azure IoT Central, ajoutez la fonction suivante à votre fichier :

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Pour définir les paramètres auxquels votre appareil répond, ajoutez la définition suivante :

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Pour gérer les paramètres mis à jour à partir de votre application Azure IoT Central, ajoutez le code suivant au fichier :

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Ajoutez le code suivant pour gérer une commande de compte à rebours envoyée à partir de l’application IoT Central :

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');

      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;

      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          client.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }

            doCountdown();
          });
        }
      });
    }
    ```

1. Ajoutez le code ci-après pour effectuer la connexion à Azure IoT Central et raccorder les fonctions dans le code client :

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Create handler for countdown command
        client.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Exécuter votre application Node.js

Exécutez la commande suivante dans votre environnement de ligne de commande :

```cmd/sh
node connectedAirConditionerAdv.js
```

En tant qu’opérateur dans votre application Azure IoT Central, pour votre appareil réel, vous pouvez :

* Voir la télémétrie dans la page **Mesures** :

    ![Afficher les données de télémétrie](media/howto-connect-nodejs/viewtelemetry.png)

* Afficher la télémétrie sur la page **Mesures** :

    ![Afficher les mesures d’emplacement](media/howto-connect-nodejs/viewlocation.png)

* Voir les valeurs de propriété d’appareil envoyées depuis votre appareil dans la page **Propriétés**. Les vignettes de propriétés de l’appareil se mettent à jour quand l’appareil se connecte :

    ![Voir les propriétés de l’appareil](media/howto-connect-nodejs/viewproperties.png)

* Définissez la vitesse du ventilateur et la température cible dans la page **Paramètres** :

    ![Définir la vitesse du ventilateur](media/howto-connect-nodejs/setfanspeed.png)

* Appelez la commande de compte à rebours à partir de la page **Commandes** :

    ![Appeler la commande de compte à rebours](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment connecter un client Node.js générique à votre application Azure IoT Central, l’étape suivante suggérée est de découvrir comment [définir un modèle d’appareil personnalisé](howto-set-up-template.md) pour votre propre appareil IoT.
