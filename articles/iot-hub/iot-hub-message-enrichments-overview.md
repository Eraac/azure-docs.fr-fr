---
title: Vue d’ensemble des enrichissements de messages Azure IoT Hub
description: Vue d’ensemble des enrichissements de messages Azure IoT Hub
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 13e35ab93fc37541548785c6355489eaf3a3efc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754551"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>Enrichissements de messages pour les messages IoT Hub-appareil-à-cloud (préversion)

Les *enrichissements de messages* sont la capacité du IoT Hub à *horodater* des messages avec des informations supplémentaires avant que les messages ne soient envoyés au point de terminaison désigné. Vous pouvez utiliser les enrichissements de messages pour inclure des données pouvant servir à simplifier le traitement en aval. Par exemple, enrichir des messages de télémétrie d’appareil avec une balise de jumeau d’appareil peut réduire la charge de clients en leur évitant d’effectuer des appels d’API doubles pour cette information.

![Flux des enrichissements de messages](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Un enrichissement de message comporte trois éléments clés :

* Clé ou nom de l’enrichissement

* Une valeur

* Un ou plusieurs [points de terminaison](iot-hub-devguide-endpoints.md) pour lequel l’enrichissement doit être appliqué.

N’importe quelle chaîne peut être utilisée en tant que clé.

La valeur peut être l’un des exemples suivants :

* N’importe quelle chaîne statique. Des valeurs dynamiques telles que des conditions, une logique et des fonctions ne sont pas autorisées. Par exemple, si vous développez une application SaaS utilisée par plusieurs clients, vous pouvez attribuer un identificateur à chaque client et rendre cet identificateur disponible dans l’application. Lorsque l’application s’exécute, IoT Hub horodate les messages de télémétrie de l’appareil avec l’identificateur du client, ce qui permet de traiter les messages différemment pour chaque client.

* Informations du jumeau d’appareil, comme son chemin d’accès. Des exemples seraient *$twin.tags.field* et *$twin.tags.latitude*.

* Le nom de l’IoT Hub qui envoie le message. Cette valeur est *$iothubname*.

## <a name="applying-enrichments"></a>Application des enrichissements

Les messages peuvent provenir de n’importe quelle source de données prise en charge par [le routage des messages IoT Hub](iot-hub-devguide-messages-d2c.md), y compris les exemples suivants :

* données de télémétrie de l’appareil comme la température ou la pression
* notifications de modification du jumeau d’appareil, modifications du jumeau d’appareil
* événements du cycle de vie de l’appareil, tels que la date de la création ou de la suppression de l’appareil

Vous pouvez ajouter des enrichissements aux messages destinés au point de terminaison intégré d’un IoT Hub, ou aux messages acheminés vers des points de terminaison personnalisés comme le stockage d’objets blob Azure, une file d’attente Service Bus ou une rubrique Service Bus.

Vous pouvez également ajouter des enrichissements aux messages qui sont publiés dans Event Grid en sélectionnant Event Grid comme point de terminaison. Pour plus d’informations, consultez [React to IoT Hub events by using Event Grid to trigger actions](iot-hub-event-grid.md) (Réagir aux événements IoT Hub en utilisant Event Grid pour déclencher des actions).

Les enrichissements sont appliqués par point de terminaison. Si vous spécifiez cinq enrichissements à horodater pour un point de terminaison spécifique, tous les messages acheminés vers ce point de terminaison sont horodatés avec les cinq mêmes enrichissements.

Pour essayer les enrichissements de messages, consultez le [didacticiel dédié aux enrichissements de messages](tutorial-message-enrichments.md)

## <a name="limitations"></a>Limites

* Vous pouvez ajouter jusqu’à 10 enrichissements par IoT Hub pour ces hubs dans le niveau de base ou standard. Pour les IoT Hubs dans le niveau gratuit, vous pouvez ajouter jusqu’à 2 enrichissements.

* Dans certains cas, si vous appliquez un enrichissement avec une valeur définie sur une balise ou une propriété dans le jumeau d’appareil, la valeur sera horodatée en tant que valeur de chaîne. Par exemple, si une valeur de l’enrichissement est définie sur $twin.tags.field, les messages sont horodatés avec la chaîne « $twin.tags.field » plutôt qu’avec la valeur de ce champ de l’appareil jumeau. Cela se produit dans les cas suivants :

   * Votre IoT Hub est dans le niveau de base. Les IoT Hubs de niveau de base ne prennent pas en charge les jumeaux d’appareil.

   * Votre IoT Hub est dans le niveau standard, mais l’appareil qui envoie le message n’a aucun jumeau d’appareil.

   * Votre IoT Hub est dans le niveau standard, mais le chemin d’accès de l’appareil jumeau utilisé pour la valeur de l’enrichissement n’existe pas. Par exemple, si la valeur de l’enrichissement est définie sur $twin.tags.location et que le jumeau d’appareil n’a pas de propriété location dans les balises, le message est horodaté avec la chaîne « $twin.tags.location ». 

* Les mises à jour d’un jumeau d’appareil peuvent prendre jusqu’à cinq minutes pour apparaître dans la valeur d’enrichissement correspondante.

* La taille totale du message, y compris des enrichissements, ne peut pas dépasser 256 Ko. Si la taille d’un message dépasse 256 Ko, l’IoT Hub supprimera le message. Vous pouvez utiliser les [métriques IoT Hub](iot-hub-metrics.md) pour identifier et déboguer des erreurs lorsque les messages sont supprimés. Par exemple, vous pouvez suivre d2c.telemetry.egress.invalid.

## <a name="pricing"></a>Tarifs

Les enrichissements de messages sont disponibles sans frais supplémentaires. Actuellement, vous êtes facturé lorsque vous envoyez un message à un IoT Hub. Vous n’êtes facturé qu’une fois pour ce message, même s’il est envoyé à plusieurs points de terminaison.

## <a name="availability"></a>Disponibilité

Cette fonctionnalité est disponible en préversion et dans toutes les régions à l’exception des suivantes : USA Est, USA Ouest, Europe Ouest, [Azure Government](/azure/azure-government/documentation-government-welcome), [Azure China 21Vianet](/azure/china) et [Azure Allemagne](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour plus d’informations sur le routage des messages vers un IoT Hub :

* [Use IoT Hub message routing to send device-to-cloud messages to different endpoints](iot-hub-devguide-messages-d2c.md) (Utiliser le routage des messages IoT Hub pour envoyer des messages appareil-à-cloud à différents points de terminaison)

* [Tutoriel : Routage d’IoT Hub](tutorial-routing.md)