---
title: Scènes, captures et images clés Video Indexer - Azure
titlesuffix: Azure Media Services
description: Cette rubrique offre une vue d'ensemble des scènes, captures et images clés Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: d312a93f83ef38fa1ae855a1e313280fc608948d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799397"
---
# <a name="scenes-shots-and-keyframes"></a>Scènes, captures et images clés

Video Indexer prend en charge la segmentation de vidéos en unités temporelles basées sur des propriétés sémantiques et structurelles. Cette fonctionnalité permet aux clients de facilement parcourir, gérer et modifier leur contenu vidéo en fonction des niveaux de détail. Par exemple, en fonction des scènes, des captures et des images clés, décrites dans cette rubrique. Actuellement, la fonctionnalité de **détection de scène** est disponible en tant que préversion.   

![Scènes, captures et images clés](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)

## <a name="scene-detection-preview"></a>Détection de scène (préversion)

Video Indexer détermine quand une scène change dans la vidéo via des signaux visuels. Une scène représente un événement unique et est composée d’une série de captures consécutives, qui sont sémantiquement liées. Une miniature de la scène constitue la première image clé de sa capture sous-jacente. Video Indexer segmente une vidéo en scènes en fonction de la cohérence des couleurs sur des captures consécutives et récupère le début et la fin de chaque scène. La détection de scène est considérée comme étant une tâche ardue, car elle implique de quantifier des aspects sémantiques d’une vidéo.

> [!NOTE]
> S’applique aux vidéos qui contiennent au moins 3 scènes.

## <a name="shot-detection"></a>Détection de plan

Video Indexer détermine quand une capture change dans la vidéo selon des signaux visuels, en effectuant le suivi des transitions brusques et progressives du jeu de couleurs des images adjacentes. Les métadonnées de la capture incluent un temps de début et de fin, ainsi que la liste des images clés inclues dans cette capture. Les captures sont des images consécutives prises de la même caméra et en même temps.

## <a name="keyframe-detection"></a>Détection d’une image clé

Sélectionne les images représentant au mieux la capture. Les images clés sont des images représentatives sélectionnées à partir de la vidéo complète, en fonction des propriétés esthétiques (par exemple, contraste et stabilité). Video Indexer récupère une liste d’ID d’images clés faisant partie des métadonnées de la capture, en fonction des clients pouvant extraire la miniature de l’image clé. 

Les images clés sont associées à des captures dans la sortie JSON. 

## <a name="next-steps"></a>Étapes suivantes

[Examiner la sortie de Video Indexer générée par l’API](video-indexer-output-json-v2.md#scenes)