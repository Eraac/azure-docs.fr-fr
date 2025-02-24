---
title: 'Attribuer des données à des clusters : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Attribuer des données à des clusters dans le service Azure Machine Learning pour évaluer les modèles de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1c2d2a02ecfb617551dd9174b87f363d57b151a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65467198"
---
# <a name="module-assign-data-to-clusters"></a>Module : Attribuer des données à des clusters

Cet article décrit comment utiliser le module *Attribuer des données à des clusters* dans l’interface visuelle Azure Machine Learning. Le module génère des prédictions via un modèle de clustering qui a été formé avec l’algorithme de *clustering k-moyennes*.

Le module Attribuer des données à des clusters retourne un jeu de données contenant les attributions probables de chaque nouveau point de données. 


## <a name="how-to-use-assign-data-to-clusters"></a>Comment utiliser le module Attribuer des données à des clusters
  
1. Dans l’interface visuelle d’Azure Machine Learning, recherchez un modèle de clustering formé précédemment. Vous pouvez créer et former un modèle de clustering à l’aide d’une des méthodes suivantes :  
  
    - Configurer l’algorithme de clustering K-moyennes à l’aide du module [Clustering k-moyennes](k-means-clustering.md) et former le modèle à l’aide d’un jeu de données et du module Former le modèle de clustering (cet article).  
  
    - Vous pouvez également ajouter un modèle de clustering formé existant à partir du groupe **Modèles enregistrés** dans votre espace de travail.

2. Attacher le modèle formé au port d’entrée gauche du module **Attribuer des données à des clusters**.  

3. Attachez un nouveau jeu de données en tant qu’entrée. 

   Dans ce jeu de données, les étiquettes sont facultatives. En règle générale, le clustering est une méthode d’apprentissage non supervisée. Vous n’êtes pas censé connaître les catégories en avance. Toutefois, les colonnes d’entrée doivent être identiques aux colonnes utilisées lors de la formation du modèle de clustering. Dans le cas contraire, une erreur se produit.

    > [!TIP]
    > Pour réduire le nombre de colonnes rédigées dans l’interface à partir des prédictions de cluster, utilisez [Sélectionner les colonnes dans le jeu de données](select-columns-in-dataset.md), puis sélectionnez un sous-ensemble de colonnes. 
    
4. Cochez la case **Cocher pour ajouter ou Décocher pour résultats uniquement** si vous voulez que les résultats comprennent le jeu de données d’entrée entier, y compris une colonne qui affiche les résultats (attributions de cluster).
  
    Si vous décochez cette case, seuls les résultats sont retournés. Cette option peut être utile lorsque vous créez des prédictions dans le cadre d’un service web.
  
5.  Exécutez l’expérience.  
  
### <a name="results"></a>Résultats

+  Pour afficher les valeurs dans le jeu de données, cliquez sur le module avec le bouton droit de la souris, sélectionnez **Result datasets** (Jeux de données de résultats), puis sélectionnez **Visualiser**.

