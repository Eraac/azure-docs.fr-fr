---
title: Migrer l’analytique d’Excel
titleSuffix: Azure Machine Learning Studio
description: Une comparaison des modèles de régression linéaire dans Excel et dans Azure Machine Learning Studio
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7db66f6f4efa5e48f2af9380115de8bcfb75cb86
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786682"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio"></a>Migrer l’analytique d’Excel vers Azure Machine Learning Studio

> *Kate Baroni* et *Ben Boatman* sont des architectes de solution du Data Insights Center of Excellence de Microsoft. Dans cet article, ils décrivent leur expérience de migration d’une suite d’analyse de régression existante vers une solution basée sur le cloud à l’aide d’Azure Machine Learning Studio.

## <a name="goal"></a>Objectif

Notre projet a commencé avec deux objectifs : 

1. l’utilisation de l’analyse prédictive pour améliorer la précision des projections de recettes mensuelles de notre organisation ; 
2. l’utilisation d’Azure Machine Learning Studio pour confirmer, optimiser, mettre à l’échelle nos résultats et en accélérer la vitesse. 

Comme beaucoup d’entreprises, notre organisation connaît un processus de prévision des recettes mensuelles. Notre petite équipe d’analystes commerciaux a été chargée d’utiliser Azure Machine Learning Studio pour prendre en charge le processus et améliorer la précision des prévisions. L’équipe a passé plusieurs mois à collecter des données provenant de plusieurs sources et à exécuter les attributs de données via l’analyse statistique afin d’identifier les principaux attributs des prévisions de vente de services. L’étape suivante a consisté à réaliser des prototypes de modèles de régression statistique sur les données dans Excel. En quelques semaines, nous avions un modèle de régression Excel plus performant que nos anciens processus de prévisions financières et de champ. C’est devenu le résultat de prédiction de référence. 

Ensuite, nous avons déplacé l’analytique prédictive vers Studio pour savoir comment Studio pouvait améliorer les performances de prédiction.

## <a name="achieving-predictive-performance-parity"></a>Atteinte de la parité des performances de prédiction
Notre priorité était d’atteindre la parité entre les modèles de régression Studio et Excel. Avec les mêmes données et la même fraction pour l’entraînement et le test des données, nous voulions obtenir une parité des performances de prédiction entre Excel et Studio. Au départ, nous a échoué. Le modèle Excel a surpassé le modèle Studio. L’échec était dû à un manque de compréhension de la configuration des outils de base dans Studio. Après une discussion avec l’équipe de produit Studio, nous avons acquis une meilleure compréhension de la configuration de base exigée pour nos jeux de données et atteint la parité entre les deux modèles. 

### <a name="create-regression-model-in-excel"></a>Création d’un modèle de régression dans Excel
Notre régression Excel utilisait le modèle de régression linéaire standard trouvé dans l’utilitaire d’analyse d’Excel. 

Nous avons calculé une *Erreur d’absolue moyenne en pourcentage* et l’avons utilisée comme mesure de performance pour le modèle. L’obtention d’un modèle fonctionnel à l’aide d’Excel nous a pris trois mois. Nous avons utilisé nos connaissances pour l’expérience Studio, ce qui a été bénéfique pour en comprendre les exigences.

### <a name="create-comparable-experiment-in-studio"></a>Créer une expérience comparable dans Studio
Nous avons suivi ces étapes pour créer notre expérience dans Studio : 

1. chargement du jeu de données dans un fichier csv vers Studio (très petit fichier) ;
2. création d’une expérience et utilisation du module [Sélectionner des colonnes dans le jeu de données][select-columns] pour sélectionner les mêmes fonctionnalités de données que dans Excel 
3. utilisation du module [Fractionner les données][split] (avec le mode *Expression relative*) pour répartir les données dans les mêmes jeux de données d’apprentissage que dans Excel 
4. expériences avec le module [Régression linéaire][linear-regression] (options par défaut uniquement), documentation et comparaison des résultats à ceux de notre modèle de régression Excel

### <a name="review-initial-results"></a>Examen des résultats initiaux
Dans un premier temps, le modèle Excel a clairement surpassé le modèle Studio : 

