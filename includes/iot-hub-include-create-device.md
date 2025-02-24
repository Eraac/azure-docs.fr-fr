---
title: Fichier Include
description: Fichier Include
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d70544866b9e321d98acd3978da145276e036025
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177162"
---
<!-- put the ## header in the file that includes this file -->

Dans cette section, vous allez créer une identité d’appareil dans le registre des identités de votre hub IoT. Un appareil ne peut pas se connecter à IoT Hub, à moins de posséder une entrée dans le registre des identités. Pour plus d’informations, consultez la section « Registre des identités » du [Guide du développeur IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md). 

1. Dans le menu de navigation de votre hub IoT, ouvrez **Appareils IoT**, puis sélectionnez **Ajouter** pour inscrire un nouvel appareil dans votre hub IoT.

    ![Créer une identité d’appareil sur le portail](./media/iot-hub-include-create-device/create-identity-portal.png)

1. Indiquez un nom pour votre nouvel appareil, par exemple **myDeviceId**, puis sélectionnez **Enregistrer**. Cette action permet de créer une identité d’appareil pour votre IoT Hub.

   ![Ajouter un nouvel appareil](./media/iot-hub-include-create-device/create-a-device.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]


1. Une fois l’appareil créé, ouvrez ce dernier à partir de la liste figurant dans le volet **Appareils IoT**. Copiez la **Chaîne de connexion – clé primaire** pour une utilisation ultérieure.

    ![Chaîne de connexion de l’appareil](./media/iot-hub-include-create-device/device-details.png)

> [!NOTE]
> Le registre des identités IoT Hub stocke uniquement les identités des appareils pour permettre un accès sécurisé à IoT Hub. Il stocke les ID des appareils et les clés à utiliser comme informations d’identification de sécurité et un indicateur activé/désactivé que vous pouvez utiliser pour désactiver l’accès pour un appareil individuel. Si votre application a besoin de stocker d’autres métadonnées spécifiques aux appareils, elle doit utiliser un magasin spécifique aux applications. Pour plus d’informations, reportez-vous au [Guide du développeur IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
