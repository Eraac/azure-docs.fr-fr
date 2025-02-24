---
title: Mappages de champs pour une indexation automatisée à l’aide d’indexeurs - Recherche Azure
description: Configurez les mappages de champs de l’indexeur Recherche Azure pour tenir compte des différences dans les noms de champs et les représentations des données.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 3546e342b535a122ea4ed3f844cd5e28a76d551a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147790"
---
# <a name="field-mappings-and-transformations-using-azure-search-indexers"></a>Mappages de champs et transformations à l’aide d’indexeurs de la fonction Recherche Azure

Lorsque vous utilisez des indexeurs Recherche Azure, il se peut que les données d’entrée ne correspondent pas tout à fait au schéma de votre index cible. Dans ce cas, vous pouvez utiliser des **mappages de champs** pour remodeler vos données pendant le processus d’indexation.

Quelques situations où les mappages de champs sont utiles :

* Votre source de données a un champ appelé `_id`, mais la Recherche Azure n’autorise pas les noms de champs commençant par un trait de soulignement. Un mappage de champ vous permet de renommer un champ.
* Vous souhaitez remplir plusieurs champs de l’index à partir des données de la même source de données. Par exemple, vous souhaiterez peut-être appliquer différents analyseurs à ces champs.
* Vous voulez remplir un champ d’index avec des données provenant de plusieurs sources de données, lesquelles utilisent des noms de champs différents.
* Vous avez besoin d’encoder ou de décoder vos données en Base64. Les mappages de champs prennent en charge plusieurs **fonctions de mappage**, y compris les fonctions d’encodage et de décodage en Base64.

