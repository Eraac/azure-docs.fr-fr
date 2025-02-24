---
title: Concepts clés pour l’administration de la solution Azure VMware par CloudSimple
description: Décrit mes concepts clés pour l’administration de la solution Azure VMware par CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6d87871fe8faaaab2e56d4a0426cd5e5f0899c8f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595616"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>Concepts clés pour l’administration de la solution Azure VMware par CloudSimple

L’administration de la solution Azure VMware par CloudSimple implique de comprendre les concepts suivants :

* Le service CloudSimple, présenté comme la solution Azure VMware par CloudSimple - Service.
* Le nœud CloudSimple, présenté comme la solution Azure VMware par CloudSimple - Nœud.
* Cloud privé CloudSimple
* Mise en réseau du service
* La machine virtuelle CloudSimple, présentée comme la solution Azure VMware par CloudSimple - Machine virtuelle.

## <a name="cloudsimple-service"></a>Service CloudSimple

Le service CloudSimple permet de créer et de gérer toutes les ressources associées aux Solutions VMware par CloudSimple à partir du portail Azure. Créez une ressource de service dans chaque région dans laquelle vous souhaitez utiliser le service.

En savoir plus sur le [service CloudSimple](cloudsimple-service.md).

## <a name="cloudsimple-node"></a>Nœud CloudSimple

Un nœud CloudSimple est un hôte de calcul et de stockage hyperconvergé complet dédié sur lequel l’hyperviseur VMware ESXi est déployé. Ce nœud est ensuite intégré aux plateformes VMware vSphere, vCenter, vSAN, et NSX. Les services de mise en réseau CloudSimple et Edge sont également activés. Chaque nœud sert d’unité de capacité de calcul et de capacité de stockage que vous pouvez configurer pour créer des [clouds privés CloudSimple](cloudsimple-private-cloud.md). Vous configurez ou réservez des nœuds dans une région où le service CloudSimple est disponible.


En savoir plus sur les [nœuds CloudSimple](cloudsimple-node.md).

## <a name="cloudsimple-private-cloud"></a>Cloud privé CloudSimple

Un cloud privé CloudSimple est un environnement isolé de la pile VMware géré par un serveur vCenter dans son propre domaine de gestion. La pile VMware inclut des hôtes ESXi, vSphere, vCenter, vSAN et NSX. Les exécutions de pile sur des nœuds dédiés (matériel complet isolé et dédié) et utilisé par les utilisateurs via les outils VMware natifs qui incluent vCenter et NSX Manager. Les nœuds dédiés sont déployés dans des emplacements Azure et sont gérés par Azure. Chaque cloud privé peut être segmenté et sécurisé à l’aide des services de mise en réseau, tels que les réseaux locaux virtuels, les sous-réseaux et les tables de pare-feu. Les connexions à votre environnement local et au réseau Azure sont créées à l’aide de connexions VPN sécurisées, privées et Azure ExpressRoute.

En savoir plus sur le [cloud privé CloudSimple](cloudsimple-private-cloud.md).

## <a name="service-networking"></a>Mise en réseau du service

Le service de CloudSimple fournit un réseau par région où votre service CloudSimple est déployé. Le réseau est un seul espace d’adressage TCP de couche 3 avec routage activé par défaut. Tous les clouds privés et sous-réseaux créés dans cette région communiquent entre eux sans aucune configuration supplémentaire. Vous créez des groupes de ports distribués sur le serveur vCenter en utilisant les réseaux locaux virtuels. Vous pouvez utiliser les fonctionnalités réseau suivantes pour configurer et sécuriser les ressources de charge de travail de votre cloud privé :

* [Réseaux locaux virtuels et sous-réseaux](cloudsimple-vlans-subnets.md)
* [Tables de pare-feu](cloudsimple-firewall-tables.md)
* [Passerelles VPN](cloudsimple-vpn-gateways.md)
* [Adresse IP publique](cloudsimple-public-ip-address.md)
* [Connexion réseau Azure](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>Machine virtuelle CloudSimple

Avec le service CloudSimple, vous pouvez gérer des machines virtuelles VMware à partir du portail Azure. Un ou plusieurs clusters ou pools de ressources de votre environnement vSphere peuvent être mappés à l’abonnement sur lequel le service est créé.

Pour en savoir plus :

* [Machines virtuelles CloudSimple](cloudsimple-virtual-machines.md)
* [Mappage de l’abonnement Azure](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
