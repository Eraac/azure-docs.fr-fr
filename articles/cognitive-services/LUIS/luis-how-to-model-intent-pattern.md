---
title: Ajout de précision à l’aide de modèles
titleSuffix: Language Understanding - Azure Cognitive Services
description: Ajouter des gabarits de modèle pour améliorer la précision de la prédiction dans les applications Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 202b9632b7a7faaf955874a0300edbe5134b7fa1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60196659"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Comment ajouter des modèles pour améliorer la précision de la prédiction
Lorsqu’une application LUIS reçoit des énoncés de point de terminaison, utilisez un [modèle](luis-concept-patterns.md) pour améliorer la précision de la prédiction des énoncés qui s’appuient sur un modèle d’ordre des mots et de choix de mot. Les modèles utilisent une [syntaxe](luis-concept-patterns.md#pattern-syntax) spécifique pour indiquer l’emplacement des éléments suivants : [entités](luis-concept-entity-types.md), [rôles](luis-concept-roles.md) d’entités et texte facultatif.

## <a name="add-template-utterance-to-create-pattern"></a>Ajouter un énoncé de gabarit pour créer un modèle
1. Ouvrez votre application en sélectionnant son nom sur la page **Mes applications**, puis sélectionnez **Patterns** (Modèles) dans le panneau gauche, sous **Improve app performance** (Améliorer les performances de l’application).

    ![Capture d’écran de la liste de modèles](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Sélectionnez l’intention appropriée pour le modèle. 

    ![Sélectionner l’intention](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Dans la zone de texte du gabarit, saisissez l’énoncé du gabarit, puis sélectionnez Entrée. Lorsque vous souhaitez entrer le nom de l’entité, utilisez la syntaxe d’entité de modèle appropriée. Commencez la syntaxe de l’entité avec `{`. La liste des entités s’affiche. Sélectionnez l’entité appropriée, puis Entrée. 

    ![Capture d’écran de l’entité pour le modèle](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Si votre entité inclut un [rôle](luis-concept-roles.md), indiquez le rôle avec un signe deux-points, `:`, après le nom de l’entité, tel que `{Location:Origin}`. La liste des rôles pour les entités s’affiche dans une liste. Sélectionnez le rôle, puis Entrée. 

    ![Capture d’écran de l’entité avec le rôle](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Lorsque vous avez sélectionné l’entité appropriée, terminez d’entrer le modèle, puis sélectionnez Entrée. Lorsque vous avez terminé d’entrer des modèles, [formez](luis-how-to-train.md) votre application.

    ![Capture d’écran du modèle d’entrée avec les deux types d’entités](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Former votre application après modification du modèle avec des modèles
Après avoir ajouté, modifié, supprimé ou réaffecté un énoncé, [formez](luis-how-to-train.md) et [publiez](luis-how-to-publish-app.md) votre application pour que vos modifications affectent les requêtes de point de terminaison. 

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Utiliser la barre d’outils contextuelle

La barre d’outils contextuelle au-dessus de la liste des modèles vous permet d’effectuer les opérations suivantes :

* Rechercher des modèles
* Modifier un modèle
* Réaffecter un modèle à une autre intention
* Réaffecter plusieurs modèles à une autre intention
* Delete-a-single-pattern
* Supprimer plusieurs modèles
* Filtrer la liste des modèles par entité
* Filter-pattern-list-by-intent
* Supprimer un filtre d’entité ou d’intention
* Ajouter un modèle à partir d’un énoncé existant sur une page d’intention ou d’entité

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un modèle](luis-tutorial-pattern.md) avec pattern.any et des rôles à l’aide d’un tutoriel.
* Découvrez comment [former](luis-how-to-train.md) votre application.
