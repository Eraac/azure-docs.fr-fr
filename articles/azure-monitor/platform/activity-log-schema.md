---
title: Schéma d’événements du journal d’activité
description: Comprendre le schéma d’événement pour les données émises dans le journal d’activité
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 1/16/2019
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: ba5e0f696f54f46fb14086b542dc3b2e64155975
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244932"
---
# <a name="azure-activity-log-event-schema"></a>Schéma d’événements du journal d’activité
Le **Journal d’activité Azure** est un journal qui fournit un aperçu de tous les événements de niveau d’abonnement qui se sont produits dans Azure. Cet article décrit le schéma d’événements par catégorie de données. Le schéma des données varie selon que vous lisez les données dans le portail, dans PowerShell, dans l’interface CLI, ou directement dans l’API REST, au lieu de [diffuser en continu les données vers le stockage ou vers des Event Hubs à l’aide d’un profil de journal](activity-log-export.md). Les exemples ci-dessous montrent le schéma, tel qu’il se présente dans le portail, PowerShell, l’interface CLI et l’API REST. Un mappage de ces propriétés vers le [schéma des journaux de diagnostic Azure](diagnostic-logs-schema.md) est fourni à la fin de cet article.

## <a name="administrative"></a>Administratif
Cette catégorie contient l’enregistrement de toutes les opérations de création, mise à jour, suppression et action effectuées par le biais du gestionnaire de ressources. Les exemples de types d’événements que vous pouvez voir dans cette catégorie incluent « créer une machine virtuelle » et « supprimer un groupe de sécurité réseau ». Toute mesure prise par un utilisateur ou une application utilisant le gestionnaire de ressources est modélisée comme une opération sur un type de ressources en particulier. Si le type d’opération est Écrire, Supprimer ou Action, les enregistrements de début et de réussite ou d’échec de cette opération sont enregistrés dans la catégorie Administrative. La catégorie Administrative inclut également toute modification apportée à un contrôle d’accès basé sur un rôle dans un abonnement.

