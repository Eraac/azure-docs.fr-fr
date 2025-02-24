---
title: Contrôle de version des Kits de développement logiciel (SDK) clients et serveurs dans Mobile Apps et Mobile Services | Microsoft Docs
description: Liste des Kits de développement logiciel (SDK) clients et des compatibilités avec les versions des Kits de développement logiciel (SDK) serveurs pour Mobile Services et Azure Mobile Apps
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: cfa6a363725c35083b32d6de1dd1371777f91907
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240303"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Contrôle de version client et serveur dans Mobile Apps et Mobile Services
La dernière version d'Azure Mobile Services est la fonctionnalité **Mobile Apps** d'Azure App Service.

Les Kits de développement logiciel (SDK) clients et serveurs Mobile Apps se fondent à l'origine sur ceux de Mobile Services, mais ils ne sont *pas* compatibles les uns avec les autres.
Autrement dit, vous devez utiliser un SDK client *Mobile Apps* avec un SDK serveur *Mobile Apps* et il en est de même pour *Mobile Services*. Ce contrat s'applique au moyen d'une valeur d'en-tête spéciale utilisée par les SDK clients et serveurs, `ZUMO-API-VERSION`.

Remarque : chaque fois que ce document fait référence à un backend *Mobile Services* , il n'est pas nécessaire qu'il soit hébergé sur Mobile Services. Il est désormais possible de faire migrer un service mobile sur App Service sans aucune modification de code, mais dans ce cas le service utilisera toujours les versions du SDK *Mobile Services*.

Pour en savoir plus sur la migration vers App Service sans aucune modification de code, consultez l’article Migrer un service Mobile Services sur Azure App Service.

## <a name="header-specification"></a>Spécification de l’en-tête
La clé `ZUMO-API-VERSION` peut être spécifiée dans l'en-tête HTTP ou dans la chaîne de requête. La valeur est une chaîne de version sous la forme **x.y.z**.

Par exemple :

GET https://service.azurewebsites.net/tables/TodoItem

HEADERS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Désactivation de la vérification de version
Vous pouvez désactiver la vérification de version en définissant la valeur **true** pour le paramètre d’application **MS_SkipVersionCheck**. Spécifiez cette valeur dans votre fichier web.config ou dans la section Paramètres de l’application du portail Azure.

> [!NOTE]
> Il existe un certain nombre de différences de comportement entre Mobile Services et Mobile Apps, en particulier dans les domaines de la synchronisation hors connexion, de l'authentification et des notifications Push. Avant de désactiver la vérification de version, assurez-vous par des tests complets que ces modifications de comportement ne suppriment pas de fonctionnalités dans votre application.

## <a name="2.0.0"></a>Client et serveur Azure Mobile Apps
### <a name="MobileAppsClients"></a> SDK clients Mobile *Apps*
La vérification de version a été introduite à partir des versions suivantes du SDK client pour **Azure Mobile Apps**:

| Plateforme cliente | Version | Valeur d'en-tête de version |
| --- | --- | --- |
| Client géré (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>Kits de développement logiciel (SDK) du serveur Mobile *Apps*
La vérification de version est incluse dans les versions suivantes du SDK serveur :

| Plateforme de serveur | Kit SDK | En-têtes de versions acceptés |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Comportement des serveurs principaux Mobile Apps
| ZUMO-API-VERSION | Valeur de MS_SkipVersionCheck | response |
| --- | --- | --- |
| x.y.z ou Null |True |200 - OK |
| Null |False/Non spécifié |400 - Requête incorrecte |
| 1.x.y |False/Non spécifié |400 - Requête incorrecte |
| 2.0.0-2.x.y |False/Non spécifié |200 - OK |
| 3.0.0-3.x.y |False/Non spécifié |400 - Requête incorrecte |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
