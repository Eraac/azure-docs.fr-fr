---
title: Support multilingue
titleSuffix: Azure Cognitive Services
description: LUIS possède un éventail de fonctionnalités au sein du service. Certaines fonctionnalités partagent la même parité de langage. Assurez-vous que les fonctionnalités qui que vous intéressent sont prises en charge dans la culture de langue que vous ciblez. Une application LUIS est propre à une culture et ne peut pas être modifiée après configuration.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/19/2019
ms.author: diberry
ms.openlocfilehash: 8f067bc005c4de9ddc87ed598b1717f8fbb29a6a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072377"
---
# <a name="language-and-region-support-for-luis"></a>Prise en charge de la région et de la langue pour LUIS

LUIS possède un éventail de fonctionnalités au sein du service. Certaines fonctionnalités partagent la même parité de langage. Assurez-vous que les fonctionnalités qui que vous intéressent sont prises en charge dans la culture de langue que vous ciblez. Une application LUIS est propre à une culture et ne peut pas être modifiée après configuration.

## <a name="multi-language-luis-apps"></a>Applications LUIS multilingues

Si vous avez besoin d’une application client LUIS multilingue, comme un chatbot, vous avez plusieurs options. Si LUIS prend en charge toutes les langues, vous développez une application LUIS pour chaque langue. Chaque application LUIS possède un ID d’application unique et un journal de point de terminaison. Si vous avez besoin d’une compréhension pour une langue non prise en charge par LUIS, vous pouvez utiliser l’[API Microsoft Translator](../Translator/translator-info-overview.md) pour traduire l’énoncé dans un langage pris en charge, envoyer l’énoncé au point de terminaison LUIS et recevoir le scores obtenu.

## <a name="languages-supported"></a>Langues prises en charge

LUIS comprend les énoncés dans les langues suivantes :

