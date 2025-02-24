---
title: Prise en charge des langues – Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Liste des langages naturels pris en charge par les fonctionnalités de la Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1a70d1b2ea504d0ccfba925810a2d19d0c7583cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60759603"
---
# <a name="language-support-for-computer-vision"></a>Prise en charge des langues pour la Vision par ordinateur

Certaines des fonctionnalités de la Vision par ordinateur prennent en charge plusieurs langues ; celles qui ne sont pas mentionnées ici ne gèrent que l’anglais.

## <a name="text-recognition"></a>Reconnaissance de texte

La Vision par ordinateur peut reconnaître du texte dans de nombreuses langues. Plus précisément, l’API [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) gère tout une série de langues, tandis que les API [Lire](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) et [Reconnaître le texte](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) ne prennent en charge que l’anglais. Pour plus d’informations sur cette fonctionnalité et sur les avantages de chaque API, voir [Reconnaître du texte imprimé et du texte manuscrit](concept-recognizing-text.md).

L’OCR détecte automatiquement la langue du matériau d’entrée ; il est donc inutile de spécifier un code de langue dans l’appel d’API. Les codes de langue sont toujours retournés dans la valeur du nœud `"language"` de la réponse JSON.

|Langage| Code de langue | API OCR |
|:-----|:----:|:-----:|
|Arabe | `ar`|✔ |
|Chinois (simplifié) | `zh-Hans`|✔ |
|Chinois (traditionnel) | `zh-Hant`|✔ |
|Tchèque | `cs` |✔ |
|Danois | `da` |✔ |
|Néerlandais | `nl` |✔ |
|Anglais | `en` |✔ |
|Finnois | `fi` |✔ |
|Anglais | `fr` |✔ |
|Allemand | `de` |✔ |
|Grec | `el` |✔ |
|Hongrois | `hu` |✔ |
|Italien | `it` |✔ |
|Japonais | `ja` |✔ |
|Coréen | `ko` |✔ |
|Norvégien | `nb` |✔ |
|Polonais | `pl` |✔ |
|Portugais | `pt` |✔ |
|Roumain | `ro` |✔ |
|Russe | `ru` |✔ |
|Serbe (cyrillique) | `sr-Cyrl` |✔ |
|Serbe (latin) | `sr-Latn` |✔ |
|Slovaque | `sk` |✔ |
|Espagnol | `es` |✔ |
|Suédois | `sw` |✔ |
|Turc | `tr` |✔ |

## <a name="image-analysis"></a>Analyse d’image

Certaines actions de l’API [Analyser – Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) peuvent retourner des résultats dans d’autres langues, spécifiées avec le paramètre de requête `language`. D’autres donnent des résultats en anglais indépendamment de la langue spécifiée ; les dernières lèvent une exception pour les langues non prises en charge. Les actions sont spécifiées avec les paramètres de requête `visualFeatures` et `details` ; pour connaître la liste des actions possibles avec l’analyse d’image, voir la [Vue d’ensemble](home.md).

|Langage | Code de langue | Catégories | Balises | Description | Adulte | Marques | Couleur | Visages | ImageType | Objets | Célébrités | Points de repère |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinois | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Anglais | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonais | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portugais | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Espagnol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser les fonctionnalités Vision par ordinateur mentionnées dans ce guide.

* [Analyser une image locale (REST)](./quickstarts/csharp-analyze.md)
* [Extraire du texte imprimé (REST)](./quickstarts/csharp-print-text.md)