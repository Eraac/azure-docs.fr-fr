---
title: Détection et prévision des anomalies de série chronologique dans Azure Data Explorer
description: Découvrez comment analyser des données de séries chronologiques pour la détection et la prévision des anomalies à l’aide d’Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: f40350129a12c7865051bcae80b74b6f9c069179
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233524"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Détection et prévision des anomalies dans Azure Data Explorer

Azure Data Explorer effectue une collecte continue de données de télémétrie à partir de services cloud ou d’appareils IoT. Ces données sont analysées afin d’extraire diverses informations, par exemple, en lien avec la surveillance de l’intégrité de services, des processus de production physiques, des tendances d’utilisation et des prévisions de charge. L’analyse est effectuée sur des séries chronologiques de métriques sélectionnées pour détecter des écarts de métriques par rapport au modèle de référence classique. Azure Data Explorer prend en charge en mode natif la création, la manipulation et l’analyse de multiples séries chronologiques. Il peut créer et analyser des milliers de séries chronologiques en quelques secondes, ce qui permet de mettre en place des solutions de supervision en temps quasi réel et d’autres workflows.

Cet article décrit en détail les fonctionnalités de détection et de prévision des anomalies de série chronologique Azure Data Explorer. Les fonctions de série chronologique applicables sont basées sur un modèle de décomposition connu robuste, dans lequel chaque série chronologique d’origine est décomposée en composants résiduels, de tendances et saisonnières. Les anomalies sont détectées par les valeurs hors norme sur le composant résiduel, tandis que la prévision est réalisée en extrapolant les composants de tendances et saisonnières. L’implémentation d’Azure Data Explorer améliore considérablement le modèle de décomposition de base grâce à la détection de saisonnalité automatique, l’analyse des valeurs hors norme robustes et l’implémentation vectorisée pour traiter des milliers de séries chronologiques en quelques secondes.

## <a name="prerequisites"></a>Prérequis

Lisez [Analyse des séries chronologiques dans Azure Data Explorer](/azure/data-explorer/time-series-analysis) pour obtenir une vue d’ensemble des fonctionnalités de séries chronologiques.

## <a name="time-series-decomposition-model"></a>Modèle de décomposition de série chronologique

L’implémentation native Azure Data Explorer pour la prédiction de séries chronologiques et la détection d’anomalies utilise un modèle de décomposition bien connu. Ce modèle s’applique aux séries chronologiques de métriques devant présenter un comportement tendance et périodique, telles que le trafic de service, les pulsations du composant et les mesures périodiques IoT pour prévoir les futures valeurs de métrique et détecter les anomalies. L’hypothèse de ce processus de régression est que, en dehors du comportement tendance et saisonnier précédent, la série chronologique est distribuée de manière aléatoire. Vous pouvez ensuite prévoir les futures valeurs de métriques à l’aide des composants de tendance et saisonniers, collectivement nommés ligne de base, et ignorer la partie résiduelle. Vous pouvez également détecter des valeurs anormales grâce à l’analyse des valeurs hors norme en utilisant uniquement la partie résiduelle.
Pour créer un modèle de décomposition, utilisez la fonction [`series_decompose()`](/azure/kusto/query/series-decomposefunction). La fonction `series_decompose()` prend un ensemble de séries chronologiques et décompose automatiquement chaque série chronologique en composants saisonniers, de tendance, résiduels et de ligne de base. 

Par exemple, vous pouvez décomposer le trafic d’un service web interne à l’aide de la requête suivante :

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![Décomposition de série chronologique](media/anomaly-detection/series-decompose-timechart.png)

* La série chronologique d’origine est étiquetée **num** (en rouge). 
* Le processus commence par la détection automatique de la saisonnalité en utilisant la fonction [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) et extrait le modèle **saisonnier** (en violet).
* Le modèle saisonnier est soustrait de la série chronologique d’origine, et une régression linéaire est exécutée à l’aide de la fonction [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) pour trouver le composant **trend** (en bleu clair).
* La fonction soustrait le composant trend et le reste devient le composant **residual** (en vert).
* Enfin, la fonction ajoute les composants saisonniers et de tendance afin de générer la **ligne de base** (en bleu).

## <a name="time-series-anomaly-detection"></a>Détection des anomalies de série chronologique

La fonction [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) recherche des points anormaux sur un ensemble de séries chronologiques. Cette fonction appelle `series_decompose()` pour générer le modèle de décomposition, puis exécute [`series_outliers()`](/azure/kusto/query/series-outliersfunction) sur le composant résiduel. `series_outliers()` calcule les scores d’anomalies pour chaque point du composant résiduel à l’aide du test des étendues de Tukey. Les scores d’anomalies au-dessus de 1.5 ou en dessous de -1.5 indiquent respectivement une augmentation ou une baisse modérée des anomalies. Les scores d’anomalies au-dessus de 3.0 ou en dessous de -3.0 indiquent une anomalie forte. 

La requête suivante permet de détecter les anomalies dans le trafic du service web interne :

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![Détection des anomalies de série chronologique](media/anomaly-detection/series-anomaly-detection.png)

* La série chronologique d’origine (en rouge). 
* Le composant (saisonnier + de tendance) de la ligne de base (en bleu).
* Les points anormaux (en violet) en haut de la série chronologique d’origine. Les points anormaux s’écartent sensiblement des valeurs de base attendues.

## <a name="time-series-forecasting"></a>Prévision de séries chronologiques

La fonction [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) prédit les valeurs futures d’un ensemble de séries chronologiques. Cette fonction appelle `series_decompose()` pour générer le modèle de décomposition, puis, pour chaque série chronologique, extrapole le composant de ligne de base dans le futur.

La requête suivante permet de prédire le trafic du service web de la semaine suivante :

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decmposition')
```

![Prévision de séries chronologiques](media/anomaly-detection/series-forecasting.png)

* Métrique d’origine (en rouge). Les valeurs futures sont manquantes et définies sur 0, la valeur par défaut.
* Extrapolez le composant de ligne de base (en bleu) pour prédire les valeurs de la semaine prochaine.

## <a name="scalability"></a>Extensibilité

La syntaxe du langage de requête Azure Data Explorer permet de traiter plusieurs séries chronologiques en un seul appel. Son implémentation optimisée unique permet d’accélérer les performances, ce qui est essentiel pour une prévision et une détection des anomalies efficaces lors de la surveillance de milliers de compteurs dans des scénarios en quasi-temps réel.

La requête suivante montre le traitement simultané de trois séries chronologiques :

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![Extensibilité des séries chronologiques](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Résumé

Ce document décrit en détail les fonctionnalités natives de détection et de prévision des anomalies de série chronologique Azure Data Explorer. Chaque série chronologique d’origine est décomposée en composants saisonniers, de tendance et résiduels pour la détection des anomalies et/ou la prévision. Ces fonctionnalités peuvent être utilisées pour des scénarios de surveillance en quasi-temps réel, tels que la détection des erreurs, la maintenance prédictive et la prévision de charge à la demande.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les [fonctionnalités d’apprentissage automatique](/azure/data-explorer/machine-learning-clustering) dans Azure Data Explorer.
