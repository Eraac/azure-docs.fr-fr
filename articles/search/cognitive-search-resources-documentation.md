---
title: Ressources de documentation sur la recherche cognitive - Recherche Azure
description: Liste annotée d’articles, de tutoriels, d’exemples et de billets de blog sur les charges de travail de recherche cognitive dans la Recherche Azure.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 41637fae5592ac292da22303071d51b43116c78b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671905"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Ressources de documentation sur les charges de travail de recherche cognitive

La recherche cognitive, désormais généralement disponible, représente une nouvelle couche d'enrichissement pour l'indexation de la Recherche Azure qui trouve des informations latentes dans des sources non textuelles et du texte indifférencié, et les transforme en contenu avec possibilité de recherche en texte intégral dans la Recherche Azure.

Les articles suivants constituent la documentation complète de la recherche cognitive.

## <a name="getting-started"></a>Prise en main
+ [Qu’est-ce que la recherche cognitive ?](cognitive-search-concept-intro.md)
+ [Démarrage rapide : Essayer la recherche cognitive sur le portail](cognitive-search-quickstart-blob.md)
+ [Tutoriel : Découvrir les API de recherche cognitive](cognitive-search-tutorial-blob.md)
+ [Exemple : Création d’une compétence personnalisée pour la recherche cognitive](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Guides pratiques
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [Guide pratique pour référencer des annotations dans un ensemble de compétences](cognitive-search-concept-annotations-syntax.md)
+ [Guide pratique pour mapper des champs sur un index](cognitive-search-output-field-mapping.md)
+ [Guide pratique pour traiter et extraire des informations à partir d’images](cognitive-search-concept-image-scenarios.md)
+ [Guide pratique pour régénérer un index de Recherche Azure](search-howto-reindex.md)
+ [Guide pratique pour définir une interface de compétences personnalisées](cognitive-search-custom-skill-interface.md)
+ [Conseils de dépannage](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Informations de référence

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ [API REST](https://docs.microsoft.com/rest/api/searchservice/)
  + [Créer un ensemble de compétences (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Créer un indexeur (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Voir aussi

+ [API REST de la Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexeurs dans Recherche Azure](search-indexer-overview.md)
+ [Qu’est-ce que Recherche Azure ?](search-what-is-azure-search.md)
