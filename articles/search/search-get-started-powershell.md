---
title: 'Démarrage rapide PowerShell : Créer, charger et interroger des index à l’aide des API REST Recherche Azure - Recherche Azure'
description: Explique comment créer un index, charger des données et exécuter des requêtes à l’aide de Invoke-RestMethod de PowerShell et de l'API REST Recherche Azure.
ms.date: 06/10/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: afd73ee3461fff11019be887dbf3078963644c5b
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485486"
---
# <a name="quickstart-create-an-azure-search-index-in-powershell-using-rest-apis"></a>Démarrage rapide : Créer un index Recherche Azure dans PowerShell à l’aide des API REST
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-get-started-postman.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-create-index-portal.md)
> 

Cet article vous explique comment créer, charger et interroger un index Recherche Azure à l'aide de PowerShell et des [API REST Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/). Cet article explique comment exécuter des commandes PowerShell de manière interactive. Vous pouvez également exécuter un script terminé. Pour télécharger une copie, accédez au référentiel [azure-search-powershell-samples](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart).

Si vous ne disposez d’aucun abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer, puis [inscrivez-vous auprès de la fonction Recherche Azure](search-create-service-portal.md).

## <a name="prerequisites"></a>Prérequis

Les services et les outils qui suivent sont utilisés dans ce guide de démarrage rapide. 

