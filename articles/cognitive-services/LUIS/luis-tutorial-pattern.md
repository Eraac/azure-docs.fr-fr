---
title: Modèles
titleSuffix: Azure Cognitive Services
description: Utilisez des modèles pour augmenter la prédiction des intentions et des entités tout en fournissant moins d’exemples d’énoncés. Le modèle est fourni par le biais d’un exemple d’énoncé de modèle, qui inclut la syntaxe pour identifier les entités et le texte pouvant être ignoré.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: e559d4a3366c45bd054cbf3a235805e048de3493
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276047"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats"></a>Didacticiel : Ajouter des formats d’énoncés de modèles courants

Dans ce tutoriel, vous allez utiliser des modèles pour augmenter la prédiction des intentions et des entités tout en fournissant moins d’exemples d’énoncés. Le modèle est fourni par le biais d’un exemple d’énoncé de modèle, qui inclut la syntaxe pour identifier les entités et le texte pouvant être ignoré. Un modèle est une combinaison de correspondance par expression et de machine learning.  Les exemples de modèles d’énoncés, ainsi que les énoncés de l’intention, permettent à LUIS de mieux comprendre quels énoncés correspondent à l’intention. 

**Dans ce tutoriel, vous allez découvrir comment :**

> [!div class="checklist"]
> * Importer l’exemple d’application 
> * Créer une intention
> * Former
> * Publish
> * Obtenir les intentions et les entités à partir du point de terminaison
> * Créer un modèle
> * Vérifier l’amélioration des prédictions de modèle
> * Marquer le texte comme pouvant être ignoré et imbriquer dans le modèle
> * Utiliser le panneau de test pour vérifier la réussite du modèle

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importer un exemple d’application

Continuez avec l’application créée dans le dernier tutoriel, nommée **HumanResources**. 

Procédez comme suit :

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json).

2. Importez le code JSON dans une nouvelle application.

3. À partir de la section **Manage (Gérer)** , sous l’onglet **Versions**, clonez la version et nommez-la `patterns`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL.

## <a name="create-new-intents-and-their-utterances"></a>Créer de nouvelles intentions et leurs énoncés

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Dans la page **Intents** (Intentions), sélectionnez **Create new intent** (Créer une intention). 

3. Entrez `OrgChart-Manager` dans la boîte de dialogue contextuelle, puis sélectionnez **Terminé**.

    ![Créer une fenêtre contextuelle de message](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. Ajoutez des exemples d’énoncés à l’intention.

    |Exemples d’énoncés|
    |--|
    |De qui John W. Smith est-il le subordonné ?|
    |À qui John W. Smith rend-il compte ?|
    |Qui est le responsable de John W. Smith ?|
    |À qui Jill Jones rend-il compte directement ?|
    |Qui est le superviseur de Jill Jones ?|

    [![Capture d’écran de LUIS ajoutant de nouveaux énoncés à l’intention](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Capture d’écran de LUIS ajoutant de nouveaux énoncés à l’intention")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    Ne vous inquiétez pas si l’entité keyPhrase est étiquetée dans les énoncés de l’intention au lieu de l’entité Employee. Les deux sont correctement prédites dans le volet de test et au point de terminaison. 

5. Sélectionnez **Intentions** dans le volet de navigation gauche.

6. Sélectionnez **Créer une intention**. 

7. Entrez `OrgChart-Reports` dans la boîte de dialogue contextuelle, puis sélectionnez **Terminé**.

8. Ajoutez des exemples d’énoncés à l’intention.

    |Exemples d’énoncés|
    |--|
    |Qui sont les subordonnés de John W. Smith ?|
    |Qui rend compte à John W. Smith ?|
    |De qui John W. Smith est-il responsable ?|
    |Qui sont les collaborateurs directs de Jill Jones ?|
    |De qui Jill Jones est-il le superviseur ?|

## <a name="caution-about-example-utterance-quantity"></a>Attention à la quantité des exemples d’énoncés

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

## <a name="train"></a>Former

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publish

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obtenir l’intention et les entités à partir du point de terminaison

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Who is the boss of Jill Jones?`. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. 

    ```json
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.353984952
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.353984952
            },
            {
                "intent": "OrgChart-Reports",
                "score": 0.214128986
            },
            {
                "intent": "EmployeeFeedback",
                "score": 0.08434003
            },
            {
                "intent": "MoveEmployee",
                "score": 0.019131
            },
            {
                "intent": "GetJobInformation",
                "score": 0.004819009
            },
            {
                "intent": "Utilities.Confirm",
                "score": 0.0043958663
            },
            {
                "intent": "Utilities.StartOver",
                "score": 0.00312064588
            },
            {
                "intent": "Utilities.Cancel",
                "score": 0.002265454
            },
            {
                "intent": "Utilities.Help",
                "score": 0.00133465114
            },
            {
                "intent": "None",
                "score": 0.0011388344
            },
            {
                "intent": "Utilities.Stop",
                "score": 0.00111166481
            },
            {
                "intent": "FindForm",
                "score": 0.0008900076
            },
            {
                "intent": "ApplyForJob",
                "score": 0.0007836131
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                }
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Cette requête n’a réussi ? Pour ce cycle de formation, elle a réussi. Les scores des deux intentions principales sont proches. Étant donné que la formation de LUIS n’est pas exactement la même chaque fois, il y a une petite variation. Ces deux scores pourraient s’inverser lors du prochain cycle de formation. Il en résulte que l’intention erronée pourrait être retournée. 

Utiliser des modèles pour augmenter significativement le score de l’intention en pourcentage et l’éloigner du score le plus élevé suivant. 

Laissez cette deuxième fenêtre de navigation s’ouvrir. Vous utiliserez cette valeur plus loin dans le didacticiel. 

## <a name="template-utterances"></a>Modèles d’énoncés
En raison de la nature du domaine des ressources humaines, il existe plusieurs moyens courants de poser des questions sur les relations des employés dans les organisations. Par exemple :

|Énoncés|
|--|
|À qui Jill Jones rend-il compte ?|
|Qui rend compte à Jill Jones ?|

Ces énoncés sont trop proches pour permettre de déterminer l’unicité contextuelle de chacun sans fournir trop d’exemples d’énoncé. Si l’on ajoute un modèle pour une intention, LUIS apprend les modèles d’énoncés courants de cette intention avec peu d’exemples d’énoncés. 

Quelques exemples de modèles d’énoncés pour cette intention :

|Exemples de modèles d’énoncés|signification de la syntaxe|
|--|--|
|À qui {Employee} rend-il compte[?]|{employé} interchangeable, ignorer [?]}|
|Qui rend compte à {Employee}[?]|{employé} interchangeable, ignorer [?]}|

