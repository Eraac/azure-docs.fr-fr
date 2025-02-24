---
title: Diffuser en continu des données en tant qu’entrées dans Azure Stream Analytics
description: Apprenez en davantage sur la configuration d’une connexion de données dans Azure Stream Analytics. Les données incluent un flux de données de partir d’événements et également des données de référence.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 1f03f9e68640edd73d2f6bb55cf205a609450658
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620500"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Diffuser en continu des données en tant qu’entrées dans Stream Analytics

Stream Analytics possède une intégration de première classe avec des flux de données Azure en tant qu’entrées provenant de trois types de ressources :

- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [stockage d’objets blob Azure](https://azure.microsoft.com/services/storage/blobs/) 

Ces ressources d’entrée peuvent résider dans le même abonnement Azure que votre travail Stream Analytics ou un autre abonnement.

### <a name="compression"></a>Compression

Stream Analytics prend en charge la compression pour toutes les sources d’entrée de flux de données. Les types de compression pris en charge sont les suivants : Aucun, GZip et Compression Deflate. La prise en charge de la compression n’est pas disponible pour les données de référence. Si le format d’entrée est des données Avro compressées, celles-ci sont gérées de façon transparente. Vous n’avez pas besoin de spécifier le type de compression avec la sérialisation Avro. 

## <a name="create-edit-or-test-inputs"></a>Créer, modifier ou tester les entrées

Vous pouvez utiliser le [Portail Azure](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md) et [Visual Studio Code](quick-create-vs-code.md) pour ajouter et afficher ou modifier des entrées existantes de votre travail de diffusion en continu. Vous pouvez également tester les connexions d’entrée et [tester les requêtes](stream-analytics-manage-job.md#test-your-query) à partir d’exemples de données du Portail Azure, de [Visual Studio](stream-analytics-vs-tools-local-run.md) et de [Visual Studio Code](vscode-local-run.md). Lorsque vous écrivez une requête, vous répertoriez l’entrée dans la clause FROM. Vous pouvez obtenir la liste des entrées disponibles à partir de la page **Requête** du portail. Si vous souhaitez utiliser plusieurs entrées, vous pouvez les `JOIN` ou écrire plusieurs requêtes `SELECT`.


## <a name="stream-data-from-event-hubs"></a>Diffuser en continu des données depuis Event Hubs

Azure Event Hubs fournit des services de réception d’événements de publication/d’abonnement hautement évolutifs. Un concentrateur Event Hub peut collecter des millions d’événements par seconde afin que vous puissiez traiter et analyser les grandes quantités de données générées par vos appareils et applications connectés. Ensemble, Event Hubs et Stream Analytics fournissent une solution complète pour des analyses en temps réel. Event Hubs vous permet d’alimenter des événements dans Azure en temps réel, et les travaux Stream Analytics peuvent traiter ces événements en temps réel. Par exemple, vous pouvez envoyer à Event Hubs des clics web, des lectures de capteurs ou des événements de journaux en ligne. Vous pouvez alors créer des travaux Stream Analytics pour utiliser Event Hubs en tant que flux de données d’entrée pour le filtrage, l’agrégation et la corrélation en temps réel.

`EventEnqueuedUtcTime` est l’horodatage de l’arrivée de l’événement dans un Event Hub et est l’horodatage par défaut des événements provenant d’Event Hubs dans Stream Analytics. Pour traiter les données en tant que flux à l’aide d’un horodatage dans la charge utile d’événement, vous devez utiliser le mot-clé [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics).

### <a name="event-hubs-consumer-groups"></a>Groupe de consommateurs Event Hubs

Vous devez configurer chaque entrée Event Hub de Stream Analytics afin qu’elle dispose de son propre groupe de consommateurs. Lorsqu’un travail contient une jointure réflexive ou plusieurs entrées, certaines entrées peuvent être lues par plusieurs lecteurs en aval. Cette situation a une incidence sur le nombre de lecteurs dans un groupe de consommateurs unique. Pour éviter de dépasser la limite des Event Hubs de cinq lecteurs par groupe de consommateurs par partition, il est recommandé de désigner un groupe de consommateurs pour chaque travail Stream Analytics. Il existe également une limite de 20 groupes de consommateurs pour un concentrateur Event Hub de niveau standard. Pour plus d’informations, consultez [Résoudre les problèmes liés aux connexions d’entrée](stream-analytics-troubleshoot-input.md).

### <a name="create-an-input-from-event-hubs"></a>Créer une entrée à partir de concentrateurs Event Hubs

Le tableau suivant décrit chaque propriété de la page **Nouvelle entrée** du portail Azure pour diffuser en continu une entrée de données depuis un Event Hub :

| Propriété | Description |
| --- | --- |
| **Alias d’entrée** |Nom convivial que vous utilisez dans la requête du travail pour faire référence à cette entrée. |
| **Abonnement** | Sélectionnez l’abonnement dans lequel se trouve la ressource du concentrateur Event Hub. | 
| **Espace de noms Event Hub** | L’espace de noms Event Hub est un conteneur pour un jeu d’entités de messagerie. Lorsque vous créez un concentrateur Event Hub, vous créez également l’espace de noms. |
| **Nom de l’Event Hub** | Nom du concentrateur Event Hub à utiliser comme entrée. |
| **Nom de la stratégie du hub d’événements** | La stratégie d’accès partagé qui fournit l’accès au concentrateur Event Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. Cette option est automatiquement renseignée, sauf si vous sélectionnez l’option pour indiquer manuellement les paramètres de l’Event Hub.|
| **Groupe de consommateurs du hub d’événements** (recommandé) | Il est vivement recommandé d’utiliser un groupe de consommateurs différent pour chaque travail Stream Analytics. Cette chaîne identifie le groupe de consommateurs à utiliser pour ingérer les données du concentrateur Event Hub. Si aucun groupe de consommateurs n’est spécifié, le travail Stream Analytics utilise le groupe de consommateurs $Default.  |
| **Format de sérialisation de l’événement** | Format de sérialisation (JSON, CSV ou Avro) du flux de données entrant.  Vérifiez que le format JSON est conforme à la spécification et n’inclut pas de 0 au début des nombres décimaux. |
| **Encodage** | Pour le moment, UTF-8 est le seul format d’encodage pris en charge. |
| **Type de compression d’événement** | Le type de compression utilisé pour lire le flux de données entrant, tel que Aucune (valeur par défaut), GZip ou Deflate. |

Si vos données proviennent d’une entrée de données d’un concentrateur Event Hub, vous avez accès aux champs de métadonnées suivants dans votre requête Stream Analytics :

| Propriété | Description |
| --- | --- |
| **EventProcessedUtcTime** |Date et heure du traitement de l’événement par Stream Analytics |
| **EventEnqueuedUtcTime** |Date et heure de la réception de l’événement par le hub d’événements. |
| **PartitionId** |ID de partition de base zéro de l’adaptateur d’entrée. |

Par exemple, en utilisant ces champs, vous pouvez écrire une requête semblable à l’exemple suivant :

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Lorsque vous utilisez Event Hub comme point de terminaison pour les itinéraires IoT Hub, vous pouvez accéder aux métadonnées IoT Hub via la [fonction GetMetadataPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue).
> 

## <a name="stream-data-from-iot-hub"></a>Diffuser en continu des données depuis IoT Hub

Azure IoT Hub est un service de réception d’événements de publication/d’abonnement hautement évolutif optimisé pour les scénarios IoT.

Le timestamp par défaut des événements provenant d’un concentrateur IoT Hub dans Stream Analytics est le timestamp de l’arrivée de l’événement dans le concentrateur IoT Hub, `EventEnqueuedUtcTime`. Pour traiter les données en tant que flux à l’aide d’un horodatage dans la charge utile d’événement, vous devez utiliser le mot-clé [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics).

### <a name="iot-hub-consumer-groups"></a>Groupe de consommateurs IoT Hub

Vous devez configurer chaque entrée IoT Hub de Stream Analytics afin qu’elle dispose de son propre groupe de consommateurs. Lorsqu’un travail contient une jointure réflexive ou s’il comporte plusieurs entrées, une entrée peut être lue par plusieurs lecteurs en aval. Cette situation a une incidence sur le nombre de lecteurs dans un groupe de consommateurs unique. Pour éviter de dépasser la limite Azure IoT Hub de cinq lecteurs par groupe de consommateurs par partition, il est recommandé de désigner un groupe de consommateurs pour chaque travail Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configurer un concentrateur IoT Hub en tant qu’entrée de flux de données

Le tableau suivant décrit chaque propriété de la page **Nouvelle entrée** du portail Azure lorsque vous configurez un concentrateur IoT Hub en tant qu’entrée de flux de données.

| Propriété | Description |
| --- | --- |
| **Alias d’entrée** | Nom convivial que vous utilisez dans la requête du travail pour faire référence à cette entrée.|
| **Abonnement** | Sélectionnez l’abonnement dans lequel se trouve la ressource du concentrateur IoT Hub. | 
| **IoT Hub** | Le nom du concentrateur IoT Hub à utiliser comme entrée. |
| **Point de terminaison** | Le point de terminaison du concentrateur IoT Hub.|
| **Nom de la stratégie d’accès partagé** | La stratégie d’accès partagé qui fournit l’accès au concentrateur IoT Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| **Clé de la stratégie d’accès partagé** | La clé d’accès partagé utilisée pour autoriser l’accès au concentrateur IoT Hub.  Cette option est automatiquement renseignée, sauf si vous sélectionnez l’option pour indiquer manuellement les paramètres du concentrateur IoT Hub. |
| **Groupe de consommateurs** | Il est vivement recommandé d’utiliser un groupe de consommateurs différent pour chaque travail Stream Analytics. Le groupe de consommateurs à utiliser pour ingérer les données du concentrateur IoT Hub. Stream Analytics utilise le groupe de consommateurs $Default, sauf si vous spécifiez autre chose.  |
| **Format de sérialisation de l’événement** | Format de sérialisation (JSON, CSV ou Avro) du flux de données entrant.  Vérifiez que le format JSON est conforme à la spécification et n’inclut pas de 0 au début des nombres décimaux. |
| **Encodage** | Pour le moment, UTF-8 est le seul format d’encodage pris en charge. |
| **Type de compression d’événement** | Le type de compression utilisé pour lire le flux de données entrant, tel que Aucune (valeur par défaut), GZip ou Deflate. |


Si vous utilisez des données de flux provenant d’un concentrateur IoT Hub, vous avez accès aux champs de métadonnées suivants dans votre requête Stream Analytics :

| Propriété | Description |
| --- | --- |
| **EventProcessedUtcTime** | Date et heure du traitement de l'événement. |
| **EventEnqueuedUtcTime** | Date et heure de la réception de l’événement par IoT Hub |
| **PartitionId** | ID de partition de base zéro de l’adaptateur d’entrée. |
| **IoTHub.MessageId** | Un ID utilisé pour corréler une communication bidirectionnelle dans un concentrateur IoT Hub. |
| **IoTHub.CorrelationId** | Un ID utilisé dans les réponses de message et les commentaires dans un concentrateur IoT Hub. |
| **IoTHub.ConnectionDeviceId** | ID d’authentification utilisé pour envoyer ce message. Cette valeur est marquée sur les messages liés aux services par le concentrateur IoT Hub. |
| **IoTHub.ConnectionDeviceGenerationId** | ID de génération de l’appareil authentifié qui a été utilisé pour envoyer ce message. Cette valeur est marquée sur les messages liés aux services par le concentrateur IoT Hub. |
| **IoTHub.EnqueuedTime** | L’heure de réception du message par le concentrateur IoT Hub. |


## <a name="stream-data-from-blob-storage"></a>Diffuser en continu des données depuis le stockage d’objets blob
Quand il est nécessaire de stocker de grandes quantités de données non structurées dans le cloud, le stockage Blob Azure offre une solution peu coûteuse et évolutive. Les données dans le stockage d’objets blob sont généralement considérées comme au repos ; les données d’objets blob peuvent toutefois être traitées comme un flux de données par Stream Analytics. 

Le traitement des journaux est un scénario couramment utilisé pour se servir des entrées de stockage d’objets blob avec Stream Analytics. Dans ce scénario, des fichiers de données de télémétrie ont été capturés à partir d’un système, et doivent être analysés et traités pour extraire des données significatives.

L’horodatage par défaut des événements de stockage d’objets blob dans Stream Analytics est l’horodatage de la dernière modification de l’objet blob, soit `BlobLastModifiedUtcTime`. Pour traiter les données en tant que flux à l’aide d’un horodatage dans la charge utile d’événement, vous devez utiliser le mot-clé [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Une tâche Stream Analytics extrait les données de l’entrée Stockage Blob Azure toutes les secondes si le fichier blob est disponible. Si le fichier blob n’est pas disponible, il existe une interruption exponentielle avec un délai maximal de 90 secondes.

Les entrées au format CSV nécessitent une ligne d’en-tête pour définir les champs du jeu de données. En outre, tous les champs de ligne d’en-tête doivent être uniques.

> [!NOTE]
> Stream Analytics ne prend pas en charge l’ajout de contenu à un objet blob existant. Stream Analytics n’affiche chaque fichier qu’une seule fois, et toutes les modifications qui sont apportées à celui-ci, une fois que le travail a lu les données, ne sont pas traitées. Une meilleure pratique consiste à télécharger toutes les données d’un objet blob en une seule fois, puis d’ajouter les événements récents supplémentaires dans un fichier blob nouveau et différent.

Dans de rares cas, lors du chargement d’un grand nombre d’objets blob, il peut arriver que Stream Analytics ignore la lecture de certains d’entre eux. Il est recommandé de charger les objets blobs à au moins 2 secondes d’intervalle les uns des autre sur le stockage Blob. Si cette option n’est pas possible, vous pouvez utiliser les concentrateurs Event Hubs pour diffuser en continu de grands volumes d’événements. 

### <a name="configure-blob-storage-as-a-stream-input"></a>Configurer un stockage d’objets blob en tant qu’entrée de flux de données 

Le tableau suivant décrit chaque propriété de la page **Nouvelle entrée** du portail Azure lorsque vous configurez un stockage d’objets blob en tant qu’entrée de flux de données.

| Propriété | Description |
| --- | --- |
| **Alias d’entrée** | Nom convivial que vous utilisez dans la requête du travail pour faire référence à cette entrée. |
| **Abonnement** | Sélectionnez l’abonnement dans lequel se trouve la ressource du concentrateur IoT Hub. | 
| **Compte de stockage** | Nom du compte de stockage dans lequel se trouvent les fichiers d’objets blob. |
| **Clé du compte de stockage** | Clé secrète associée au compte de stockage. Cette option est automatiquement renseignée, sauf si vous sélectionnez l’option pour indiquer manuellement les paramètres du stockage d’objets blob. |
| **Conteneur** | Conteneur pour les entrées d’objets blob. Les conteneurs fournissent un regroupement logique des objets blob stockés dans le service d’objets blob Microsoft Azure. Lorsque vous chargez un objet blob dans le service de stockage Blob Azure, vous devez spécifier un conteneur pour cet objet blob. Vous pouvez choisir l’option **Use existing container** (Utiliser un conteneur existant) ou **Créer nouveau**.|
| **Modèle de chemin d’accès** (facultatif) | Chemin d’accès au fichier utilisé pour localiser les objets blob dans le conteneur spécifié. Dans le chemin d’accès, vous pouvez spécifier une ou plusieurs instances de l’une des trois variables suivantes : `{date}`, `{time}` ou `{partition}`<br/><br/>Exemple 1 : `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exemple 2 : `cluster1/logs/{date}`<br/><br/>Le caractère `*` n’est pas une valeur autorisée pour le préfixe du chemin d’accès. Seuls les <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caractères d’objet blob Azure</a> valides sont autorisés. N’incluez pas de noms de conteneurs ou de fichiers. |
| **Format de date** (facultatif) | Format de date suivant lequel les fichiers sont organisés si vous utilisez la variable de date dans le chemin d’accès. Exemple : `YYYY/MM/DD` |
| **Format d’heure** (facultatif) |  Format d’heure suivant lequel les fichiers sont organisés si vous utilisez la variable d’heure dans le chemin d’accès. Actuellement, la seule valeur possible est `HH` pour les heures. |
| **Format de sérialisation de l’événement** | Format de sérialisation (JSON, CSV ou Avro) du flux de données entrant.  Vérifiez que le format JSON est conforme à la spécification et n’inclut pas de 0 au début des nombres décimaux. |
| **Encodage** | Pour CSV et JSON, UTF-8 est le seul format d’encodage actuellement pris en charge. |
| **Compression** | Le type de compression utilisé pour lire le flux de données entrant, tel que Aucune (valeur par défaut), GZip ou Deflate. |

Si vos données proviennent d’une source de stockage d’objets blob, vous avez accès aux champs de métadonnées suivants dans votre requête Stream Analytics :

| Propriété | Description |
| --- | --- |
| **BlobName** |Nom de l’objet blob d’entrée d’où provient l’événement. |
| **EventProcessedUtcTime** |Date et heure du traitement de l’événement par Stream Analytics |
| **BlobLastModifiedUtcTime** |Date et heure de la dernière modification apportée à l’objet blob. |
| **PartitionId** |ID de partition de base zéro de l’adaptateur d’entrée. |

Par exemple, en utilisant ces champs, vous pouvez écrire une requête semblable à l’exemple suivant :

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Démarrage rapide : Créer un travail Stream Analytics à l’aide du portail Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
