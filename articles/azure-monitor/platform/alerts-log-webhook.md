---
title: Actions webhook pour les alertes de journal dans Alertes Azure
description: Cet article comment une règle d’alerte de journal utilisant un espace de travail Log Analytics ou des informations d’application envoie des données en tant que webhook HTTP et des détails sur les différentes personnalisations possibles.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: cad1b0ab484d172000bd62146a88a27bfab1e9f2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448772"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Actions webhook pour les règles d’alerte de journal
Quand une [alerte de journal est créée dans Azure](alerts-log.md), vous avez l’option de la [configuration à l’aide de groupes d’actions](action-groups.md) pour exécuter une ou plusieurs actions.  Cet article décrit les différentes actions webhook disponibles et les détails de la configuration du webhook personnalisé basé sur JSON.

> [!NOTE]
> Vous pouvez également utiliser le [schéma d’alerte commun](https://aka.ms/commonAlertSchemaDocs), qui offre l’avantage de générer une seule charge utile d’alerte extensible et unifiée sur tous les services d’alerte dans Azure Monitor, pour les intégrations de votre webhook. [Plus d’informations sur les définitions de schéma d’alerte commun.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Actions de webhook

Les actions de webhook permettent d’appeler un processus externe par le biais d’une simple requête HTTP POST.  Le service appelé doit prendre en charge les webhooks et déterminer comment il doit utiliser toute charge utile qu’il reçoit.    

Les propriétés requises par les actions webhook sont décrites dans le tableau suivant :

| Propriété | Description |
|:--- |:--- |
| URL du webhook |URL du webhook. |
| Charge utile JSON personnalisée |Charge utile personnalisée à envoyer avec le webhook, lorsque vous choisissez cette option au moment de la création de l’alerte. Détails disponibles dans [Gérer les alertes de journal](alerts-log.md) |

> [!NOTE]
> L’utilisation du bouton Afficher le Webhook avec l’option *Inclure une charge utile Json personnalisée pour webhook* pour Alerte du journal affiche un exemple de charge utile de webhook pour la personnalisation fournie. Il ne contient aucune donnée réelle et représentative du schéma JSON utilisé pour les alertes de journal. 

Les webhooks incluent une URL et une charge utile au format JSON qui correspond aux données envoyées au service externe.  Par défaut, la charge utile comprend les valeurs du tableau suivant :  Vous pouvez choisir de remplacer cette charge utile par une charge utile personnalisée de votre choix.  Dans ce cas, vous pouvez reprendre les variables de chacun des paramètres indiquées dans le tableau pour inclure les valeurs correspondantes dans votre charge utile personnalisée.


| Paramètre | Variable | Description |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |Nom de la règle d’alerte. |
| Severity |#severity |Gravité définie pour l’alerte de journal déclenchée. |
| AlertThresholdOperator |#thresholdoperator |Opérateur de seuil de la règle d’alerte.  *Supérieur à* ou *Inférieur à*. |
| AlertThresholdValue |#thresholdvalue |Valeur de seuil de la règle d’alerte. |
| LinkToSearchResults |#linktosearchresults |Lien vers le portail Analytics qui retourne les enregistrements de la requête ayant créé l’alerte. |
| ResultCount |#searchresultcount |Nombre d’enregistrements dans les résultats de recherche. |
| Heure de fin de l’intervalle de recherche |#searchintervalendtimeutc |Heure de fin de la requête au format UTC, format - mm/jj/aaaa HH:mm:ss AM/PM. |
| Intervalle de recherche |#searchinterval |Fenêtre de temps de la règle d’alerte, format - HH:mm:ss. |
| Heure de début de l’intervalle de recherche |#searchintervalstarttimeutc |Heure de début de la requête au format UTC, format - mm/jj/aaaa HH:mm:ss AM/PM. 
| SearchQuery |#searchquery |Requête de recherche de journal utilisée par la règle d’alerte. |
| SearchResults |"IncludeSearchResults": true|Enregistrements retournés par la requête en tant que tableau JSON, limités aux 1 000 premiers enregistrements ; si "IncludeSearchResults": true est ajouté dans la définition de webhook JSON personnalisé en tant que propriété de niveau supérieur. |
| Type d’alerte| #alerttype | Le type de règle d’alerte de journal configuré : [Mesure de métriques](alerts-unified-log.md#metric-measurement-alert-rules) ou [Nombre de résultats](alerts-unified-log.md#number-of-results-alert-rules).|
| WorkspaceID |#workspaceid |ID de votre espace de travail Log Analytics. |
| ID de l'application |#applicationid |ID de votre application Application Insights. |
| Identifiant d’abonnement |#subscriptionid |ID de votre abonnement Azure utilisé. 

> [!NOTE]
> LinkToSearchResults passe des paramètres comme SearchQuery, Search Interval StartTime et Search Interval End Time dans l’URL au portail Azure pour les afficher dans la section Analytics. Le portail Azure limite la taille des URI à environ 2000 caractères et n’ouvre *pas* le lien fourni dans les alertes si les valeurs des paramètres dépassent ladite limite. Les utilisateurs peuvent entrer manuellement des détails pour afficher les résultats dans le portail Analytics ou utiliser [l’API REST Application Insights Analytics](https://dev.applicationinsights.io/documentation/Using-the-API) ou [l’API REST Log Analytics](/rest/api/loganalytics/) pour récupérer les résultats par programmation. 

Par exemple, vous pouvez spécifier la charge utile personnalisée suivante qui inclut un paramètre unique appelé *text*.  Le service appelé par ce webhook s’attendrait à recevoir ce paramètre.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Cette charge utile peut donner ce qui suit quand elle est envoyée au webhook.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Comme toutes les variables dans un webhook personnalisé doivent être spécifiées dans un boîtier JSON comme « #searchinterval », le webhook résultant aura également des données de variable à l’intérieur d’un boîtier comme « 00:05:00 ».

Pour inclure les résultats de la recherche dans une charge utile personnalisée, vérifiez que **IncudeSearchResults** est défini comme une propriété de niveau supérieur dans la charge utile json. 

## <a name="sample-payloads"></a>Exemples de charges utiles
Cette section présente une exemple de charge utile de webhook pour les alertes de journal, notamment lorsque la charge utile est standard et quand elle est personnalisée.

### <a name="standard-webhook-for-log-alerts"></a>Webhook standard pour les alertes de journal 
Ces deux exemples indiquaient une charge utile fictive ne comprenant que deux colonnes et deux lignes.

#### <a name="log-alert-for-azure-log-analytics"></a>Alerte de journal pour Azure Log Analytics
Voici un exemple de charge utile pour une action standard webhook *sans utiliser d’option Json personnalisée* pour des alertes de journal basées sur une analytique des journaux d’activité.

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> La valeur du champ de gravité peut changer si vous avez [modifié votre préférence API](alerts-log-api-switch.md) pour les alertes de journal sur Log Analytics.


#### <a name="log-alert-for-azure-application-insights"></a>Alerte de journal pour Azure Application Insights
Voici un exemple de charge utile pour un webhook standard *sans option Json personnalisée* en cas d’utilisation pour des alertes de journal basées sur des informations d’application.
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Alerte de journal avec charge utile JSON personnalisée
Par exemple, pour créer une charge utile personnalisée qui inclut uniquement le nom de l’alerte et les résultats de recherche, vous pouvez utiliser ce qui suit : 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Voici un exemple de charge utile pour une action de webhook personnalisée pour une alerte de journal quelconque.
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [alertes de journal dans les alertes Azure ](alerts-unified-log.md)
- Comprendre la [gestion des alertes de journal dans Azure](alerts-log.md)
- Créer et gérer des [groupes d’actions dans Azure](action-groups.md)
- En savoir plus sur [Application Insights](../../azure-monitor/app/analytics.md)
- En savoir plus sur [les requêtes dans les journaux](../log-query/log-query-overview.md). 

