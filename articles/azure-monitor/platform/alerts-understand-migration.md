---
title: Comprendre le fonctionnement de l’outil de migration volontaire pour les alertes Azure Monitor
description: Découvrez comment l’outil de migration des alertes fonctionne et résolvez les problèmes.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 015000388c5629dbd8ed8833931a809ebd738bd6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295526"
---
# <a name="understand-how-the-migration-tool-works"></a>Comprendre le fonctionnement de l’outil de migration

Comme [précédemment annoncé](monitoring-classic-retirement.md), les alertes classiques dans Azure Monitor seront mises hors service le 31 août 2019 (c’était initialement prévu pour le 30 juin 2019). Un outil de migration est disponible dans le portail Azure pour les clients qui utilisent des règles d’alerte classiques et qui souhaitent déclencher la migration eux-mêmes.

Cet article explique le fonctionnement de l’outil de migration volontaire. Il décrit également les solutions pour certains problèmes courants.

> [!NOTE]
> En raison de retards dans le déploiement de l’outil de migration, la date de mise hors service des alertes classiques a été [repoussée au 31 août 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) (la date annoncée à l’origine était le 30 juin 2019).

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Les règles d’alerte classiques qui ne seront pas migrées

> [!IMPORTANT]
> Les alertes de journal d’activité (y compris les alertes d’intégrité de service) et les alertes de journal ne sont pas affectées par la migration. La migration s’applique uniquement aux règles d’alerte classiques décrites [ici](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

Bien que l’outil peut migrer presque toutes les [règles d’alerte classiques](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform), il existe quelques exceptions. Les règles d’alerte suivantes ne seront pas migrées avec l’outil (ou lors de la migration automatique à partir de septembre 2019) :

- Les règles d’alerte classiques des métriques d’invité pour les machines virtuelles (Windows et Linux). Consultez les [conseils permettant de recréer ces règles d’alerte dans les nouvelles alertes de métriques](#guest-metrics-on-virtual-machines) plus loin dans cet article.
- Les règles d’alerte classiques des métriques de stockage classiques. Consultez les [conseils pour la surveillance de vos comptes de stockage classiques](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Les règles d’alerte classiques pour certaines métriques de compte de stockage. Consultez les [détails](#storage-account-metrics) plus loin dans cet article.
- Les règles d’alerte classiques sur certaines métriques Cosmos DB. Les détails seront ajoutés dans une prochaine mise à jour.

Si votre abonnement a des règles classiques de ce type, vous devez les migrer manuellement. Étant donné que nous ne pouvons pas fournir une migration automatique, les alertes de métrique classiques existantes de ces types continueront à fonctionner jusqu'à juin 2020. Cette extension vous donne le temps de passer aux nouvelles alertes. Toutefois, aucune nouvelle alerte classique ne pourra être créée après août 2019.

> [!NOTE]
> Outre les exceptions répertoriées ci-dessus, si vos règles d’alerte classiques ne sont pas valides, par exemple si elles portent sur des [métriques déconseillées](#classic-alert-rules-on-deprecated-metrics) ou des ressources qui ont été supprimées, elles ne seront pas migrées pendant la migration volontaire. Les règles d’alerte classiques non valides de ce type seront supprimées lors de la migration automatique.

### <a name="guest-metrics-on-virtual-machines"></a>Métriques d’invité sur les machines virtuelles

Avant de pouvoir créer de nouvelles alertes de métrique sur les métriques d’invité, les métriques d’invité doivent être envoyées au magasin de métriques personnalisées Azure Monitor. Suivez ces instructions pour activer le récepteur Azure Monitor dans les paramètres de diagnostic :

- [Activer les métriques invitées pour les machines virtuelles Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Activer les métriques invitées pour les machines virtuelles Linux](collect-custom-metrics-linux-telegraf.md)

Une fois ces étapes effectuées, vous pouvez créer de nouvelles alertes de métrique sur les métriques d’invité. Et une fois que vous avez créé les nouvelles alertes de métrique, vous pouvez supprimer les alertes classiques.

### <a name="storage-account-metrics"></a>Métriques du compte de stockage

Toutes les alertes classiques sur les comptes de stockage peuvent être migrées, à l’exception des alertes sur ces métriques :

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

Les règles d’alerte classiques sur les métriques de pourcentage doivent être migrées selon [le mappage entre les anciennes et nouvelles métriques de stockage](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Les seuils devront être modifiés en conséquence, car la nouvelle métrique disponible est absolue.

Les règles d’alerte classiques sur AnonymousThrottlingError, SASThrottlingError et ThrottlingError doivent être fractionnées en deux nouvelles alertes, car il n’existe aucune métrique combinée qui fournit les mêmes fonctionnalités. Les seuils devront être adaptés de manière appropriée.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Règles d’alerte classiques sur des métriques déconseillées

Il s’agit de règles d’alerte classiques sur des métriques qui étaient précédemment prises en charge, mais qui ont été déconseillées par la suite. Un petit pourcentage de clients peut avoir des règles d’alerte classiques non valides sur de telles métriques. Dans la mesure où ces règles d’alerte ne sont pas valides, elles ne seront pas migrées.

| Type de ressource| Métriques déconseillées |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, throttling, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Comment les nouvelles règles d’alerte et les groupes d’actions équivalents sont créés

L’outil de migration convertit vos règles d’alerte classiques en nouvelles règles d’alerte et groupes d’actions équivalents. Pour la plupart des règles d’alerte classiques, les nouvelles règles d’alerte équivalentes portent sur la même métrique avec les mêmes propriétés, comme `windowSize` et `aggregationType`. Toutefois, il existe des règles d’alerte classiques portant sur des métriques qui ont une métrique équivalente différente dans le nouveau système. Les principes suivants s’appliquent à la migration des alertes classiques, sauf mention contraire dans la section ci-dessous :

- **Fréquence** : Définit la fréquence de vérification de la condition pour une règle d’alerte classique ou nouvelle. La `frequency` dans les règles d’alerte classiques n’était pas configurable par l’utilisateur et était toujours de 5 minutes pour tous les types de ressources à l’exception des composants d’Application Insights, pour lesquels elle était de 1 minute. La fréquence des règles équivalentes est donc également définie sur 5 minutes et 1 minute respectivement.
- **Type d’agrégation** : Définit la façon dont les métriques sont agrégées pendant la fenêtre d’intérêt. Le `aggregationType` est également le même entre les alertes classiques et les nouvelles alertes pour la plupart des métriques. Dans certains cas, étant donné que la métrique est différente entre les alertes classiques et les nouvelles alertes, le `aggregationType` équivalent ou le `primary Aggregation Type` défini pour la métrique est utilisé.
- **Unités** : La propriété de la métrique sur laquelle l’alerte est créée. Certaines métriques équivalentes ont des unités différentes. Le seuil est ajusté de manière appropriée en fonction des besoins. Par exemple, si la métrique d’origine utilise les secondes comme unité mais que la nouvelle métrique équivalente utilise les millisecondes en tant qu’unité, le seuil d’origine est multiplié par 1000 pour garantir le même comportement.
- **Taille de la fenêtre** : Définit la fenêtre pendant laquelle les données de métrique sont agrégées pour être comparées au seuil. Pour les valeurs de `windowSize` standard telles que 5 minutes, 15 minutes, 30 minutes, 1 heure, 3 heures, 6 heures, 12 heures ou 1 jour, aucune modification n’est effectuée pour la nouvelle règle d’alerte équivalente. Pour les autres valeurs, la `windowSize` la plus proche est choisie. Pour la plupart des clients, cette modification n’a aucun impact. Pour un petit pourcentage de clients, il pourrait être nécessaire d’ajuster le seuil pour obtenir un comportement rigoureusement identique.

Dans les sections suivantes, nous détaillons les métriques qui ont une métrique différente et équivalente dans le nouveau système. Les métriques identiques pour les règles d’alerte classiques et nouvelles ne sont pas répertoriées. Vous trouverez une liste des métriques prises en charge dans le nouveau système [ici](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

Pour les services de compte de stockage comme les objets blob, les tables, les fichiers et les files d’attente, les métriques suivantes sont mappées aux métriques équivalentes, comme indiqué ci-dessous :

| Métrique dans les alertes classiques | Métrique équivalente dans les nouvelles alertes | Commentaires|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Métrique de transactions avec les dimensions « ResponseType » = « AuthorizationError » et « Authentication » = « Anonymous »| |
| AnonymousClientOtherError | Métrique de transactions avec les dimensions « ResponseType » = « ClientOtherError » et « Authentication » = « Anonymous » | |
| AnonymousClientTimeOutError| Métrique de transactions avec les dimensions « ResponseType » = « ClientTimeOutError » et « Authentication » = « Anonymous » | |
| AnonymousNetworkError | Métrique de transactions avec les dimensions « ResponseType » = « NetworkError » et « Authentication » = « Anonymous » | |
| AnonymousServerOtherError | Métrique de transactions avec les dimensions « ResponseType » = « ServerOtherError » et « Authentication » = « Anonymous » | |
| AnonymousServerTimeOutError | Métrique de transactions avec les dimensions « ResponseType » = « ServerTimeOutError » et « Authentication » = « Anonymous » | |
| AnonymousSuccess | Métrique de transactions avec les dimensions « ResponseType » = « Success » et « Authentication » = « Anonymous » | |
| AuthorizationError | Métrique de transactions avec les dimensions « ResponseType » = « AuthorizationError » | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capacité | BlobCapacity | Utilisez 'average' au lieu de 'last' pour `aggregationType`. Les métriques s’appliquent uniquement aux services blob |
| ClientOtherError | Métrique de transactions avec les dimensions « ResponseType » = « ClientOtherError »  | |
| ClientTimeoutError | Métrique de transactions avec les dimensions « ResponseType » = « ClientTimeOutError » | |
| ContainerCount | ContainerCount | Utilisez 'average' au lieu de 'last' pour `aggregationType`. Les métriques s’appliquent uniquement aux services blob |
| NetworkError | Métrique de transactions avec les dimensions « ResponseType » = « NetworkError » | |
| ObjectCount | BlobCount| Utilisez 'average' au lieu de 'last' pour `aggregationType`. Les métriques s’appliquent uniquement aux services blob |
| SASAuthorizationError | Métrique de transactions avec les dimensions « ResponseType » = « AuthorizationError » et « Authentication » = « SAS » | |
| SASClientOtherError | Métrique de transactions avec les dimensions « ResponseType » = « ClientOtherError » et « Authentication » = « SAS » | |
| SASClientTimeOutError | Métrique de transactions avec les dimensions « ResponseType » = « ClientTimeOutError » et « Authentication » = « SAS » | |
| SASNetworkError | Métrique de transactions avec les dimensions « ResponseType » = « NetworkError » et « Authentication » = « SAS » | |
| SASServerOtherError | Métrique de transactions avec les dimensions « ResponseType » = « ServerOtherError » et « Authentication » = « SAS » | |
| SASServerTimeOutError | Métrique de transactions avec les dimensions « ResponseType » = « ServerTimeOutError » et « Authentication » = « SAS » | |
| SASSuccess | Métrique de transactions avec les dimensions « ResponseType » = « Success » et « Authentication » = « SAS » | |
| ServerOtherError | Métrique de transactions avec les dimensions « ResponseType » = « ServerOtherError » | |
| ServerTimeOutError | Métrique de transactions avec les dimensions « ResponseType » = « ServerTimeOutError »  | |
| Succès | Métrique de transactions avec les dimensions « ResponseType » = « Success » | |
| TotalBillableRequests| Transactions | |
| TotalEgress | Sortie | |
| TotalIngress | Entrée | |
| TotalRequests | Transactions | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

Pour Application Insights, les métriques équivalentes sont indiquées ci-dessous :

| Métrique dans les alertes classiques | Métrique équivalente dans les nouvelles alertes | Commentaires|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/duration| Multipliez le seuil d’origine par 1000, car les unités de mesure classiques sont exprimées en secondes et les nouvelles en millisecondes.  |
| basicExceptionBrowser.count | exceptions/browser|  Utilisez 'count' au lieu de 'sum' pour `aggregationType`. |
| basicExceptionServer.count | exceptions/server| Utilisez 'count' au lieu de 'sum' pour `aggregationType`.  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| Multipliez le seuil d’origine par 1000, car les unités de mesure classiques sont exprimées en secondes et les nouvelles en millisecondes.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  Multipliez le seuil d’origine par 1000, car les unités de mesure classiques sont exprimées en secondes et les nouvelles en millisecondes. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Multipliez le seuil d’origine par 1000, car les unités de mesure classiques sont exprimées en secondes et les nouvelles en millisecondes.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Multipliez le seuil d’origine par 1000, car les unités de mesure classiques sont exprimées en secondes et les nouvelles en millisecondes.  |
| clientPerformance.total.value | browserTimings/totalDuration| Multipliez le seuil d’origine par 1000, car les unités de mesure classiques sont exprimées en secondes et les nouvelles en millisecondes.  |
| performanceCounter.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| Le seuil doit être modifié en conséquence, car la métrique d’origine était partagée sur tous les cœurs et la nouvelle métrique est normalisée sur un seul cœur. L’outil de migration ne modifie pas les seuils.  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | requests/duration| Multipliez le seuil d’origine par 1000, car les unités de mesure classiques sont exprimées en secondes et les nouvelles en millisecondes.  |
| request.rate | requests/rate|   |
| requestFailed.count | requests/failed| Utilisez 'count' au lieu de 'sum' pour `aggregationType`.   |
| view.count | pageViews/count| Utilisez 'count' au lieu de 'sum' pour `aggregationType`.   |

### <a name="how-equivalent-action-groups-are-created"></a>Comment les groupes d’actions équivalents sont créés

Les règles d’alerte classiques avaient les actions d’e-mail, de webhook, d’application logique et de runbook liées à la règle d’alerte elle-même. Les nouvelles règles d’alerte utilisent des groupes d’actions qui peuvent être réutilisés dans plusieurs règles d’alerte. L’outil de migration crée un groupe d’actions unique pour les actions similaires, quel que soit le nombre de règles d’alerte utilisant l’action. Les groupes d’actions créés par l’outil de migration utilisent le format d’attribution de noms « Migrated_AG* ».

## <a name="rollout-phases"></a>Phases de déploiement

L’outil de migration est livré en plusieurs phases aux clients qui utilisent des règles d’alerte classiques. Les propriétaires d’abonnements recevront un e-mail lorsque leur abonnement sera prêt à être migré à l’aide de l’outil.

> [!NOTE]
> Étant donné que l’outil est déployé en plusieurs phases, vous pourriez constater que certains de vos abonnements ne sont pas encore prêts à être migrés lors des premières phases.

La plupart des abonnements sont actuellement marqués comme prêts pour la migration. Seuls les abonnements qui ont des alertes classiques sur les types de ressources suivants ne sont pas encore prêts pour la migration.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.documentDB/databases
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>Qui peut déclencher la migration ?

Tout utilisateur ayant le rôle intégré de contributeur de surveillance au niveau de l’abonnement peut déclencher la migration. Les utilisateurs qui ont un rôle personnalisé avec les autorisations suivantes peuvent également déclencher la migration :

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

> [!NOTE]
> En plus d’avoir les autorisations ci-dessus, votre abonnement doit être inscrit avec le fournisseur de ressources Microsoft.AlertsManagement. Cela est nécessaire pour réussir la migration des alertes d’anomalies d’échec sur Application Insights. 

## <a name="common-problems-and-remedies"></a>Problèmes courants et solutions

Après avoir [déclenché la migration](alerts-using-migration-tool.md), vous recevez un e-mail aux adresses que vous avez fournies pour vous avertir que la migration est terminée ou si une action est nécessaire de votre part. Cette section décrit certains problèmes courants et comment les résoudre.

### <a name="validation-failed"></a>Échec de validation

En raison des modifications apportées récemment aux règles d’alerte classiques dans votre abonnement, l’abonnement ne peut pas être migré. Ce problème est temporaire. Vous pourrez redémarrer la migration une fois que l’état de migration sera de nouveau **prêt pour la migration**, dans quelques jours.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>Une stratégie ou un verrou d’étendue nous empêche de migrer vos règles

Dans le cadre de la migration, de nouvelles alertes de métrique et de nouveaux groupes d’actions seront créés, et les règles d’alerte classiques seront alors supprimées. Toutefois, une stratégie ou un verrou d’étendue empêche la création de ressources. En fonction de la stratégie ou du verrou d’étendue, certaines ou l’intégralité des règles n’ont pas pu être migrées. Vous pouvez résoudre ce problème en supprimant le verrou d’étendue ou la stratégie temporairement et en déclenchant à nouveau la migration.

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour utiliser l’outil de migration](alerts-using-migration-tool.md)
- [Préparer la migration](alerts-prepare-migration.md)