> [!NOTE]
> Le mappage de champ des indexeurs de la Recherche Azure fournit un moyen simple pour mapper des champs de données à des champs d’index, avec quelques options pour la conversion de données. Les données plus complexes devront peut-être être prétraitées pour être converties dans un format facile à indexer.
>
> Microsoft Azure Data Factory est une solution puissante, basée sur le cloud, qui permet d’importer et de transformer des données. Vous pouvez également écrire du code pour transformer les données sources avant l’indexation. Pour obtenir des exemples de code, voir [Modèle de données relationnelles](search-example-adventureworks-modeling.md) et [Modèle de facettes multiniveaux](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Configurer des mappages de champs

Un mappage de champs se compose de trois parties :

1. Un `sourceFieldName`, qui représente un champ de votre source de données. Cette propriété est requise.
2. Un `targetFieldName`facultatif, qui représente un champ de votre index de recherche. Si omis, le nom de la source de données est utilisé.
3. Une `mappingFunction`facultative, qui peut transformer vos données à l'aide d'une des fonctions prédéfinies. La liste complète des fonctions est présentée [ci-dessous](#mappingFunctions).

Les mappages de champs sont ajoutés au tableau `fieldMappings` dans la définition de l’indexeur.

## <a name="map-fields-using-the-rest-api"></a>Mapper des champs avec l’API REST

Vous pouvez ajouter des mappages de champs lors de la création d’un indexeur avec la requête d’API [Créer un indexeur](https://docs.microsoft.com/rest/api/searchservice/create-Indexer). Vous pouvez gérer les mappages de champs d’un indexeur existant avec la requête d’API [Mise à jour d’un indexeur](https://docs.microsoft.com/rest/api/searchservice/update-indexer).

Par exemple, voici comment mapper un champ source à un champ cible avec un nom différent :

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Un champ source peut être référencé dans plusieurs mappages de champs. L’exemple suivant montre comment dupliquer (fork) un champ, en copiant le même champ source dans deux champs d’index différents :

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Search utilise une comparaison qui ne respecte pas la casse pour résoudre les noms de champ et de fonction dans les mappages de champs. C'est pratique (vous n'avez besoin de faire attention à la casse), mais cela signifie que votre source de données et votre index ne peuvent pas comporter des champs qui diffèrent uniquement par la casse.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mapper des champs avec le Kit de développement logiciel (SDK) .NET

Vous définissez des mappages de champs dans le Kit de développement logiciel (SDK) .NET à l’aide de la classe [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping), qui possède les propriétés `SourceFieldName` et `TargetFieldName`, ainsi qu’une référence `MappingFunction` en option.

Vous pouvez spécifier des mappages de champs lors de la construction de l’indexeur, ou ultérieurement, en définissant directement la propriété `Indexer.FieldMappings`.

L’exemple C# suivant définit les mappages de champs lors de la construction d’un indexeur.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Fonctions de mappage de champs

Une fonction de mappage de champ transforme le contenu d’un champ avant son stockage dans l’index. Les fonctions de mappage actuellement prises en charge sont les suivantes :

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>Fonction base64Encode

Exécute l’encodage Base64 *sécurisé pour les URL* de la chaîne d'entrée. Suppose que l'entrée est encodée en UTF-8.

#### <a name="example---document-key-lookup"></a>Exemple de recherche d’une clé de document

Seuls les caractères sécurisés pour les URL peuvent apparaître dans une clé de document Recherche Azure (car les clients doivent pouvoir traiter le document à l’aide de l’[API de recherche](https://docs.microsoft.com/rest/api/searchservice/lookup-document)). Si le champ source de votre clé contient des caractères non sécurisés pour les URL, vous pouvez utiliser la fonction `base64Encode` pour les convertir au moment de l’indexation.

Une fois que vous avez récupéré la clé encodée au moment de la recherche, vous pouvez utiliser la fonction `base64Decode` pour obtenir la valeur de clé d’origine, et l’utiliser pour récupérer le document source.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

Si vous n’incluez aucune propriété de paramètre pour votre fonction de mappage, la valeur par défaut est `{"useHttpServerUtilityUrlTokenEncode" : true}`.

La fonction Recherche Azure prend en charge deux encodages Base64. Vous devez utiliser les mêmes paramètres lors de l’encodage et du décodage du même champ. Pour décider quels paramètres utiliser, consultez les [options relatives à l’encodage base64](#base64details).

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>Fonction base64Decode

Effectue le décodage en Base64 de la chaîne d'entrée. L’entrée est considérée comme une chaîne encodée en Base64 et *sécurisée pour les URL*.

#### <a name="example---decode-blob-metadata-or-urls"></a>Exemple : décodage de métadonnées de blob ou d’URL

Votre source de données peut contenir des chaînes encodées en Base64, telles que des chaînes de métadonnées de blob ou des URL web, que vous voulez rendre disponibles pour des recherches sous la forme de texte brut. Vous pouvez utiliser la fonction `base64Decode` pour transformer les données encodées en chaînes normales lors du remplissage de votre index de recherche.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Si vous n’incluez aucune propriété de paramètre, la valeur par défaut est `{"useHttpServerUtilityUrlTokenEncode" : true}`.

La fonction Recherche Azure prend en charge deux encodages Base64. Vous devez utiliser les mêmes paramètres lors de l’encodage et du décodage du même champ. Pour décider quels paramètres utiliser, reportez-vous aux [options relatives à l’encodage base64](#base64details).

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Options d’encodage en Base64

La fonction Recherche Azure prend en charge deux encodages en Base64 : l’encodage de **jetons d’URL HttpServerUtility**, et l’encodage **Base64 sécurisé pour les URL sans remplissage**. Une chaîne encodée en Base64 lors de l’indexation devra être décodée avec les mêmes options de d’encodage, faute de quoi le résultat ne correspondra pas à la version d’origine.

Si les paramètres `useHttpServerUtilityUrlTokenEncode` ou `useHttpServerUtilityUrlTokenDecode` d’encodage et de décodage, respectivement, sont définis sur `true`, `base64Encode` se comporte comme [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) et `base64Decode` se comporte comme [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Si vous n’utilisez pas le composant .NET Framework complet (par exemple, vous utilisez .NET Core ou une autre infrastructure) pour produire les valeurs de clé afin d’émuler le comportement de Recherche Azure, vous devez définir `useHttpServerUtilityUrlTokenEncode` et `useHttpServerUtilityUrlTokenDecode` sur `false`. Selon la bibliothèque que vous utilisez, les fonctions d’encodage et de décodage en Base64 peuvent différer de celles utilisées par la fonction Recherche Azure.

Le tableau suivant compare les différents encodages base64 de la chaîne `00>00?00`. Pour déterminer quel traitement supplémentaire s’avère nécessaire (le cas échéant) pour vos fonctions base64, appliquez votre fonction d’encodage de bibliothèque à la chaîne `00>00?00` et comparez le résultat obtenu au résultat attendu `MDA-MDA_MDA`.

| Encodage | Résultat d’encodage base64 | Traitement supplémentaire après l’encodage de bibliothèque | Traitement supplémentaire avant l’encodage de bibliothèque |
| --- | --- | --- | --- |
| Base64 avec remplissage | `MDA+MDA/MDA=` | Utiliser des caractères sécurisés pour les URL et supprimer le remplissage | Utiliser des caractères base64 standard et ajouter le remplissage |
| Base64 sans remplissage | `MDA+MDA/MDA` | Utiliser des caractères sécurisés pour les URL | Utiliser des caractères base64 standard |
| Base64 sécurisé pour les URL avec remplissage | `MDA-MDA_MDA=` | Supprimer le remplissage | Ajouter le remplissage |
| Base64 sécurisé pour les URL sans remplissage | `MDA-MDA_MDA` | Aucun | Aucun |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>Fonction extractTokenAtPosition

Divise un champ de chaîne en utilisant le séparateur spécifié et récupère le jeton à la position spécifiée dans le fractionnement résultant.

Cette fonction utilise les paramètres suivants :

* `delimiter`: une chaîne à utiliser comme séparateur lors du fractionnement de la chaîne d'entrée.
* `position`: une position entière à base zéro du jeton à choisir une fois la chaîne d'entrée fractionnée.

Par exemple, si l’entrée est `Jane Doe`, que le `delimiter` est `" "` (espace) et que la `position` est 0, le résultat est `Jane` ; si la `position` est 1, le résultat est `Doe`. Si la position fait référence à un jeton qui n’existe pas, une erreur est retournée.

#### <a name="example---extract-a-name"></a>Exemple : extraction d’un nom

Votre source de données contient un champ `PersonName` et vous souhaitez l’indexer en tant que deux champs `FirstName` et `LastName` distincts. Vous pouvez utiliser cette fonction pour fractionner l'entrée en utilisant l'espace comme séparateur.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>Fonction jsonArrayToStringCollection

Transforme une chaîne formatée en tant que tableau de chaînes JSON en un tableau de chaînes utilisable pour remplir un champ `Collection(Edm.String)` dans l'index.

Par exemple, si la chaîne d’entrée est `["red", "white", "blue"]`, le champ cible de type `Collection(Edm.String)` est rempli avec les valeurs `red`, `white` et `blue`. Pour les valeurs d’entrée qui ne peuvent pas être analysées en tant que tableaux de chaînes JSON, une erreur est retournée.

#### <a name="example---populate-collection-from-relational-data"></a>Exemple : remplissage d’une collection avec des données relationnelles

Microsoft Azure SQL Database n’inclut aucun type de données intégré qui se mappe naturellement aux champs `Collection(Edm.String)` dans Recherche Azure. Pour remplir les champs de la collection de chaînes, vous pouvez prétraiter votre source de données en tant que tableau de chaînes JSON, puis utiliser la fonction de mappage `jsonArrayToStringCollection`.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Pour obtenir un exemple détaillé illustrant la transformation des données relationnelles en champs de collection d’index, voir [Modèle de données relationnelles](search-example-adventureworks-modeling.md).
