---
title: 'Modèles de connectivité ExpressRoute : Azure| Microsoft Docs'
description: Cet article décrit les différents modes de connectivité entre le réseau du client et les services Microsoft Azure, Office 365 et Dynamics 365. Clients peuvent faire appel à des fournisseurs MPLS, des échanges de cloud et des fournisseurs Ethernet.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: ef53f9d3cefa35624d6f94dc98db4ebe8b6646cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60367442"
---
# <a name="expressroute-connectivity-models"></a>Modèles de connectivité ExpressRoute
Vous pouvez créer une connexion entre votre réseau local et le cloud Microsoft de trois façons différentes : à l’aide d’une [colocalisation avec échange de cloud](#CloudExchange), d’une [connexion Ethernet point à point](#Ethernet)ou d’une [connexion universelle (IPVPN)](#IPVPN). Les fournisseurs de connectivité peuvent offrir un ou plusieurs modèles de connectivité. Vous pouvez contacter votre fournisseur de connectivité pour choisir le modèle qui vous convient le mieux.
<br><br>

![Schéma des modèles de connectivité ExpressRoute](./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png)

## <a name="CloudExchange"></a>Colocalisation avec un échange de cloud
Si vous êtes colocalisé avec une installation dans le cadre d’un échange de cloud, vous pouvez commander des interconnexions virtuelles vers le cloud de Microsoft via l’échange Ethernet du fournisseur de colocalisation. Les fournisseurs de colocalisation peuvent offrir des interconnexions de couche 2 ou des interconnexions de couche 3 gérées entre votre infrastructure dans l’installation de colocalisation et le cloud de Microsoft.

## <a name="Ethernet"></a>Connexions Ethernet point à point
Vous pouvez connecter vos centres de données/bureaux locaux au cloud de Microsoft via des liaisons Ethernet point à point. Les fournisseurs Ethernet point à point peuvent offrir des connexions de couche 2 ou des connexions de couche 3 gérées entre votre site et le cloud de Microsoft.

## <a name="IPVPN"></a>Réseaux universels (IPVPN)
Vous pouvez intégrer votre réseau étendu au cloud de Microsoft. Les fournisseurs IPVPN (généralement des VPN MPLS) offrent une connectivité universelle entre vos succursales et vos centres de données. Le cloud de Microsoft peut être interconnecté à votre réseau étendu afin qu’il apparaisse comme n’importe quelle autre succursale. Les fournisseurs de réseaux étendus offrent généralement une connectivité de couche 3 gérée. Les capacités et fonctionnalités ExpressRoute sont identiques pour tous les modèles de connectivité ci-dessus. 

## <a name="next-steps"></a>Étapes suivantes
* Découvrez en détail les connexions ExpressRoute et les domaines de routage. Consultez la page [Circuits ExpressRoute et domaines de routage](expressroute-circuit-peerings.md).
* Découvrez en détail les fonctionnalités ExpressRoute. Consultez la [présentation technique d’ExpressRoute](expressroute-introduction.md).
* Recherchez un fournisseur de services. Consultez la page [Partenaires ExpressRoute et emplacements d’homologation](expressroute-locations.md).
* Vérifiez que toutes les conditions préalables sont remplies. Consultez la page [Configuration requise pour ExpressRoute](expressroute-prerequisites.md).
* Reportez-vous aux conditions requises pour le [routage](expressroute-routing.md), la [traduction d’adresses réseau](expressroute-nat.md) et la [qualité de service](expressroute-qos.md).
* Configurez votre connexion ExpressRoute.
  * [Création d’un circuit ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configuration du routage](expressroute-howto-routing-portal-resource-manager.md)
  * [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)