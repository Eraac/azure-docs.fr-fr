---
title: Détecter la langue avec l’API REST Analyse de texte | Microsoft Docs
description: Détectez la langue à l’aide de l’API REST Analyse de texte d’Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: e1adeb34cf999f471bb183e4d7de9c65427252bb
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986511"
---
# <a name="example-detect-language-with-text-analytics"></a>Exemple : Détecter la langue avec Analyse de texte

La fonctionnalité [Détection de langue](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) de l’API REST Analyse de texte évalue le texte en entrée pour chaque document et retourne les identificateurs de langue avec un score qui indique la puissance de l’analyse.

Cette capacité est utile pour les magasins de contenu qui collectent du texte arbitraire dont la langue est inconnue. Vous pouvez analyser les résultats de cette analyse pour déterminer la langue utilisée dans le document d’entrée. La réponse retourne également un score qui reflète la confiance du modèle. La valeur du score est comprise entre 0 et 1.

La fonctionnalité Détection de langue peut détecter une grande variété de langues, de variantes, de dialectes, et certaines langues régionales ou de culture. La liste exacte des langues pour cette fonctionnalité n’est pas publiée.

Si vous avez du contenu exprimé dans une langue moins fréquemment utilisée, vous pouvez essayer la fonctionnalité Détection de langue pour voir si elle retourne un code. La réponse pour les langues qui ne peuvent pas être détectées est `unknown`.

> [!TIP]
> Analyse de texte fournit également une image conteneur Docker basée sur Linux pour la détection de la langue. Vous pouvez ainsi [installer et exécuter le conteneur Analyse de texte](text-analytics-how-to-install-containers.md) à proximité de vos données.

## <a name="preparation"></a>Préparation

Vous devez disposer des documents JSON dans ce format : ID et texte.

