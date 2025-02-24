---
title: Gestionnaires d’événements Azure Event Grid
description: Décrit les gestionnaires d’événements pris en charge pour Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 915d1284d66438219fc9aba893512e5f6a5b02b3
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305044"
---
# <a name="event-handlers-in-azure-event-grid"></a>Gestionnaires d’événements dans Azure Event Grid

Un gestionnaire d’événements désigne l’endroit où l’événement est envoyé. Le gestionnaire effectue des actions supplémentaires pour traiter l’événement. Plusieurs services Azure sont automatiquement configurés pour gérer les événements. Vous pouvez également utiliser n’importe quel webhook pour la gestion des événements. À cette fin, il n’est pas nécessaire que le webhook soit hébergé dans Azure. Event Grid ne prend en charge que les points de terminaison Webhook HTTPS.

Cet article fournit des liens vers du contenu pour chaque gestionnaire d’événements.

## <a name="azure-automation"></a>Azure Automation

Utilisez Azure Automation pour traiter les événements avec des runbooks automatisés.

|Intitulé  |Description  |
|---------|---------|
|[Tutoriel : Intégrer Azure Automation à Event Grid et Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Créez une machine virtuelle, qui envoie un événement. L’événement déclenche un runbook Automation qui balise la machine virtuelle et déclenche un message qui est envoyé à un canal Microsoft Teams. |

## <a name="azure-functions"></a>Azure Functions

Utilisez Azure Functions pour les réponses serverless à des événements.

Quand vous utilisez Azure Functions en tant que gestionnaire, utilisez le déclencheur Event Grid au lieu de déclencheurs HTTP génériques. Event Grid valide automatiquement les déclencheurs de fonction Event Grid. Dans le cas des déclencheurs HTTP génériques, vous devez implémenter la [réponse de validation](security-authentication.md#webhook-event-delivery).

|Intitulé  |Description  |
|---------|---------|
| [Déclencheur Event Grid pour Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Vue d’ensemble de l’utilisation du déclencheur Event Grid dans Functions. |
| [Tutoriel : Automatiser le redimensionnement des images chargées à l’aide d’Event Grid](resize-images-on-storage-blob-upload-event.md) | Les utilisateurs chargent les images par le biais de l’application web sur le compte de stockage. Quand un objet blob de stockage est créé, Event Grid envoie un événement à l’application de fonction, qui redimensionne l’image chargée. |
| [Tutoriel : Diffuser en continu des Big Data dans un entrepôt de données](event-grid-event-hubs-integration.md) | Quand Event Hubs crée un fichier Capture, Event Grid envoie un événement à une application de fonction. L’application récupère le fichier Capture et migre les données vers un entrepôt de données. |
| [Tutoriel : Exemples d’intégration d’Azure Service Bus et d’Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid envoie des messages à partir de la rubrique Service Bus à l’application de fonction et à l’application logique. |

## <a name="event-hubs"></a>Event Hubs

Utilisez Event Hubs si votre solution reçoit des événements plus vite qu’elle ne peut les traiter. Votre application traite à son propre rythme les événements d’Event Hubs. Vous pouvez mettre à l’échelle votre système de traitement des événements de façon à gérer les événements entrants.

Event Hubs peut servir de source d’événement ou de gestionnaire d’événements. L’article suivant montre comment utiliser Event Hubs comme un gestionnaire.

|Intitulé  |Description  |
|---------|---------|
| [Démarrage rapide : Acheminer des événements personnalisés vers Azure Event Hubs avec Azure CLI et Event Grid](custom-event-to-eventhub.md) | Envoie un événement personnalisé à un hub d’événements afin qu’il soit traité par une application. |
| [Modèle Resource Manager : rubrique personnalisée et point de terminaison Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Modèle Resource Manager qui crée un abonnement pour une rubrique personnalisée. Il envoie les événements vers Azure Event Hubs. |

Pour obtenir des exemples d’utilisation d’Event Hubs comme une source, consultez [Source Event Hubs](event-sources.md#event-hubs).

## <a name="hybrid-connections"></a>les connexions hybrides

Utilisez les connexions hybrides Azure Relay pour envoyer des événements aux applications se trouvant dans un réseau d’entreprise et qui ne disposent pas d’un point de terminaison accessible publiquement.

|Intitulé  |Description  |
|---------|---------|
| [Tutoriel : Envoyer des événements à une connexion hybride](custom-event-to-hybrid-connection.md) | Envoie un événement personnalisé vers une connexion hybride existante pour son traitement par une application d’écouteur. |

## <a name="logic-apps"></a>Logic Apps

Utilisez Logic Apps pour automatiser les processus métier afin de répondre aux événements.

|Intitulé  |Description  |
|---------|---------|
| [Tutoriel : Surveiller les modifications d’une machine virtuelle avec Azure Event Grid et Azure Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) | Une application logique surveille les modifications apportées à une machine virtuelle et envoie des e-mails à ce sujet. |
| [Tutoriel : Envoyer des notifications par e-mail sur des événements Azure IoT Hub à l’aide de Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Une application logique envoie un e-mail de notification chaque fois qu’un appareil est ajouté à votre hub IoT. |
| [Tutoriel : Exemples d’intégration d’Azure Service Bus et d’Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid envoie des messages à partir de la rubrique Service Bus à l’application de fonction et à l’application logique. |

## <a name="service-bus-queue-preview"></a>File d’attente du Bus de service (version préliminaire)

Utiliser Service Bus comme un gestionnaire d’événements pour acheminer vos événements dans Event Grid directement vers les files d’attente de Bus de Service pour une utilisation dans les scénarios de mise en mémoire tampon ou de commande et de contrôle dans les applications d’entreprise. La version préliminaire ne fonctionne pas avec les rubriques Service Bus et les Sessions, mais il fonctionne avec tous les niveaux de files d’attente Service Bus.

Veuillez noter que tout Service Bus comme un gestionnaire est en version préliminaire publique, vous devez installer l’extension CLI ou PowerShell lorsque utilise ces informations pour créer des abonnements aux événements.

### <a name="using-cli"></a>Utiliser l'interface CLI

Pour Azure CLI, l’exemple suivant s’abonne un se connecte et rubrique Event Grid à une file d’attente Service Bus :

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="queue-storage"></a>Stockage File d’attente

Utilisez le stockage File d’attente pour recevoir des événements qui doivent être extraits. Vous pouvez éventuellement utiliser le Stockage File d’attente pour gérer un processus long qui met trop de temps à répondre. En envoyant des événements au Stockage File d’attente, l’application peut les extraire et les traiter à son propre rythme.

|Intitulé  |Description  |
|---------|---------|
| [Démarrage rapide : Acheminer des événements personnalisés vers le Stockage File d’attente Azure avec Azure CLI et Event Grid](custom-event-to-queue-storage.md) | Décrit comment envoyer des événements personnalisés à un stockage File d’attente. |

## <a name="webhooks"></a>WebHooks

Utilisez des webhooks pour les points de terminaison personnalisables qui répondent aux événements.

|Intitulé  |Description  |
|---------|---------|
| Démarrage rapide : Créer et acheminer des événements personnalisés avec [Azure CLI](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md) et le [portail](custom-event-quickstart-portal.md). | Montre comment envoyer des événements personnalisés à un webhook. |
| Démarrage rapide : Acheminer des événements de stockage Blob vers un point de terminaison web avec [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) et le [portail](blob-event-quickstart-portal.md). | Montre comment envoyer des événements de stockage d’objets blob à un webhook. |
| [Démarrage rapide : Envoyer des événements de registre de conteneurs](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Montre comment utiliser Azure CLI pour envoyer des événements de registre de conteneurs. |
| [Vue d’ensemble : Recevoir des événements sur un point de terminaison HTTP](receive-events.md) | Décrit comment valider un point de terminaison HTTP pour recevoir des événements à partir d’un abonnement à des événements et désérialiser des événements. |

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Event Grid, consultez [À propos d’Event Grid](overview.md).
* Pour une prise en main rapide d’Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md).