La syntaxe `{Employee}` marque le type et l’emplacement de l’entité dans l’énoncé de modèle. La syntaxe facultative, `[?]`, marque les mots ou la ponctuation facultatifs. LUIS établit une correspondance avec l’énoncé et ignore le texte facultatif à l’intérieur des crochets.

Bien que la syntaxe ressemble à des expressions régulières, il ne s’agit pas d’expressions régulières. Seule la syntaxe d’accolade, `{}`, et de crochet, `[]`, est prise en charge. Ils peuvent être imbriqués jusqu’à deux niveaux.

Pour qu’un modèle corresponde à un énoncé, les entités au sein de l’énoncé doivent d’abord correspondre aux entités du modèle d’énoncé. Cependant, le modèle contribue uniquement à la prédicition d’intentions, mais pas d’entités. 

**Même si les modèles vous permettent de fournir moins d’exemples d’énoncés, si les entités ne sont pas détectées, le modèle ne correspond pas.**

## <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Ajouter les modèles pour l’intention OrgChart-Manager

1. Sélectionnez **Build** dans le menu supérieur.

2. Dans le volet de navigation gauche, sous **Améliorer les performances de l’application**, sélectionnez **Modèles**.

3. Sélectionnez l’intention **OrgChart-Manager**, puis entrez les modèles d’énoncés suivants :

    |Modèles d’énoncés|
    |:--|
    |De qui {Employee} est-il le subordonné[?]|
    |À qui {Employee} rend-il compte[?]|
    |Qui est le responsable de {Employee}[?]|
    |À qui {Employee} rend-il directement compte[?]|
    |Qui est le superviseur de {Employee}[?]|
    |Qui est le patron de {Employee}[?]|

    Les entités avec des rôles utilisent une syntaxe comprenant le nom de rôle, et sont traitées dans un [tutoriel séparé pour les rôles](luis-tutorial-pattern-roles.md). 

    Si vous tapez le modèle d’énoncé, LUIS vous aide à remplir l’entité lorsque vous entrez l’accolade gauche, `{`.

    [![Capture d’écran de la saisie d’énoncés de modèle pour une intention](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. Toujours sur la page Modèles, sélectionnez l’intention **OrgChart-Reports**, puis entrez les modèles d’énoncés suivants :

    |Modèles d’énoncés|
    |:--|
    |Qui sont les subordonnés de {Employee}[?]|
    |Qui rend compte à {Employee}[?]|
    |De qui {Employee} est-il responsable[?]|
    |Qui sont les collaborateurs directs de{Employee}[?]|
    |Qui supervise {Employee}[?]|
    |Qui est le chef de {Employee}[?]|

## <a name="query-endpoint-when-patterns-are-used"></a>Point de terminaison de requête lorsque des modèles sont utilisés

Maintenant que les modèles sont ajoutés à l’application, formez, publiez et interrogez l’application sur le point de terminaison du runtime de prédiction.

1. Former et publier à nouveau l’application.

1. Rebasculez les onglets de navigateur vers l’onglet URL de point de terminaison.

1. Accédez à la fin de l’URL dans la barre d’adresses, puis entrez `Who is the boss of Jill Jones?` en tant qu’énoncé. Le dernier paramètre de la chaîne de requête est `q`, l’énoncé est **query**. 

    ```json
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.9999989
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.9999989
            },
            {
                "intent": "OrgChart-Reports",
                "score": 7.616303E-05
            },
            {
                "intent": "EmployeeFeedback",
                "score": 7.84204349E-06
            },
            {
                "intent": "GetJobInformation",
                "score": 1.20674213E-06
            },
            {
                "intent": "MoveEmployee",
                "score": 7.91245157E-07
            },
            {
                "intent": "None",
                "score": 3.875E-09
            },
            {
                "intent": "Utilities.StartOver",
                "score": 1.49E-09
            },
            {
                "intent": "Utilities.Confirm",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Help",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Stop",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Cancel",
                "score": 1.225E-09
            },
            {
                "intent": "FindForm",
                "score": 1.123077E-09
            },
            {
                "intent": "ApplyForJob",
                "score": 5.625E-10
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                },
                "role": ""
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

La prédiction de l’intention est maintenant beaucoup plus élevée.

## <a name="working-with-optional-text-and-prebuilt-entities"></a>Utilisation d’un texte facultatif et d’entités prédéfinies

Les énoncés de modèle précédents dans ce didacticiel possédaient quelques exemples de texte facultatifs tels que l’utilisation possessive de la lettre s, `'s`, et l’utilisation du point d’interrogation, `?`. Supposons que vous devez tenir compte des dates actuelles et futures dans l’énoncé.

Les exemples d’énoncés sont :

|Intention|Exemples d’énoncés avec un texte facultatif et des entités prédéfinies|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Chacun de ces exemples utilise une conjugaison du verbe, `was`, `is`, `will be`, ainsi qu’une date, `March 3`, `now`, et `in a month`, que requiert LUIS pour effectuer correctement ses prédictions. Notez que les deux derniers exemples utilisent presque le même texte à l’exception de `in` et `on`.

Exemples d’énoncés de modèle pour ces informations facultatives : 

|Intention|Exemples d’énoncés avec un texte facultatif et des entités prédéfinies|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


L’utilisation de la syntaxe facultative des crochets, `[]`, facilite l’ajout d’un énoncé de modèle à ce texte facultatif, ce dernier peut se voir imbriqué à un deuxième niveau, `[[]]`, et peut inclure des entités ou du texte.


**Question : Pourquoi toutes les lettres `w`, la première lettre de chaque énoncé de modèle, sont en minuscules ? Le fait qu’elles soient en majuscules ou en minuscules n’est-il pas facultatif ?** L’énoncé soumis au point de terminaison de requête par l’application cliente, est converti en minuscules. L’énoncé de modèle peut être en majuscules ou minuscules, tout comme l’énoncé du point de terminaison. La comparaison est toujours effectuée après la conversion en minuscules.

**Question : Pourquoi le nombre prédéfini ne fait pas partie de l’énoncé de modèle alors que le 3 mars est prédit à la fois comme un nombre `3` et une date `March 3` ?** L’énoncé de modèle utilise une date en fonction du contexte, soit littéralement comme dans `March 3`, ou de façon abstraite comme dans `in a month`. Une date peut contenir un nombre mais un nombre ne peut pas nécessairement être considéré comme une date. Utilisez toujours l’entité qui représente au mieux le type à retourner dans les résultats JSON de prédiction.  

**Question : Qu’en est-il des énoncés incompréhensibles comme `Who will {Employee}['s] manager be on March 3?`.** Les conjugaisons grammaticalement différentes, comme dans le cas où le `will` et le `be` sont séparés, doivent constituer un nouvel énoncé de modèle. L’énoncé de modèle existant ne correspond pas. Bien que l’intention de l’énoncé n’ait pas changé, le placement du mot dans l’énoncé a changé. Cette modification affecte la prédiction dans LUIS. Vous pouvez [regrouper et/ou](#use-the-or-operator-and-groups) les temps des verbes pour combiner ces énoncés. 

**N’oubliez pas : les entités sont les premières trouvées, le modèle étant ensuite mis en correspondance.**

## <a name="edit-the-existing-pattern-template-utterance"></a>Modifier l’énoncé de modèle existant

1. Sur le site web de LUIS, sélectionnez **Générer** dans le menu supérieur, puis sélectionnez **Modèles** dans le menu de gauche. 

1. Recherchez l’énoncé de modèle existant, `Who is {Employee}['s] manager[?]`, puis sélectionnez les points de suspension (***...***) à droite, puis sélectionnez **Modifier** dans le menu contextuel. 

1. Modifiez l’énoncé de modèle en : `who is {Employee}['s] manager [[on]{datetimeV2}?]`

## <a name="add-new-pattern-template-utterances"></a>Ajouter de nouveaux énoncés de modèle

1. Alors que vous êtes toujours dans la section **Modèles** de**Générer**, ajoutez plusieurs nouveaux énoncés de modèle. Sélectionnez **OrgChart-Manager** dans le menu déroulant d’intentions et entrez chacun des énoncés de modèle suivants :

    |Intention|Exemples d’énoncés avec un texte facultatif et des entités prédéfinies|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Effectuez l’apprentissage de l’application.

3. Sélectionnez **Test** en haut du panneau pour ouvrir le panneau de test. 

4. Entrez plusieurs énoncés de test pour vérifier que le modèle est mis en correspondance et que le score de l’intention est très élevé. 

    Après avoir entré le premier énoncé, sélectionnez **Inspecter** sous le résultat, afin que vous puissiez voir tous les résultats de prédiction. Chaque énoncé doit avoir l’intention **OrgChart-Manager** et doit extraire les valeurs pour les entités d’Employee et datetimeV2.

    |Énoncé|
    |--|
    |Qui sera le responsable de Jill Jones|
    |qui sera le responsable de Jill Jones|
    |Qui sera le responsable de Jill Jones ?|
    |qui sera le responsable de Jill Jones le 3 mars|
    |Qui sera le responsable de Jill Jones le mois prochain|
    |Qui sera le responsable de Jill Jones dans un mois ?|

Tous ces énoncés ont trouvé les entités à l’intérieur, par conséquent, ils correspondent au même modèle et ont un score de prédiction élevé.

## <a name="use-the-or-operator-and-groups"></a>Utiliser l’opérateur OR et les groupes

Plusieurs des énoncés de modèle précédents sont très proches. Utilisez la syntaxe **group** `()` et **OR** `|` afin de réduire les énoncés de modèle. 

Les 2 modèles suivants peuvent être combinés dans un modèle unique à l’aide de la syntaxe group `()` et OR `|`.

|Intention|Exemples d’énoncés avec un texte facultatif et des entités prédéfinies|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

Le nouvel énoncé de modèle sera : 

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`. 

Cet exemple utilise un **groupe** autour de la conjugaison du verbe requis `in` et `on` facultatifs avec un canal **ou** entre les deux. 

1. Sur la page **Modèles**, sélectionnez le filtre **OrgChart-Manager**. Limitez la liste en recherchant `manager`. 

    ![Recherchez dans les modèles d’intention OrgChart-Manager le terme « manager »](./media/luis-tutorial-pattern/search-patterns.png)

1. Conservez une version de l’énoncé de modèle (à modifier dans l’étape suivante) et supprimez les autres variations. 

1. Modifiez l’énoncé de modèle en : 

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

1. Effectuez l’apprentissage de l’application.

1. Utilisez le volet de test pour tester les versions de l’énoncé :

    |Énoncés à entrer dans le volet de test|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|    
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|


## <a name="use-the-utterance-beginning-and-ending-anchors"></a>Utilisez les ancres de début et de fin de l’énoncé

La syntaxe du modèle fournit la syntaxe d’ancre de début et de fin d’énoncé, à savoir le signe insertion, `^`. Les ancres de début et de fin d’énoncé peuvent être utilisées ensemble pour cibler des énoncé très spécifiques et éventuellement littéraux ou utilisées séparément pour cibler des intentions. 

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel ajoute deux intentions pour des énoncés qui étaient difficiles à prédire avec une précision élevée sans avoir de nombreux exemples d’énoncés. L’ajout de modèles pour ces intentions a permis à LUIS de mieux prédire l’intention avec un score sensiblement plus élevé. Le marquage des entités et du texte pouvant être ignoré a permis à LUIS d’appliquer le modèle à une plus grande variété d’énoncés.

> [!div class="nextstepaction"]
> [En savoir plus sur l’utilisation de rôles avec un modèle](luis-tutorial-pattern-roles.md)
