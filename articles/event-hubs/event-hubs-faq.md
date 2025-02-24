---
title: Forum Aux Questions (FAQ) - Azure Event Hubs | Microsoft Docs
description: Cet article contient une liste des questions fréquemment posées (FAQ) sur Azure Event Hubs, ainsi que leurs réponses.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 05/15/2019
ms.author: shvija
ms.openlocfilehash: e1ec6987f1a142e9bf9cd4413cfb4444bde1b7dd
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796995"
---
# <a name="event-hubs-frequently-asked-questions"></a>Forum Aux Questions (FAQ) sur Event Hubs

## <a name="general"></a>Généralités

### <a name="what-is-an-event-hubs-namespace"></a>Qu’est-ce qu’un espace de noms Event Hubs ?
Un espace de noms est un conteneur d’étendue pour les rubriques Event Hub/Kafka. Il vous donne une valeur unique de [nom de domaine complet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Un espace de noms constitue un conteneur d’application qui peut héberger plusieurs rubriques Event Hub/Kafka. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>À quel moment puis-je créer un espace de noms plutôt que d’en utiliser un ?
Les allocations de capacité ([unités de débit (TU)](#throughput-units)) sont facturées au niveau de l’espace de noms. Un espace de noms est également associé à une région.

Peut-être préférerez-vous créer un espace de noms plutôt que d’en utiliser un dans l’un des scénarios suivants : 

- Vous avez besoin d’un Event Hub associé à une nouvelle région.
- Vous avez besoin d’un Event Hub associé à un autre abonnement.
- Vous avez besoin d’un Event Hub avec une allocation de capacité distincte (autrement dit, la capacité nécessaire pour l’espace de noms avec l’Event Hub ajouté dépasse le seuil de 40 TU et vous ne souhaitez pas utiliser le cluster dédié).  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Quelle est la différence entre les niveaux De Base et Standard pour les hubs d’événements ?

Le niveau Standard d’Azure Event Hubs fournit des fonctionnalités supérieures à celles disponibles dans le niveau de base. Les fonctionnalités suivantes sont incluses dans le niveau Standard :

* Rétention plus longue des événements
* Connexions réparties supplémentaires, avec frais supplémentaires pour le dépassement du nombre inclus
* Plusieurs [groupes de consommateurs](event-hubs-features.md#consumer-groups)
* [Capture](event-hubs-capture-overview.md)
* [Intégration de Kafka](event-hubs-for-kafka-ecosystem-overview.md)

Pour plus d’informations sur les niveaux tarifaires, notamment pour Event Hubs Dedicated, consultez la rubrique [Informations tarifaires pour Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Où Azure Event Hubs est-il disponible ?

Azure Event Hubs est disponible dans toutes les régions Azure prises en charge. Pour obtenir une liste, visitez la page [Régions Azure](https://azure.microsoft.com/regions/).  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Puis-je utiliser une connexion AMQP unique pour l’envoi et la réception à partir de plusieurs concentrateurs d’événements ?

Oui, à condition que la totalité des concentrateurs d’événements se trouve dans le même espace de noms.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Quelle est la période de rétention maximale pour les événements ?

Le niveau Standard des hubs d’événements prend actuellement en charge une période de rétention maximale de sept jours. Les concentrateurs d’événements ne sont pas destinés à servir de magasin de données permanent. Les périodes de rétention supérieures à 24 heures sont destinées aux scénarios dans lesquels il est utile de pouvoir reproduire un flux d'événements sur les mêmes systèmes ; par exemple, pour tester ou vérifier un nouveau modèle d’apprentissage de machine sur des données existantes. Si vous avez besoin que la rétention des messages dure plus de sept jours, activez [Event Hubs Capture](event-hubs-capture-overview.md) dans votre hub d’événements pour en tirer (pull) les données et les importer dans le compte de stockage ou le compte Azure Data Lake Service de votre choix. L’activation de Capture est facturée en fonction des unités de débit achetées.

### <a name="how-do-i-monitor-my-event-hubs"></a>Comment puis-je surveiller mes Event Hubs ?
Event Hubs émet des métriques exhaustives qui fournissent l’état de vos ressources à [Azure Monitor](../azure-monitor/overview.md). Elles vous permettent également d’évaluer l’intégrité globale du service Event Hubs non seulement au niveau de l’espace de noms mais également au niveau de l’entité. En savoir plus sur la supervision proposée pour les [Azure Event Hubs](event-hubs-metrics-azure-monitor.md).

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Quels ports du pare-feu dois-je ouvrir ? 
Vous pouvez utiliser les protocoles suivants avec Azure Service Bus pour envoyer et recevoir des messages :

- Advanced Message Queuing Protocol (AMQP)
- HTTP
- Apache Kafka

Consultez le tableau suivant pour savoir quels ports de sortie vous devez ouvrir afin d’utiliser ces protocoles dans le but de communiquer avec Azure Event Hubs. 

| Protocole | Ports | Détails | 
| -------- | ----- | ------- | 
| AMQP | 5671 et 5672 | Consultez le [Guide du protocole AMQP](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Voir [Utiliser Azure Event Hubs à partir d’applications Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Quelles adresses IP dois-je ajouter à la liste verte ?
Pour trouver les adresses IP à ajouter à la liste verte de vos connexions, procédez comme suit :

1. Exécutez la commande suivante depuis une invite de commandes : 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Notez l’adresse IP renvoyée dans `Non-authoritative answer`. La seule modification susceptible d’entraîner une conséquence serait une restauration de l’espace de noms sur un autre cluster.

Si vous utilisez la redondance de zone pour votre espace de noms, vous devez suivre quelques étapes supplémentaires : 

1. Tout d’abord, exécutez nslookup sur l’espace de noms.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Notez le nom dans la section **Réponse ne faisant pas autorité**, qui se présente dans l’un des formats suivants : 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Exécutez nslookup pour chacun d’eux avec des suffixes s1, s2 et s3 pour obtenir les adresses IP des 3 instances en cours d’exécution dans 3 zones de disponibilité. 

## <a name="apache-kafka-integration"></a>Intégration d’Apache Kafka

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Comment intégrer mon application Kafka existante dans Event Hubs ?
Event Hubs fournit un point de terminaison Kafka qui peut être utilisé par vos applications Apache Kafka. Pour bénéficier de l’expérience PaaS Kafka, il est nécessaire de modifier la configuration. Il fournit une alternative à l’exécution de votre cluster Kafka. Event Hubs prend en charge Apache Kafka 1.0 et les versions clientes plus récentes, et fonctionne avec vos applications, outils et frameworks Kafka. Pour plus d’informations, consultez [Dépôt Event Hubs pour Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Quels changements de configuration doivent être effectués pour que mon application existante puisse communiquer avec les Event Hubs ?
Pour vous connecter à un Event Hub compatible avec Kafka, vous devez mettre à jour les configurations du client Kafka. Cela est possible en créant un espace de noms de Hubs d’événements et en obtenant la [chaîne de connexion](event-hubs-get-connection-string.md). Modifiez le fichier bootstrap.servers pour qu’il pointe vers le FQDN Event Hubs et sur le port 9093. Mettez à jour le fichier sasl.jaas.config pour diriger le client Kafka vers votre point de terminaison Kafka Event Hubs (qui est la chaîne de connexion que vous avez obtenue), avec une authentification correcte comme indiqué ci-dessous :

bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

Exemple :

bootstrap.servers=dummynamespace.servicebus.windows.net:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=";

Remarque : Si sasl.jaas.config n’est pas une configuration prise en charge dans votre framework, recherchez les configurations qui sont utilisées pour définir le nom d’utilisateur et le mot de passe SASL, et utilisez-les à la place. Définissez le nom d’utilisateur sur $ConnectionString et le mot de passe sur votre chaîne de connexion Event Hubs.

### <a name="what-is-the-messageevent-size-for-kafka-enabled-event-hubs"></a>Quelle est la taille du message ou de l’événement pour les Event Hubs compatibles avec Kafka ?
La taille maximale autorisée pour les Event Hubs compatibles avec Kafka est de 1 Mo.

## <a name="throughput-units"></a>Unités de débit

### <a name="what-are-event-hubs-throughput-units"></a>Que sont les unités de débit des hubs d'événements ?
Le débit dans les Event Hubs définit la quantité de données en méga-octets ou le nombre (en milliers) d’événements de 1 Ko qui entrent et sortent par les Event Hubs. Ce débit est mesuré en unités de débit (TU). Achetez des unités de débit pour pouvoir utiliser le service Event Hubs. Vous pouvez sélectionner explicitement les unités de débit Event Hubs en utilisant soit le portail, soit les modèles Resource Manager Event Hubs. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Les unités de débit s’appliquent-elles à tous les hubs d’événements dans un espace de noms ?
Oui, les unités de débit s’appliquent à tous les hubs d’événements dans un espace de noms Event Hubs. Cela signifie que vous achetez des unités de débit au niveau de l’espace de noms et que vous les partagez entre les Event Hubs sous cet espace de noms. Chaque TU donne accès à l’espace de noms pour les fonctionnalités suivantes :

- Jusqu’à 1 Mo par seconde d’événements d’entrée (événements envoyés à un concentrateur d’événements), mais pas plus de 1 000 événements d’entrée, opérations de gestion ou appels d’API de contrôle par seconde.
- Jusqu’à 2 Mo par seconde d’événements de sortie (événements consommés à partir d’un hub d’événements), mais pas plus de 4 096 événements de sortie.
- Jusqu’à 84 Go de stockage d’événements (suffisant pour la période de rétention de 24 heures par défaut).

### <a name="how-are-throughput-units-billed"></a>Comment les unités de débit sont-elles facturées ?
Les unités de débit des hubs d’événements sont facturées par heure. La facturation est basée sur le nombre maximal d’unités sélectionné pendant l’heure donnée. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Comment puis-je optimiser l’utilisation des unités de débit ?
Vous pouvez commencer avec une seule unité de débit (TU) et activer [la majoration automatique](event-hubs-auto-inflate.md). La fonctionnalité de majoration automatique fonctionnalité vous permet d’augmenter le nombre d’unités de débit à mesure que votre trafic/charge utile augmente. Vous pouvez également définir une limite supérieure pour le nombre d’unités de débit.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Comment la fonctionnalité Majoration automatique Event Hubs opère-t-elle ?
La fonctionnalité de majoration automatique vous permet de mettre à l’échelle les unités de débit. Cela signifie que vous pouvez commencer par acheter des unités de débit basses et que la majoration automatique fait monter en puissance à mesure que votre entrée augmente. Il s’agit d’une option rentable qui vous donne le contrôle complet du nombre de TU à gérer. Cette fonctionnalité est une fonctionnalité de **montée en puissance uniquement**, mais vous pouvez contrôler la descente en puissance en modifiant le nombre de TU. 

Vous pouvez commencer avec un faible nombre d’unités de débit, par exemple, 2 TU. Si vous prévoyez que votre trafic peut atteindre 15 unités de débit, activez la fonctionnalité de majoration automatique sur votre espace de noms et fixez la limite maximale à 15 unités de débit. Vous pouvez maintenant augmenter le nombre de TU automatiquement dès que votre trafic augmente.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>L’activation de la fonctionnalité de majoration automatique est-elle payante ?
**Aucun coût** n’est associé à l’activation de cette fonctionnalité. 

### <a name="how-are-throughput-limits-enforced"></a>Comment les limites d’unités de débit sont-elles appliquées ?
Si le débit d’entrée total ou le taux d’événements d’entrée total de tous les concentrateurs d’événements d’un espace de noms dépassent les allocations d’unité de débit agrégées, les expéditeurs sont limités et reçoivent des erreurs indiquant que le quota d’entrée a été dépassé.

Si le débit de sortie total ou le taux d’événements de sortie total de tous les concentrateurs d’événements d’un espace de noms dépassent les allocations d’unité de débit agrégées, les récepteurs sont limités et reçoivent des erreurs indiquant que le quota de sortie a été dépassé. Les quotas d’entrée et de sortie sont appliqués séparément afin qu’aucun expéditeur n’entraîne un ralentissement de la consommation d’événements ni qu’aucun récepteur n’empêche l’envoi d’événements à un concentrateur d’événements.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Y a-t-il une limite au nombre d’unités de débit (TU) qui peuvent être réservées/sélectionnées ?
Sur une offre mutualisée, les unités de débit peuvent atteindre 40 TU (vous pouvez sélectionner jusqu’à 20 TU dans le portail et augmenter un ticket de support pour le porter à 40 TU sur le même espace de noms). Au-delà de 40 TU, Event Hubs propose le modèle basé sur les ressources/capacités appelé **Clusters dédiés Event Hubs**. Les clusters dédiés sont vendus en unités de capacité (CU).

## <a name="dedicated-clusters"></a>Clusters dédiés

### <a name="what-are-event-hubs-dedicated-clusters"></a>Que sont les clusters Event Hubs Dedicated ?
Les clusters Event Hubs Dedicated offrent des déploiements à locataire unique pour les clients les plus exigeants. Cette offre constitue un cluster basé sur la capacité qui n’est pas liée par des unités de débit. Cela signifie que vous pouvez utiliser le cluster pour ingérer et diffuser vos données en continu en fonction de l’utilisation de l’UC et de la mémoire du cluster. Pour plus d’informations, consultez [Clusters Event Hubs Dedicated](event-hubs-dedicated-overview.md).

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Qu’est-ce qu’une seule unité de capacité me permet d’atteindre ?
Pour un cluster dédié, la quantité que vous pouvez ingérer et diffuser dépend de divers facteurs tels que vos producteurs, les consommateurs, la vitesse à laquelle vous ingérez et transformez, et bien plus encore. 

Le tableau suivant présente les résultats de référence que nous avons obtenus au cours de nos tests :

| Forme de la charge utile | Récepteurs | Bande passante en entrée| Messages en entrée | Bande passante en sortie | Messages en sortie | Nombre total d’unités de débit | Unités de débit par unité de capacité |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Lots de 100x1 Ko | 2 | 400 Mo/s | 400 000 msg/s | 800 Mo/s | 800 000 msg/s | 400 unités de débit | 100 unités de débit | 
| Lots de 10x10 Ko | 2 | 666 Mo/s | 66 600 msg/s | 1,33 Go/s | 133 000 msg/s | 666 unités de débit | 166 unités de débit |
| Lots de 6x32 Ko | 1 | 1,05 Go/s | 34 000 msg/s | 1,05 Go/s | 34 000 msg/s | 1 000 unités de débit | 250 unités de débit |

Lors des tests, les critères suivants ont été utilisés :

- Un cluster Event Hubs dédié avec quatre unités de capacité (CU) a été utilisé. 
- Le hub d’événements utilisé pour l’ingestion possédait 200 partitions. 
- Les données ingérées ont été reçues par deux applications réceptrices provenant de toutes les partitions.

Les résultats vous donnent une idée de ce qui peut être réalisé avec un cluster Event Hubs dédié. De plus, un cluster dédié est fourni avec la fonctionnalité Event Hubs Capture activée pour vos scénarios de micro-batching et de rétention à long terme.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Comment créer un cluster Event Hubs Dedicated ?
Vous créez un cluster dédié Event Hubs en soumettant une [requête de support d’augmentation du quota](https://portal.azure.com/#create/Microsoft.Support) ou en contactant l’[équipe Event Hubs](mailto:askeventhubs@microsoft.com). Le déploiement et la mise à disposition de votre cluster demandent généralement deux semaines. Ce processus est temporaire jusqu’à ce qu’un libre-service complet soit disponible via le portail Azure ou les modèles Azure Resource Manager, ce qui prend environ deux heures pour déployer le cluster.

## <a name="best-practices"></a>Bonnes pratiques

### <a name="how-many-partitions-do-i-need"></a>De combien de partitions ai-je besoin ?

Le nombre de partitions sur un hub d’événements ne peut pas être modifié après configuration. Avec cela à l’esprit, il est important de réfléchir au nombre de partitions dont vous avez besoin avant de commencer. 

Event Hubs est conçu pour autoriser un lecteur de partition unique par groupe de consommateurs. Dans la plupart des cas, le paramètre par défaut de quatre partitions est suffisant. Si vous cherchez mettre à l’échelle le traitement des événements, vous souhaiterez peut-être ajouter des partitions supplémentaires. Il n’y a aucune limite de débit spécifique sur une partition, mais le débit cumulé dans votre espace de noms est limité par le nombre d’unités de débit. Lorsque vous augmentez le nombre d’unités de débit de votre espace de noms, vous souhaiterez peut-être avoir des partitions supplémentaires pour permettre aux lecteurs fonctionnant simultanément d’atteindre leur débit maximal.

Toutefois, si vous disposez d’un modèle dans lequel votre application a une affinité avec une partition particulière, l’augmentation du nombre de partitions pourrait n’offrir aucun avantage. Pour plus d’informations, consultez [Disponibilité et cohérence](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Tarifs

### <a name="where-can-i-find-more-pricing-information"></a>Où puis-je obtenir des informations complémentaires sur la tarification ?

Pour des informations complètes sur la tarification des hubs d’événements, consultez la rubrique [Tarification des hubs d’événements](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Existe-t-il des frais pour la rétention de plus de 24 heures des événements de hubs d'événements ?

Le niveau Standard Event Hubs permet de conserver les messages au-delà de 24 heures, avec un maximum de sept jours. Si la taille du nombre total d’événements stockés dépasse l’allocation de stockage pour le nombre d’unités de débit sélectionnées (84 Go par unité de débit), la taille excédentaire est facturée au tarif de stockage Blob Azure publié. L'allocation de stockage dans chaque unité de débit couvre tous les frais de stockage pour les périodes de rétention de 24 heures (par défaut), même si l'unité de débit est utilisée pour l'allocation d'entrée maximale.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Comment la taille de stockage des hubs d'événements est-elle calculée et facturée ?

La taille totale de tous les événements stockés, notamment de toute surcharge interne pour les en-têtes d’événements ou les structures de stockage sur disque dans tous les concentrateurs d’événements, est mesurée tout au long de la journée. À la fin de la journée, la taille maximale de stockage est calculée. L'allocation de stockage quotidienne est calculée en fonction du nombre minimal d'unités de débit qui ont été sélectionnées pendant la journée (chaque unité de débit fournit une allocation de 84 Go). Si la taille totale dépasse l'allocation de stockage quotidienne calculée, l'excédent de stockage est facturé selon les taux de stockage Azure Blob (au taux **Stockage localement redondant** ).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Comment les événements d'entrée des hubs d'événements sont-ils calculés ?

Chaque événement envoyé à un concentrateur d’événements est considéré comme un message facturable. Un *événement d’entrée* est défini comme une unité de données inférieure ou égale à 64 Ko. Tout événement dont la taille est inférieure ou égale à 64 Ko est considéré comme un événement facturable. Si l’événement est supérieur à 64 Ko, le nombre d’événements facturables est calculé en fonction de la taille de l’événement, par multiples de 64 Ko. Par exemple, un événement de 8 Ko envoyé au concentrateur d’événements est facturé comme un événement, mais un message de 96 Ko envoyé au concentrateur d’événements est facturé comme deux événements.

Les événements utilisés à partir d’un concentrateur d’événements, ainsi que les opérations de gestion et les appels de contrôle comme les points de contrôle, ne sont pas comptabilisés comme des événements d’entrée facturables, mais s’ajoutent à l’allocation d’unité de débit.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Des frais de connexion répartie s'appliquent-ils aux hubs d'événements ?

Des frais de connexion s'appliquent uniquement lorsque le protocole AMQP est utilisé. Il n'y a aucun frais de connexion pour l'envoi d'événements à l'aide de HTTP, quel que soit le nombre de systèmes ou de périphériques d’envoi. Si vous prévoyez d’utiliser AMQP (par exemple, pour améliorer le flux d’événements ou activer la communication bidirectionnelle sur des scénarios de commande et de contrôle IoT), reportez-vous à la page [Informations tarifaires pour Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) pour plus de détails sur le nombre de connexions incluses dans chaque niveau de service.

### <a name="how-is-event-hubs-capture-billed"></a>Comment est facturé Event Hubs Capture ?

Capture est activé lorsque la fonctionnalité de capture est activée pour chaque concentrateur d’événements de l’espace de noms. Event Hubs Capture est facturé à l’heure, par unité de débit achetée. La facturation d’Event Hubs Capture reflète les variations du nombre d’unités de débit sous forme d’incréments d’heure pleine. Pour plus d’informations sur la facturation d’Event Hubs Capture, consultez [Informations tarifaires pour Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Suis-je facturé pour le compte de stockage que je sélectionne pour Event Hubs Capture ?

Capture utilise un compte de stockage que vous fournissez lorsqu’il est activé sur un concentrateur d’événements. Ce compte de stockage étant le vôtre, les éventuels changements de configurations seront facturés sur votre abonnement Azure.

## <a name="quotas"></a>Quotas

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Y a-t-il des quotas associés aux Event Hubs ?

Pour obtenir la liste de tous les quotas d’Event Hubs, consultez la page [quotas](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Pourquoi ne suis-je pas en mesure de créer un espace de noms après l'avoir supprimé d'un autre abonnement ? 
Lorsque vous supprimez un espace de noms d’un abonnement, patientez pendant 4 heures avant de le recréer avec le même nom dans un autre abonnement. Sinon, le message d’erreur suivant peut s’afficher : `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Quelles sont les exceptions générées par Event Hubs et les actions recommandées ?

Pour obtenir la liste des exceptions Event Hubs potentielles, consultez la page [Vue d’ensemble des exceptions](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Journaux de diagnostic

Event Hubs prend en charge deux types de [journaux de diagnostics](event-hubs-diagnostic-logs.md) (journaux d’activité d’erreurs Capture et journaux d’activité des opérations), qui sont tous deux représentés en json et peuvent être activés via le portail Azure.

### <a name="support-and-sla"></a>Prise en charge et contrats SLA

Un support technique pour les hubs d'événements est disponible via les [forums de la communauté](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus). La gestion de la facturation et des abonnements est fournie gratuitement.

Pour en savoir plus sur notre contrat SLA, consultez la section [Contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/) .

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble d’Event Hubs](event-hubs-what-is-event-hubs.md)
* [Créer un hub d’événements](event-hubs-create.md)
* [Majoration automatique Event Hubs](event-hubs-auto-inflate.md)
