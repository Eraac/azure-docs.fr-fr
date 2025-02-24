---
title: Préparer la migration des alertes classiques Azure Monitor en mettant à jour les applications logiques et les runbooks
description: Découvrez comment modifier les webhooks, les applications logiques et les runbooks pour préparer la migration volontaire.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: bdbd45c2b10dec8f1c0a85110747a470e818dbf9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66015605"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Préparer les applications logiques et les runbooks pour la migration des règles d’alerte classiques

Comme [précédemment annoncé](monitoring-classic-retirement.md), les alertes classiques dans Azure Monitor seront mises hors service en septembre 2019 (c’était initialement prévu pour juillet 2019). Un outil de migration est disponible dans le Portail Azure pour les clients qui utilisent des règles d’alerte classiques et qui souhaitent déclencher la migration eux-mêmes.

> [!NOTE]
> En raison de retards dans le déploiement de l’outil de migration, la date de mise hors service des alertes classiques a été repoussée au 31 août 2019 (la date annoncée à l’origine était le 30 juin 2019).

Si vous choisissez de migrer volontairement vos règles d’alerte classiques vers de nouvelles règles d’alerte, n’oubliez pas qu’il existe certaines différences entre les deux systèmes. Cet article décrit ces différences et explique comment préparer la modification.

## <a name="api-changes"></a>Modifications d'API

Les API qui créent et gèrent les règles d’alerte classiques (`microsoft.insights/alertrules`) diffèrent des API qui créent et gèrent les nouvelles alertes de métrique (`microsoft.insights/metricalerts`). Si vous créez et gérez actuellement des règles d’alerte classiques par programmation, mettez à jour vos scripts de déploiement pour utiliser les nouvelles API.

Le tableau suivant référence les interfaces de programmation pour les alertes classiques et les nouvelles alertes :

|         |Alertes classiques  |Nouvelles alertes de métrique |
|---------|---------|---------|
|API REST     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az monitor metrics alert](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Référence](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Référence](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Modèle Azure Resource Manager | [Pour les alertes classiques](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Pour les nouvelles alertes de métrique](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Modifications de charge utile de notification

Le format de charge utile de notification est légèrement différent pour les [règles d’alerte classiques](alerts-webhooks.md) et les [nouvelles alertes de métrique](alerts-metric-near-real-time.md#payload-schema). Si des actions de webhook, d’application logique ou de runbook sont déclenchées par des règles d’alerte classiques, vous devez mettre à jour ces points de terminaison de notification pour accepter le format de charge utile des nouvelles alertes de métrique.

Utilisez le tableau suivant pour mapper les champs de charge utile de webhook du format classique vers le nouveau format :

|  |Alertes classiques  |Nouvelles alertes de métrique |
|---------|---------|---------|
|L’alerte a-t-elle été activée ou résolue ?    | **statut**       | **data.status** |
|Informations contextuelles sur l’alerte     | **context**        | **data.context**        |
|Date et heure auxquelles l’alerte a été activée ou résolue     | **context.timestamp**       | **data.context.timestamp**        |
| ID de la règle d’alerte | **context.id** | **data.context.id** |
| Nom de la règle d’alerte | **context.name** | **data.context.name** |
| Description de la règle d’alerte | **context.description** | **data.context.description** |
| Condition de règle d’alerte | **context.condition** | **data.context.condition** |
| Nom de métrique | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Agrégation de temps (agrégation de la métrique sur la fenêtre d’évaluation)| **data.context.condition.timeAggregation** | **data.context.condition.timeAggregation** |
| Période d’évaluation | **context.condition.windowSize** | **data.context.condition.windowSize** |
| Opérateur (comparaison entre la valeur de métrique agrégée et le seuil) | **context.condition.operator** | **data.context.condition.operator** |
| Seuil | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| Valeur de métrique | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| Identifiant d’abonnement | **context.subscriptionId** | **data.context.subscriptionId** |
| Groupe de ressources de la ressource affectée | **context.resourceGroup** | **data.context.resourceGroup** |
| Nom de la ressource affectée | **context.resourceName** | **data.context.resourceName** |
| Type de la ressource affectée | **context.resourceType** | **data.context.resourceType** |
| ID de ressource de la ressource affectée | **context.resourceId** | **data.context.resourceId** |
| Lien direct vers la page de résumé de la ressource sur le Portail | **context.portalLink** | **data.context.portalLink** |
| Champs de charge utile personnalisée à transmettre à l’application logique ou au webhook | **properties** | **data.properties** |

Les charges utiles sont similaires, comme vous pouvez le voir. La section suivante propose :

- Des détails sur la modification des applications logiques pour utiliser le nouveau format.
- Un exemple de runbook qui analyse la charge utile de notification pour les nouvelles alertes.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Modifier une application logique pour recevoir une notification d’alerte métrique

Si vous utilisez des applications logiques avec les alertes classiques, vous devez modifier votre code d’application logique pour analyser la charge utile des nouvelles alertes de métrique. Procédez comme suit :

1. Créez une application logique.

1. Utilisez le modèle « Azure Monitor - Metrics Alert Handler ». Ce modèle comporte un déclencheur de **requête HTTP** avec le schéma approprié défini.

    ![logic-app-template](media/alerts-migration/logic-app-template.png "Modèle d’alerte métrique")

1. Ajoutez une action pour héberger votre logique de traitement.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Utiliser un runbook d’automatisation qui reçoit une notification d’alerte de métrique

L’exemple suivant fournit le code PowerShell à utiliser dans votre runbook. Ce code peut analyser les charges utiles pour les règles d’alerte de métrique classiques et les nouvelles règles d’alerte de métrique.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Pour obtenir un exemple complet de runbook qui arrête une machine virtuelle lorsqu’une alerte est déclenchée, consultez la [documentation Azure Automation](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Intégration des partenaires par le biais de webhooks

La plupart de [nos partenaires qui s’intègrent avec les alertes classiques](https://docs.microsoft.com/azure/azure-monitor/platform/partners) prennent déjà en charge les nouvelles alertes de métrique. Voici quelques intégrations connues qui fonctionnent déjà avec les nouvelles alertes de métrique :

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Si vous utilisez une intégration des partenaires qui n’est pas répertoriée ici, vérifiez auprès du fournisseur d’intégration que l’intégration fonctionne avec les nouvelles alertes de métrique.

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour utiliser l’outil de migration](alerts-using-migration-tool.md)
- [Comprendre le fonctionnement de l’outil de migration](alerts-understand-migration.md)
