---
title: Vue d’ensemble du routage de contenu basé sur l’URL de Azure Application Gateway
description: Cette page fournit une présentation du routage de contenu basé sur l’URL Azure Application Gateway, de la configuration de l’élément UrlPathMap et de la règle PathBasedRouting.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 1/8/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 2f0bd0a20b866d342802014f1034a7ed9d939d53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831354"
---
# <a name="url-path-based-routing-overview"></a>Présentation du routage basé sur le chemin d’accès de l’URL

Le routage basé sur le chemin d’accès de l’URL vous permet d’acheminer le trafic vers des pools de serveurs principaux en fonction des chemins d’accès de l’URL de la demande. 

L’un des scénarios consiste à acheminer les demandes pour différents types de contenu vers des pools de serveurs principaux différents.

Dans l’exemple suivant, Application Gateway achemine le trafic pour contoso.com à partir de trois pools de serveurs principaux, par exemple : VideoServerPool, ImageServerPool et DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Les requêtes pour http\://contoso.com/video/* sont routées vers VideoServerPool, et celles pour http\://contoso.com/images/* vers ImageServerPool. DefaultServerPool est sélectionné si aucun des modèles de chemin d’accès ne correspond.

> [!IMPORTANT]
> Les règles sont traitées suivant leur ordre d’affichage dans le portail. Il est vivement recommandé de configurer des écouteurs multisites avant un écouteur de base.  Vous avez ainsi l’assurance que le trafic est acheminé vers le serveur principal approprié. Si un écouteur de base est indiqué en premier et correspond à une demande entrante, elle est traitée par cet écouteur.

## <a name="urlpathmap-configuration-element"></a>Élément de configuration UrlPathMap

L’élément urlPathMap est utilisé pour spécifier les modèles de chemin d’accès aux mappages de pools de serveurs principaux. L’exemple de code suivant est un extrait de l’élément urlPathMap issu du fichier de modèle.

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

### <a name="pathpattern"></a>PathPattern

PathPattern est une liste de modèles de chemin à utiliser pour la correspondance. Chaque modèle doit commencer par le signe « / », et le seul emplacement autorisé pour un astérisque (« * ») est à la fin après un signe « / ». La chaîne transmise à l’outil de correspondance de chemin n’inclut pas de texte après le premier signe « ? » ou « # ». De plus, ces caractères ne sont pas autorisés. Sinon, tous les caractères autorisés dans une URL sont autorisés dans PathPattern.

Les modèles pris en charge varient selon la version d'Application Gateway déployée, à savoir v1 ou v2 :

#### <a name="v1"></a>v1

Les règles de chemin ne respectent pas la casse.

|Modèle de chemin d’accès v1  |Prise en charge ?  |
|---------|---------|
|`/images/*`     |Oui|
|`/images*`     |no|
|`/images/*.jpg`     |no|
|`/*.jpg`     |no|
|`/Repos/*/Comments/*`     |no|
|`/CurrentUser/Comments/*`     |Oui|

#### <a name="v2"></a>v2

Les règles de chemin respectent la casse.

|Modèle de chemin d’accès v2  |Prise en charge ?  |
|---------|---------|
|`/images/*`     |Oui|
|`/images*`     |Oui|
|`/images/*.jpg`     |no|
|`/*.jpg`     |no|
|`/Repos/*/Comments/*`     |no|
|`/CurrentUser/Comments/*`     |Oui|

Pour plus d’informations, vous pouvez consulter un [modèle Resource Manager utilisant le routage basé sur URL](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) .

## <a name="pathbasedrouting-rule"></a>Règle PathBasedRouting

La règle RequestRoutingRule de type PathBasedRouting est utilisée pour lier un écouteur à un élément urlPathMap. Toutes les demandes qui sont reçues par cet écouteur sont acheminées en fonction de la stratégie spécifiée dans l’élément urlPathMap.
Exemple de la règle PathBasedRouting :

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/ urlPathMaps/{urlpathMapName}"
    },

}
    }
]
```

## <a name="next-steps"></a>Étapes suivantes

Après vous être familiarisé avec le routage de contenu basé sur URL, accédez à la section [Créer une passerelle d’application à l’aide du routage basé sur URL](application-gateway-create-url-route-portal.md) pour créer une passerelle d’application avec les règles de routage URL.
