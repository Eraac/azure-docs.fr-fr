---
title: Extension de géométries GeoJSON dans Azure Maps | Microsoft Docs
description: Découvrir comment étendre les géométries GeoJSON dans Azure Maps
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: be3c31951c4721a861f9239c5220419dec11b6bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60799137"
---
# <a name="extending-geojson-geometries"></a>Extension de géométries GeoJSON

Azure Maps fournit une liste de puissantes API permettant de rechercher dans/avec des fonctionnalités géographiques.
Ces API sont normalisées en fonction de la [spécification GeoJSON][1] pour représenter les fonctionnalités géographiques (par exemple des limites d’état, des itinéraires).  

La [spécification GeoJSON][1] ne prend en charge que les géométries suivantes :

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

Certaines API Azure Maps (par exemple, [Search Inside Geometry](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) acceptent les géométries comme « Circle », qui ne relèvent pas de la [spécification GeoJSON][1].

Cet article fournit une explication détaillée sur la façon dont Azure Maps étend la [spécification GeoJSON][1] pour représenter certaines géométries.

### <a name="circle"></a>Circle

La géométrie `Circle` n’est pas prise en charge par la [spécification GeoJSON][1]. Nous utilisons l’objet `GeoJSON Feature` pour représenter un cercle.

Une géométrie `Circle` représentée à l’aide de l’objet `GeoJSON Feature` __doit__ contenir les éléments suivants :

1. Center
   >Le centre du cercle est représenté par un type `GeoJSON Point`.

2. Radius
   >Le `radius` du cercle est représenté à l’aide des propriétés de `GeoJSON Feature`. La valeur du rayon est exprimée en _mètres_ et doit être du type `double`.

3. Subtype
   >La géométrie circle doit également contenir la propriété `subType`. Cette propriété doit faire partie des propriétés de la `GeoJSON Feature`, et sa valeur doit être _Circle_.


#### <a name="example"></a>Exemples

Voici comment vous allez représenter un cercle centré au niveau de la latitude 47,639754 et de la longitude -122,126986, avec un rayon égal à 100 mètres, à l’aide d’un objet `GeoJSON Feature` :

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

[1]: https://tools.ietf.org/html/rfc7946
