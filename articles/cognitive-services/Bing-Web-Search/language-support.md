---
title: Prise en charge linguistique - API Recherche Web Bing
titleSuffix: Azure Cognitive Services
description: Liste des langages naturels, des pays et des régions pris en charge par l’API Recherche Web Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 18b124ca7f6f270488fa8e010d2b1c0404f8e9e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66384774"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Langages et régions pris en charge par l’API Recherche Web Bing

L’API Recherche Web Bing prend en charge plus d’une trentaine de pays ou régions, dont beaucoup possèdent plusieurs langues. En utilisant une requête pour spécifier un pays ou une région, vous pouvez affiner les résultats de la recherche en fonction des centres d’intérêt dans ce pays ou cette région. Il se peut que les résultats incluent des liens vers Bing qui permettent de localiser l’expérience utilisateur Bing en fonction du pays, de la région ou de la langue spécifié.

Vous pouvez spécifier un pays ou une région à l’aide du paramètre de requête `cc`. Lorsqu’un pays ou une région est spécifié, vous devez indiquer un ou plusieurs codes de langue avec l’[`Accept-Language`en-tête](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers). Utilisez le [tableau des marchés](#markets) pour consulter la liste des langues prises en charge pour chaque marché.

Vous pouvez également indiquer le marché à l’aide du paramètre de requête `mkt` et d’un code issu du tableau **Marchés**. En indiquant un marché, vous renseignez simultanément un pays/une région et une langue prédéfinie. Vous pouvez définir clairement la langue avec le paramètre de requête `setLang`.

## <a name="countriesregions"></a>Pays/régions

|Pays/région|Code|
|-------|----|
|Argentine|AR|
|Australie|AU|
|Autriche|AT|
|Belgique|BE|
|Brésil|BR|
|Canada|CA|
|Chili|CL|
|Danemark|DK|
|Finlande|FI|
|France|FR|
|Allemagne|DE|
|Hong Kong (R.A.S.)|HK|
|Inde|IN|
|Indonésie|ID|
|Italie|IT|
|Japon|JP|
|Corée du Sud|KR|
|Malaisie|MY|
|Mexique|MX|
|Pays-bas|NL|
|Nouvelle-Zélande|NZ|
|Norvège|NON|
|Chine|CN|
|Pologne|PL|
|Portugal|PT|
|Philippines|PH|
|Russie|RU|
|Arabie Saoudite|SA|
|Afrique du Sud|ZA|
|Espagne|ES|
|Suède|SE|
|Suisse|CH|
|Taïwan|TW|
|Turquie|TR|
|Royaume-Uni|GB|
|États-Unis|FR|

## <a name="markets"></a>Marchés

|Pays/région|Langage|Code du marché|
|-------|--------|-----------|
|Argentine|Espagnol|es-AR|
|Australie|Anglais|en-AU|
|Autriche|Allemand|de-AT|
|Belgique|Néerlandais|nl-BE|
|Belgique|Anglais|fr-BE|
|Brésil|Portugais|pt-br|
|Canada|Anglais|en-CA|
|Canada|Anglais|fr-CA|
|Chili|Espagnol|es-CL|
|Danemark|Danois|da-DK|
|Finlande|Finnois|fi-FI|
|France|Anglais|fr-FR|
|Allemagne|Allemand|de-DE|
|Hong Kong (R.A.S.)|Chinois traditionnel|zh-HK|
|Inde|Anglais|en-IN|
|Indonésie|Anglais|en-ID|
|Italie|Italien|it-IT|
|Japon|Japonais|ja-JP|
|Corée du Sud|Coréen|ko-KR|
|Malaisie|Anglais|en-MY|
|Mexique|Espagnol|es-MX|
|Pays-bas|Néerlandais|nl-NL|
|Nouvelle-Zélande|Anglais|en-NZ|
|Norvège|Norvégien|no-NO|
|Chine|Chinois|zh-CN|
|Pologne|Polonais|pl-PL|
|Portugal|Portugais|pt-PT|
|Philippines|Anglais|en-PH|
|Russie|Russe|ru-RU|
|Arabie Saoudite|Arabe|ar-SA|
|Afrique du Sud|Anglais|en-ZA|
|Espagne|Espagnol|es-ES|
|Suède|Suédois|sv-SE|
|Suisse|Anglais|fr-CH|
|Suisse|Allemand|de-CH|
|Taïwan|Chinois traditionnel|zh-TW|
|Turquie|Turc|tr-TR|
|Royaume-Uni|Anglais|en-GB|
|États-Unis|Anglais|en-US|
|États-Unis|Espagnol|es-US|

## <a name="next-steps"></a>Étapes suivantes

* [Informations de référence sur l’API Recherche d’images Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
