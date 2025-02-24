---
title: Vue d’ensemble du point de terminaison de services de réseau virtuel dans Azure Database pour PostgreSQL - Serveur unique
description: Découvrez comment les points de terminaison de service de réseau virtuel pour Azure Database pour PostgreSQL - Serveur unique fonctionnent.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c873abcdf3c64f8357c584c8e24809270946bf74
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073475"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql---single-server"></a>Utiliser des points de terminaison de service de réseau virtuel et des règles pour Azure Database pour PostgreSQL - Serveur unique

Les *règles de réseau virtuel* désignent une fonctionnalité de sécurité de pare-feu qui permet de contrôler si votre serveur Azure Database pour PostgreSQL doit accepter ou non les communications provenant de sous-réseaux spécifiques dans des réseaux virtuels. Cet article explique pourquoi la fonctionnalité de règle de réseau virtuel est parfois la meilleure solution pour autoriser en toute sécurité des communications sur votre serveur Azure Database pour PostgreSQL.

Pour créer une règle de réseau virtuel, il doit d’abord exister un [réseau virtuel][vm-virtual-network-overview] (VNet) et un [point de terminaison de service de réseau virtuel][vm-virtual-network-service-endpoints-overview-649d] pour la règle à référencer. L’image suivante illustre le fonctionnement d’un point de terminaison de service de réseau virtuel avec Azure Database pour PostgreSQL :

