---
title: Création d’un index Azure Search dans le portail Azure - Recherche Azure
description: Découvrez comment créer un index pour Recherche Azure à l’aide des concepteurs d’index intégrés du portail.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6a2bac71c37cc750eb24e3492ecdcdf0b2333cce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60817320"
---
# <a name="create-an-azure-search-index-in-the-portal"></a>Créer un index de Recherche Azure dans le portail

Recherche Azure inclut un concepteur d’index intégré dans le portail, très utile pour des prototypes ou pour créer un [index de recherche](search-what-is-an-index.md) hébergé sur votre service Recherche Azure. L’outil est utilisé pour la construction de schéma. Lorsque vous enregistrez la définition, un index vide devient entièrement exprimé dans Recherche Azure. Le choix de la manière de le charger à l’aide de données interrogeables vous appartient.

Le concepteur d’index est une approche parmi d’autres pour la création d’un index. Vous pouvez également créer un index par programmation à l’aide des API [.NET](search-create-index-dotnet.md) ou [REST](search-create-index-rest-api.md).

## <a name="start-index-designer"></a>Démarrer le concepteur d’index

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez le tableau de bord du service. Vous pouvez cliquer sur **Tous les services** dans la barre d’index pour afficher les « services de recherche » existants dans l’abonnement actuel. 

2. Cliquez sur le lien **Ajouter un index** de la barre de commandes en haut de la page.

   ![Lien Ajouter un index dans la barre de commandes](media/search-create-index-portal/add-index.png "Lien Ajouter un index dans la barre de commandes")

3. Nommez votre index Azure Search. Les noms d’index sont référencés dans les opérations d’indexation et de requête. Le nom de l’index est ensuite intégré dans l’URL de point de terminaison utilisé pour les connexions à l’index, et il est utilisé pour l’envoi de requêtes HTTP dans l’API REST Azure Search.

   * Le nom doit commencer par une lettre.
   * N’utilisez que des lettres minuscules, chiffres ou tirets (« - »).
   * Le nom ne doit pas dépasser 60 caractères.

## <a name="add-fields"></a>Ajouter des champs

La composition de l’index inclut une *collection de champs* qui définit les données qu’il est possible de rechercher dans votre index. Globalement, la collection de champs spécifie la structure des documents que vous chargez séparément. Une collection de champs inclut des champs obligatoires et facultatifs, nommés et typés, avec des attributs d’index déterminant la façon dont le champ peut être utilisé.

