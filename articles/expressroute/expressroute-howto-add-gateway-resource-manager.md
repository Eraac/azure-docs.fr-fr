---
title: 'Ajouter une passerelle de réseau virtuel à un réseau virtuel pour ExpressRoute : PowerShell : Azure | Microsoft Docs'
description: Cet article vous explique comment ajouter une passerelle de réseau virtuel à un réseau virtuel Resource Manager déjà créé pour ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6ab701adcc2122270c607604f222ac5edbceeb37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730508"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Configurer une passerelle de réseau virtuel pour ExpressRoute à l’aide de PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classic - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vidéo - portail Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Cet article vous permet d’ajouter, de redimensionner et de supprimer une passerelle de réseau virtuel pour un réseau virtuel préexistant. Les étapes de cette configuration s’appliquent aux réseaux virtuels qui ont été créés à l’aide du modèle de déploiement Resource Manager pour une configuration ExpressRoute. Pour en savoir plus, consultez [À propos des passerelles de réseau virtuel pour ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Avant tout chose

### <a name="working-with-powershell"></a>Travailler avec PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloud-shell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Liste de référence de configuration

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez créé la passerelle de réseau virtuel, vous pouvez lier votre réseau virtuel à un circuit ExpressRoute. Consultez [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md).
