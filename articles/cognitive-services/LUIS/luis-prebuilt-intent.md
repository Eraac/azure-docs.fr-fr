---
title: Intentions prédéfinies
titleSuffix: Azure Cognitive Services
description: LUIS inclut un ensemble d’intentions prédéfinies pour ajouter rapidement des scénarios courants d’utilisateur conversationnel.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 8e11b6d5a9c9df10e789c8e0be3a90d94ecf3d23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60505658"
---
# <a name="add-prebuilt-intents-for-common-intents"></a>Ajouter des intentions prédéfinies pour les intentions courantes 

LUIS inclut un ensemble d’intentions prédéfinies à partir des domaines prédéfinis pour ajouter rapidement des intentions et des énoncés courants. Il s’agit d’un moyen simple et rapide d’ajouter des fonctionnalités à votre application cliente conversationnelle sans avoir à concevoir les modèles pour ces fonctionnalités. 

## <a name="add-a-prebuilt-intent"></a>Ajouter une intention prédéfinie

1. Sur la page **My Apps** (Mes applications), sélectionnez votre application. Vous ouvrez ainsi votre application à la section **Build** (Générer). 

1. Sur la page **Intents** (Intentions), sélectionnez **Add prebuilt intent** (Ajouter une intention prédéfinie) à partir de la barre d’outils au-dessus de la liste des intentions. 

1. Sélectionnez l’intention **Utilities.Cancel** à partir de la boîte de dialogue contextuelle. 

    ![Ajouter une intention prédéfinie](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Cliquez sur le bouton **Terminé**.

## <a name="train-and-test"></a>Effectuer l’apprentissage et tester

1. Après avoir ajouté l’intention, effectuez l’apprentissage de l’application en sélectionnant **Effectuer l’apprentissage** dans la barre d’outils en haut à droite. 

1. Testez la nouvelle intention en sélectionnant **Tester** dans la barre d’outils de droite. 

1. Dans la zone de texte, entrez des énoncés d’annulation :

    |Énoncé de test|Scores de prédictions|
    |--|:--|
    |Je souhaite annuler mon vol.|0.67|
    |Annuler l’achat.|0.52|
    |Annuler la réunion.|0.56|

    ![Tester l’intention prédéfinie](./media/luis-prebuilt-intents/test.png)

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Entités prédéfinies](./luis-prebuilt-entities.md)