1. Ajoutez des champs pour fournir des spécifications complètes sur les documents à charger, en définissant un [type de données](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) pour chacun d’eux. Par exemple, si les documents comportent un *id*, un *nom d’hôtel*, une *adresse*, une *ville* et une *région*, créez un champ correspondant pour chacun de ces éléments dans l’index. Passez en revue les [conseils de conception de la section ci-dessous](#design) pour vous aider à définir les attributs.

2. Spécifiez un champ *clé* de type Edm.String. Les valeurs de ce champ doivent identifier chaque document de façon unique. Par défaut, le champ est nommé *id*, mais vous pouvez le renommer tant que la chaîne est conforme aux [règles d’affectation de noms](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Par exemple, si votre collection de champs comprend *hotel-id*, vous pouvez choisir cela pour votre clé. Chaque index Azure Search doit obligatoirement comporter un champ clé de type chaîne.

3. Définissez des attributs sur chaque champ. Le concepteur d’index exclut tous les attributs qui sont invalides pour le type de données, mais ne suggère pas ce qu’il faut inclure. Passez en revue les directives de la section suivante pour comprendre le rôle des attributs.

    La documentation de l’API Azure Search inclut des exemples de code utilisant un index *hotels* simple. Dans la capture d’écran ci-dessous, vous pouvez voir la définition de l’index, qui inclut l’analyseur de langue française spécifié lors de la définition de l’index, que vous pouvez recréer comme vous entraîner dans le portail.

    ![Index de la démonstration Hotels](media/search-create-index-portal/field-definitions.png "Index de la démonstration Hotels")

4. Lorsque vous avez terminé, cliquez sur **Créer** pour enregistrer et créer l’index.

<a name="design"></a>

## <a name="set-attributes"></a>Définir des attributs

Même si vous pouvez ajouter de nouveaux champs à tout moment, les définitions de champ existantes sont verrouillées pour toute la durée de vie de l’index. C’est pourquoi les développeurs utilisent généralement le portail pour créer des index simples, tester des idées ou rechercher une définition de paramètre. Il est plus efficace d’effectuer des itérations fréquentes sur la conception d’un index si vous suivez une approche basée sur du code pour reconstruire l’index facilement.

Des analyseurs et générateurs de suggestions sont associés aux champs avant l’enregistrement de l’index. Veillez à ajouter des analyseurs de langage ou des générateurs de suggestions à la définition de votre index lorsque vous le créez.

Les champs de type chaîne sont souvent marqués avec les attributs **Possibilité de recherche** et **Récupérable**. Vous pouvez utiliser les champs **Triable**, **Filtrable** et **À choix multiples** pour affiner les résultats de recherche.

Les attributs d’un champ déterminent son utilisation, par exemple s’il est utilisé dans la recherche en texte intégral, la navigation par facettes, les opérations de tri et ainsi de suite. Le tableau suivant décrit chaque attribut.

|Attribut|Description|  
|---------------|-----------------|  
|**searchable**|Recherche en texte intégral, avec analyse lexicale (césure de mots) lors de l’indexation. Si vous définissez un champ avec possibilité de recherche sur une valeur comme « journée ensoleillée », cette valeur est fractionnée au niveau interne en jetons individuels « journée » et « ensoleillée ». Pour en savoir plus, consultez la rubrique [Fonctionnement de la recherche en texte intégral](search-lucene-query-architecture.md).|  
|**filterable**|Référencé dans les requêtes **$filter**. Les champs filtrables de type `Edm.String` ou `Collection(Edm.String)` ne font pas l’objet d’une analyse lexicale, les comparaisons ne concernent donc que les correspondances exactes. Par exemple, si vous définissez un champ avec la valeur « journée ensoleillée », la requête `$filter=f eq 'sunny'` ne renverra aucune correspondance, contrairement à `$filter=f eq 'sunny day'`. |  
|**sortable**|Le système trie les résultats par score par défaut, mais vous pouvez configurer le tri en fonction des champs des documents. Les champs de type `Collection(Edm.String)` ne sont pas **triables**. |  
|**facetable**|Généralement utilisé dans une présentation des résultats de recherche qui inclut un nombre de correspondances par catégorie (par exemple, les hôtels dans une ville spécifique). Cette option ne peut pas être utilisée avec des champs de type `Edm.GeographyPoint`. Les champs de type `Edm.String` qui sont **filtrables**, **triables** ou **à choix multiples** ne peuvent pas dépasser 32 Ko de longueur. Pour plus d’informations, consultez l’article [Créer un index (API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index).|  
|**key**|Identificateur unique des documents dans l’index. Un seul champ doit être choisi comme champ clé et il doit être de type `Edm.String`.|  
|**retrievable**|Définit si le champ peut être retourné dans un résultat de recherche. Cet attribut est utile quand vous voulez utiliser un champ (comme *profit margin*) comme mécanisme de filtre, de tri ou de score, mais que vous ne voulez pas qu’il soit visible par l’utilisateur final. Il doit être `true` for `key` .|  

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé un index Azure Search, vous pouvez passer à l’étape suivante : [charger des données interrogeables dans l’index](search-what-is-data-import.md).

Sinon, vous pouvez étudier les [index de manière plus approfondie](search-what-is-an-index.md). Outre la collection de champs, un index spécifie également des analyseurs, des générateurs de suggestions, des profils de score et des paramètres CORS. Le portail comprend des onglets permettant de définir les éléments les plus courants : les champs, les analyseurs et les générateurs de suggestions. Pour créer ou modifier d’autres éléments, vous pouvez utiliser l’API REST ou le kit de développement logiciel (SDK) .NET.

## <a name="see-also"></a>Voir aussi

 [Fonctionnement de la recherche en texte intégral](search-lucene-query-architecture.md)  
 [API REST du service de recherche](https://docs.microsoft.com/rest/api/searchservice/)[SDK .NET](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

