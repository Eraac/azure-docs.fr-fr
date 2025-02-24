---
title: Solution Azure Key Vault dans Azure Monitor | Microsoft Docs
description: La solution Azure Key Vault dans Azure Monitor permet de consulter les journaux Azure Key Vault.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: bwren
ms.openlocfilehash: 481b643f2f7201a2a1745c7aef9ddd81883da020
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60498346"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Solution Azure Key Vault Analytics dans Azure Monitor

![Symbole de Key Vault](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Vous pouvez utiliser la solution Azure Key Vault dans Azure Monitor pour consulter les journaux AuditEvent Azure Key Vault.

Pour l’utiliser, vous devez activer la journalisation des diagnostics d’Azure Key Vault et diriger les diagnostics vers un espace de travail Log Analytics. Il n’est pas nécessaire d’écrire les journaux d’activité dans le stockage Blob Azure.

> [!NOTE]
> En janvier 2017, le mode d’envoi des journaux d’activité de Key Vault vers Log Analytics a changé. Si le titre de la solution Key Vault que vous utilisez comprend la mention *(déprécié)* , consultez la section [Migration à partir d’une ancienne version de Key Vault](#migrating-from-the-old-key-vault-solution) pour connaître la marche à suivre.
>
>

## <a name="install-and-configure-the-solution"></a>Installer et configurer la solution
Suivez les instructions suivantes pour installer et configurer la solution Azure Key Vault :

1. Procédez de la manière décrite dans [Ajouter des solutions Azure Monitor à partir de la Galerie Solutions](../../azure-monitor/insights/solutions.md) pour ajouter la solution Azure Key Vault à votre espace de travail Log Analytics.
2. Activer la journalisation des diagnostics pour les ressources Key Vault à surveiller à l’aide du [portai](#enable-key-vault-diagnostics-in-the-portal) ou de [PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Activer les diagnostics Key Vault dans le portail

1. Dans le portail Azure, accédez à la ressource Key Vault que vous voulez surveiller.
2. Sélectionnez *Paramètres de diagnostic* pour ouvrir la page suivante.

   ![Image de la vignette Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Cliquez sur *Activer les diagnostics* pour ouvrir la page suivante.

   ![Image de la vignette Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Donnez un nom au paramètre de diagnostic.
5. Cochez la case à côté de l’option *Envoyer à Log Analytics*.
6. Sélectionnez un espace de travail Log Analytics existant ou créez-en un.
7. Pour activer les journaux d’activité *AuditEvent*, cochez la case située sous Journal.
8. Cliquez sur *Enregistrer* pour activer la journalisation des diagnostics dans un espace de travail Log Analytics.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Activer les diagnostics Key Vault avec PowerShell
Le script PowerShell suivant fournit un exemple illustrant comment utiliser `Set-AzDiagnosticSetting` pour activer la journalisation des diagnostics pour Key Vault :
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Examiner les détails de la collecte de données par Azure Key Vault
La solution Azure Key Vault collecte des journaux de diagnostics directement à partir de Key Vault.
Il n’est pas nécessaire d’écrire les journaux d’activité dans le stockage Blob Azure, et aucun agent n’est requis pour la collecte de données.

Le tableau suivant présente les méthodes de collecte des données et d’autres détails sur le mode de collecte de données pour Azure Key Vault.

| Plateforme | Agent direct | Agent Systems Center Operations Manager | Azure | Operations Manager requis ? | Données de l’agent Operations Manager envoyées via un groupe d’administration | Fréquence de collecte |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | à l'arrivée |

## <a name="use-azure-key-vault"></a>Utiliser Azure Key Vault
Une fois la [solution installée](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview), affichez les données Key Vault en cliquant sur la vignette **Key Vault Analytics** située dans la page **Vue d’ensemble** d’Azure Monitor. Ouvrez cette page à partir du menu **Azure Monitor** en cliquant sur **Plus** sous la section **Insights**. 

![Image de la vignette Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-tile.png)

Après avoir cliqué sur la vignette **Key Vault Analytics**, vous pouvez consulter des récapitulatifs de vos journaux et en approfondir les détails pour les catégories suivantes :

* Volume de toutes les opérations de Key Vault dans le temps
* Volumes des opérations en échec dans le temps
* Latence opérationnelle moyenne par opération
* Qualité de service des opérations, avec le nombre d’opérations qui prennent plus de 1 000 ms et la liste de ces opérations

![Image du tableau de bord d’Azure Key Vault](media/azure-key-vault/log-analytics-keyvault01.png)

![Image du tableau de bord d’Azure Key Vault](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Pour afficher les détails d’une opération
1. Dans la page **Vue d’ensemble**, cliquez sur la vignette **Key Vault Analytics**.
2. Sur le tableau de bord **Azure Key Vault**, examinez les informations résumées dans l’un des panneaux, puis cliquez sur l’une d’elles pour afficher des informations détaillées dans la page Recherche de journal.

    Sur l’une des pages de recherche de journal, vous pouvez afficher les résultats par date, les résultats détaillés et votre historique de recherches de journaux. Vous pouvez également filtrer par facettes pour affiner les résultats.

## <a name="azure-monitor-log-records"></a>Enregistrements de journaux Azure Monitor
La solution Azure Key Vault analyse les enregistrements de type **KeyVaults** qui sont collectés à partir des [journaux AuditEvent](../../key-vault/key-vault-logging.md) dans les Diagnostics Azure.  Les propriétés de ces enregistrements figurent dans le tableau suivant :  

| Propriété | Description |
|:--- |:--- |
| Type |*AzureDiagnostics* |
| SourceSystem |*Microsoft Azure* |
| callerIpAddress |Adresse IP du client qui a effectué la demande. |
| Catégorie | *AuditEvent* |
| CorrelationId |GUID facultatif que le client peut transférer pour mettre en corrélation les journaux d’activité côté client avec les journaux d’activité côté service (Key Vault). |
| DurationMs |Délai nécessaire pour répondre à la demande API REST, en millisecondes. La latence du réseau n’étant pas incluse dans ce chiffre, le temps mesuré côté client peut ne pas correspondre à cette durée. |
| httpStatusCode_d |Code d’état HTTP retourné par la demande (par exemple, *200*) |
| id_s |ID unique de la demande. |
| identity_claim_appid_g | GUID de l’ID d’application |
| OperationName |Nom de l’opération, comme décrit dans [journalisation d’Azure Key Vault](../../key-vault/key-vault-logging.md) |
| operationVersion |Version d’API REST demandée par le client (par exemple *2015-06-01*) |
| requestUri_s |URI de la demande. |
| Ressource |Nom du Key Vault. |
| ResourceGroup |Groupe de ressources du Key Vault. |
| ResourceId |ID de ressource Azure Resource Manager Pour les journaux d’activité de coffre de clés, il s’agit de l’ID de ressource du coffre de clés. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *VAULTS* |
| ResultSignature |État HTTP (par exemple *OK*) |
| ResultType |Résultat de la demande de l’API REST (par exemple, *Réussite*) |
| SubscriptionId |ID de l’abonnement Azure contenant le Key Vault. |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migration à partir d’une ancienne version de Key Vault
En janvier 2017, le mode d’envoi des journaux d’activité de Key Vault vers Log Analytics a changé. Ces modifications présentent les avantages suivants :
+ Les journaux sont écrits directement dans un espace de travail Log Analytics sans avoir besoin d’utiliser un compte de stockage.
+ La durée de latence est plus courte entre le moment où les journaux d’activité sont générés et celui où ils deviennent disponibles dans Log Analytics.
+ Le nombre d’étapes de configuration est réduit.
+ Tous les types de diagnostics Azure sont au même format.

Pour utiliser la solution mise à jour :

1. [Configurez les diagnostics pour qu’ils soient directement envoyés à un espace de travail Log Analytics à partir de Key Vault](#enable-key-vault-diagnostics-in-the-portal).  
2. Activez la solution Azure Key Vault en procédant de la manière décrite dans [Ajouter des solutions Azure Monitor à partir de la Galerie Solutions](../../azure-monitor/insights/solutions.md).
3. Mettez à jour les requêtes, les tableaux de bord et les alertes enregistrés pour qu’ils utilisent le nouveau type de données.
   + Le type KeyVaults est remplacé par : AzureDiagnostics. Vous pouvez utiliser ResourceType pour filtrer les journaux d’activité Key Vault.
   + Au lieu de `KeyVaults`, utilisez `AzureDiagnostics | where ResourceType'=="VAULTS"`.
   + Champs : (les noms de champs sont sensibles à la casse)
   + Pour tous les champs dont le suffixe est \_s, \_d ou \_g, remplacez le premier caractère du nom par une lettre minuscule.
   + Pour tous les champs dont le suffixe est \_o, les données sont réparties sur plusieurs champs, selon les noms de champs imbriqués. Par exemple, l’UPN de l’appelant est stocké dans un champ `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`.
   + Le champ CallerIpAddress est remplacé par CallerIPAddress
   + Le champ RemoteIPCountry n’est plus présent
4. Supprimez la solution *Key Vault Analytics (déprécié)* . Avec PowerShell, utilisez `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`.

Les données collectées avant la modification ne seront pas visibles dans la nouvelle solution. Vous pouvez continuer à interroger ces données à l’aide de l’ancien type et des anciens noms de champs.

## <a name="troubleshooting"></a>Résolution de problèmes
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Étapes suivantes
* Utilisez les [Requêtes de journal dans Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) pour afficher des données Azure Key Vault détaillées.