La taille du document doit être inférieure à 5 120 caractères par document. Vous pouvez avoir jusqu’à 1000 éléments (ID) par collection. La collection est soumise dans le corps de la requête. Voici un exemple de contenu que vous pouvez soumettre pour détection de la langue :

   ```
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },                
            {
                "id": "5",
                "text": "Этот документ на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Étape 1 : Structurer la requête

Pour plus d’informations sur la définition de la requête, consultez [Appeler l’API Analyse de texte](text-analytics-how-to-call-api.md). Les points suivants sont réaffirmés pour des raisons pratiques :

+ Créez une requête POST. Pour passer en revue la documentation de l’API pour cette requête, consultez [API Détection de langue](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

+ Définissez le point de terminaison HTTP pour la détection de la langue. Utilisez une ressource Analyse de texte sur Azure ou un [conteneur Analyse de texte](text-analytics-how-to-install-containers.md) instancié. Il doit inclure la ressource `/languages` : `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`.

+ Définissez un en-tête de demande pour inclure la clé d’accès pour les opérations d’Analyse de texte. Pour plus d’informations, consultez [Rechercher des points de terminaison et des clés d’accès](text-analytics-how-to-access-key.md).

+ Dans le corps de la demande, fournissez la collection de documents JSON que vous avez préparée pour cette analyse.

> [!Tip]
> Utilisez [Postman](text-analytics-how-to-call-api.md) ou ouvrez la **console de test d’API** dans la [documentation](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) pour structurer une requête et la publier dans le service.

## <a name="step-2-post-the-request"></a>Étape 2 : Publier la requête

L’analyse est effectuée à la réception de la requête. Pour connaître la taille et le nombre de requêtes que vous pouvez envoyer par minute et seconde, consultez la section [Limites de données](../overview.md#data-limits) dans la vue d’ensemble.

Rappelez-vous que le service est sans état. Aucune donnée n’est stockée dans votre compte. Les résultats sont retournés immédiatement dans la réponse.


## <a name="step-3-view-the-results"></a>Étape 3 : View the results

Toutes les requêtes POST retournent une réponse au format JSON avec les ID et les propriétés détectées.

La sortie est retournée immédiatement. Vous pouvez diffuser les résultats vers une application qui accepte JSON ou enregistrer la sortie dans un fichier sur le système local. Ensuite, importez la sortie dans une application que vous pouvez utiliser pour trier, rechercher et manipuler les données.

Les résultats de l’exemple de requête doivent ressembler au code JSON suivant. Notez qu’il s’agit d’un document unique contenant plusieurs éléments. La sortie est en anglais. Les identificateurs de langue incluent un nom convivial et un code de langue au format [ISO 639-1](https://www.iso.org/standard/22109.html).

Un score positif de 1,0 exprime le niveau de confiance possible le plus haut de l’analyse.



```
{
    "documents": [
        {
            "id": "1",
            "detectedLanguages": [
                {
                    "name": "English",
                    "iso6391Name": "en",
                    "score": 1
                }
            ]
        },
        {
            "id": "2",
            "detectedLanguages": [
                {
                    "name": "Spanish",
                    "iso6391Name": "es",
                    "score": 1
                }
            ]
        },
        {
            "id": "3",
            "detectedLanguages": [
                {
                    "name": "French",
                    "iso6391Name": "fr",
                    "score": 1
                }
            ]
        },
        {
            "id": "4",
            "detectedLanguages": [
                {
                    "name": "Chinese_Simplified",
                    "iso6391Name": "zh_chs",
                    "score": 1
                }
            ]
        },
        {
            "id": "5",
            "detectedLanguages": [
                {
                    "name": "Russian",
                    "iso6391Name": "ru",
                    "score": 1
                }
            ]
        }
    ],
```

### <a name="ambiguous-content"></a>Contenu ambigu

Si l’analyseur ne parvient pas à analyser l’entrée, il retourne `(Unknown)`. C’est le cas, par exemple, si vous soumettez un bloc de texte composé uniquement de chiffres arabes.

```
    {
      "id": "5",
      "detectedLanguages": [
        {
          "name": "(Unknown)",
          "iso6391Name": "(Unknown)",
          "score": "NaN"
        }
      ]
```
### <a name="mixed-language-content"></a>Contenu en plusieurs langues

Un contenu en plusieurs langues dans un document retourne la langue la plus représentée dans le contenu, mais avec un score positif inférieur. Le score reflète la puissance marginale de cette évaluation. Dans l’exemple suivant, l’entrée est un mélange d’anglais, d’espagnol et de français. L’analyseur compte les caractères dans chaque segment afin de déterminer la langue prédominante.

**Input**

```
{
  "documents": [
    {
      "id": "1",
      "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
    }
  ]
}
```

**Sortie**

Le résultat obtenu se compose de la langue prédominante, avec un score inférieur à 1,0, qui indique un faible niveau de confiance.

```
{
  "documents": [
    {
      "id": "1",
      "detectedLanguages": [
        {
          "name": "Spanish",
          "iso6391Name": "es",
          "score": 0.9375
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="summary"></a>Résumé

Dans cet article, vous avez vu les concepts et le flux de travail de détection de langue à l’aide de l’API Analyse de texte dans Azure Cognitive Services. Les points suivants ont été décrits et illustrés :

+ [Détection de langue](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) est disponible pour une grande variété de langues, de variantes, de dialectes, et certaines langues régionales ou de culture.
+ Les documents JSON figurant dans le corps de la requête incluent un ID et un texte.
+ La requête POST s’effectue sur un point de terminaison `/languages`, à l’aide [d’une clé d’accès et d’un point de terminaison](text-analytics-how-to-access-key.md) personnalisés valides pour votre abonnement.
+ La sortie de réponse se compose d’identificateurs de langue pour chaque ID de document. La sortie peut être diffusée vers n’importe quelle application qui accepte JSON, par exemple Excel et Power BI, pour n’en citer que quelques-unes.

## <a name="see-also"></a>Voir aussi 

 [Vue d’ensemble d’Analyse de texte](../overview.md)  
 [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)</br>
 [Page produit d’Analyse de texte](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyser les sentiments](text-analytics-how-to-sentiment-analysis.md)