|  | Excel | Studio |
| --- |:---:|:---:|
| Performances | | |
| <ul style="list-style-type: none;"><li>Carré R ajusté</li></ul> |0,96 |N/A |
| <ul style="list-style-type: none;"><li>Coefficient de <br />détermination</li></ul> |N/A |0,78<br />(faible précision) |
| Erreur d'absolue moyenne |9,5 M $ |19,4 M $ |
| Erreur d’absolue moyenne (%) |6,03 % |12,2 % |

Lorsque nous avons présenté notre processus et les résultats obtenus aux chercheurs de données et développeurs de l’équipe Machine Learning, ils ont rapidement fourni des conseils utiles. 

* Quand vous utilisez le module [Régression linéaire][linear-regression] dans Studio, deux méthodes sont fournies :
  * Descente dégradée en ligne : plus adapté aux problèmes de plus grande échelle
  * moindres carrés ordinaires : il s’agit de la méthode qui vient à l’esprit de la plupart des personnes qui entendent « régression linéaire ». Pour les petits groupes de données, les moindres carrés ordinaires peuvent être un meilleur choix.
* Envisagez d’ajuster le paramètre de poids de régularisation L2 pour améliorer les performances. Il est défini à 0,001 par défaut, mais pour notre petit ensemble de données, nous l’avons défini à 0,005 pour améliorer les performances. 

### <a name="mystery-solved"></a>Mystère résolu !
Quand nous avons appliqué les recommandations, nous avons obtenu les mêmes performances de référence dans Studio et Excel : 

|  | Excel | Studio (Initial) | Studio avec moindres carrés |
| --- |:---:|:---:|:---:|
| Valeur étiquetée |Chiffres réels (numérique) |identique |identique |
| Apprenant |Excel -> Analyse des données -> Régression |Régression linéaire. |régression linéaire |
| Options de l’apprenant |N/A |Valeurs par défaut |moindres carrés ordinaires<br />L2 = 0,005 |
| Jeu de données |26 lignes, 3 fonctionnalités, 1 étiquette. Tout au format numérique. |identique |identique |
| Split : Former |Excel a exécuté la formation sur les 18 premières lignes, et le test sur les 8 dernières lignes. |identique |identique |
| Split : Test |Formule de régression Excel appliquée aux 8 dernières lignes |identique |identique |
| **Performances** | | | |
| Carré R ajusté |0,96 |N/A | |
| Coefficient de détermination |N/A |0,78 |0,952049 |
| Erreur d'absolue moyenne |9,5 M $ |19,4 M $ |9,5 M $ |
| Erreur d’absolue moyenne (%) |<span style="background-color: 00FF00;"> 6,03 %</span> |12,2 % |<span style="background-color: 00FF00;"> 6,03 %</span> |

En outre, les coefficients Excel ont été efficaces par rapport aux pondérations de fonctionnalités du modèle formé Azure :

|  | Coefficients Excel | Poids des fonctionnalités Azure |
| --- |:---:|:---:|
| Interception/Décalage |19470209,88 |19328500 |
| Fonctionnalité A |0,832653063 |0,834156 |
| Fonctionnalité B |11071967,08 |11007300 |
| Fonctionnalité C |25383318,09 |25140800 |

## <a name="next-steps"></a>Étapes suivantes
Nous avons voulu utiliser le service web Machine Learning dans Excel. Nos analystes commerciaux s’appuient sur Excel et nous avions besoin d’une méthode pour appeler le service web Machine Learning avec une ligne de données Excel et qu’il renvoie la valeur prédite vers Excel. 

Nous souhaitions également optimiser notre modèle, à l’aide des options et des algorithmes disponibles dans Studio.

### <a name="integration-with-excel"></a>Intégration à Excel
Notre solution était de faire fonctionner notre modèle de régression Machine Learning en créant un service web à partir du modèle formé. En quelques minutes, le service Web a été créé et nous avons pu l’appeler directement à partir d’Excel pour retourner une valeur de recettes prédites. 

La section *tableau de bord des services Web* comprend un classeur Excel à télécharger. Le classeur est déjà mis en forme avec les informations de schéma et l’API de service Web. Lorsque vous cliquez sur *Télécharger un classeur Excel*, il s’ouvre et vous pouvez l’enregistrer sur votre ordinateur local. 