| Langage |Paramètres régionaux  |  Domaine prédéfini | Entité prédéfinie | Recommandations de liste d’expression | \**[Analyse de texte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Sentiment et<br>mots clés)|
|--|--|:--:|:--:|:--:|:--:|
| Anglais (États-Unis) |`en-US` | ✔ | ✔  |✔|✔|
| *[Chinois](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Néerlandais |`nl-NL` |-|  -   |-|✔|
| Français (France) |`fr-FR` |-| ✔ |✔ |✔|
| Français (Canada) |`fr-CA` |-|   -   |-|✔|
| Allemand |`de-DE` |-| ✔ |✔ |✔|
| Italien |`it-IT` |-| ✔ |✔|✔|
| *[Japonais](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|Expression clé uniquement|
| Coréen |`ko-KR` |-|   -   |-|Expression clé uniquement|
| Portugais (Brésil) |`pt-BR` |-| ✔ |✔ |pas toutes les sous-cultures|
| Espagnol (Espagne) |`es-ES` |-| ✔ |✔|✔|
| Espagnol (Mexique)|`es-MX` |-|  -   |✔|✔|
| Turc | `tr-TR` |-|-|-|Sentiment uniquement|


La prise en charge linguistique varie pour les [entités prédéfinies](luis-reference-prebuilt-entities.md) et les [domaines prédéfinis](luis-reference-prebuilt-domains.md).

### <a name="chinese-support-notes"></a>*Notes pour la prise en charge du chinois

 - Dans la culture `zh-cn`, LUIS attend le jeu de caractères chinois simplifié plutôt que le jeu de caractères chinois traditionnel.
 - Les noms des intentions, les entités, les fonctionnalités et les expressions régulières peuvent être en caractères chinois ou romans.
 - Pour plus d’informations sur les domaines prédéfinis pris en charge dans la culture `zh-cn`, voir la [référence des domaines prédéfinis](luis-reference-prebuilt-domains.md).
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>*Notes pour la prise en charge du japonais

 - LUIS ne fournit pas d’analyse syntaxique et ne comprend pas la différence entre le japonais informel et Keigo. Vous devez donc intégrer les différents niveaux de formalité sous formes d’exemples pour vos applications.
     - でございます ne signifie pas la même chose que です.
     - です ne signifie pas la même chose que だ.

### <a name="text-analytics-support-notes"></a>\*\*Notes sur la prise en charge de l’analyse de texte
L’analyse de texte inclut l’entité keyPhrase prédéfinie et l’analyse des sentiments. Seul le portugais est pris en charge pour les sous-cultures : `pt-PT` et `pt-BR`. Toutes les autres cultures sont prises en charge au niveau de la culture principale. En savoir plus sur les [langues prises en charge](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) par l’analyse de texte.

### <a name="speech-api-supported-languages"></a>Langues prises en charge par l’API Microsoft Speech
Consultez les [langues prises en charge](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) par Speech pour connaître les langues du mode de dictée de Speech.

### <a name="bing-spell-check-supported-languages"></a>Langues prises en charge par la vérification orthographique Bing
Consultez les [langues prises en charge](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) par la vérification orthographique Bing pour obtenir la liste des langues prises en charge et leur état.

## <a name="rare-or-foreign-words-in-an-application"></a>Mots rares ou étrangers dans une application
Dans la culture `en-us`, LUIS apprend à distinguer la plupart des mots anglais, y compris l’argot. Dans la culture `zh-cn`, LUIS apprend distinguer la plupart des caractères chinois. Si vous utilisez un mot rare en `en-us` ou caractère en `zh-cn`, et que LUIS semble incapable de distinguer ce mot ou ce caractère, vous pouvez ajouter le mot ou le caractère à une [fonctionnalité de la liste d’expressions](luis-how-to-add-features.md). Par exemple, les mots n’appartenant pas à la culture de l’application (autrement dit, les mots étrangers) doivent être ajoutés à une fonctionnalité de liste d’expressions. Cette liste d’expressions doit être marquée comme non interchangeable, pour indiquer que l’ensemble de mots rares constitue une classe que LUIS doit apprendre à reconnaître, mais que ces mots ne sont ni synonymes, ni interchangeables entre eux.

### <a name="hybrid-languages"></a>Langues hybrides
Les langues hybrides combinent des mots de deux cultures, comme l’anglais et le chinois. Ces langues ne sont pas prises en charge dans LUIS, car une application est basée sur une culture unique.

## <a name="tokenization"></a>Création de jetons
Pour effectuer l’apprentissage machine, LUIS décompose l’énoncé en [jetons](luis-glossary.md#token) selon la culture.

|Langage|  chaque espace ou caractère spécial | niveau caractère|mots composés|[entité renvoyée sous forme de jeton](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Chinois||✔||✔|
|Néerlandais|||✔|✔|
|Anglais (en-us)|✔ ||||
|Français (fr-FR)|✔||||
|Français (fr-CA)|✔||||
|Allemand|||✔|✔|
|Italien|✔||||
|Japonais||||✔|
|Coréen||✔||✔|
|Portugais (Brésil)|✔||||
|Espagnol (es-ES)|✔||||
|Espagnol (es-MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Versions personnalisées du générateur de jetons

Les cultures suivantes ont des versions personnalisées du générateur de jetons :

|Culture|Version|Objectif|
|--|--|--|
|Allemand<br>`de-de`|1.0.0|Segmente les mots composés en composants simples à l’aide d’un générateur de jetons de type Machine Learning.<br>L’énoncé `Ich fahre einen krankenwagen` est transformé en `Ich fahre einen kranken wagen`. Permet de marquer indépendamment `kranken` et `wagen` comme des entités différentes.|
|Allemand<br>`de-de`|1.0.2|Segmente les mots en les fractionnant sur les espaces.<br> L’énoncé `Ich fahre einen krankenwagen` reste un jeton unique. `krankenwagen` est donc marqué comme une seule entité. |

### <a name="migrating-between-tokenizer-versions"></a>Changer de version du générateur de jetons
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID. 

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

La segmentation du texte en unités lexicales se produit au niveau de l’application. La tokenisation au niveau de la version n’est pas prise en charge. 

[Importez le fichier comme une nouvelle application](luis-how-to-start-new-app.md#import-an-app-from-file), au lieu d’une version, pour que l’application ait un autre ID, mais utilise la version du générateur de jetons spécifiée dans le fichier. 