+ [PowerShell 5.1 ou version ultérieure](https://github.com/PowerShell/PowerShell) avec [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) pour connaître les étapes séquentielles et interactives.

+ [Créez un service Recherche Azure](search-create-service-portal.md) ou [recherchez un service existant](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) dans votre abonnement actuel. Vous pouvez utiliser un service gratuit pour ce guide de démarrage rapide. 

## <a name="get-a-key-and-url"></a>Obtenir une clé et une URL

Les appels REST requièrent l’URL du service et une clé d’accès et ce, sur chaque demande. Un service de recherche est créé avec les deux. Ainsi, si vous avez ajouté votre abonnement à la fonction Recherche Azure, procédez comme suit pour obtenir les informations nécessaires :

1. [Connectez-vous au portail Azure](https://portal.azure.com/), puis dans la page **Vue d’ensemble** du service de recherche, récupérez l’URL. Voici un exemple de point de terminaison : `https://mydemo.search.windows.net`.

2. Dans **Paramètres** > **Clés**, obtenez une clé d’administration pour avoir des droits d’accès complets sur le service. Il existe deux clés d’administration interchangeables, fournies pour assurer la continuité de l’activité au cas où vous deviez en remplacer une. Vous pouvez utiliser la clé primaire ou secondaire sur les demandes d’ajout, de modification et de suppression d’objets.

![Obtenir une clé d’accès et un point de terminaison HTTP](media/search-get-started-postman/get-url-key.png "Obtenir une clé d’accès et un point de terminaison HTTP")

Toutes les demandes nécessitent une clé API sur chaque demande envoyée à votre service. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

## <a name="connect-to-azure-search"></a>Se connecter à la Recherche Azure

1. Dans PowerShell, créez un objet **$headers** pour stocker le type de contenu et la clé API. Remplacez la clé API d’administration (YOUR-ADMIN-API-KEY) par une clé valide pour votre service de recherche. Vous définissez cet en-tête une seule fois pour toute la durée de la session, mais vous l'ajoutez à chaque requête. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Créez un objet **$url** qui spécifie la collection d'index du service. Remplacez le nom du service de recherche (YOUR-SEARCH-SERVICE-NAME) par un service de recherche valide.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06"
    ```

3. Exécutez **Invoke-RestMethod** pour envoyer une requête GET au service et vérifier la connexion. Ajoutez **ConvertTo-Json** afin d'afficher les réponses renvoyées par le service.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Si le service est vide et ne contient aucun index, les résultats se présenteront comme suit. Sinon, vous verrez une représentation JSON des définitions d’index.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 – Créer un index

Sauf si vous utilisez le portail, le service doit contenir un index pour vous permettre de charger des données. Cette étape définit l’index et l’envoie (push) au service. L’[API REST Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) est utilisée pour cette étape.

Un index doit contenir un nom et une collection de champs. La collection de champs définit la structure d'un *document*. Chaque champ a un nom, un type et des attributs qui déterminent la façon dont il est utilisé (par exemple, s’il permet d’effectuer une recherche en texte intégral, et s’il est filtrable ou récupérable dans les résultats de la recherche). Dans un index, l’un des champs de type `Edm.String` doit être désigné comme la *clé* pour l’identité du document.

Cet index est nommé « hotels-quickstart » et contient les définitions de champ que vous voyez ci-dessous. Il s’agit d’un sous-ensemble d’un [index Hotels](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) plus grand utilisé dans d’autres procédures pas à pas. Nous l’avons volontairement tronqué dans ce démarrage rapide par souci de concision.

1. Collez cet exemple dans PowerShell pour créer un objet **$body** contenant le schéma d’index.

    ```powershell
    $body = @"
    {
        "name": "hotels-quickstart",  
        "fields": [
            {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
            {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
            {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
            {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
            {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
            {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Address", "type": "Edm.ComplexType", 
            "fields": [
            {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
            {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
            ]
         }
      ]
    }
    "@
    ```

2. Définissez l’URI vers la collection d’index de votre service et l'index *hotels-quickstart*.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06"
    ```

3. Exécutez la commande avec **$url**, **$headers** et **$body** pour créer l’index sur le service. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Les résultats doivent se présenter comme suit (tronqués au niveau des deux premiers champs par souci de concision) :

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> À des fins de vérification, vous pouvez également consulter la liste des index dans le portail.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – Charger des documents

Pour envoyer (push) des documents, utilisez une requête HTTP POST au point de terminaison de l’URL de votre index. Pour cette tâche, l’API REST est destinée à l’[ajout, la mise à jour ou la suppression de documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

1. Collez cet exemple dans PowerShell pour créer un objet **$body** contenant les documents que vous souhaitez charger. 

    Cette requête comprend deux enregistrements intégraux et un enregistrement partiel. L’enregistrement partiel indique que vous pouvez charger des documents incomplets. Le paramètre `@search.action` spécifie la manière dont l’indexation est effectuée. Les valeurs valides incluent upload, merge, mergeOrUpload et delete. Le comportement mergeOrUpload crée un nouveau document pour hotelId = 3 ou met à jour le contenu, le cas échéant.

    ```powershell
    $body = @"
    {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": 
            {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": 
            {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    "@
    ```

1. Définissez le point de terminaison sur la collection de documents *hotels-quickstart* et incluez l'opération d'indexation (indexes/hotels-quickstart/docs/index).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06"
    ```

1. Exécutez la commande avec **$url**, **$headers** et **$body** pour charger des documents dans l’index hotels-quickstart.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Les résultats doivent ressembler à l’exemple suivant. Vous devez voir un [code d’état de 201](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
        "value":  [
                    {
                        "key":  "1",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "2",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "3",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3 – Rechercher dans un index

Cette étape explique comment interroger un index à l’aide de [l’API Rechercher des documents](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Utilisez des guillemets simples pour la recherche $urls. Les chaînes de requête contiennent des caractères **$** , et vous n'êtes pas tenu de les placer en échappement si l'intégralité de la chaîne est placée entre guillemets simples.

1. Définissez le point de terminaison vers la collection de documents *hotels-quickstart* et ajoutez un paramètre **search** à transmettre dans une chaîne de requête. 
  
   Cette chaîne exécute une recherche vide (recherche=*), renvoyant une liste non classée (résultat de la recherche = 1.0) de documents arbitraires. Par défaut, Recherche Azure renvoie 50 correspondances à la fois. Telle qu’elle est structurée, cette requête renvoie la structure et les valeurs d’un document entier. Ajoutez **$count = true** pour obtenir le nombre de tous les documents dans les résultats.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$count=true'
    ```

1. Exécutez la commande pour envoyer le **$url** au service.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Les résultats se présentent comme suit.

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

Essayez quelques autres exemples de requête pour avoir un aperçu de la syntaxe. Vous pouvez effectuer une recherche de chaîne, textualiser les requêtes $filter, limiter le jeu de résultats, restreindre la recherche à des champs spécifiques, etc.

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up"></a>Nettoyer 

Vous devez supprimer l’index si vous n’en avez plus besoin. Un service gratuit est limité à trois index. Vous souhaiterez peut-être supprimer un index que vous n’utilisez pas activement pour parcourir d’autres didacticiels.

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06'

# Delete the index
Invoke-RestMethod -Uri $url -Headers $headers -Method Delete
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez utilisé PowerShell pour parcourir le flux de travail de base permettant de créer et d'accéder à du contenu dans Recherche Azure. En prenant ces concepts en compte, nous vous recommandons de passer à des scénarios plus avancés, tels que l’indexation à partir de sources de données Azure ;

> [!div class="nextstepaction"]
> [Tutoriel REST : Indexer et rechercher des données semi-structurées (objets blob JSON) dans Recherche Azure](search-semi-structured-data.md)