### <a name="sample-event"></a>Exemple d’événement
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Description des propriétés
| Nom de l’élément | Description |
| --- | --- |
| autorisation |Objet blob des propriétés RBAC de l’événement. Inclut généralement les propriétés « action », « role » et « scope ». |
| caller |Adresse e-mail de l’utilisateur qui a effectué l’opération, la revendication UPN ou la revendication SPN basée sur la disponibilité. |
| channels |L’une des valeurs suivantes : « Admin », « Opération » |
| réclamations |Le jeton JWT utilisé par Active Directory pour authentifier l’utilisateur ou l’application afin d’effectuer cette opération dans Resource Manager. |
| correlationId |Généralement un GUID au format chaîne. Les événements qui partagent un correlationId appartiennent à la même action uber. |
| description |Description textuelle statique d’un événement. |
| eventDataId |Identificateur unique d’un événement. |
| eventName | Nom convivial de l’événement administratif. |
| category | Toujours « Administrative » |
| httpRequest |Objet blob décrivant la requête Http. Inclut généralement clientRequestId, clientIpAddress et la méthode (méthode HTTP. Par exemple, PUT). |
| level |Niveau de l’événement. L’une des valeurs suivantes : « Critique », « Erreur », « Avertissement » et « Informatif » |
| resourceGroupName |Nom du groupe de ressources de la ressource affectée. |
| resourceProviderName |Nom du fournisseur de ressources de la ressource affectée. |
| resourceType | Type de ressource affecté par un événement administratif. |
| resourceId |ID de ressource de la ressource affectée. |
| operationId |Un GUID partagé par les événements correspondant à une opération unique. |
| operationName |Nom de l’opération. |
| properties |Jeu de paires `<Key, Value>` (c’est-à-dire Dictionary) décrivant les détails de l’événement. |
| status |Chaîne décrivant l’état de l’opération. Voici plusieurs valeurs courantes : « Started », « In Progress », « Succeeded », « Failed », « Active », « Resolved ». |
| subStatus |En général, le code d’état HTTP de l’appel REST correspondant. Peut également inclure d’autres chaînes décrivant un sous-état, comme les valeurs courantes suivantes : OK (code d'état HTTP : 200), Créé (code d’état HTTP : 201), Accepté (code d’état HTTP : 202, Aucun contenu (code d’état HTTP : 204, Requête incorrecte (code d’état HTTP : 400, Introuvable (code d’état HTTP : 404), Conflit (code d’état HTTP : 409), Erreur interne du serveur (code d’état HTTP : 500), Service indisponible (code d'état HTTP : 503), Dépassement de délai de la passerelle (code d'état HTTP : 504). |
| eventTimestamp |Horodatage lorsque l’événement a été généré par le service Azure traitant la demande correspondant à l’événement. |
| submissionTimestamp |Horodatage lorsque l’événement est devenu disponible pour l’interrogation. |
| subscriptionId |ID d’abonnement Azure. |

## <a name="service-health"></a>État d’intégrité du service
Cette catégorie contient l’enregistrement de tout incident de l’état d’intégrité du service qui se sont produits dans Azure. Un exemple du type d’événement que vous pouvez voir dans cette catégorie est « SQL Azure dans la région USA Est rencontre des temps d’arrêt. » Les événements d’intégrité du service se présentent sous cinq types : action requise, récupération assistée, incident, maintenance, informations ou sécurité et n’apparaissent que si une ressource de votre abonnement est affectée par l’événement.

### <a name="sample-event"></a>Exemple d’événement
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Reportez-vous à l’article [Notifications d’intégrité du service](./../../azure-monitor/platform/service-notifications.md) pour plus d’informations sur les valeurs dans les propriétés.

## <a name="resource-health"></a>Intégrité des ressources
Cette catégorie contient l’enregistrement de tout événement d’intégrité de la ressource survenu dans vos ressources Azure. Par exemple, cette catégorie peut comporter le type d’événement suivant : « L’état d’intégrité de la machine virtuelle est passé à Indisponible ». Les événements d’intégrité de ressource peuvent représenter l’un des quatre états d’intégrité : Disponible, Indisponible, Détérioré et Inconnu. En outre, les événements d’intégrité de ressource peuvent être initiés par la plateforme ou initiés par l’utilisateur.

### <a name="sample-event"></a>Exemple d’événement

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>Description des propriétés
| Nom de l’élément | Description |
| --- | --- |
| channels | Toujours « Admin, opération » |
| correlationId | Un GUID au format chaîne. |
| description |Description textuelle statique de l’événement d’alerte. |
| eventDataId |Identificateur unique de l'événement d’alerte. |
| category | Toujours « ResourceHealth » |
| eventTimestamp |Horodatage lorsque l’événement a été généré par le service Azure traitant la demande correspondant à l’événement. |
| level |Niveau de l’événement. L’une des valeurs suivantes : « Critical », « Error », « Warning », « Informational » et « Verbose » |
| operationId |Un GUID partagé par les événements correspondant à une opération unique. |
| operationName |Nom de l’opération. |
| resourceGroupName |Nom du groupe de ressources qui contient la ressource. |
| resourceProviderName |Toujours « Microsoft.Resourcehealth/healthevent/action ». |
| resourceType | Type de ressource affecté par un événement Resource Health. |
| resourceId | Nom de l’ID de ressource de la ressource affectée. |
| status |Chaîne décrivant l’état de l’événement d’intégrité. Les valeurs peuvent être les suivantes : Active, Resolved, InProgress, Updated. |
| subStatus | Généralement, nul pour les alertes. |
| submissionTimestamp |Horodatage lorsque l’événement est devenu disponible pour l’interrogation. |
| subscriptionId |ID d’abonnement Azure. |
| properties |Jeu de paires `<Key, Value>` (c’est-à-dire Dictionary) décrivant les détails de l’événement.|
| properties.title | Chaîne conviviale qui décrit l’état d’intégrité de la ressource. |
| properties.details | Chaîne conviviale qui fournit des informations supplémentaires sur l’événement. |
| properties.currentHealthStatus | État d’intégrité actuel de la ressource. L’une des valeurs suivantes : « Available », « Unavailable », « Available » et « Unknown ». |
| properties.previousHealthStatus | État d’intégrité précédent de la ressource. L’une des valeurs suivantes : « Available », « Unavailable », « Available » et « Unknown ». |
| properties.type | Description du type d’événement d’intégrité de la ressource. |
| properties.cause | Description de la cause de l’événement d’intégrité de la ressource. « UserInitiated » ou « PlatformInitiated ». |


## <a name="alert"></a>Alerte
Cette catégorie contient l’enregistrement de toutes les activations des alertes Azure. Un exemple du type d’événement que vous pouvez voir dans cette catégorie est « % du processeur sur myVM a été supérieur à 80 pour les 5 dernières minutes. » Une variété de systèmes Azure possèdent un concept d’alertes : vous pouvez définir une règle quelconque et recevoir une notification lorsque les conditions correspondent à cette règle. Chaque fois qu’un type d’alerte Azure pris en charge « s’active » ou si les conditions sont remplies pour générer une notification, un enregistrement de l’activation est également envoyé à cette catégorie du journal d’activité.

### <a name="sample-event"></a>Exemple d’événement

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Description des propriétés
| Nom de l’élément | Description |
| --- | --- |
| caller | Toujours Microsoft.Insights/alertRules |
| channels | Toujours « Admin, opération » |
| réclamations | Objet blob JSON avec le type de SPN (nom principal de service) ou de ressource du moteur d’alertes. |
| correlationId | Un GUID au format chaîne. |
| description |Description textuelle statique de l’événement d’alerte. |
| eventDataId |Identificateur unique de l'événement d’alerte. |
| category | Toujours « Alert » |
| level |Niveau de l’événement. L’une des valeurs suivantes : « Critique », « Erreur », « Avertissement » et « Informatif » |
| resourceGroupName |Nom du groupe de ressources de la ressource affectée s’il s’agit d’une alerte métrique. Pour les autres types d’alertes, il s’agit du nom du groupe de ressources qui contient l’alerte elle-même. |
| resourceProviderName |Nom du fournisseur de ressources de la ressource affectée s’il s’agit d’une alerte métrique. Pour les autres types d’alertes, il s’agit du nom du fournisseur de ressources pour l’alerte elle-même. |
| resourceId | Nom de l’ID de ressource de la ressource affectée s’il s’agit d’une alerte métrique. Pour les autres types d’alertes, il s’agit du nom de l’ID de ressource pour l’alerte elle-même. |
| operationId |Un GUID partagé par les événements correspondant à une opération unique. |
| operationName |Nom de l’opération. |
| properties |Jeu de paires `<Key, Value>` (c’est-à-dire Dictionary) décrivant les détails de l’événement. |
| status |Chaîne décrivant l’état de l’opération. Voici plusieurs valeurs courantes : « Started », « In Progress », « Succeeded », « Failed », « Active », « Resolved ». |
| subStatus | Généralement, nul pour les alertes. |
| eventTimestamp |Horodatage lorsque l’événement a été généré par le service Azure traitant la demande correspondant à l’événement. |
| submissionTimestamp |Horodatage lorsque l’événement est devenu disponible pour l’interrogation. |
| subscriptionId |ID d’abonnement Azure. |

### <a name="properties-field-per-alert-type"></a>Champ Propriétés par type d’alerte
Le champ Propriétés contient des valeurs différentes en fonction de la source de l’événement d’alerte. Deux fournisseurs d’événements d’alertes courants sont les alertes du journal d’activité et les alertes métriques.

#### <a name="properties-for-activity-log-alerts"></a>Propriétés pour les alertes du journal d’activité
| Nom de l’élément | Description |
| --- | --- |
| properties.subscriptionId | L’ID d’abonnement depuis l’événement du journal d’activité qui a provoqué l’activation de cette règle d’alerte du journal d’activité. |
| properties.eventDataId | L’ID des données de l’événement depuis l’événement du journal d’activité qui a provoqué l’activation de cette règle d’alerte du journal d’activité. |
| properties.resourceGroup | Le groupe de ressources depuis l’événement du journal d’activité qui a provoqué l’activation de cette règle d’alerte du journal d’activité. |
| properties.resourceId | L’ID de ressource depuis l’événement du journal d’activité qui a provoqué l’activation de cette règle d’alerte du journal d’activité. |
| properties.eventTimestamp | L’horodatage des événements de l’événement du journal d’activité qui a provoqué l’activation de cette règle d’alerte du journal d’activité. |
| properties.operationName | Le nom de l’opération depuis l’événement du journal d’activité qui a provoqué l’activation de cette règle d’alerte du journal d’activité. |
| Properties.Status | L’état depuis l’événement du journal d’activité qui a provoqué l’activation de cette règle d’alerte du journal d’activité.|

#### <a name="properties-for-metric-alerts"></a>Propriétés des alertes métriques
| Nom de l’élément | Description |
| --- | --- |
| Propriétés. RuleUri | L’ID de ressource de la règle d’alerte métrique elle-même. |
| Propriétés. Nom_règle | Le nom de la règle d’alerte métrique. |
| Propriétés. RuleDescription | La description de la règle d’alerte métrique (telle que définie dans la règle d’alerte). |
| Propriétés. Seuil | La valeur de seuil utilisée dans l’évaluation de la règle d’alerte métrique. |
| properties.WindowSizeInMinutes | La taille de la fenêtre utilisée dans l’évaluation de la règle d’alerte métrique. |
| properties.Aggregation | Le type d’agrégation défini dans la règle d’alerte métrique. |
| properties.Operator | L’opérateur conditionnel utilisé dans l’évaluation de la règle d’alerte métrique. |
| properties.MetricName | Le nom métrique utilisé dans l’évaluation de la règle d’alerte métrique. |
| properties.MetricUnit | L’unité métrique utilisée dans l’évaluation de la règle d’alerte métrique. |

## <a name="autoscale"></a>Autoscale
Cette catégorie contient l’enregistrement de tous les événements liés au fonctionnement du moteur de mise à l’échelle automatique selon les paramètres d’échelle automatique définis dans votre abonnement. Un exemple du type d’événement que vous pouvez voir dans cette catégorie est « Échec de l’action de monter en puissance de la mise à l’échelle automatique. » À l’aide de la mise à l’échelle automatique, vous pouvez automatiquement augmenter ou diminuer la taille des instances dans un type de ressource pris en charge basé sur l’heure du jour et/ou les données de charge (métriques) à l’aide d’un paramètre de mise à l’échelle automatique. Lorsque les conditions sont remplies pour monter ou descendre en puissance, les événements de démarrage réussis ou échoués sont enregistrés dans cette catégorie.

### <a name="sample-event"></a>Exemple d’événement
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Description des propriétés
| Nom de l’élément | Description |
| --- | --- |
| caller | Always Microsoft.Insights/autoscaleSettings |
| channels | Toujours « Admin, opération » |
| réclamations | Objet blob JSON avec le type de SPN (nom principal de service) ou de ressource du moteur de mise à l’échelle automatique. |
| correlationId | Un GUID au format chaîne. |
| description |Description textuelle statique de l’événement de mise à l’échelle automatique. |
| eventDataId |Identificateur unique de l'événement de mise à l’échelle automatique. |
| level |Niveau de l’événement. L’une des valeurs suivantes : « Critique », « Erreur », « Avertissement » et « Informatif » |
| resourceGroupName |Nom du groupe de ressources du paramètre de mise à l’échelle automatique. |
| resourceProviderName |Nom du fournisseur de ressources du paramètre de mise à l’échelle automatique. |
| resourceId |ID de ressource du paramètre de mise à l’échelle automatique. |
| operationId |Un GUID partagé par les événements correspondant à une opération unique. |
| operationName |Nom de l’opération. |
| properties |Jeu de paires `<Key, Value>` (c’est-à-dire Dictionary) décrivant les détails de l’événement. |
| properties.Description | Description détaillée de ce que fait le moteur de mise à l’échelle automatique. |
| properties.ResourceName | ID de ressource de la ressource affectée (la ressource sur laquelle l’action de mise à l’échelle a été effectuée) |
| properties.OldInstancesCount | Le nombre d’instances avant la prise d’effet de l’action de mise à l’échelle automatique. |
| properties.NewInstancesCount | Le nombre d’instances après la prise d’effet de l’action de mise à l’échelle automatique. |
| properties.LastScaleActionTime | Horodatage de lorsque l’action de mise à l’échelle s’est produite. |
| status |Chaîne décrivant l’état de l’opération. Voici plusieurs valeurs courantes : « Started », « In Progress », « Succeeded », « Failed », « Active », « Resolved ». |
| subStatus | Généralement, nul pour la mise à l’échelle automatique. |
| eventTimestamp |Horodatage lorsque l’événement a été généré par le service Azure traitant la demande correspondant à l’événement. |
| submissionTimestamp |Horodatage lorsque l’événement est devenu disponible pour l’interrogation. |
| subscriptionId |ID d’abonnement Azure. |

## <a name="security"></a>Sécurité
Cette catégorie contient l’enregistrement de toutes les alertes générées par Azure Security Center. Voici un exemple du type d’événement que vous pouvez voir dans cette catégorie : « Suspicious double extension file executed. » (Fichier à extension double suspect exécuté.).

### <a name="sample-event"></a>Exemple d’événement
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Description des propriétés
| Nom de l’élément | Description |
| --- | --- |
| channels | Toujours Operation (Opération). |
| correlationId | Un GUID au format chaîne. |
| description |Description textuelle statique de l’événement de sécurité. |
| eventDataId |Identificateur unique de l’événement de sécurité. |
| eventName |Nom convivial de l’événement de sécurité. |
| category | Toujours « Security » |
| id |URI (Unique Resource Identifier) de l’événement de sécurité. |
| level |Niveau de l’événement. L’une des valeurs suivantes : « Critique », « Erreur », « Avertissement » ou « Informatif » |
| resourceGroupName |Nom du groupe de ressources de la ressource. |
| resourceProviderName |Nom du fournisseur de ressources pour Azure Security Center. Toujours Microsoft.Security. |
| resourceType |Type de ressource qui a généré l’événement de sécurité, par exemple « Microsoft.Security/locations/alerts ». |
| resourceId |ID de ressource de l’alerte de sécurité. |
| operationId |Un GUID partagé par les événements correspondant à une opération unique. |
| operationName |Nom de l’opération. |
| properties |Jeu de paires `<Key, Value>` (c’est-à-dire Dictionary) décrivant les détails de l’événement. Ces propriétés varient selon le type d’alerte de sécurité. Pour obtenir une description des types d’alertes qui proviennent de Security Center, consultez [cette page](../../security-center/security-center-alerts-type.md). |
| properties.Severity |Niveau de gravité. Les valeurs possibles sont High (Élevé), Medium (Moyen) ou Low (Bas). |
| status |Chaîne décrivant l’état de l’opération. Voici plusieurs valeurs courantes : « Started », « In Progress », « Succeeded », « Failed », « Active », « Resolved ». |
| subStatus | Généralement nul pour les événements de sécurité. |
| eventTimestamp |Horodatage lorsque l’événement a été généré par le service Azure traitant la demande correspondant à l’événement. |
| submissionTimestamp |Horodatage lorsque l’événement est devenu disponible pour l’interrogation. |
| subscriptionId |ID d’abonnement Azure. |

## <a name="recommendation"></a>Recommandation
Cette catégorie contient l’enregistrement de toutes les nouvelles recommandations générées pour vos services. Exemple de recommandation : « Utiliser les groupes à haute disponibilité pour une meilleure tolérance de panne. » Quatre types d’événements Recommandation peuvent être générés : Haute disponibilité, Performances, Sécurité et Optimisation des coûts. 

### <a name="sample-event"></a>Exemple d’événement
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Description des propriétés
| Nom de l’élément | Description |
| --- | --- |
| channels | Toujours Operation (Opération). |
| correlationId | Un GUID au format chaîne. |
| description |Description textuelle statique de l’événement de recommandation |
| eventDataId | Identificateur unique de l’événement de recommandation. |
| category | Toujours « Recommandation » |
| id |Identificateur de ressource unique de l’événement de recommandation. |
| level |Niveau de l’événement. L’une des valeurs suivantes : « Critique », « Erreur », « Avertissement » ou « Informatif » |
| operationName |Nom de l’opération.  Toujours « Microsoft.Advisor/generateRecommendations/action »|
| resourceGroupName |Nom du groupe de ressources de la ressource. |
| resourceProviderName |Nom du fournisseur de ressources pour la ressource à laquelle cette recommandation s’applique, comme « MICROSOFT.COMPUTE » |
| resourceType |Nom du type de ressource pour la ressource à laquelle cette recommandation s’applique, comme « MICROSOFT.COMPUTE/virtualmachines » |
| resourceId |ID de ressource de la ressource à laquelle la recommandation s’applique |
| status | Toujours « Active » |
| submissionTimestamp |Horodatage lorsque l’événement est devenu disponible pour l’interrogation. |
| subscriptionId |ID d’abonnement Azure. |
| properties |Jeu de paires `<Key, Value>` (c’est-à-dire, Dictionary) décrivant les détails de la recommandation.|
| properties.recommendationSchemaVersion| Version de schéma des propriétés de la recommandation publiée dans l’entrée du journal d’activité |
| properties.recommendationCategory | Catégorie de la recommandation. Les valeurs possibles sont « High Availability » (Haute disponibilité), « Performance », « Security » (Sécurité) et « Cost » (Coût) |
| properties.recommendationImpact| Impact de la recommandation. Les valeurs possibles sont « High » (Élevé), « Medium » (Moyen) ou « Low » (Bas) |
| properties.recommendationRisk| Risque de la recommandation. Les valeurs possibles sont « Error » (Erreur), « Warning » (Avertissement) et « None » (Aucun). |

## <a name="policy"></a>Stratégie

Cette catégorie contient les enregistrements de toutes les opérations d’action à effet effectuées par [Azure Policy](../../governance/policy/overview.md). Cette catégorie pourrait par exemple contenir les types d’événements _Audit_ et _Deny_ (Refus). Chaque action effectuée par Policy est modélisée en tant qu’opération sur une ressource.

### <a name="sample-policy-event"></a>Exemple d’événement Azure Policy

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Descriptions des propriétés d’événements Azure Policy

| Nom de l’élément | Description |
| --- | --- |
| autorisation | Tableau de propriétés RBAC de l’événement. Pour les nouvelles ressources, il s’agit de l’action et de l’étendue de la requête ayant déclenché l’évaluation. Pour les ressources existantes, l’action est « Microsoft.Resources/checkPolicyCompliance/read ». |
| caller | Pour les nouvelles ressources, il s’agit de l’identité qui a lancé un déploiement. Pour les ressources existantes, il s’agit du GUID du fournisseur de ressources Microsoft Azure Policy Insights. |
| channels | Les événements Azure Policy utilisent uniquement le canal « Operation ». |
| réclamations | Le jeton JWT utilisé par Active Directory pour authentifier l’utilisateur ou l’application afin d’effectuer cette opération dans Resource Manager. |
| correlationId | Généralement un GUID au format chaîne. Les événements qui partagent un correlationId appartiennent à la même action uber. |
| description | Ce champ est vide pour les événements Azure Policy. |
| eventDataId | Identificateur unique d’un événement. |
| eventName | « BeginRequest » ou « EndRequest ». « BeginRequest » est utilisé pour les évaluations auditIfNotExists et deployIfNotExists retardées, et quand un effet deployIfNotExists démarre un déploiement de modèle. Toutes les autres opérations retournent « EndRequest ». |
| category | Déclare l’événement de journal d’activité comme appartenant à « Policy ». |
| eventTimestamp | Horodatage lorsque l’événement a été généré par le service Azure traitant la demande correspondant à l’événement. |
| id | Identificateur unique de l’événement sur la ressource spécifique. |
| level | Niveau de l’événement. Audit utilise « Warning » et Deny utilise « Error ». Une erreur auditIfNotExists ou deployIfNotExists peut générer « Warning » ou « Error », en fonction du niveau de gravité. Tous les autres événements Azure Policy utilisent « Informational ». |
| operationId | Un GUID partagé par les événements correspondant à une opération unique. |
| operationName | Nom de l’opération, directement en corrélation avec l’effet Azure Policy. |
| resourceGroupName | Nom du groupe de ressources de la ressource évaluée. |
| resourceProviderName | Nom du fournisseur de ressources de la ressource évaluée. |
| resourceType | Pour les nouvelles ressources, il s’agit du type en cours d’évaluation. Pour les ressources existantes, retourne « Microsoft.Resources/checkPolicyCompliance ». |
| resourceId | ID de ressource de la ressource évaluée. |
| status | Chaîne décrivant l’état du résultat de l’évaluation Azure Policy. La plupart des évaluations Azure Policy retournent « Succeeded », mais un effet Deny retourne « Failed ». Les erreurs dans auditIfNotExists ou deployIfNotExists retournent également « Failed ». |
| subStatus | Le champ est vide pour les événements Azure Policy. |
| submissionTimestamp | Horodatage lorsque l’événement est devenu disponible pour l’interrogation. |
| subscriptionId | ID d’abonnement Azure. |
| properties.isComplianceCheck | Retourne « False » quand une nouvelle ressource est déployée ou que les propriétés Resource Manager d’une ressource existante sont mises à jour. Tous les autres [déclencheurs d’évaluation](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) génèrent la valeur « True ». |
| properties.resourceLocation | Région Azure de la ressource en cours d’évaluation. |
| properties.ancestors | Liste séparée par des virgules de groupes d’administration parents, classés du parent direct au grand-parent le plus éloigné. |
| properties.policies | Inclut des détails sur la définition de stratégie, l’affectation, l’effet et les paramètres dont cette évaluation Azure Policy est le résultat. |
| relatedEvents | Ce champ est vide pour les événements Azure Policy. |

## <a name="mapping-to-diagnostic-logs-schema"></a>Mappage vers le schéma des journaux de diagnostic

Lorsque vous diffusez en continu le contenu du journal d’activité Azure vers un compte de stockage ou vers un espace de noms Event Hubs, les données suivent le [schéma des journaux de diagnostic Azure](./diagnostic-logs-schema.md). Voici le mappage des propriétés du schéma ci-dessus vers le schéma des journaux de diagnostic :

| Propriété du schéma des journaux de diagnostic | Propriété du schéma de l’API REST Journal d’activité | Notes |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | subscriptionId, resourceType et resourceGroupName sont déduits à partir de resourceId. |
| operationName | operationName.value |  |
| category | Partie du nom de l’opération | Fraction du type d’opération - « Écrire »/« Supprimer »/« Action » |
| resultType | status.value | |
| resultSignature | substatus.value | |
| resultDescription | description |  |
| durationMS | N/A | Toujours 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identité | propriétés de revendication et d’autorisation |  |
| Niveau | Niveau |  |
| location | N/A | Emplacement de traitement de l’événement. *Il ne s’agit pas de l’emplacement de la ressource, mais de l’endroit où l’événement a été traité. Cette propriété sera supprimée dans une prochaine mise à jour.* |
| properties | properties.eventProperties |  |
| properties.eventCategory | category | Si properties.eventCategory n’est pas présent, la catégorie est « Administrative » |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | properties |  |


## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur le journal d’activité](activity-logs-overview.md)
* [Exporter le journal d’activité vers Stockage Azure ou Event Hubs](activity-log-export.md)

