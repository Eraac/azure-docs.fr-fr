---
title: Gérer l’utilisation et les coûts pour les journaux Azure Monitor | Microsoft Docs
description: Découvrez comment changer le plan tarifaire et gérer la stratégie de rétention et de volume des données de votre espace de travail Log Analytics dans Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: bcfefc9698f7f251e99531750e19e7c06395e064
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655703"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gérer l’utilisation et les coûts avec les journaux Azure Monitor

> [!NOTE]
> Cet article décrit comment contrôler vos coûts dans Azure Monitor en définissant la période de rétention des données de votre espace de travail Log Analytics.  Pour plus d’informations, consultez l’article suivant.
> - L’article [Monitoring usage and estimated costs](usage-estimated-costs.md) (Surveillance de l’utilisation et estimation des coûts) explique comment visualiser l’utilisation et les coûts estimés avec plusieurs fonctionnalités de surveillance Azure en fonction des différents modèles de tarification. Il explique également comment modifier votre modèle de tarification.

Les journaux Azure Monitor sont conçus pour la mise à l’échelle et la prise en charge de la collecte, de l’indexation et du stockage de quantités importantes de données quotidiennes provenant de toute source de votre entreprise ou déployées dans Azure.  Si ce peut être un élément moteur pour votre organisation, la rentabilité est au final ce qui importe le plus. À cette fin, il est important de comprendre que le coût d’un espace de travail Log Analytics n’est pas seulement fonction du volume des données collectées, mais qu’il dépend aussi du plan sélectionné et de la durée de stockage des données générées à partir de vos sources connectées.  

Dans cet article, nous allons passer en revue les méthodes permettant de surveiller de façon proactive la croissance du stockage et du volume de données, et définir des limites pour contrôler les coûts associés. 

Le coût des données peut être considérable en fonction des facteurs suivants : 

- Le volume de données généré et ingéré dans l’espace de travail 
    - Le nombre de solutions de gestion activées
    - Le nombre de systèmes surveillés
    - Le type de données collectées à partir de chaque ressource analysée 
- La durée pendant laquelle que vous décidez de conserver vos données 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>Comprendre l’utilisation de votre espace de travail et l’estimation des coûts

Les journaux Azure Monitor permettent d’estimer facilement les coûts en fonction des modèles d’utilisation récente. Pour ce faire, utilisez le tableau de bord **Utilisation et estimation des coûts de Log Analytics** pour examiner et analyser l’utilisation de données. Ce tableau montre la quantité de données collectée par chaque solution, la quantité de données conservée, et fournit une estimation des coûts en fonction de la quantité de données ingérées et de toute rétention supplémentaire au-delà du montant inclus.

