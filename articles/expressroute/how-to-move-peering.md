---
title: Déplacer un peering public vers le peering Microsoft | Microsoft Docs
description: Cet article explique comment déplacer votre appairage public vers l’appairage Microsoft sur ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 5581e2a5c2fe2ee5e154870f7ffc2ab1c3c0f3b4
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592124"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Déplacer un appairage public vers l’appairage Microsoft

Cet article vous permet de déplacer la configuration d’un appairage public vers l’appairage Microsoft sans temps d’arrêt. ExpressRoute prend en charge l’utilisation du peering Microsoft avec les filtres de routes dans les services PaaS Azure, tels que le stockage Azure et Azure SQL Database. Vous avez maintenant besoin d’un seul domaine de routage pour accéder aux services SaaS et PaaS Microsoft. Vous pouvez utiliser les filtres de routes afin de publier sélectivement les préfixes de service PaaS pour les régions Azure que vous souhaitez utiliser.

Le peering public Azure possède une adresse IP NAT associée à chaque session BGP. Le peering Microsoft vous permet de configurer vos propres répartitions NAT, ainsi que d'utiliser des filtres de routage pour des publications de préfixe sélectif. Le peering public est un service unidirectionnel avec lequel la connectivité est toujours initiée de votre réseau étendu aux services Microsoft Azure. Ces derniers ne sont pas en mesure d’initier des connexions à votre réseau via ce domaine de routage.

Une fois l’homologation publique activée, vous êtes en mesure de vous connecter à tous les services Azure. Nous ne vous permettons pas de sélectionner les services pour lesquels nous publions les itinéraires. Pour sa part, le peering Microsoft est une connectivité bidirectionnelle où la connexion peut être lancée à partir du service Microsoft Azure avec votre réseau étendu. Pour plus d’informations sur les domaines de routage et le peering, consultez [Circuits ExpressRoute et domaines de routage](expressroute-circuit-peerings.md).

## <a name="before"></a>Avant de commencer

Pour vous connecter à l’appairage Microsoft, vous devez configurer et gérer un processus NAT. Votre fournisseur de connectivité peut-configurer et gérer le processus NAT en tant que service géré. Si vous envisagez d’accéder aux services SaaS et PaaS Azure avec l’appairage Microsoft, vous devez dimensionner le pool d’adresses IP NAT correctement. Pour plus d’informations sur le processus NAT pour ExpressRoute, consultez [Configuration NAT requise pour l’homologation Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering). Quand vous vous connectez à Microsoft via Azure ExpressRoute (peering Microsoft), vous disposez de plusieurs connexions à Microsoft. L’une d’elles est votre connexion Internet existante, et l’autre est celle établie via ExpressRoute. Il se peut qu’une partie du trafic circulant vers Microsoft passe par Internet mais revienne via ExpressRoute, et inversement.

![Connectivité bidirectionnelle](./media/how-to-move-peering/bidirectional-connectivity.jpg)

> [!Warning]
> Le pool IP NAT proposé à Microsoft ne doit pas être publié sur Internet. Cela interromprait la connectivité avec d'autres services Microsoft.

Reportez-vous à [Routage asymétrique avec chemins d’accès réseau multiples](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing) pour bien comprendre le routage asymétrique avant de configurer le peering Microsoft.

* Si vous utilisez le peering public et disposez actuellement de règles de réseau IP pour les adresses IP publiques qui sont utilisées pour accéder au [Stockage Azure](../storage/common/storage-network-security.md) ou à [Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), vérifiez que le pool d’adresses IP NAT configuré avec l’appairage Microsoft se trouve bien dans la liste des adresses IP publiques du compte de stockage Azure ou du compte Azure SQL.<br>
* Pour passer à l’appairage Microsoft sans temps d’arrêt, suivez les étapes fournies dans cet article, dans l’ordre indiqué.

## <a name="create"></a>1. Créer un appairage Microsoft

Si l’appairage Microsoft n’a pas été créé, utilisez un des articles suivants pour le créer. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l’appairage Microsoft pour votre circuit.

Si la couche 3 est gérée par vous-même, les informations suivantes sont requises avant de continuer :

