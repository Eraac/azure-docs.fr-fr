---
title: Détection des messages dupliqués dans Azure Service Bus | Microsoft Docs
description: Détecter les messages dupliqués dans Service Bus
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 286c5850400242224e710a7883d3d3dc175cef12
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273207"
---
# <a name="duplicate-detection"></a>Détection des doublons

Si une application échoue en raison d’une erreur irrécupérable immédiatement après avoir envoyé un message, et que l’instance d’application redémarrée considère à tort que le message précédent n’a pas été remis, l’opération d’envoi suivante entraîne le réaffichage du même message dans le système.

Il est également possible qu’une erreur se produise sur le client ou le réseau à un moment antérieur, et qu’un message envoyé soit confirmé dans la file d’attente, avec l’accusé de réception non retourné au client. Dans ce scénario, le client ne connait pas avec certitude le résultat de l’opération d’envoi.

La détection des doublons lève ce type d’incertitude en permettant à l’expéditeur de renvoyer le même message, et à la file d’attente ou la rubrique d’ignorer les copies en double.

L’activation de la détection des doublons vous aide à effectuer le suivi du *MessageId*, contrôlé par l’application, de chaque message envoyé dans une file d’attente ou une rubrique pendant une fenêtre de temps spécifiée. Si un nouveau message est envoyé avec un *MessageId* journalisé pendant la fenêtre de temps, le message est signalé comme étant accepté (réussite de l’opération d’envoi), mais le message qui vient d’être envoyé est instantanément ignoré et supprimé. Excepté le *MessageId*, aucune autre partie du message n’est prise en compte.

Le contrôle de l’identificateur par l’application est essentiel, car lui seul permet à l’application d’associer l’identificateur *MessageId* à un contexte de processus métier à partir duquel il peut être reconstruit en cas d’échec.

Pour un processus métier dans lequel plusieurs messages sont envoyés durant le traitement d’un contexte d’application, *MessageId* peut se composer de l’identificateur de contexte de l’application, par exemple un numéro de bon de commande, et de l’objet du message, par exemple, **12345.2017/paiement**.

*MessageId* peut aussi être un GUID, mais associer l’identificateur au processus métier offre l’avantage de fournir une répétabilité prévisible, ce qui permet de tirer pleinement parti de la fonctionnalité de détection des doublons.

> [!NOTE]
> Si la déduction dupliquée est activée et que la clé de partition ou l’ID de session n’est pas définie, l’ID du message est utilisé comme clé de partition. Si l’ID du message n’est pas défini, les bibliothèques .NET et AMQP génèrent automatiquement un ID de message pour le message. Pour plus d’informations, consultez l’article [Utiliser des clés de partition](service-bus-partitioning.md#use-of-partition-keys).

## <a name="enable-duplicate-detection"></a>Activer la détection des doublons

Dans le portail, activez la fonctionnalité au moment de la création de l’entité en cochant la case **Activer la détection dupliquée**, qui est désélectionnée par défaut. Utilisez le même paramètre pour les rubriques que vous créez.

![][1]

> [!IMPORTANT]
> Vous ne pouvez pas activer/désactiver la détection des doublons après la création de la file d’attente. Vous ne pouvez le faire qu’au moment de la création de la file d’attente. 

Par programmation, définissez l’indicateur avec la propriété [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) sur l’API .NET Framework complète. Avec l’API Azure Resource Manager, la valeur se définit avec la propriété [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values).

L’historique de la durée de détection des doublons est de 30 secondes par défaut pour les files d’attente et les rubriques, et de sept jours au maximum. Vous pouvez modifier ce paramètre dans la fenêtre des propriétés de file d’attente et de rubrique dans le portail Azure.

![][2]

Par programmation, vous pouvez définir la taille de la fenêtre de détection des doublons pendant laquelle les ID des messages sont conservés, à l’aide de la propriété [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) dans l’API .NET Framework complète. Avec l’API Azure Resource Manager, la valeur se définit avec la propriété [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values).

L’activation de la détection des doublons et la taille de la fenêtre ont un impact direct sur le débit des files d’attente (et des rubriques), car tous les ID de messages enregistrés doivent être vérifiés par rapport à l’identificateur de message qui vient d’être envoyé.

En maintenant la fenêtre à une petite taille, vous avez moins d’ID de messages à conserver et à vérifier, et l’impact sur le débit reste ainsi limité. Pour les entités à débit élevé qui nécessitent la détection des doublons, essayez de garder la fenêtre aussi petite que possible.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la messagerie Service Bus, consultez les articles suivants :

* [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Utilisation des rubriques et abonnements Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
