---
title: 'Régression linéaire : référence sur le module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Régression linéaire dans Azure Machine Learning service pour créer un modèle de régression linéaire à utiliser dans une expérience.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 040f726a703d34a95bae7d5b7cdd766655c62a4e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029714"
---
# <a name="linear-regression-module"></a>Module Régression linéaire
Cet article décrit un module de l’interface visuelle (en préversion) pour Azure Machine Learning service.

Ce module permet de créer un modèle de régression linéaire utilisable dans une expérience.  Une régression linéaire tente d’établir une relation linéaire entre une ou plusieurs variables indépendantes et un résultat numérique ou une variable dépendante. 

Ce module vous permet de définir une méthode de régression linéaire, puis d’effectuer l’apprentissage d’un modèle à l’aide d’un jeu de données étiqueté. Le modèle ainsi formé permet ensuite d’effectuer des prédictions.

## <a name="about-linear-regression"></a>À propos de la régression linéaire

La régression linéaire est une méthode statistique courante qui a été largement adoptée dans le domaine de l’apprentissage automatique, puis améliorée avec de nombreuses nouvelles méthodes d’ajustement de ligne de régression et de mesure des erreurs. Au sens le plus simple, la régression fait référence à la prédiction d’une cible numérique. La régression linéaire constitue toujours un bon choix lorsque vous souhaitez disposer d’un modèle simple pour une tâche prédictive de base. La régression linéaire tend également à bien fonctionner avec des jeux de données éparses et de grande dimension peu complexes.

Azure Machine Learning prend en charge divers modèles de régression, en plus de la régression linéaire. Cependant, le terme « régression » peut être interprété dans un sens général, et certains types de régressions fournis par d’autres outils ne sont pas pris en charge.

+ La régression classique implique une variable indépendante et une variable dépendante. C’est ce qu’on appelle une *régression simple*.  Ce module prend en charge la régression simple.

+ Une *régression linéaire multiple* implique au moins deux variables indépendantes qui contribuent à déterminer une seule variable dépendante. Lorsque plusieurs entrées sont utilisées pour prédire un résultat numérique unique, on parle également de *régression linéaire multivariée ou multidimensionnelle*.

    Le module **Régression linéaire** peut résoudre ces problèmes, à l’instar de la plupart des autres modules de régression.

+ Une *régression à plusieurs étiquettes* est la tâche de prédiction de plusieurs variables dépendantes à l’intérieur d’un modèle unique. Par exemple, dans une régression logistique à plusieurs étiquettes, un échantillon peut être attribué à plusieurs étiquettes différentes (ce qui diffère de la tâche de prédiction de plusieurs niveaux au sein d’une variable de classe unique).

    Ce type de régression n’est pas pris en charge dans Azure Machine Learning. Pour prédire plusieurs variables, créez un apprenant distinct pour chaque sortie que vous souhaitez prédire.

Pendant des années, les statisticiens ont développé des méthodes de régression de plus en plus sophistiquées. Cela vaut également pour la régression linéaire. Ce module prend en charge deux méthodes pour mesurer les erreurs et ajuster la ligne de régression : la méthode des moindres carrés ordinaires et la méthode de descente de gradient.

- La méthode de **descente de gradient** minimise la quantité d’erreurs à chaque étape du processus d’apprentissage du modèle. Il existe de nombreuses variantes de la méthode de descente de gradient, et son optimisation pour diverses problématiques d’apprentissage a été largement étudiée. Si vous choisissez cette option comme **méthode de solution**, vous pouvez définir une série de paramètres pour contrôler la taille d’étape, le taux d’apprentissage et ainsi de suite. Cette option prend également en charge l’utilisation d’un balayage de paramètre intégré.

- La méthode des **moindres carrés ordinaires** est l’une des techniques les plus couramment utilisées en matière de régression linéaire. Elle l’est, par exemple, dans les outils d’analyse pour Microsoft Excel.

    La méthode des moindres carrés ordinaires fait référence à la fonction de perte, qui quantifie une erreur en calculant la somme du carré de l’écart entre la valeur réelle et la ligne prédite, puis ajuste le modèle en réduisant au minimum l’erreur quadratique. Cette méthode suppose une forte relation linéaire entre les entrées et la variable dépendante.

## <a name="configure-linear-regression"></a>Configurer une régression linéaire

Ce module prend en charge deux méthodes d’ajustement d’un modèle de régression, avec différentes options :

