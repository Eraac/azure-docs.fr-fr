---
title: Compétence de recherche cognitive Modélisation - Recherche Azure
description: Extrayez des métadonnées et des informations structurées à partir de données non structurées pour les modeler en type complexe dans un pipeline d’enrichissement Recherche Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 058b6c979346d9dcce36940432d0e222e919dba9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540826"
---
#   <a name="shaper-cognitive-skill"></a>Compétence cognitive Modélisation

La compétence **Modélisation** regroupe plusieurs entrées dans un [type complexe](search-howto-complex-data-types.md) qui peut être référencé plus tard dans le pipeline d’enrichissement. La compétence **Modélisation** vous permet essentiellement de créer une structure, de définir le nom des membres de cette structure et d’assigner des valeurs à chaque membre. Parmi les exemples de champs regroupés utiles dans les scénarios de recherche, citons la combinaison d’un nom et d’un prénom dans une seule structure, d’une ville et d’un état dans une seule structure, ou du nom et d’une date de naissance dans une seule structure pour établir une identité unique.

La version d’API détermine la profondeur de modélisation que vous pouvez obtenir. 

| Version de l'API | Comportements de modélisation | 
|-------------|-------------------|
| Version 2019-05-06-Preview de l’API REST (Kit de développement logiciel (SDK) .NET non pris en charge) | Objets complexes, profondeur à plusieurs niveaux, dans une définition de compétence **Modélisation**. |
| 2019-05-06** (mise à la disposition générale), 2017-11-11-Preview| Objets complexes, profondeur à un niveau. Une forme à plusieurs niveaux requiert le chaînage de plusieurs étapes du modélisateur.|

Telle que fournie par `api-version=2019-05-06-Preview`, la compétence **Modélisation** illustrée dans le [scénario 3](#nested-complex-types) ajoute une nouvelle propriété facultative *sourceContext* à l’entrée. Les propriétés *source* et *sourceContext* s’excluent mutuellement. Si l’entrée est dans le contexte de la compétence, utilisez simplement *source*. Si l’entrée est dans un contexte *différent* de celui de la compétence, utilisez *sourceContext*. La propriété *sourceContext* implique que vous définissiez une entrée imbriquée avec l’élément spécifique traité comme source. 

Dans la réponse, pour toutes les versions d’API, le nom de la sortie est toujours « output ». En interne, le pipeline peut mapper un autre nom, comme « analyzedText » comme indiqué dans les exemples ci-dessous, mais la compétence **Modélisation** elle-même retourne « output » dans la réponse. Cet aspect peut être important si vous effectuez un débogage de documents enrichis et notez la différence de nommage, ou si vous générez une compétence personnalisée et que vous structurez la réponse vous-même.

> [!NOTE]
> La compétence **Modélisation** n’est pas liée à une API Cognitive Services et son utilisation ne vous est pas facturée. Toutefois, vous devez toujours [attacher une ressource Cognitive Services](cognitive-search-attach-cognitive-services.md) pour remplacer l’option de ressource **Gratuit** qui vous limite à un petit nombre d’enrichissements quotidiens par jour.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Scénario 1 : types complexes

Considérez un scénario dans lequel vous souhaitez créer une structure appelée *analyzedText* dotée de deux membres : *text* et *sentiment*. Dans un index Recherche Azure, un champ en plusieurs parties pouvant faire l’objet d’une recherche est appelé un *type complexe* et il est souvent créé lorsque les données sources présentent une structure complexe correspondante qui mappe à ce champ.

Toutefois, une autre approche de la création de types complexes consiste à utiliser la compétence **Modélisation**. Lorsque vous incluez cette compétence dans un ensemble de compétences, les opérations en mémoire pendant le traitement de l’ensemble de compétences peuvent sortir des formes de données avec des structures imbriquées, qui peuvent alors être mappées à un type complexe dans votre index. 

L’exemple de définition de compétence suivant fournit les noms de membre comme entrée. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Exemple d’index

Un ensemble de compétences est appelé par un indexeur, et un indexeur nécessite un index. Une représentation de champ complexe dans votre index peut se présenter comme dans l’exemple suivant. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Entrée de la compétence

Un document JSON entrant fournissant des données d’entrée exploitables pour cette compétence **Modélisation** pourrait ressembler à ceci :

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>Sortie de la compétence

La compétence **Modélisation** génère un nouvel élément appelé *analyzedText* avec les éléments combinés *text* et *sentiment*. Cette sortie est conforme au schéma d’index. Elle est importée et indexée dans un index Recherche Azure.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>Scénario 2 : regroupement des entrées

Dans un autre exemple, imaginez qu’à différents stades du traitement du pipeline, vous avez extrait le titre d’un livre et des titres de chapitre sur des pages différentes du livre. Vous pouvez maintenant créer une structure unique composée de ces différentes entrées.

La définition de la compétence **Modélisation** pour ce scénario peut se présenter comme dans l’exemple suivant :

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Sortie de la compétence
Dans ce cas, la compétence **Modélisation** aplatit tous les titres de chapitre en un tableau unique. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Scénario 3 : regroupement des entrées à partir de contextes imbriqués

> [!NOTE]
> Les structures imbriquées prises en charge dans [la version d’API REST 2019-05-06-Preview](search-api-preview.md) peuvent être utilisées dans une [base de connaissances](knowledge-store-concept-intro.md) ou dans un index Recherche Azure.

Imaginons la situation suivante : vous avez le titre, les chapitres et le contenu d’un livre et vous avez été exécuté une reconnaissance d’entité, ainsi que des expressions clés sur le contenu. À présent, vous devez agréger les résultats à partir des différentes compétences dans une seule forme avec le nom de chapitre, les entités et les expressions clés.

La définition de la compétence **Modélisation** pour ce scénario peut se présenter comme dans l’exemple suivant :

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Sortie de la compétence
Dans ce cas, le **modélisateur** crée un type complexe. Cette structure existe en mémoire. Si vous souhaitez l’enregistrer dans une base de connaissances, vous devez créer une projection dans votre ensemble de compétences, définissant les caractéristiques de stockage.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Voir aussi

+ [Compétences prédéfinies](cognitive-search-predefined-skills.md)
+ [Guide pratique pour définir un ensemble de compétences](cognitive-search-defining-skillset.md)
+ [How to use complex types](search-howto-complex-data-types.md) (Comment utiliser les types complexes)
+ [Vue d’ensemble de la base de connaissances](knowledge-store-concept-intro.md)
+ [Prise en main de la Base de connaissances](knowledge-store-howto.md)