![Exemple de fonctionnement d’un point de terminaison de service de réseau virtuel](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> Cette fonctionnalité est disponible dans toutes les régions de cloud public Azure où Azure Database pour PostgreSQL est déployé pour les serveurs à usage général et à mémoire optimisée.
> En cas de peering de réseau virtuel, si le trafic passe par une passerelle de réseau virtuel commune avec des points de terminaison de service et qu’il est supposé transiter par l’homologue, créez une règle ACL/VNet pour permettre aux machines virtuelles Azure du réseau virtuel de la passerelle d’accéder au serveur Azure Database pour PostgreSQL.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologie et description

**Réseau virtuel :** Vous pouvez avoir des réseaux virtuels associés à votre abonnement Azure.

**Sous-réseau :** Un réseau virtuel contient des **sous-réseaux**. Toutes les machines virtuelles Azure que vous avez sont assignées à des sous-réseaux. Un sous-réseau peut contenir plusieurs machines virtuelles ou d’autres nœuds de calcul. Les nœuds de calcul qui se trouvent en dehors de votre réseau virtuel ne peuvent pas accéder à ce dernier, sauf si vous configurez votre sécurité pour leur en donner l’accès.

**Point de terminaison de service de réseau virtuel :** Un [point de terminaison de service de réseau virtuel][vm-virtual-network-service-endpoints-overview-649d] est un sous-réseau dont les valeurs de propriétés incluent un ou plusieurs noms de type de service Azure formels. Dans cet article, nous nous intéressons au nom de type de **Microsoft.Sql**, qui fait référence au service Azure nommé SQL Database. Ce nom de service s’applique également aux services Azure Database pour PostgreSQL et MySQL. Il est important de noter que lorsque le nom de service **Microsoft.Sql** est appliqué à un point de terminaison de service de réseau virtuel, il configure le trafic de point de terminaison de service pour l’ensemble des services Azure SQL Database, y compris les serveurs Azure Database pour PostgreSQL et Azure Database pour MySQL sur le sous-réseau. 

**Règle de réseau virtuel :** Une règle de réseau virtuel pour le serveur Azure Database pour PostgreSQL est un sous-réseau répertorié dans la liste de contrôle d’accès (ACL) du serveur Azure Database pour PostgreSQL. Pour figurer dans l’ACL pour votre serveur Azure Database pour PostgreSQL, le sous-réseau doit contenir le nom de type **Microsoft.Sql**.

Une règle de réseau virtuel donne l’instruction au serveur Azure Database pour PostgreSQL d’accepter les communications provenant de tout nœud se trouvant sur le sous-réseau.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Avantages d’une règle de réseau virtuel

Les machines virtuelles sur vos sous-réseaux ne peuvent pas communiquer avec votre serveur Azure Database pour PostgreSQL sans une intervention de votre part. La création d’une règle de réseau virtuel permet d’établir la communication. Le choix de l’approche des règles de réseau virtuel peut être justifié par contraste et comparaison avec les options de sécurité concurrentes offertes par le pare-feu.

### <a name="a-allow-access-to-azure-services"></a>R. Autoriser l’accès aux services Azure

Le volet de sécurité de connexion dispose d’un bouton **ACTIVÉ/DÉSACTIVÉ** étiqueté **Autoriser l’accès aux services Azure**. Le paramètre **ACTIVÉ** autorise les communications provenant de toutes les adresses IP Azure et de tous les sous-réseaux Azure. Ces adresses IP ou sous-réseaux Azure ne vous appartiennent peut-être pas. Ce paramètre **ACTIVÉ** est sans doute plus ouvert que vous souhaitez que le soit votre serveur Azure Database pour PostgreSQL. La fonctionnalité de règle de réseau virtuel offre un contrôle beaucoup plus précis.

### <a name="b-ip-rules"></a>B. Règles IP

Le pare-feu du serveur Azure Database pour PostgreSQL permet de spécifier des plages d’adresses IP à partir desquelles les communications sont acceptées sur le serveur Azure Database pour PostgreSQL. Cette approche est indiquée pour les adresses IP stables qui se trouvent en dehors du réseau privé Azure. Mais un grand nombre de nœuds à l’intérieur du réseau privé Azure sont configurés avec des adresses IP *dynamiques*. Les adresses IP dynamiques peuvent changer, par exemple quand la machine virtuelle est redémarrée. Spécifier une adresse IP dynamique dans une règle de pare-feu au sein d’un environnement de production serait inimaginable.

Vous pouvez récupérer l’option IP en obtenant une adresse IP *statique* pour votre machine virtuelle. Pour plus d’informations, consultez [Configurer des adresses IP privées pour une machine virtuelle à l’aide du portail Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

L’approche des IP statiques peut toutefois devenir difficile à gérer, et elle est coûteuse quand elle est appliquée à grande échelle. Les règles de réseau virtuel sont plus faciles à établir et à gérer.

### <a name="c-cannot-yet-have-azure-database-for-postgresql-on-a-subnet-without-defining-a-service-endpoint"></a>C. Impossible d’avoir un serveur Azure Database pour PostgreSQL sur un sous-réseau sans définir de point de terminaison de service

Si votre serveur **Microsoft.Sql** était un nœud sur un sous-réseau de votre réseau virtuel, tous les nœuds situés dans le réseau virtuel pourraient communiquer avec le serveur Azure Database pour PostgreSQL. Dans ce cas, vos machines virtuelles pourraient communiquer avec Azure Database pour PostgreSQL sans avoir à utiliser de règles de réseau virtuel ni de règles IP.

Toutefois, depuis août 2018, le service Azure Database pour PostgreSQL ne figure plus parmi les services pouvant être assignés directement à un sous-réseau.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Informations sur les règles de réseau virtuel

Cette section fournit des informations sur les règles de réseau virtuel.

### <a name="only-one-geographic-region"></a>Une seule région géographique

Chaque point de terminaison de service de réseau virtuel s’applique à une seule région Azure. Le point de terminaison ne permet pas à d’autres régions d’accepter les communications provenant du sous-réseau.

Une règle de réseau virtuel est limitée à la région à laquelle s’applique son point de terminaison sous-jacent.

### <a name="server-level-not-database-level"></a>Niveau serveur, et non niveau base de données

Chaque règle de réseau virtuel s’applique à tout le serveur Azure Database pour PostgreSQL, et pas seulement à une base de données particulière sur le serveur. En d’autres termes, la règle de réseau virtuel s’applique au niveau du serveur, et non au niveau de la base de données.

#### <a name="security-administration-roles"></a>Rôles d’administration de la sécurité

Il existe une séparation des rôles de sécurité dans l’administration des points de terminaison de service de réseau virtuel. Chacun des rôles suivants doit réaliser une action :

- **Administrateur réseau :** &nbsp; Activez le point de terminaison.
- **Administrateur de base de données :** &nbsp; Mettez à jour la liste de contrôle d’accès (ACL) pour ajouter un sous-réseau donné sur le serveur Azure Database pour PostgreSQL.

*Alternative RBAC :*

Les rôles d’administrateur de réseau et d’administrateur de base de données disposent de plus de fonctionnalités que nécessaires pour gérer les règles de réseau virtuel. Seule une partie de ces fonctionnalités est réellement nécessaire.

Vous avez la possibilité d’utiliser le [contrôle d’accès en fonction du rôle (RBAC)][rbac-what-is-813s] dans Azure pour créer un rôle personnalisé unique disposant uniquement des fonctionnalités nécessaires. Le rôle personnalisé peut être utilisé au lieu d’impliquer l’administrateur de réseau ou l’administrateur de base de données. Votre surface d’exposition de sécurité est inférieure si vous assignez un rôle personnalisé à un utilisateur au lieu de lui assigner les deux principaux rôles d’administrateur.

> [!NOTE]
> Il peut arriver que l’instance Azure Database pour PostgreSQL et le sous-réseau de réseau virtuel se trouvent dans des abonnements différents. Dans ce cas, vous devez vérifier les configurations suivantes :
> - Les deux abonnements doivent se trouver dans le même locataire Azure Active Directory.
> - L’utilisateur dispose des autorisations requises pour lancer des opérations, telles que l’activation des points de terminaison de service et l’ajout d’un sous-réseau de réseau virtuel sur le serveur donné.

## <a name="limitations"></a>Limites

Pour Azure Database pour PostgreSQL, la fonctionnalité de règle de réseau virtuel présente les limitations suivantes :

- Une application web peut être mappée à une adresse IP privée dans un sous-réseau/réseau virtuel. Même si les points de terminaison de service sont activés à partir du réseau virtuel/sous-réseau donné, les connexions entre l’application web et le serveur présenteront une source IP publique Azure, et non une source de sous-réseau/réseau virtuel. Pour activer la connectivité à partir d’une application web vers un serveur disposant de règles de pare-feu de réseau virtuel, vous devez activer le paramètre Autoriser l’accès des services Azure au serveur.

- Dans le pare-feu pour votre serveur Azure Database pour PostgreSQL, chaque règle de réseau virtuel fait référence à un sous-réseau. Tous ces sous-réseaux référencés doivent être hébergés dans la même région géographique qui héberge Azure Database pour PostgreSQL.

- Chaque serveur Azure Database pour PostgreSQL peut avoir jusqu’à 128 entrées ACL pour un réseau virtuel donné.

- Les règles de réseau virtuel s’appliquent uniquement à des réseaux virtuels Azure Resource Manager, et non à des réseaux avec un [modèle de déploiement Classic][arm-deployment-model-568f].

- Le fait d’activer les points de terminaison de service de réseau virtuel sur Azure Database pour PostgreSQL à l’aide du nom de service **Microsoft.Sql** a pour effet d’activer également les points de terminaison pour tous les services Azure Database : Azure Database pour MySQL, Azure Database pour PostgreSQL, Azure SQL Database et Azure SQL Data Warehouse.

- Les points de terminaison de service de réseau virtuel sont uniquement pris en charge pour les serveurs Usage général et Mémoire optimisée.

- Sur le pare-feu, les plages d’adresses IP s’appliquent aux éléments de mise en réseau suivants, contrairement aux règles de réseau virtuel :
    - [Réseau privé virtuel (VPN) site à site (S2S)][vpn-gateway-indexmd-608y]
    - En local via [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Si votre réseau est connecté au réseau Azure via l’utilisation [d’ExpressRoute][expressroute-indexmd-744v], chaque circuit est configuré avec deux adresses IP publiques sur Microsoft Edge. Les deux adresses IP sont utilisées pour se connecter aux services Microsoft, comme le stockage Azure, à l’aide de l’homologation publique Azure.

Pour permettre la communication de votre circuit avec Azure Database pour PostgreSQL, vous devez créer des règles de réseau IP pour les adresses IP publiques de vos circuits. Pour rechercher les adresses IP publiques de votre circuit ExpressRoute, ouvrez un ticket de support avec ExpressRoute dans le portail Azure.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Ajout d’une règle de pare-feu de réseau virtuel à votre serveur sans activer les points de terminaison de service Vnet

La simple définition d’une règle de pare-feu ne permet pas de sécuriser le serveur sur le réseau virtuel. Vous devez également **activer** les points de terminaison de service du réseau virtuel pour que la sécurité soit appliquée. Quand vous **activez** les points de terminaison de service, le sous-réseau de votre réseau virtuel est arrêté le temps de passer de l’état **Désactivé** à l’état **Activé**. Cela est particulièrement vrai dans le contexte de grands réseaux virtuels. Vous pouvez utiliser l’indicateur **IgnoreMissingServiceEndpoint** pour réduire ou éliminer le temps d’arrêt pendant la transition.

Vous pouvez définir l’indicateur **IgnoreMissingServiceEndpoint** à l’aide d’Azure CLI ou du Portail Azure.

## <a name="related-articles"></a>Articles connexes
- [Réseaux virtuels Azure][vm-virtual-network-overview]
- [Points de terminaison de service de réseau virtuel Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Étapes suivantes
Pour des articles dédiés à la création de règles de réseau virtuel, consultez :
- [Create and manage Azure Database for PostgreSQL VNet rules using the Azure portal](howto-manage-vnet-using-portal.md) (Créer et gérer des règles de réseau virtuel Azure Database pour PostgreSQL à l’aide du portail Azure)
- [Create and manage Azure Database for PostgreSQL VNet rules using Azure CLI](howto-manage-vnet-using-cli.md) (Créer et gérer des règles de réseau virtuel Azure Database pour PostgreSQL à l’aide d’Azure CLI)


<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml
