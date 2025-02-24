---
title: Présentation de l’API Vision par ordinateur - Vision par ordinateur
titlesuffix: Azure Cognitive Services
description: Le service Vision par ordinateur offre aux développeurs un accès à des algorithmes avancés pour le traitement d’images et le renvoi d’informations.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f92d8ce8e95962558b3bdb7b4a4d8fe70c725f46
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604495"
---
# <a name="what-is-computer-vision"></a>Qu’est-ce que le service Vision par ordinateur ?

Le service Vision par ordinateur d’Azure offre aux développeurs un accès à des algorithmes avancés permettant de traiter des images et de retourner des informations. Pour analyser une image, vous pouvez télécharger une image ou spécifier une URL d’image. Les algorithmes de traitement d’images peuvent analyser le contenu de différentes manières, selon les composants visuels qui vous intéressent. Par exemple, le service Vision par ordinateur peut déterminer si une image contient du contenu pour adultes ou choquant, ou rechercher tous les visages humains dans une image.

Vous pouvez utiliser le service Vision par ordinateur dans votre application en utilisant un kit SDK natif ou en appelant directement l’API REST. Cette page couvre globalement ce que vous pouvez faire avec le service Vision par ordinateur.

## <a name="analyze-images-for-insight"></a>Analyser des images pour obtenir des informations

Vous pouvez analyser des images pour obtenir des informations détaillées sur leurs composants et caractéristiques visuels. Toutes les fonctionnalités listées dans le tableau ci-dessous sont fournies par l’API [Analyser l’image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).

| Action | Description |
| ------ | ----------- |
|**[Identifier les composants visuels à l’aide de balises](concept-tagging-images.md)**|Identifier les composants visuels d’une image et les marquer à l’aide de balises à partir d’un ensemble de milliers d’objets, d’êtres vivants, de scènes et d’actions reconnaissables. Quand les étiquettes sont ambigües ou inhabituelles, la réponse de l’API fournit des conseils pour clarifier le contexte de l’étiquette. Le balisage ne se limite pas au sujet principal, comme une personne au premier plan, mais il inclut également le décor (intérieur ou extérieur), le mobilier, les outils, les plantes, les animaux, les accessoires, les gadgets, etc.|
|**[Détecter des objets](concept-object-detection.md)**| La détection d’objets est similaire au balisage, mais l’API retourne les coordonnées de cadre englobant pour chaque balise appliquée. Par exemple, si une image contient un chien, un chat et une personne, l’opération de détection répertorie ces objets ainsi que leurs coordonnées dans l’image. Vous pouvez utiliser cette fonctionnalité pour traiter d’autres relations entre les objets dans une image. Elle vous permet également de savoir quand il existe plusieurs instances de la même balise dans une image.|
|**[Détecter les marques](concept-brand-detection.md)**|Identifiez les marques commerciales dans les images ou vidéos à partir d’une base de données de milliers de logos internationaux. Vous pouvez utiliser cette fonctionnalité, par exemple, pour déterminer quelles marques sont les plus populaires sur les réseaux sociaux ou prédominants en termes de placement de produit dans les médias.|
|**[Catégoriser une image](concept-categorizing-images.md)**|Identifier et catégoriser une image dans son ensemble en utilisant une [taxonomie des catégories](Category-Taxonomy.md) comprenant des hiérarchies héréditaires parent/enfant. Les catégories peuvent être utilisées seules ou avec nos nouveaux modèles de balisage.<br/>Actuellement, l’anglais est la seule langue prise en charge pour le balisage et la catégorisation des images.|
|**[Décrire une image](concept-describing-images.md)**|Générer une description de l’intégralité d’une image dans un langage lisible utilisant des phrases complètes. Les algorithmes du service Vision par ordinateur génèrent différentes descriptions selon les objets identifiés dans l’image. Chacune des descriptions est évaluée, et un score de confiance est généré. Une liste est ensuite renvoyée, classée du score de confiance plus élevé au plus bas.|
|**[Détecter les visages](concept-detecting-faces.md)** |Détecter les visages dans une image et fournir des informations sur chaque visage détecté. Le service Vision par ordinateur indique les coordonnées, le rectangle, le sexe et l’âge pour chaque visage détecté.<br/>Le service Vision par ordinateur fournit un sous-ensemble des fonctionnalités du service [Visage](/azure/cognitive-services/face/). Utilisez le service Visage pour effectuer une analyse plus détaillée (reconnaissance faciale, détection de la posture, etc.).|
|**[Détecter les types d’images](concept-detecting-image-types.md)**|Détecter les caractéristiques relatives à une image, par exemple si une image est un dessin au trait ou s’il pourrait s’agir d’une image clipart.|
|**[Détecter le contenu spécifique à un domaine](concept-detecting-domain-content.md)**|Utiliser des modèles de domaine pour détecter et identifier le contenu spécifique à un domaine dans une image, notamment pour reconnaître des célébrités ou des éléments géographiques. Par exemple, si une image contient des célébrités, le service Vision par ordinateur peut utiliser un modèle de domaine pour célébrités afin de déterminer si les personnes détectées dans l’image correspondent à des célébrités connues.|
|**[Détecter le jeu de couleurs](concept-detecting-color-schemes.md)**|Analyser l’utilisation des couleurs dans une image. Le service Vision par ordinateur peut déterminer si une image est en noir et blanc ou en couleur. Pour les images en couleur, il peut également identifier les couleurs dominantes et d’accentuation.|
|**[Générer une miniature](concept-generating-thumbnails.md)**|Analyser le contenu d’une image pour en générer une miniature. Le service Vision par ordinateur commence par générer une miniature de haute qualité, puis analyse les objets contenus dans l’image pour déterminer la *zone d’intérêt*. Il rogne ensuite l’image pour conserver uniquement la zone d’intérêt. La miniature générée peut être présentée à l’aide de proportions différentes de celles de l’image d’origine selon les besoins de chacun.|
|**[Obtenir la zone d’intérêt](concept-generating-thumbnails.md#area-of-interest)**|Analyser le contenu d’une image pour retourner les coordonnées de la *zone d’intérêt*. Au lieu de rogner l’image et de générer une miniature, le service Vision par ordinateur retourne les coordonnées de cadre englobant de la région pour que l’application appelante puisse modifier l’image d’origine de la manière souhaitée.|

## <a name="extract-text-from-images"></a>Extraire du texte à partir des images

Vous pouvez utiliser l’[API Read](concept-recognizing-text.md#read-api) du service Vision par ordinateur pour extraire le texte imprimé ou manuscrit d’images dans un flux de caractères exploitable automatiquement. L’API Read utilise nos derniers modèles et fonctionne avec le texte dont les surfaces et arrière-plans diffèrent, tels que les accusés de réception, les affiches, les cartes de visite, les lettres et les tableaux blancs. Actuellement, l’anglais est la seule langue prise en charge.

Vous pouvez également utiliser l’API [OCR (reconnaissance optique des caractères)](concept-recognizing-text.md#ocr-optical-character-recognition-api) pour extraire le texte imprimé en plusieurs langues. Le cas échéant, la fonction OCR corrige la rotation du texte reconnu et fournit les coordonnées du cadre de chaque mot. La fonction OCR prend en charge 25 langues et détecte automatiquement la langue du texte reconnu.



## <a name="moderate-content-in-images"></a>Modérer du contenu dans les images

Vous pouvez utiliser Vision par ordinateur pour [détecter le contenu pour adultes et choquant](concept-detecting-adult-content.md) dans une image et retourner un score de confiance pour les deux. Vous pouvez définir le filtre pour la détection de contenu choquant et réservé aux adultes sur une échelle variable afin de répondre à vos besoins spécifiques.

## <a name="use-containers"></a>Utiliser des conteneurs

[Utilisez des conteneurs Vision par ordinateur](computer-vision-how-to-install-containers.md) pour reconnaître le texte imprimé et le texte manuscrit localement en installant un conteneur Docker normalisé plus près de vos données.

## <a name="image-requirements"></a>Exigences des images

Le service Vision par ordinateur peut analyser des images qui répondent aux exigences suivantes :

- L’image doit être au format JPEG, PNG, GIF ou BMP
- La taille de fichier de l’image doit être inférieure à 4 mégaoctets (Mo)
- Les dimensions de l’image doivent être supérieures à 50 x 50 pixels
  - Pour l’OCR, la taille de l’image doit être comprise entre 50 x 50 et 4200 x 4200 pixels

## <a name="data-privacy-and-security"></a>Sécurité et confidentialité des données

Comme avec tous les services Cognitive Services, les développeurs utilisant le service Vision par ordinateur doivent connaître les politiques de Microsoft relatives aux données client. Pour en savoir plus, consultez la [page Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) dans le Centre de gestion de la confidentialité Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Démarrez avec le service Vision par ordinateur en suivant un guide de démarrage rapide :

- [Démarrage rapide : Analyser une image](quickstarts-sdk/csharp-analyze-sdk.md)
- [Démarrage rapide : Extraire du texte manuscrit](quickstarts-sdk/csharp-hand-text-sdk.md)
- [Démarrage rapide : Générer une miniature](quickstarts-sdk/csharp-thumb-sdk.md)
