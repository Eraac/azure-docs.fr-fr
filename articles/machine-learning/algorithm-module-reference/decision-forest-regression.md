---
title: 'Régression de forêt d’arbres de décision : informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module de régression de forêt d’arbres de décision dans Azure Machine Learning Service pour créer un modèle de régression basé sur un ensemble d’arbres de décision.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d372adf75d46fdedb7a6f2b17e47822475d1f155
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65442361"
---
# <a name="decision-forest-regression-module"></a>Module Régression de forêt d’arbres de décision

Cet article décrit un module de l’interface visuelle (préversion) d’Azure Machine Learning service.

Utilisez ce module pour créer un modèle de régression basé sur un ensemble d’arbres de décision.

Une fois que vous avez configuré le modèle, vous devez le former à l’aide d’un jeu de données étiqueté et du module [Entraîner un modèle](./train-model.md).  Le modèle ainsi formé permet ensuite d’effectuer des prédictions. 

## <a name="how-it-works"></a>Fonctionnement

Les arbres de décision sont des modèles non paramétriques qui effectuent une séquence de tests simples pour chaque instance, en traversant une structure de données d’arbre binaire jusqu’à atteindre un nœud terminal (décision).

Les arbres de décision présentent les avantages suivants :

- Ils sont efficaces en matière de calcul et d’utilisation de la mémoire, lors de la formation et de la prédiction.

- Ils peuvent représenter des limites de décisions non linéaires.

- Ils effectuent la sélection des fonctionnalités intégrées ainsi que leur classification et sont résilients en présence de fonctionnalités bruyantes.

Ce modèle de régression se compose d’un ensemble d’arbres de décision. Chaque arbre d’une forêt de décision de régression génère une distribution gaussienne sous la forme d’une prédiction. Une agrégation est effectuée sur l’ensemble des arbres, afin de trouver la distribution gaussienne la plus proche de la distribution combinée, pour tous les arbres du modèle.

Pour en savoir plus sur l’infrastructure théorique de cet algorithme et son implémentation, consultez cet article : [Decision Forests: A Unified Framework for Classification, Regression, Density Estimation, Manifold Learning and Semi-Supervised Learning](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#) (Forêts de décision : Structure unifiée de classification, de régression, d’estimation de la densité, d’apprentissage polyvalent et d’apprentissage semi-supervisé)

## <a name="how-to-configure-decision-forest-regression-model"></a>Comment configurer le modèle de régression de forêt de décision

1. Ajoutez le module **Régression de forêt d’arbres de décision** à l’expérience. Vous pouvez rechercher le module dans l’interface sous **Machine Learning**, **Initialiser le modèle** et **Régression**.

2. Ouvrez les propriétés du module et, pour **Méthode de rééchantillonnage**, choisissez la méthode utilisée pour créer des arbres individuels.  Vous pouvez choisir **l’ensachage** ou la **réplication**.

    - **Ensachage** : l’ensachage est aussi appelé bagging ou *bootstrap aggregating* en anglais. Chaque arbre d’une forêt de décision de régression génère une distribution gaussienne, sous la forme d’une prédiction. L’agrégation consiste à trouver une distribution gaussienne dont les deux premiers moments correspondent à des moments du mélange de distributions gaussiennes obtenu via la combinaison de toutes les distributions gaussiennes renvoyées par des arbres individuels.

         Pour en savoir plus, consultez l’entrée Wikipedia relative au [Bootstrap Aggregating](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Réplication** : avec la réplication, l’apprentissage de chaque arbre est effectué à partir des mêmes données d’entrée. Le prédicat fractionné utilisé pour chaque nœud d’arbre est toujours déterminé de manière aléatoire, et les arbres sont variés.

         Pour en savoir plus sur le processus d’apprentissage avec l’option **Répliquer**, voir [Decision Forests for Computer Vision and Medical Image Analysis (Forêts de décision pour la vision par ordinateur et l’analyse des images médicales) Criminisi and J. Shotton. Springer 2013](https://research.microsoft.com/projects/decisionforests/).

3. Spécifiez le mode d’apprentissage du modèle en définissant l’option **Create trainer mode** (Créer un mode d’apprentissage).

    - **Single Parameter** (Paramètre unique)

      Si vous savez comment vous voulez configurer le modèle, vous pouvez fournir un ensemble spécifique de valeurs en tant qu’arguments. Vous l’avez peut-être découvert ces valeurs par expérimentation, ou vous les avez reçues à titre de guide.



4. Dans le champ **Number of trees constructed** (Nombre d’arbres construits), indiquez le nombre total d’arbres de décision à créer dans l’ensemble. En créant plusieurs arbres de décision, vous pouvez obtenir une meilleure couverture, mais cette opération augmente la durée d’apprentissage.

    > [!TIP]
    > Cette valeur contrôle également le nombre d’arbres affichés lorsque vous visualisez le modèle formé. Si vous souhaitez afficher ou imprimer un arbre unique, vous pouvez définir la valeur sur 1. Toutefois, cela signifie qu’un seul arbre est généré (celui qui inclut le jeu de paramètres initial) et qu’aucune autre itération n’est effectuée.

5. Dans le champ **Maximum depth of the decision trees** (Profondeur maximale des arbres de décision), saisissez un nombre pour limiter la profondeur maximale de n’importe quel arbre de décision. Le fait d’augmenter la profondeur de l’arbre peut améliorer la précision, mais cela peut entraîner un surajustement et rallonger la durée de formation.

6. Dans le champ **Nombre de fractionnements aléatoires par nœud**, saisissez le nombre de fractionnements à utiliser lors de la génération de chaque nœud de l’arbre. Un *fractionnement* signifie que les fonctionnalités à chaque niveau de l’arbre (nœud) sont séparées au hasard.

7. Dans le champ **Minimum number of samples per leaf node** (Nombre minimal d’échantillons par nœud terminal), indiquez le nombre minimum de cas requis pour la création d’un nœud terminal dans un arbre.

     En augmentant cette valeur, vous augmentez le seuil pour la création des règles. Par exemple, avec la valeur par défaut de 1, un seul cas suffit à entraîner la création d’une règle. Si vous définissez la valeur de ce champ sur 5, les données de formation doivent contenir au moins cinq cas remplissant les mêmes conditions.


9. Connectez un jeu de données étiqueté, sélectionnez une colonne d’étiquette unique contenant deux résultats maximum et connectez-vous à [Entraîner un modèle](./train-model.md).

    - Si vous définissez l’option **Create trainer mode** (Créer un mode d’apprentissage) sur **Single Parameter** (Paramètre unique), utilisez le module [Entraîner un modèle](./train-model.md) pour entraîner le modèle.

   

10. Exécutez l’expérience.

### <a name="results"></a>Résultats

Une fois la formation terminée :

+ Pour voir l’arbre créé pour chaque itération, cliquez avec le bouton droit de la souris sur la sortie du module de formation et sélectionnez**Visualiser**.

+ Pour afficher les règles de chaque nœud, cliquez sur chaque arbre et descendez dans la hiérarchie des fractionnements.

+ Pour enregistrer un instantané du modèle formé, cliquez avec le bouton droit de la souris sur la sortie du module de formation et sélectionnez **Save As Trained Model** (Enregistrer en tant que modèle formé). Cette copie du modèle n’est pas mise à jour lors des exécutions consécutives de l’expérience. 

## <a name="next-steps"></a>Étapes suivantes

Consultez les [modules disponibles](module-reference.md) pour Azure Machine Learning Service. 