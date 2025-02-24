---
title: Limitation de requêtes avancée avec Gestion des API Azure
description: Découvrez comment créer et appliquer des stratégies de limitation de fréquence et de quota souples avec Gestion des API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 22c3987121e2ab3479274c89c359c679f5f1135e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61087121"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Limitation de requêtes avancée avec Gestion des API Azure
La possibilité de limiter les requêtes entrantes est un rôle clé du service Gestion des API Azure. En contrôlant la fréquence des requêtes ou le nombre total de requêtes/données transférées, Gestion des API permet aux fournisseurs d’API de protéger leurs API contre les abus et de créer de la valeur pour différents niveaux de produits API.

## <a name="product-based-throttling"></a>Limitation basée sur le produit
À ce jour, les fonctionnalités de limitation de fréquence sont limitées afin de porter sur un abonnement produit spécifique, défini dans le portail Azure. Cela permet aux fournisseurs d’API d’appliquer des limites aux développeurs qui ont souscrit pour utiliser leurs API. Toutefois, cela ne permet pas, par exemple, de limiter les utilisateurs finaux des API. Il est possible pour un seul utilisateur de l'application du développeur de consommer le quota entier et d’empêcher d’autres clients du développeur d'être en mesure d'utiliser l'application. De la même façon, plusieurs clients générant un volume élevé de requêtes peuvent limiter l'accès aux utilisateurs occasionnels.

## <a name="custom-key-based-throttling"></a>Limitation basée sur une clé personnalisée
Les nouvelles stratégies [rate-limit-by-key](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) et [quota-by-key](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) fournissent une solution plus souple pour le contrôle du trafic. Ces nouvelles stratégies vous permettent de définir des expressions pour identifier les clés qui servent à effectuer le suivi de l’utilisation du trafic. Il est plus facile d’en comprendre le fonctionnement avec un exemple. 

## <a name="ip-address-throttling"></a>Limitation par adresse IP
Les stratégies suivantes limitent l’adresse IP d’un client à 10 appels par minute, avec un total d’un million d’appels et 10 000 Ko de bande passante par mois. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Si tous les clients sur Internet utilisent une adresse IP unique, cela peut être un moyen efficace de limiter l'utilisation par utilisateur. Toutefois, il est probable que plusieurs utilisateurs partagent une même adresse IP publique, parce qu’ils accèdent à Internet via un périphérique NAT. En dépit de cela, le `IpAddress` peut être la meilleure option pour les API qui autorisent l’accès non authentifié.

## <a name="user-identity-throttling"></a>Limitation par identité d'utilisateur
Si un utilisateur final est authentifié, une clé de limitation de la clé peut être générée en fonction d’informations qui identifient cet utilisateur de façon unique.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Cet exemple montre l’en-tête d’autorisation, pour le convertir en objet `JWT` et utiliser le sujet du jeton pour identifier l’utilisateur et l’utiliser comme la clé de limitation du débit. Si l’identité de l’utilisateur est stockée dans le `JWT` comme l’une des autres revendications, cette valeur peut être utilisée à la place.

## <a name="combined-policies"></a>Stratégies combinées
Bien que les nouvelles stratégies de limitation offrent davantage de contrôle que les stratégies de limitation existantes, il est toujours utile de combiner les deux fonctions. La limitation par clé d’abonnement produit ([Limiter la fréquence des appels par abonnement](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) et [Définir le quota d’utilisation par abonnement](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)) constitue un excellent moyen de permettre la monétisation d’une API en facturant en fonction des niveaux d’utilisation. Le contrôle plus fin sur la limitation de la bande passante par utilisateur est gratuite et empêche que le comportement de certains utilisateurs dégrade l'expérience des autres. 

## <a name="client-driven-throttling"></a>Limitation par client
Lorsque la clé de limitation est définie en utilisant une [expression de stratégie](/azure/api-management/api-management-policy-expressions), le fournisseur d'API est celui qui choisit comment définir la limitation. Toutefois, un développeur peut souhaiter contrôler la limitation de débit de leurs propres clients. Cela peut être possible si le fournisseur de l'API introduit un en-tête personnalisé afin de permettre à l'application client du développeur de communiquer la clé à l'API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Cela permet à l'application client du développeur de laisser le choix de la création de la clé de limitation de la fréquence. Un développeur client peut créer ses propres niveaux de fréquence en allouant des jeux de clés aux utilisateurs et en effectuant la rotation de l’utilisation des clés.

## <a name="summary"></a>Résumé
Gestion des API Azure permet la limitation du débit et du devis pour à la fois protéger et valoriser votre service API. Les nouvelles stratégies de limitation avec les règles de portée personnalisées vous permettent un contrôle plus fin sur les stratégies afin de permettre à vos clients de créer de meilleures applications. Les exemples de cet article illustrent l'utilisation de ces nouvelles stratégies avec la création de clés de limitation appliquées aux adresses IP clientes, à l’identité de l'utilisateur et les valeurs générées par le client. Toutefois, il existe de nombreuses autres parties du message qui peuvent être utilisées telles que l’agent utilisateur, les fragments de chemin d'URL, ou la taille des messages.

## <a name="next-steps"></a>Étapes suivantes
Faites-nous part de vos commentaires dans le thread Disqus de cette rubrique. Il serait intéressant d’en savoir davantage sur les autres valeurs de clé potentielles qui se sont avérées être un choix judicieux dans vos scénarios.

