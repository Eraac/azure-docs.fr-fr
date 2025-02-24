---
title: CLI Azure Service Fabric - sfctl mesh code-package-log | Microsoft Docs
description: Décrit les commandes sfctl mesh code-package-log de l’interface de ligne de commande CLI Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: e7bc8491071946eaa2e322517e5d36d681a49130
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837638"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Obtenez les journaux d’activité concernant le conteneur du package de code spécifié pour le réplica de service donné.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| get | Obtient les journaux d’activité à partir du conteneur. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
Obtient les journaux d’activité à partir du conteneur.

Obtenez les journaux d’activité concernant le conteneur du package de code spécifié du réplica de service.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --app-name --application-name [obligatoire] | Le nom de l’application. |
| --code-package-name           [obligatoire] | Nom du package de code du service. |
| --replica-name                [obligatoire] | Nom du réplica Service Fabric. |
| --service-name                [obligatoire] | Nom du service. |
| --tail | Nombre de lignes à afficher à partir de la fin des journaux d’activité. La valeur par défaut est 100. « all » pour afficher les journaux d’activité complets. |

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux d’activité de débogage. |
| --help -h | Affiche ce message d’aide et quitte. |
| --output -o | Format de sortie.  Valeurs autorisées \: json, jsonc, table, tsv.  Valeur par défaut \: json. |
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et des exemples, consultez : http\://jmespath.org/. |
| --verbose | Augmente le détail de la journalisation. Utilisez --debug pour les journaux d’activité de débogage complets. |


## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).