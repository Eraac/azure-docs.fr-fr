---
title: Terminologie de SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Terminologie de SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c069203e94872452c11a7e6cebccd213e0af639c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706935"
---
# <a name="know-the-terms"></a>Maîtriser la terminologie

Plusieurs définitions communes sont largement utilisées dans ce guide sur l’architecture et le déploiement technique. Veuillez noter les termes suivants et leur signification :

- **IaaS** : Infrastructure as a Service.
- **PaaS** : Platform as a Service.
- **SaaS** : Software as a Service.
- **Composant SAP** : application SAP individuelle, comme ERP Central Component (ECC), Business Warehouse (BW), Solution Manager ou Enterprise Portal (EP). Les composants SAP peuvent être basés sur des technologies ABAP ou Java traditionnelles ou une application non basée sur NetWeaver telle que Business Objects.
- **Environnement SAP** : un ou plusieurs composants SAP regroupés de manière logique pour exécuter une fonction métier, comme le développement, l’assurance qualité, la formation, la récupération d’urgence ou la production.
- **Paysage SAP** : fait référence à l’ensemble des ressources SAP dans votre paysage informatique. Le paysage SAP comprend tous les environnements de production et les autres types d’environnements.
- **Système SAP** : combinaison de la couche SGBD et de la couche Application d’un système de développement SAP ERP, d’un système de test SAP BW ou d’un système de production SAP CRM, par exemple. Les déploiements Azure ne prennent pas en charge la séparation de ces deux couches entre les sites locaux et Azure. Un système SAP est déployé en local ou dans Azure. Vous pouvez déployer les différents systèmes d’un paysage SAP dans Azure ou en local. Par exemple, vous pouvez déployer les systèmes de test et de développement SAP CRM dans Azure et le système de production SAP CRM en local. Pour SAP HANA sur Azure (grandes instances), vous devez héberger la couche Application SAP des systèmes SAP dans des machines virtuelles et l’instance SAP HANA sur une unité dans le tampon SAP HANA sur Azure (grandes instances).
- **Tampon de grande instance** : pile d’infrastructure matérielle SAP HANA certifiée TDI et dédiée à l’exécution d’instances SAP HANA dans Azure.
- **SAP HANA sur Azure (grandes instances) :** nom officiel de l’offre dans Azure permettant d’exécuter des instances HANA sur le matériel SAP HANA certifié TDI qui est déployé dans des tampons de grande instance dans différentes régions Azure. Le terme associé *Grande instance HANA* est la forme abrégée de *SAP HANA sur Azure (grandes instances)* largement utilisée dans ce guide de déploiement technique.
- **Entre différents locaux** : décrit un scénario dans lequel les machines virtuelles sont déployées dans un abonnement Azure qui dispose d’une connectivité de site à site, multisite ou Azure ExpressRoute entre les centres de données locaux et Azure. Dans la documentation Azure courante, ces types de déploiements sont également décrits comme des scénarios intersites. La connexion a pour but d’étendre les domaines locaux, Azure Active Directory/OpenLDAP local et le DNS local à Azure. Le paysage local est étendu aux ressources Azure des abonnements Azure. Grâce à cette extension, les machines virtuelles peuvent faire partie du domaine local. 

   Les utilisateurs du domaine local peuvent accéder aux serveurs et exécuter des services sur ces machines virtuelles (tels que les services SGBD). La communication et la résolution de noms entre les machines virtuelles déployées en local et les machines virtuelles déployées dans Azure sont possibles. Il s’agit du scénario classique dans lequel la plupart des ressources SAP sont déployées. Pour plus d’informations, consultez [Passerelle VPN Azure](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) et [Créer un réseau virtuel avec une connexion de site à site à partir du portail Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Tenant** : un client déployé dans le tampon de grande instance HANA est isolé dans un *locataire.* Un locataire est isolé des autres locataires dans la couche de mise en réseau, de stockage et de calcul. Les unités de stockage et compute assignées aux différents abonnés ne peuvent pas se voir ni communiquer entre elles sur le niveau de tampon de grande instance HANA. Un client peut opter pour des déploiements dans plusieurs locataires. Même dans ce cas, aucune communication n’est établie entre les locataires sur le niveau de tampon de grande instance HANA.
- **Catégorie de référence SKU** : pour la grande instance HANA, les deux catégories suivantes de références SKU sont proposées :
    - **Classe de type I** : S72, S72m, S96, S144, S144m, S192, S192m et S192xm
    - **Classe de type II** : S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm et S960m


De nombreuses ressources supplémentaires sont disponibles sur le déploiement d’une charge de travail SAP dans le cloud. Si vous planifiez un déploiement de SAP HANA dans Azure, vous devez être expérimenté, connaitre les principes d’Azure IaaS et le déploiement des charges de travail SAP sur Azure IaaS. Avant de continuer, consultez [Utiliser des solutions SAP sur des machines virtuelles Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour plus d’informations. 

**Étapes suivantes**
- Voir [Certification HLI](hana-certification.md)