+ [Créer un modèle de régression utilisant la descente de gradient en ligne](#bkmk_GradientDescent)

    La descente de gradient est une meilleure fonction de perte pour les modèles plus complexes, ou qui ont trop eu de données d’apprentissage compte tenu du nombre de variables.



+ [Ajuster un modèle de régression utilisant les moindres carrés ordinaires](#bkmk_OrdinaryLeastSquares)

    Pour les jeux de données de petite taille, il est préférable de sélectionner la méthode des moindres carrés ordinaires. Elle devrait donner produire des résultats similaires à ceux obtenus dans Excel.

## <a name="bkmk_OrdinaryLeastSquares"></a> Créer un modèle de régression utilisant les moindres carrés ordinaires

1. Ajoutez le module **Linear Regression Model** (Modèle de régression linéaire) à votre expérience dans l’interface.

    Ce module figure dans la catégorie **Machine Learning**. Développez **Initialize Model** (Initialiser un modèle), développez **Regression** (Régression), puis faites glisser le module **Linear Regression Model** (Modèle de régression linéaire) vers votre expérience.

2. Dans le volet **Properties** (Propriétés), dans la liste déroulante **Solution method** (Méthode de la solution), sélectionnez **Ordinary Least Squares** (Moindres carrés ordinaires). Cette option spécifie la méthode de calcul utilisée pour déterminer la ligne de régression.

3. Dans **L2 regularization weight** (Pondération de régularisation L2), tapez la valeur à utiliser en tant que pondération pour la régularisation L2. Nous vous recommandons d’utiliser une valeur différente de zéro pour éviter tout surajustement.

     Pour en savoir plus sur l’incidence de la régularisation sur l’ajustement du modèle, voir cet article : [Régularisations L1 et L2 pour l’apprentissage automatique](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Activez l’option **Include intercept term** (Inclure un terme d’interception) si vous souhaitez afficher le terme de l’interception.

    Désactivez cette option si vous n’avez pas besoin de réviser la formule de régression.

5. Pour **Random number seed** (Valeur initiale aléatoire), vous pouvez taper une valeur pour amorcer le générateur de nombres aléatoires que le modèle utilise.

    L’utilisation d’une valeur de départ est utile si vous souhaitez conserver les mêmes résultats entre les différentes exécutions de la même expérience. Autrement, par défaut, une valeur de l’horloge système est utilisée.


7. Ajoutez le module [Train Model](./train-model.md) (Entraîner le modèle) à votre expérience, puis connectez un jeu de données étiqueté.

8. Exécutez l’expérience.

## <a name="results-for-ordinary-least-squares-model"></a>Résultats de modèle de moindres carrés ordinaires

Une fois l’apprentissage terminé :

+ Pour afficher les paramètres du modèle, cliquez avec le bouton droit sur la sortie du formateur, puis sélectionnez **Visualize** (Visualiser).

+ Pour effectuer des prédictions, connectez le modèle formé au module [Score Model](./score-model.md) (Noter le modèle), ainsi qu’un jeu de données de nouvelles valeurs. 


## <a name="bkmk_GradientDescent"></a> Créer un modèle de régression utilisant la descente de gradient en ligne

1. Ajoutez le module **Linear Regression Model** (Modèle de régression linéaire) à votre expérience dans l’interface.

    Ce module figure dans la catégorie **Machine Learning**. Développez **Initialize Model** (Initialiser un modèle), développez **Regression** (Régression), puis faites glisser le module **Linear Regression Model** (Modèle de régression linéaire) vers votre expérience

2. Dans le volet **Properties** (Propriétés), dans la liste déroulante **Solution method** (Méthode de la solution), sélectionnez **Online Gradient Descent** (Descente de gradient en ligne) comme méthode de calcul pour déterminer la ligne de régression.

3. Pour **Create trainer mode** (créer un mode d’apprentissage), indiquer si vous souhaitez effectuer l’apprentissage du modèle avec un ensemble prédéfini de paramètres, ou si vous souhaitez optimiser le modèle à l’aide d’un balayage de paramètre.

    + **Paramètre unique** : si vous savez comment vous voulez configurer le réseau de régression linéaire, vous pouvez fournir un ensemble spécifique de valeurs en tant qu’arguments.

   
4. Pour **Learning rate** (Taux d’apprentissage), spécifiez le taux d’apprentissage initial pour l’optimiseur de descente de gradient stochastique.

5. Pour **Number of training epochs** (Nombre de cycles de formation), tapez une valeur indiquant le nombre d’itérations de l’algorithme sur les exemples. Pour les jeux de données comportant un petit nombre d’exemples, ce nombre doit être suffisamment important pour atteindre la convergence.

6. **Normaliser les fonctionnalités** : si vous avez déjà normalisé les données numériques utilisées pour l’apprentissage du modèle, vous pouvez désactiver cette option. Par défaut, le module normalise toutes les entrées numériques en les ramenant à une plage comprise entre 0 et 1.

    > [!NOTE]
    > 
    > N’oubliez pas d’appliquer la même méthode de normalisation aux nouvelles données utilisées pour la notation.

7. Dans **L2 regularization weight** (Pondération de régularisation L2), tapez la valeur à utiliser en tant que pondération pour la régularisation L2. Nous vous recommandons d’utiliser une valeur différente de zéro pour éviter tout surajustement.

    Pour en savoir plus sur l’incidence de la régularisation sur l’ajustement du modèle, voir cet article : [Régularisations L1 et L2 pour l’apprentissage automatique](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Sélectionnez l’option **Decrease learning rate** (Réduire le taux d’apprentissage) si vous voulez que le taux d’apprentissage décroisse à mesure que les itérations progressent.  

10. Pour **Random number seed** (Valeur initiale aléatoire), vous pouvez taper une valeur pour amorcer le générateur de nombres aléatoires que le modèle utilise. L’utilisation d’une valeur de départ est utile si vous souhaitez conserver les mêmes résultats entre les différentes exécutions de la même expérience.


12. Ajoutez un jeu de données étiqueté et l’un des modules d’apprentissage.

    Si vous n’utilisez pas de balayage de paramètre, utilisez le module [Train Model](train-model.md) (Entraîner le modèle).

13. Exécutez l’expérience.

## <a name="results-for-online-gradient-descent"></a>Résultats pour la descente de gradient en ligne

Une fois l’apprentissage terminé :

+ Pour effectuer des prédictions, connectez le modèle formé au module [Score Model](./score-model.md) (Noter le modèle), ainsi que de nouvelles données d’entrée.


## <a name="next-steps"></a>Étapes suivantes

Voir l’[ensemble de modules disponibles](module-reference.md) pour Azure Machine Learning service. 