---
title: Qu’est-ce que l’API Lecteur immersif ?
titleSuffix: Azure Cognitive Services
description: Découvrez-en plus sur l’API Lecteur immersif.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 4500b6213c549ab9977fe8f2d849ffa8089d04b9
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718434"
---
# <a name="what-is-immersive-reader"></a>Qu’est-ce que le lecteur immersif ?

Le [lecteur immersif](https://www.onenote.com/learningtools) est un outil qui permet d’implémenter des techniques éprouvées pour améliorer la compréhension dans le cadre de l’apprentissage de la lecture, l’apprentissage d’une langue et pour les personnes avec des difficultés d’apprentissage, comme la dyslexie.

Vous pouvez utiliser le lecteur immersif dans votre application web à l’aide du SDK du lecteur immersif.

## <a name="what-does-immersive-reader-do"></a>Que fait le lecteur immersif ?

Le lecteur immersif est conçu pour rendre la lecture plus accessible pour tout le monde.

* Affiche le contenu dans une fenêtre de lecture minimaliste

  ![Lecteur immersif](./media/immersive-reader.png)

* Affiche des images des mots couramment utilisés

  ![Imagier](./media/picture-dictionary.png)

* Met en évidence les noms, verbes, adjectifs et adverbes

  ![Éléments de texte](./media/parts-of-speech.png)

* Lit à haute voix votre contenu pour vous

  ![Lire à haute voix](./media/read-aloud.png)

* Traduit votre contenu dans une autre langue

  ![Traduction](./media/translation.png)

* Décompose les mots en syllabes

  ![Syllabation](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>Comment fonctionne le lecteur immersif ?

Le lecteur immersif est une application web autonome qui, lorsqu’elle est appelée à l’aide du SDK JavaScript Lecteur immersif, s’affiche en haut de votre application web existante via un `iframe`. Lorsque vous appelez l’API pour lancer le lecteur immersif, vous spécifiez le contenu que vous souhaitez afficher dedans. Notre SDK gère la création et la mise en forme du `iframe` et la communication avec le service principal du lecteur immersif, qui traite le contenu pour la synthèse vocale, la traduction et ainsi de suite.

## <a name="next-steps"></a>Étapes suivantes

Prise en main du lecteur immersif :

* Passer au [guide de démarrage rapide](./quickstart.md)
* Explorer le [SDK Lecteur immersif sur GitHub](https://github.com/Microsoft/immersive-reader-sdk)
* Lire la [référence du SDK Lecteur immersif](./reference.md)