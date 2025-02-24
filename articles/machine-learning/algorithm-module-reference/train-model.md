---
title: 'Entraîner le modèle : informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module **Train Model** (Entraîner le modèle) dans Azure Machine Learning service pour effectuer l’apprentissage d’un modèle de classification ou de régression.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 29d0f698456b83c1520a92bc7df47b26540325f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028109"
---
# <a name="train-model-module"></a>Module de formation de modèle

Cet article décrit un module de l’interface visuelle (préversion) pour Azure Machine Learning service.

Utilisez ce module pour effectuer l’apprentissage d’un modèle de classification ou de régression. La formation a lieu une fois que vous avez défini un modèle et ses paramètres et nécessite des données avec balises. Vous pouvez également utiliser le module **Train Model** (Entraîner le modèle) pour effectuer l’apprentissage d’un modèle existant avec de nouvelles données. 

## <a name="how-the-training-process-works"></a>Fonctionnement du processus de formation

Dans Azure Machine Learning, la création et l’utilisation d’un modèle Machine Learning s’effectuent généralement en trois étapes. 

1. Vous configurez un modèle en choisissant un type d’algorithme particulier et en définissant ses paramètres ou hyperparamètres. Choisissez l’un des types de modèles suivants : 

    + Modèles de **classification**, basée sur les réseaux neuronaux, arbres de décision, forêts de décision et autres algorithmes
    + Modèles de **régression**, qui peuvent inclure une régression linéaire standard ou qui utilisent d’autres algorithmes, notamment les réseaux neuronaux et la régression bayésienne  

2. Fournissez un jeu de données doté d’une étiquette et dont les données sont compatibles avec l’algorithme. Connectez les données et le modèle au module **Train Model** (Entraîner le modèle).

    La formation produit un format binaire spécifique, l’iLearner, qui encapsule les modèles statistiques appris à partir des données. Vous ne pouvez pas modifier ou lire ce format directement. Toutefois, les autres modules peuvent utiliser ce modèle formé. 
    
    Vous pouvez également afficher les propriétés du modèle. Pour plus d’informations, consultez la section Résultats.

3. Une fois la formation terminée, utilisez le modèle formé avec l’un des [modules de notation](./score-model.md) pour effectuer des prédictions sur de nouvelles données.

## <a name="how-to-use-train-model"></a>Comment utiliser le module **Train Model** (Entraîner le modèle)  
  
1.  Dans Azure Machine Learning, configurez un modèle de classification ou de régression.
    
2. Ajoutez le module **Train Model** (Entraîner le modèle) à l’expérience.  Ce module figure dans la catégorie **Machine Learning**. Développez la section **Former**, puis faites glisser le module **Train Model** (Entraîner le modèle) dans votre expérience.
  
3.  Sous l’entrée de gauche, joignez le modèle non formé. Joignez le jeu de données d’apprentissage à l’entrée droite du module **Train Model** (Entraîner le modèle).

    Le jeu de données d’apprentissage doit contenir une colonne d’étiquette. Toutes les lignes sans étiquette sont ignorées.
  
4.  Pour **Étiqueter la colonne**, cliquez sur **Launch column selector** (Lancer le sélecteur de colonne), puis choisissez une seule colonne contenant les résultats que le modèle peut utiliser pour la formation.
  
    - Pour les problèmes de classification, la colonne d’étiquette doit contenir des valeurs **catégorielles** ou **discrètes**. Il peut s’agir, par exemple, d’une évaluation oui/non, d’un code ou nom de classification de maladie ou d’un groupe de revenus.  Si vous choisissez une colonne non catégorielle, le module renverra une erreur pendant la formation.
  
    -   Pour les problèmes de régression, la colonne d’étiquette doit contenir des données **numériques** qui représentent la variable de réponse. Dans l’idéal, les données numériques représentent une échelle continue. 
    
    Il peut s’agir, par exemple, de l’évaluation du risque de crédit, du temps entre deux pannes escompté pour un disque dur ou du nombre prévu d’appels à un centre d’appels sur une journée ou à une heure donnée.  Si vous ne choisissez pas une colonne numérique, une erreur peut se produire.
  
    -   Si vous ne spécifiez pas la colonne d’étiquette à utiliser, Azure Machine Learning tente de déduire la colonne appropriée en utilisant les métadonnées du jeu de données. S’il choisit la mauvaise colonne, utilisez le sélecteur de colonne pour résoudre ce problème.
  
    > [!TIP] 
    > Si vous avez des difficultés à utiliser le sélecteur de colonne, consultez l’article [Sélectionner des colonnes dans le jeu de données](./select-columns-in-dataset.md) pour obtenir des conseils. Il décrit quelques scénarios courants et fournit des conseils pour utiliser les options **WITH RULES** (Avec règles) et **BY NAME** (Par nom).
  
5.  Exécutez l’expérience. Si vous avez une grande quantité de données, cela peut prendre un certain temps.

## <a name="bkmk_results"></a> Résultats

Une fois le modèle formé :

+ Pour afficher les paramètres et pondérations de fonctionnalités du modèle, cliquez avec le bouton droit sur la sortie, puis sélectionnez **Visualiser**.
+ Pour utiliser le modèle dans d’autres expériences, cliquez avec le bouton droit sur le modèle et sélectionnez **Enregistrer le modèle**. Tapez le nom du modèle. 

    Le modèle est enregistré en tant qu’instantané non mis à jour par les exécutions répétées de l’expérience.
+ Afin d’utiliser le modèle pour la prédiction de nouvelles valeurs, connectez-le au module [Score Model](./score-model.md) (Noter le modèle) avec les nouvelles données d’entrée.


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 