* Un sous-réseau /30 pour le lien principal. Il doit s’agir d’un préfixe IPv4 public valide vous appartenant et enregistré dans un registre RIR / IRR. À partir de ce sous-réseau, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième adresse IP utilisable pour son routeur.<br>
* Un sous-réseau /30 pour le lien secondaire. Il doit s’agir d’un préfixe IPv4 public valide vous appartenant et enregistré dans un registre RIR / IRR. À partir de ce sous-réseau, vous allez attribuer la première adresse IP utilisable à votre routeur. Microsoft utilise la deuxième adresse IP utilisable pour son routeur.<br>
* Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN. Vous devez utiliser le même ID VLAN pour le lien principal et pour le lien secondaire.<br>
* Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets.<br>
* Préfixes publiés : Vous devez fournir la liste de tous les préfixes que vous prévoyez de publier sur la session BGP. Seuls les préfixes d'adresses IP publiques sont acceptés. Si vous prévoyez d’envoyer un jeu de préfixes, vous pouvez envoyer une liste séparée par des virgules. Ces préfixes doivent être enregistrés en votre nom dans un registre RIR / IRR.<br>
* Nom du registre de routage : Vous pouvez spécifier les registres RIR/IRR sur lesquels le numéro AS et les préfixes sont inscrits.

* **Facultatif** - ASN du client : Si vous publiez des préfixes non enregistrés dans le numéro AS de peering, vous pouvez spécifier le numéro AS avec lequel ils sont enregistrés.<br>
* **Facultatif** : hachage MD5 si vous choisissez d’en utiliser un.

Vous trouverez des instructions détaillées pour activer le peering Microsoft dans les articles suivants :

* [Créer l’appairage Microsoft à l’aide du portail Azure](expressroute-howto-routing-portal-resource-manager.md#msft)<br>
* [Créer l’appairage Microsoft à l’aide d’Azure PowerShell](expressroute-howto-routing-arm.md#msft)<br>
* [Créer l’appairage Microsoft à l’aide de l’interface de ligne de commande Azure](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Vérifier que l’appairage Microsoft est activé

Vérifiez que l’appairage Microsoft est activé et que les préfixes publics publiés sont dans l’état configuré.

* [Portail Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)<br>
* [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)<br>
* [Interface de ligne de commande Azure](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Configurer un filtre de routes et le joindre au circuit

Par défaut, le peering Microsoft ne publie pas de préfixes tant qu’un filtre de routes n’est pas joint au circuit. Quand vous créez une règle de filtre de routes, vous pouvez spécifier la liste des communautés de service pour les régions Azure que vous souhaitez utiliser pour les services PaaS Azure. Vous pouvez ainsi filtrer les routes en toute souplesse, comme l’illustre la capture d’écran suivante :

![Fusionner l’appairage public](./media/how-to-move-peering/routefilter.jpg)

Configurez les filtres de routes à l’aide des articles suivants :

* [Configurer des filtres de routes pour l’appairage Microsoft à l’aide du portail Azure](how-to-routefilter-portal.md)<br>
* [Configurer des filtres de routes pour l’appairage Microsoft à l’aide d’Azure PowerShell](how-to-routefilter-powershell.md)<br>
* [Configurer des filtres de routes pour l’appairage Microsoft à l’aide de l’interface de ligne de commande Azure](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Supprimer l’appairage public

Après avoir vérifié que l’appairage Microsoft est configuré et que les préfixes que vous souhaitez utiliser sont correctement publiés sur l’appairage Microsoft, vous pouvez supprimer l’appairage public. Pour supprimer l’appairage public, utilisez l’un des articles suivants :

* [Supprimer un appairage public Azure à l’aide du portail Azure](expressroute-howto-routing-portal-resource-manager.md#deletepublic)<br>
* [Supprimer un appairage public Azure à l’aide d’Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)<br>
* [Supprimer un appairage public Azure à l’aide de l’interface de ligne de commande](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. Afficher les homologations
  
Vous pouvez afficher une liste de tous les circuits et homologations ExpressRoute dans le portail Azure. Pour plus d’informations, consultez [Afficher les détails d’homologation Microsoft](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur ExpressRoute, consultez la [FAQ sur ExpressRoute](expressroute-faqs.md).