![Télécharger un classeur Excel à partir du tableau de bord des services web](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

Avec le classeur ouvert, copiez vos paramètres prédéfinis dans la section de paramètres bleue comme illustré ci-dessous. Une fois les paramètres saisis, Excel appelle le service web Machine Learning et les étiquettes notées prédites s’affichent dans la section Valeurs prédites verte. Le classeur continue à créer des prédictions pour les paramètres en fonction de votre modèle formé pour tous les éléments de ligne saisis sous Paramètres. Pour plus d’informations sur l’utilisation de cette fonctionnalité, consultez [Utilisation d’un service Web Microsoft Azure Machine Learning à partir de Microsoft Excel](consuming-from-excel.md). 

![Connexion du classeur Excel du modèle au service web déployé](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Optimisation et autres expériences
Maintenant que nous disposions d’une référence avec notre modèle Excel, nous sommes passés à l’optimisation de notre modèle de régression linéaire Machine Learning. Nous avons utilisé le module [Sélection de caractéristiques par filtrage][filter-based-feature-selection] afin d’améliorer notre sélection des éléments de données initiaux. Cela nous a permis d’améliorer les performances de 4,6 % pour l’erreur d’absolue moyenne. Dans les projets à venir, nous utiliserons cette fonctionnalité qui pourrait nous éviter des semaines d’itération des attributs de données afin de trouver le jeu de fonctionnalités approprié pour la modélisation. 

Ensuite, nous prévoyons d’inclure des algorithmes supplémentaires comme les méthodes [bayésiennes][bayesian-linear-regression]or [Boosted Decision Trees][boosted-decision-tree-regression] dans notre expérience afin d’en comparer les performances. 

Si vous souhaitez expérimenter la régression, un bon jeu de données d’essai est l’exemple de jeu de données de régression d’efficacité énergétique, qui comporte un grand nombre d’attributs numériques. Le jeu de données est fourni dans le cadre des exemples de jeux de données de Studio. Vous pouvez utiliser de nombreux modules d’apprentissage pour prédire la charge de chauffe ou de refroidissement. Le tableau ci-dessous est une comparaison des différents apprentissages de régression à l’aide du jeu de données d’efficacité énergétique, afin de prédire la charge de refroidissement variable cible : 

| Modèle | Erreur d'absolue moyenne | Erreur du carré moyen racine | Erreur d’absolue relative | Erreur carrée relative | Coefficient de détermination |
| --- | --- | --- | --- | --- | --- |
| Arbre de décision optimisé |0,930113 |1,4239 |0,106647 |0,021662 |0,978338 |
| Régression linéaire (descente dégradée) |2,035693 |2,98006 |0,233414 |0,094881 |0,905119 |
| Régression de réseau neuronal |1,548195 |2,114617 |0,177517 |0,047774 |0,952226 |
| Régression linéaire (moindres carrés ordinaires) |1,428273 |1,984461 |0,163767 |0,042074 |0,957926 |

## <a name="key-takeaways"></a>Points clés
Nous avons beaucoup appris en exécutant la régression Excel et les expériences Studio en parallèle. La création du modèle de référence dans Excel et sa comparaison aux modèles utilisant la [régression linéaire][linear-regression] de Machine Learning nous a aidés à découvrir Studio. Nous avons découvert des possibilités d’amélioration des performances du modèle et de sélection des données. 

Nous avons également découvert qu’il était recommandé d’utiliser la [sélection de caractéristiques par filtrage][filter-based-feature-selection] pour accélérer les projets de prédictions. En appliquant la sélection des caractéristiques à vos données, vous pouvez créer un modèle amélioré dans Studio, avec de meilleures performances globales. 

La capacité à transférer des prévisions analytiques à partir de Studio vers Excel permet une augmentation considérable de la capacité à fournir efficacement des résultats à un vaste public d’utilisateurs professionnels. 

## <a name="resources"></a>Ressources
Voici des ressources pour vous aider à utiliser la régression : 

* Régression dans Excel. Si vous n’avez jamais tenté d’effectuer une régression dans Excel, ce didacticiel vous facilite la tâche : [https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Régression et prévisions. Tyler Chessman a écrit un article de blog expliquant comment effectuer des prévisions de série chronologique dans Excel, qui contient une excellente description de la régression linéaire pour les débutants. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* Régression linéaire (moindres carrés ordinaires) : défauts, problèmes et pièges. Pour une introduction et une description de la régression : [https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