![Utilisation et estimation des coûts](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Pour explorer vos données plus en détail, cliquez sur l’icône en haut à droite d’un des graphiques sur la page **Utilisation et estimation des coûts**. Vous pouvez maintenant utiliser cette requête pour obtenir de plus amples informations sur votre utilisation.  

![Vue Journaux d’activité](media/manage-cost-storage/logs.png)

La page **Utilisation et estimation des coûts** vous permet de consulter votre volume de données pour le mois. Ce volume inclut toutes les données reçues et conservées dans votre espace de travail Log Analytics.  En haut de la page, cliquez sur **Détails d’utilisation** pour afficher le tableau de bord d’utilisation contenant des informations sur les tendances de volume de données par source, ordinateur et offre. Pour afficher et définir une limite quotidienne ou pour modifier la période de rétention, cliquez sur **Gestion du volume de données**.
 
Les frais liés à Log Analytics sont ajoutés à votre facture Azure. Les informations relatives à votre facture Azure sont affichées dans la section Facturation du portail Azure ou sur le [portail de facturation Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Limite quotidienne

Vous pouvez configurer une limite quotidienne et restreindre l’ingestion quotidienne de votre espace de travail, mais soyez vigilant, car votre objectif n’est pas d’atteindre la limite quotidienne.  Si vous l’atteignez, vous perdrez des données pour le reste de la journée, ce qui peut impacter les autres services et solutions Azure dont les fonctionnalités dépendent de la disponibilité de données à jour dans l’espace de travail.  Ces fonctionnalités peuvent correspondre, par exemple, à votre capacité à observer et à recevoir des alertes lorsque les conditions d’intégrité des ressources de service informatique sont impactées.  La limite quotidienne est destinée à être utilisée comme un moyen de gérer l’augmentation inattendue du volume de données de vos ressources managées ou lorsque vous souhaitez limiter les frais non planifiés de votre espace de travail.  

Lorsque cette limite quotidienne est atteinte, la collecte des types de données facturables s’arrête pour le reste de la journée. Une bannière d’avertissement s’affiche en haut de la page de l’espace de travail Log Analytics sélectionné, et un événement d’opération est envoyé vers la table *Opération* dans la catégorie **LogManagement**. La collecte de données reprend après l’heure de réinitialisation définie dans *La limite quotidienne est fixée à*. Nous vous recommandons de définir une règle d’alerte en fonction de cet événement d’opération, configuré pour avertir lorsque la limite de données quotidienne a été atteinte. 

> [!NOTE]
> La limite quotidienne n’arrête pas la collecte de données à partir d’Azure Security Center.

### <a name="identify-what-daily-data-limit-to-define"></a>Identifier la limite de données quotidienne à définir

Consultez [Utilisation et estimation des coûts Log Analytics](usage-estimated-costs.md) pour comprendre les tendances d’ingestion des données et la limite quotidienne de volume à définir. Effectuez cette opération avec précaution, car vous ne pourrez plus surveiller vos ressources, une fois que la limite sera atteinte. 

### <a name="manage-the-maximum-daily-data-volume"></a>Gérer le volume de données maximal quotidien

Les étapes suivantes décrivent la configuration d’une limite pour gérer le volume des données ingérées quotidiennement par l’espace de travail Log Analytics.  

1. À partir de votre espace de travail, sélectionnez **Utilisation et estimation des coûts** dans le volet gauche.
2. Cliquez sur **Gestion du volume de données** en haut de la page **Utilisation et estimation des coûts** de l’espace de travail sélectionné. 
3. Par défaut, la limite quotidienne est **DÉSACTIVÉE** : cliquez sur **ACTIVER** pour l’activer, puis définissez la limite de volume de données en Go/jour.

    ![Configurer la limite de données dans Log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Alerte lorsque la limite quotidienne est atteinte

Si nous présentons un indice visuel dans le portail Azure lorsque le seuil limite des données est atteint, ce comportement n’est pas nécessairement en harmonie avec la façon dont vous gérez les problèmes opérationnels exigeant une attention immédiate.  Pour recevoir une notification d’alerte, vous pouvez créer une règle d’alerte dans Azure Monitor.  Pour en savoir plus, reportez-vous à l’article sur [la création, l’affichage et la gestion des alertes](alerts-metric.md).

Pour vous aider à démarrer, voici les paramètres que nous recommandons pour l’alerte :

- Cible : sélectionnez votre ressource Log Analytics.
- Critères : 
   - Nom du signal : Recherche personnalisée dans les journaux
   - Requête de recherche : Operation | where Detail has 'OverQuota'
   - Basé sur : Nombre de résultats
   - Condition : Supérieur à
   - Seuil : 0
   - Période : 5 (minutes)
   - Fréquence : 5 (minutes)
- Nom de la règle d'alerte : limite de données quotidienne atteinte
- Gravité : avertissement (Sev 1)

Une fois que l’alerte est définie et que la limite est atteinte, l’alerte est déclenchée et effectue la réponse définie dans le groupe d’actions. Elle peut informer votre équipe via des e-mails et des SMS ou automatiser des actions à l’aide de Webhooks ou de runbooks Automation, ou [en s’intégrant à une solution ITSM externe](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Changer la période de rétention des données

Les étapes suivantes décrivent la configuration de la durée de conservation des données de journal dans votre espace de travail.
 
1. À partir de votre espace de travail, sélectionnez **Utilisation et estimation des coûts** dans le volet gauche.
2. Cliquez sur **Gestion du volume de données** en haut de la page **Utilisation et estimation des coûts**.
3. Dans le volet, déplacez le curseur pour augmenter ou diminuer le nombre de jours, puis cliquez sur **OK**.  Si vous avez opté pour le niveau *Gratuit*, vous ne pouvez pas modifier la période de rétention de données et vous devez passer au niveau payant afin de contrôler ce paramètre.

    ![Changer le paramètre de conservation des données de l’espace de travail](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
La rétention peut également être [définie via ARM](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) à l’aide du paramètre `dataRetention`. En outre, si vous définissez la conservation des données sur 30 jours, vous pouvez déclencher un vidage immédiat d’anciennes données à l’aide du paramètre `immediatePurgeDataOn30Days`, ce qui peut être utile pour les scénarios liés à la conformité. Cette fonctionnalité est exposée uniquement via ARM. 

## <a name="legacy-pricing-tiers"></a>Niveaux de tarification hérités

Les abonnements qui incluaient un espace de travail Log Analytics ou une ressource Application Insights avant le 2 avril 2018, ou qui sont liés à un contrat Entreprise commencé avant le 1er février 2019, auront toujours accès aux niveaux tarifaires hérités : **Gratuit**, **Autonome (par Go)** et **Par nœud (OMS)** .  Les espaces de travail du niveau tarifaire Gratuit présentent une ingestion des données quotidienne limitée à 500 Mo (à l’exception des types de données de sécurité collectés par Azure Security Center) et une conservation des données limitée à 7 jours. Le niveau tarifaire Gratuit est conçu à des fins d’évaluation uniquement. Les espaces de travail des niveaux tarifaires Autonome et Par nœud présentent une rétention configurable par l’utilisateur jusqu’à 2 ans. Les espaces de travail créés avant le mois d’avril 2016 ont également accès aux niveaux tarifaires **Standard** et **Premium** d’origine. Plus d’informations sur les limitations des niveaux tarifaires sont disponibles [ici](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Pour utiliser les droits que vous obtenez à l’achat de la suite OMS E1, OMS E2 ou du module complémentaire OMS pour System Center, sélectionnez le niveau tarifaire *Par nœud* de Log Analytics.

Les premiers utilisateurs de Log Analytics ont également accès aux niveaux tarifaires **Standard** et **Premium** d’origine, dont la conservation des données est de 30 et 365 jours, respectivement. 

## <a name="changing-pricing-tier"></a>Changement de niveau tarifaire

Si votre espace de travail Log Analytics a accès aux niveaux tarifaires hérités, pour changer de niveau tarifaire hérité :

1. Dans le portail Azure, à partir du volet des abonnements Log Analytics, sélectionnez un espace de travail.

2. Dans le volet de l’espace de travail, sous **Général**, sélectionnez **Niveau tarifaire**.  

3. Sous **Niveau tarifaire**, sélectionnez un niveau tarifaire et cliquez sur **Sélectionner**.  
    ![Plan tarifaire sélectionné](media/manage-cost-storage/workspace-pricing-tier-info.png)

Vous pouvez également [définir le niveau tarifaire via ARM](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) à l’aide du paramètre `ServiceTier`. 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Dépannage si Log Analytics ne collecte plus de données

Si vous utilisez le niveau tarifaire hérité Gratuit et que vous avez envoyé plus de 500 Mo de données le même jour, la collecte de données s’arrête pour le reste de la journée. La limite quotidienne est la principale raison pour laquelle Log Analytics arrête la collecte de données ou des données semblent manquantes.  Log Analytics crée un événement de type Opération lorsque la collecte de données démarre et s’arrête. Exécutez la requête suivante dans la recherche pour vérifier si vous atteignez la limite quotidienne et si des données sont manquantes : 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Lorsque la collecte de données s’arrête, le paramètre OperationStatus a la valeur **Warning**. Lorsque la collecte de données démarre, le paramètre OperationStatus a la valeur **Succeeded**. Le tableau suivant décrit les raisons pour lesquelles la collecte de données s’arrête et suggère une action pour la reprendre :  

|Raison pour laquelle la collecte s’arrête| Solution| 
|-----------------------|---------|
|Limite quotidienne de niveau tarifaire hérité Gratuit atteinte |Attendez le jour suivant pour que la collecte redémarre automatiquement ou passez à un niveau tarifaire payant.|
|La limite quotidienne de votre espace de travail a été atteinte|Attendez que la collecte redémarre automatiquement ou augmentez la limite du volume de données quotidien décrite dans la section Gérer le volume de données maximal quotidien. L’heure de réinitialisation de la limite quotidienne s’affiche sur la page **Gestion du volume de données**. |
|Abonnement Azure à l’état interrompu pour la raison suivante :<br> Fin de l’essai gratuit<br> Expiration du Pass Azure<br> Limite de dépense mensuelle atteinte (par exemple, sur un abonnement MSDN ou Visual Studio)|Passer à un abonnement payant<br> Supprimer la limite ou attendre sa réinitialisation|

Pour être informé de l’arrêt de la collecte de données, utilisez les étapes décrites dans *Créer une limite quotidienne de données*. Utilisez les étapes décrites dans [Créer un groupe d’actions](action-groups.md) pour configurer une action d’e-mail, de webhook ou de runbook pour la règle d’alerte. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Résolution des problèmes à l’origine d’une utilisation plus importante que prévu

Une utilisation plus importante est due à l’un des éléments suivants, voire les deux :
- Plus de nœuds que prévu envoient des données à l’espace de travail Log Analytics
- Plus de données que prévu sont envoyées à l’espace de travail Log Analytics

## <a name="understanding-nodes-sending-data"></a>Présentation des nœuds qui envoient des données

Pour plus d’informations sur le nombre d’ordinateurs qui ont envoyé quotidiennement des pulsations au cours du mois passé, utilisez

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

Pour obtenir une liste d’ordinateurs qui seront facturés en tant que nœuds si l’espace de travail se trouve au niveau tarifaire Par nœud hérité, recherchez les nœuds qui envoient des **types de données facturés** (certains types de données sont gratuits). Pour ce faire, utilisez la [propriété](log-standard-properties.md#_isbillable) `_IsBillable` et utilisez le champ tout à gauche du nom de domaine complet. Vous obtenez la liste des ordinateurs avec des données facturées :

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Le nombre de nœuds facturables indiqué peut être estimé comme suit : 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| billableNodes=dcount(computerName)
```

> [!NOTE]
> L’exécution d’analyses sur différents types de données étant coûteuse, utilisez ces requêtes `union withsource = tt *` avec parcimonie. Cette requête remplace l’ancienne méthode d’interrogation d’informations par ordinateur avec le type de données Utilisation.  

Un calcul plus précis de ce qui est réellement facturé consiste à obtenir le nombre d’ordinateurs par heure qui envoient des types de données facturés. (Pour les espaces de travail du niveau tarifaire Par nœud hérité, Log Analytics calcule le nombre de nœuds qui doivent être facturés sur une base horaire.) 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>Présentation du volume de données ingéré

Sur la page **Utilisation et estimation des coûts**, le graphique *Ingestion de données par solution* montre le volume total des données envoyées et la quantité envoyée par chaque solution. Vous pouvez ainsi dégager des tendances, par exemple si l’utilisation des données globales (ou l’utilisation par une solution particulière) augmente, reste stable ou diminue. La requête utilisée pour générer ce résultat est

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

Notez que la clause « where IsBillable = true » exclut les types de données de certaines solutions pour lesquels il n’existe aucun frais d’ingestion. 

Vous pouvez explorer de façon plus précise et déterminer ainsi des tendances pour des types de données spécifiques, par exemple si vous souhaitez étudier les données de journaux d’activité IIS :

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>Volume de données par ordinateur

Pour afficher la **taille** des événements facturables ingérés par ordinateur, utilisez la [propriété](log-standard-properties.md#_billedsize) `_BilledSize` qui fournit la taille en octets :

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

La [propriété](log-standard-properties.md#_isbillable) `_IsBillable` spécifie si les données ingérées occasionnent des frais.

Pour afficher le nombre d’événements **facturables** reçus par ordinateur, utilisez 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by count_ nulls last
```

Si vous souhaitez afficher les types de données facturables qui envoient des données à un ordinateur spécifique, utilisez :

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Volume de données par ressource Azure, groupe de ressources ou abonnement

Pour les données provenant de nœuds hébergés dans Azure, vous pouvez obtenir la **taille** des événements facturables ingérés __par ordinateur__. Utilisez la [propriété](log-standard-properties.md#_resourceid) _ResourceId qui fournit le chemin d’accès complet à la ressource :

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Pour les données provenant de nœuds hébergés dans Azure, vous pouvez obtenir la **taille** des événements facturables ingérés __par abonnement Azure__. Analysez la propriété `_ResourceId` comme suit :

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

La définition de `subscriptionId` sur `resourceGroup` permet d’afficher le volume de données ingéré facturable par groupe de ressources Azure. 


> [!NOTE]
> Certains champs du type de données Utilisation, bien que faisant partie du schéma, sont maintenant déconseillés et leurs valeurs ne seront plus fournies. Il s’agit de **Computer** et des champs liées à l’ingestion (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** et **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Interrogation de types de données courants

Pour explorer plus en détail la source de données d’un type de données particulier, voici quelques exemples de requêtes :

+ une solution de **sécurité**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ une solution de **gestion des journaux**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ le type de données **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ le type de données **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ le type de données **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ le type de données **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Conseils pour réduire le volume de données

Voici quelques suggestions pour réduire le volume de journaux d’activité collectés :

| Source du volume de données important | Comment réduire le volume de données |
| -------------------------- | ------------------------- |
| Événements de sécurité            | Sélectionnez [les événements de sécurité courants ou minimaux](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier). <br> Modifier la stratégie d’audit de sécurité pour collecter les événements nécessaires uniquement. Plus particulièrement, examinez la nécessité de collecter des événements pour : <br> - [plateforme de filtrage de l’audit](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [registre de l’audit](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [système de fichiers de l’audit](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [objet de noyau d’audit](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [manipulation du descripteur de l’audit](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - stockage amovible de l’audit |
| Compteurs de performances       | Modifiez la [configuration du compteur de performances](data-sources-performance-counters.md) de façon à : <br> - Réduire la fréquence de collecte <br> - Réduire le nombre de compteurs de performance |
| Journaux d’événements                 | Modifiez la [configuration du journal d’événements](data-sources-windows-events.md) de façon à : <br> - Réduire le nombre de journaux des événements collectés <br> - Collecter uniquement les niveaux d’événement requis Par exemple, ne collectez pas les événements de niveau *Informations*. |
| syslog                     | Modifiez la [configuration du syslog](data-sources-syslog.md) de façon à : <br> - Réduire le nombre d’installations collectées <br> - Collecter uniquement les niveaux d’événement requis Par exemple, ne collectez pas les événements de niveau *Informations* et *Débogage*. |
| AzureDiagnostics           | Modifiez la collection de journaux de ressources pour : <br> - Réduire le nombre de journaux d’activité d’envoi de ressources à Log Analytics <br> - Collecter uniquement les journaux d’activité nécessaires |
| Données de solution d’ordinateurs n’ayant pas besoin de la solution | Utilisez le [ciblage de solution](../insights/solution-targeting.md) pour collecter des données des groupes d’ordinateurs requis uniquement. |

### <a name="getting-security-and-automation-node-counts"></a>Obtention du nombre de nœuds Security et Automation

Si vous utilisez un niveau tarifaire « Par nœud (OMS) », vous êtes facturé en fonction du nombre de nœuds et de solutions que vous utilisez, et le nombre de nœuds Insights et Analytics pour lesquels vous êtes facturé s’affichera dans la table à la page **Utilisation et estimation des coûts**.  

Pour afficher le nombre de nœuds Security distincts, vous pouvez utiliser la requête :

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Pour afficher le nombre de nœuds Automation distincts, utilisez la requête :

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="create-an-alert-when-data-collection-is-high"></a>Créer une alerte lorsque la collection de données est volumineuse

Cette section décrit la création d’une alerte si :
- Le volume de données dépasse une quantité spécifiée.
- Le volume de données est censé dépasser une quantité spécifiée.

Les Alertes Azure prennent en charge les [alertes de journal](alerts-unified-log.md) qui utilisent des requêtes de recherche. 

La requête suivante obtient un résultat quand plus de 100 Go de données sont collectés dans les dernières 24 heures :

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type 
| where DataGB > 100
```

La requête suivante utilise une formule simple pour prévoir le moment où plus de 100 Go de données seront envoyés en une journée : 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type 
| where EstimatedGB > 100
```

Pour alerter sur un volume de données différent, remplacez la valeur de 100 dans les requêtes par le nombre de Go pour lequel vous souhaitez créer une alerte.

Utilisez les étapes décrites dans [Création d’une alerte de journal](alerts-metric.md) pour être averti lorsque la collection de données est plus volumineuse que prévu.

Lors de la création de l’alerte pour la première requête, lorsque plus de 100 Go de données sont collectés en 24 heures, définissez :  

- **Définir la condition d’alerte** spécifiez votre espace de travail Log Analytics comme cible de la ressource.
- **Critères d’alerte** spécifiez les éléments suivants :
   - **Nom du signal** sélectionnez **Recherche de journal personnalisée**
   - **Requête de recherche** sur `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - La **logique d’alerte** est **basée sur**  le *nombre de résultats* et **Condition** est *supérieur à* un **seuil**  de *0*
   - **Période de temps** de *1440* minutes et **fréquence des alertes** toutes les *60* minutes comme les données d’utilisation ne se mettent à jour qu’une fois par heure.
- **Définir les détails de l’alerte** spécifiez les éléments suivants :
   - **Nom** sur *Data volume greater than 100 GB in 24 hours* (Volume de données supérieur à 10 Go en 24 heures)
   - **Gravité** : *Avertissement*

Spécifiez un [groupe d’actions](action-groups.md) existant ou créez-en un nouveau afin que l’alerte de journal corresponde aux critères, vous êtes informé.

Lors de la création de l’alerte pour la seconde requête, lorsqu’il est prévu que plus de 100 Go de données seront collectés en 24 heures, définissez :

- **Définir la condition d’alerte** spécifiez votre espace de travail Log Analytics comme cible de la ressource.
- **Critères d’alerte** spécifiez les éléments suivants :
   - **Nom du signal** sélectionnez **Recherche de journal personnalisée**
   - **Requête de recherche** sur `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - La **logique d’alerte** est **basée sur**  le *nombre de résultats* et **Condition** est *supérieur à* un **seuil**  de *0*
   - **Période de temps** de *180* minutes et **fréquence des alertes** toutes les *60* minutes comme les données d’utilisation ne se mettent à jour qu’une fois par heure.
- **Définir les détails de l’alerte** spécifiez les éléments suivants :
   - **Nom** à *Data volume expected to greater than 100 GB in 24 hours* (Volume de données attendu supérieur à 10 Go en 24 heures)
   - **Gravité** : *Avertissement*

Spécifiez un [groupe d’actions](action-groups.md) existant ou créez-en un nouveau afin que l’alerte de journal corresponde aux critères, vous êtes informé.

Lorsque vous recevez une alerte, utilisez les étapes de la section suivante pour résoudre les problèmes à l’origine d’une utilisation plus importante que prévu.

## <a name="limits-summary"></a>Synthèse des limites

D’autres limites de Log Analytics s’appliquent, certaines d’entre elles dépendant du niveau tarifaire de Log Analytics. Elles sont décrites [ici](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Étapes suivantes

- Consultez [Recherche de données dans les journaux Azure Monitor](../log-query/log-query-overview.md) pour apprendre à utiliser le langage de recherche. Vous pouvez utiliser des requêtes de recherche pour effectuer des analyses supplémentaires sur les données d’utilisation.
- Utilisez les étapes décrites dans [Création d’une alerte de journal](alerts-metric.md) pour être averti lorsqu’un critère de recherche est rempli.
- Utilisez le [ciblage de solution](../insights/solution-targeting.md) pour collecter des données des groupes d’ordinateurs requis uniquement.
- Pour configurer une règle efficace de collecte d’événements, passez en revue [Stratégie de filtrage d’Azure Security Center](../../security-center/security-center-enable-data-collection.md).
- Modifier la [configuration du compteur de performances](data-sources-performance-counters.md).
- Pour modifier vos paramètres de collecte d’événements, consultez [Configuration du journal des événements](data-sources-windows-events.md).
- Pour modifier vos paramètres de collecte de messages syslog, consultez [Configuration syslog](data-sources-syslog.md).