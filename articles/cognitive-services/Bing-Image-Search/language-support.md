---
title: Prise en charge linguistique - API Recherche d’images Bing
titleSuffix: Azure Cognitive Services
description: Découvrez les langues et pays/régions pris en charge par l’API Recherche d’images Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 6f4c354c89fa00d5fc65c635f5f6315761be2f01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66384141"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Langues et régions prises en charge par l’API Recherche d’images Bing

L’API Recherche d’images Bing prend en charge plus d’une trentaine de pays/régions, dont beaucoup possèdent plusieurs langues. Spécifier un pays/une région avec une requête sert principalement à affiner les résultats de la recherche en fonction des centres d’intérêt dans ce pays/cette région. En outre, les résultats peuvent contenir des liens vers Bing, servant à localiser l’expérience utilisateur de Bing en fonction du pays/de la région ou de la langue spécifiée.

Pour spécifier le pays/la région et la langue, utilisez pour le paramètre de requête `mkt` (marché) l’un des codes du tableau **Marchés** ci-dessous. Le marché spécifie à la fois un pays/une région et une langue. Si l’utilisateur souhaite afficher du texte dans une langue différente, utilisez pour le paramètre de requête `setLang` le code langue approprié.

De même, vous pouvez spécifier le pays/la région à l’aide du paramètre de requête `cc`. Si vous spécifiez un pays/une région, vous devez également spécifier un ou plusieurs codes de langue à l’aide de l’en-tête HTTP `Accept-Language`. Les langues prises en charge varient selon le pays/la région ; elles sont indiquées pour chaque pays/région dans le tableau Marchés.

> [!NOTE]
> L’API Images populaires n’est actuellement disponible que sur les marchés suivants :
> - en-US (anglais, États-Unis)
> - en-CA (anglais, Canada)
> - en-AU (anglais, Australie)
> - zh-CN (chinois, Chine)

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
Pour plus d’informations sur les points de terminaison de Recherche d’actualités Bing, consultez [Référence de l’API Recherche d’images v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).
