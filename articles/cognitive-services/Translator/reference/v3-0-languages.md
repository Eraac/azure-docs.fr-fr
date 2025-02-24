---
title: Méthode Languages de l’API de traduction de texte Translator Text
titlesuffix: Azure Cognitive Services
description: Utilisez la méthode Languages de l’API de traduction de texte Translator Text.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 7bac97b7edbfa0f7a03a65e462106b33160c6d1a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435074"
---
# <a name="translator-text-api-30-languages"></a>API de traduction de texte Translator Text 3.0 : Languages

Permet d’obtenir l’ensemble des langues actuellement prises en charge par d’autres opérations de l’API de traduction de texte Translator Text. 

## <a name="request-url"></a>URL de la demande

Envoyez une demande `GET` à :
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Paramètres de la demande

Les paramètres de demande transmis à la chaîne de requête sont les suivants :

<table width="100%">
  <th width="20%">Paramètre de requête.</th>
  <th>Description</th>
  <tr>
    <td>api-version</td>
    <td><em>Paramètre obligatoire</em>.<br/>Version de l’API demandée par le client. La valeur doit être `3.0`.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Paramètre facultatif*.<br/>Liste de noms séparée par des virgules définissant le groupe de langues à renvoyer. Les noms de groupe autorisés sont les suivants : `translation`, `transliteration` et `dictionary`. Si aucune étendue n’est fournie, tous les groupes sont renvoyés, ce qui équivaut à transmettre `scope=translation,transliteration,dictionary`. Pour déterminer si l’ensemble des langues prises en charge est approprié dans le cadre de votre scénario, consultez la description de [l’objet de réponse](#response-body).</td>
  </tr>
</table> 

Les en-têtes de requête sont les suivants :

<table width="100%">
  <th width="20%">headers</th>
  <th>Description</th>
  <tr>
    <td>Accept-Language</td>
    <td>*En-tête de requête facultatif*.<br/>Langue à utiliser pour les chaînes d’interface utilisateur. Certains des champs dans cette réponse sont les noms de langue ou de région. Utilisez ce paramètre pour définir la langue dans laquelle ces noms sont renvoyés. La langue est spécifiée en fournissant une balise de langue BCP 47 bien formée. Par exemple, utilisez la valeur `fr` pour demander des noms en français ou utilisez la valeur `zh-Hant` pour demander des noms en chinois traditionnel.<br/>Les noms sont fournis en anglais lorsqu’aucune langue cible n’est spécifiée ou lorsqu’aucune traduction n’est pas disponible.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*En-tête de requête facultatif*.<br/>GUID généré par le client et servant à identifier la requête de façon unique.</td>
  </tr>
</table> 

L’authentification n’est pas nécessaire pour obtenir les ressources de langue.

## <a name="response-body"></a>Response body

Un client utilise le paramètre de requête `scope` pour définir les groupes de langues qui l’intéresse.

* `scope=translation` fournit les langues prises en charge pour traduire le texte d’une langue dans une autre.

* `scope=transliteration` fournit des fonctions de conversion de texte dans une langue, d’un script à l’autre.

* `scope=dictionary` fournit des paires de langues pour lesquelles les opérations `Dictionary` renvoient des données.

Un client peut récupérer plusieurs groupes simultanément en indiquant une liste de noms séparée par des virgules. Par exemple, `scope=translation,transliteration,dictionary` peut renvoyer les langues prises en charge pour tous les groupes.

Une réponse correcte est un objet JSON avec une propriété pour chaque groupe demandé :

```json
{
    "translation": {
        //... set of languages supported to translate text (scope=translation)
    },
    "transliteration": {
        //... set of languages supported to convert between scripts (scope=transliteration)
    },
    "dictionary": {
        //... set of languages supported for alternative translations and examples (scope=dictionary)
    }
}
```

La valeur de chaque propriété prend la forme suivante.

* Propriété `translation`

  La valeur de la propriété `translation` est un dictionnaire composé de paires (clé, valeur). Chaque clé est une balise de langue BCP 47. Une clé identifie une langue pour laquelle du texte peut être traduit de ou vers une autre langue. La valeur associée à cette clé est un objet JSON avec des propriétés décrivant la langue :

  * `name`: nom d’affichage de la langue dans les paramètres régionaux demandés via l’en-tête `Accept-Language`.

  * `nativeName`: nom d’affichage de la langue dans les paramètres régionaux natifs de cette langue.

  * `dir`: sens de l’écriture, `rtl` pour les langues qui se lisent de droite à gauche, ou `ltr` pour les langues qui se lisent de gauche à droite.

  Voici un exemple :
          
  ```json
  {
    "translation": {
      ...
      "fr": {
        "name": "French",
        "nativeName": "Français",
        "dir": "ltr"
      },
      ...
    }
  }
  ```

* Propriété `transliteration`

  La valeur de la propriété `transliteration` est un dictionnaire composé de paires (clé, valeur). Chaque clé est une balise de langue BCP 47. Une clé identifie une langue pour laquelle du texte peut être converti d’un script à l’autre. La valeur associée à cette clé est un objet JSON avec des propriétés décrivant la langue et ses scripts pris en charge :

  * `name`: nom d’affichage de la langue dans les paramètres régionaux demandés via l’en-tête `Accept-Language`.

  * `nativeName`: nom d’affichage de la langue dans les paramètres régionaux natifs de cette langue.

  * `scripts`: liste de scripts à partir desquels exécuter la conversion. Chaque élément de la liste `scripts` possède des propriétés :

    * `code`: code identifiant le script.

    * `name`: nom d’affichage du script dans les paramètres régionaux demandés via l’en-tête `Accept-Language`.

    * `nativeName`: nom d’affichage de la langue dans les paramètres régionaux natifs de cette langue.

    * `dir`: sens de l’écriture, `rtl` pour les langues qui se lisent de droite à gauche, ou `ltr` pour les langues qui se lisent de gauche à droite.

    * `toScripts`: liste de scripts disponibles en lesquels convertir le texte. Chaque élément de la liste `toScripts` possède des propriétés `code`, `name`, `nativeName` et `dir`, comme décrit précédemment.

  Voici un exemple :

  ```json
  {
    "transliteration": {
      ...
      "ja": {
        "name": "Japanese",
        "nativeName": "日本語",
        "scripts": [
          {
            "code": "Jpan",
            "name": "Japanese",
            "nativeName": "日本語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Latn",
                "name": "Latin",
                "nativeName": "ラテン語",
                "dir": "ltr"
              }
            ]
          },
          {
            "code": "Latn",
            "name": "Latin",
            "nativeName": "ラテン語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Jpan",
                "name": "Japanese",
                "nativeName": "日本語",
                "dir": "ltr"
              }
            ]
          }
        ]
      },
      ...
    }
  }
  ```

* Propriété `dictionary`

  La valeur de la propriété `dictionary` est un dictionnaire composé de paires (clé, valeur). Chaque clé est une balise de langue BCP 47. La clé identifie une langue pour laquelle d’autres traductions et des traductions inverses sont disponibles. La valeur est un objet JSON qui décrit la langue source et les langues cibles avec les traductions disponibles :

  * `name`: nom d’affichage de la langue source dans les paramètres régionaux demandés via l’en-tête `Accept-Language`.

  * `nativeName`: nom d’affichage de la langue dans les paramètres régionaux natifs de cette langue.

  * `dir`: sens de l’écriture, `rtl` pour les langues qui se lisent de droite à gauche, ou `ltr` pour les langues qui se lisent de gauche à droite.

  * `translations`: liste de langues avec des traductions alternatives et des exemples pour la requête exprimée en langue source. Chaque élément de la liste `translations` possède des propriétés :

    * `name`: nom d’affichage de la langue cible dans les paramètres régionaux demandés via l’en-tête `Accept-Language`.

    * `nativeName`: nom d’affichage de la langue cible dans les paramètres régionaux natifs de cette langue.

    * `dir`: sens de l’écriture, `rtl` pour les langues qui se lisent de droite à gauche, ou `ltr` pour les langues qui se lisent de gauche à droite.
    
    * `code`: code de langue identifiant la langue cible.

  Voici un exemple :

  ```json
  "es": {
    "name": "Spanish",
    "nativeName": "Español",
    "dir": "ltr",
    "translations": [
      {
        "name": "English",
        "nativeName": "English",
        "dir": "ltr",
        "code": "en"
      }
    ]
  },
  ```

La structure de l’objet de réponse n’évoluera pas sans changement de la version de l’API. Pour la même version de l’API, la liste des langues disponibles peut évoluer au fil du temps, car Microsoft Translator étoffe en permanence la liste des langues prises en charge par ses services.

La liste des langues prises en charge ne changera pas souvent. Pour économiser de la banque passante et améliorer les temps de réponse, mieux vaut envisager une application cliente qui met en cache les ressources de langue et la balise de l’entité correspondante (`ETag`). Par la suite, l’application cliente peut interroger le service de manière périodique (par exemple, une fois toutes les 24 heures) afin de récupérer le jeu de langues prises en charge le plus récent. Le transfert de la valeur `ETag` actuelle dans un champ d’en-tête `If-None-Match` permet au service d’optimiser la réponse. Si celle-ci n’a pas été modifiée, le service renvoie un code d’état 304 et un corps de réponse vide.

## <a name="response-headers"></a>En-têtes de réponse

<table width="100%">
  <th width="20%">headers</th>
  <th>Description</th>
  <tr>
    <td>ETag</td>
    <td>Valeur actuelle de la balise d’entité pour les groupes de langues prises en charge demandés. Pour effectuer d’autres demandes plus efficaces, le client peut envoyer la valeur `ETag` dans un champ d’en-tête `If-None-Match`.
    </td>
  </tr>
  <tr>
    <td>X-RequestId</td>
    <td>Valeur générée par le service pour identifier la demande. Elle sert à des fins de dépannage.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Codes d’état de réponse

Voici les codes d’état HTTP qu’une demande peut retourner. 

<table width="100%">
  <th width="20%">Code d’état</th>
  <th>Description</th>
  <tr>
    <td>200</td>
    <td>Vous avez réussi !</td>
  </tr>
  <tr>
    <td>304</td>
    <td>La ressource n’a pas été modifiée depuis la version indiquée par les en-têtes de requête `If-None-Match`.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>L’un des paramètres de requête est manquant ou non valide. Corrigez les paramètres de demande avant de réessayer.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Le serveur a rejeté la requête, car le client a dépassé les limites de requête.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Une erreur inattendue s’est produite. Si l’erreur persiste, signalez-la en fournissant les informations suivantes : date et heure de la défaillance, identificateur de la demande dans l’en-tête de réponse,`X-RequestId` et identificateur du client dans l’en-tête de demande `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Serveur temporairement indisponible. relancez la requête. Si l’erreur persiste, signalez-la en fournissant les informations suivantes : date et heure de la défaillance, identificateur de la demande dans l’en-tête de réponse,`X-RequestId` et identificateur du client dans l’en-tête de demande `X-ClientTraceId`.</td>
  </tr>
</table> 

Si une erreur se produit, la requête renvoie également une réponse d'erreur JSON. Le code d’erreur est un nombre à 6 chiffres qui combine le code d’état HTTP à 3 chiffres et un nombre à 3 chiffres qui sert à catégoriser plus précisément l’erreur. Vous trouverez les codes d’erreur les plus courants sur la [page Référence de l’API de traduction de texte Translator Text v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exemples

L’exemple suivant montre comment récupérer les langues prises en charge pour la traduction du texte.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```

---
