---
title: Limites de service de la préversion publique d’Azure Digital Twins | Microsoft Docs
description: Découvrez les limites de service de la préversion publique d’Azure Digital Twins.
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: dwalther
ms.openlocfilehash: f3239fa5d21078795e7b063cb0364e905cba25c1
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846620"
---
# <a name="public-preview-service-limits"></a>Limites de service de la préversion publique

Dans la préversion publique, Azure Digital Twins présente les limites temporaires suivantes au niveau des abonnements, des instances et des taux.

Ces limites ont pour but de simplifier l’apprentissage du nouveau service et de ses nombreuses fonctionnalités.

> [!NOTE]
> Ces limites sont réduites ou supprimées dans la version en disponibilité générale (GA).

## <a name="per-subscription-limits"></a>Limites par abonnement

Dans la préversion publique, chaque abonnement Azure ne peut créer ou exécuter qu’une seule instance d’Azure Digital Twins à la fois.

> [!TIP]
> Si vous supprimez votre instance, vous pouvez en créer une nouvelle.

## <a name="per-instance-limits"></a>Limites par instance

Chaque instance d’Azure Digital Twins peut comprendre :

- Une ressource **IoTHub** intégrée créée automatiquement lors de la configuration du service.
- Exactement un point de terminaison **EventHub** pour le type d’événement **DeviceMessage**.
- Jusqu’à trois points de terminaison **EventHub**, **ServiceBus** ou **EventGrid** du type d’événement **SensorChange**, **SpaceChange** , **TopologyOperation** ou **UdfCustom**.

> [!NOTE]
> Certains paramètres qui sont généralement définies lors de la création des entités Azure IoT ci-dessus ne sont pas nécessaires pendant la préversion publique.
> - Pour connaître les spécifications d’API les plus récentes, consultez la [documentation de référence de Swagger](./how-to-use-swagger.md).

## <a name="azure-digital-twins-management-api-limits"></a>Limites de l’API de gestion Azure Digital Twins

Les limites de taux de requêtes pour votre API de gestion Azure Digital Twins sont les suivantes :

- 100 requêtes par seconde à l’API de gestion Azure Digital Twins.
- Une requête d’API de gestion Azure Digital Twins peut retourner jusqu’à 1 000 objets.

> [!IMPORTANT]
> Si vous dépassez la limite de 1 000 objets, vous recevez un message d’erreur et devez simplifier votre requête.

## <a name="user-defined-functions-rate-limits"></a>Limites du taux de fonctions définies par l’utilisateur

Les limites suivantes définissent le nombre total d’appels de fonction définie par l’utilisateur qui peuvent être effectués vers votre instance d’Azure Digital Twins :

- 400 appels de bibliothèque cliente par seconde
- 100 appels **SendNotification** par seconde

> [!NOTE]
> D’autres limites temporaires peuvent être appliquées aux actions suivantes :
> - Modifications apportées aux métadonnées de l’objet de topologie
> - Mises à jour apportées à la définition de fonction définie par l’utilisateur
> - Premier envoi de données de télémétrie par des appareils

## <a name="device-telemetry-limits"></a>Limites de télémétrie des appareils

Les limites suivantes restreignent le nombre total de messages que vos appareils peuvent envoyer à votre instance d’Azure Digital Twins :

- 100 messages par seconde

## <a name="next-steps"></a>Étapes suivantes

- Pour tester un exemple Azure Digital Twins, consultez [Démarrage rapide : Rechercher des salles disponibles](./quickstart-view-occupancy-dotnet.md).