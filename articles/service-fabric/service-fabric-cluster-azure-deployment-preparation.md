---
title: Planifier un déploiement de cluster Azure Service Fabric | Microsoft Docs
description: Découvrez-en plus sur la planification et la préparation d'un déploiement de cluster de production Service Fabric sur Azure.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: aljo
ms.openlocfilehash: 0f3a9010805ec1a18490f6f530f60d7a3c763398
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60387857"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Planifier et préparer un déploiement de cluster

Planifier et préparer un déploiement de cluster de production est très important.  De nombreux facteurs sont à prendre en compte.  Cet article vous guide tout au long des étapes de préparation de déploiement de cluster.

## <a name="read-the-best-practices-information"></a>Lire les informations en matière de meilleures pratiques
Pour une bonne gestion des applications et clusters Azure Service Fabric, nous vous recommandons certaines opérations afin de renforcer la fiabilité de votre environnement de production.  Pour plus d'informations, consultez [Meilleures pratiques relatives aux applications et aux clusters Service Fabric](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>Sélectionner le système d'exploitation pour le cluster
Service Fabric permet la création de clusters Service Fabric sur toute machine virtuelle ou tout ordinateur exécutant Windows Server ou Linux.  Avant de déployer votre cluster, vous devez choisir le système d’exploitation :  Windows ou Linux.  Chaque nœud (machine virtuelle) du cluster exécute le même système d’exploitation et vous ne pouvez pas combiner des machines virtuelles Linux et Windows au sein du même cluster.

## <a name="capacity-planning"></a>planification de la capacité
Pour un déploiement de production, la planification de la capacité est une étape importante. Voici quelques éléments à prendre en compte dans le cadre de ce processus.

* Nombre initial de types de nœuds pour votre cluster 
* Propriétés de chaque type de nœud (taille, nombre d'instances, principal ou non, accessibilité sur Internet, nombre de machines virtuelles, etc.)
* Caractéristiques de fiabilité et de durabilité du cluster

### <a name="select-the-initial-number-of-node-types"></a>Sélectionner le nombre initial de types de nœuds
Vous devez d’abord déterminer l’utilisation du cluster que vous créez. Quels types d’applications planifiez-vous de déployer dans ce cluster ? Votre application inclut-elle plusieurs services ? Si oui, ces services doivent-ils être publics ou accessibles sur Internet ? Vos services (qui composent votre application) ont-ils des besoins d’infrastructure différents tels qu’une RAM plus volumineuse ou des cycles processeur plus élevés ? Un cluster Service Fabric peut être constitué de plusieurs types de nœuds : un type de nœud principal et un ou plusieurs types de nœuds non principaux. Chaque type de nœud est mappé à un groupe de machines virtuelles identiques. Chaque type de nœud peut ensuite faire l’objet d’une montée ou descente en puissance de manière indépendante, avoir différents jeux de ports ouverts et présenter différentes métriques de capacité. Des [propriétés de nœud et contraintes de placement][ placementconstraints] peut être configurées pour limiter des services spécifiques à des types de nœuds spécifiques.  Pour plus d'informations, consultez [Nombre de types de nœuds avec lesquels votre cluster doit démarrer](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with).

### <a name="select-node-properties-for-each-node-type"></a>Sélectionner les propriétés de nœud pour chaque type de nœud
Les types de nœuds définissent les références, le nombre et les propriétés des machines virtuelles du groupe identique correspondant.

La taille minimale des machines virtuelles pour chaque type de nœud est déterminée par le [niveau de durabilité][durability] que vous choisissez.

Le nombre minimal de machines virtuelles pour le type de nœud principal est déterminé par le [niveau de fiabilité][reliability] que vous choisissez.

Consultez les recommandations minimales en matière de [types de nœuds principaux](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance), [charges de travail avec état sur les types de nœuds non principaux](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads) et [charges de travail sans état sur les types de nœuds non principaux](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads). 

Tout nombre supérieur au nombre minimal de nœuds doit dépendre du nombre de réplicas des applications/services que vous souhaitez exécuter dans ce type de nœud.  [Planifier la capacité pour les applications Service Fabric](service-fabric-capacity-planning.md) vous aide à estimer les ressources requises pour exécuter vos applications. Vous pourrez ensuite mettre à l'échelle le cluster pour l'ajuster à l'évolution de la charge de travail. 

### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Sélectionner les niveaux de durabilité et de fiabilité du cluster
Le niveau de durabilité est utilisé pour indiquer au système les privilèges dont disposent vos machines virtuelles avec l’infrastructure Azure sous-jacente. Dans le type de nœud principal, ce privilège permet à Service Fabric de suspendre toute demande de l’infrastructure au niveau de la machine virtuelle (par exemple, un redémarrage de la machine virtuelle, une réinitialisation de la machine virtuelle ou une migration de machine virtuelle) qui influe sur la configuration requise du quorum pour les services système et vos services avec état. Dans les types de nœuds non principaux, ce privilège permet à Service Fabric de suspendre toute demande de l’infrastructure au niveau de la machine virtuelle (comme le redémarrage de la machine virtuelle, la réinitialisation de la machine virtuelle, la migration de machine virtuelle, etc.) qui influe sur la configuration requise du quorum pour vos services avec état.  Pour connaître les avantages liés aux différents niveaux et savoir quel niveau utiliser et quand, consultez [Caractéristiques de durabilité du cluster][durability].

Le niveau de fiabilité est utilisé pour définir le nombre de réplicas des services système que vous voulez exécuter dans ce cluster sur le type de nœud principal. Plus le nombre de réplicas est important, plus les services système sont fiables dans votre cluster.  Pour connaître les avantages liés aux différents niveaux et savoir quel niveau utiliser et quand, consultez [Caractéristiques de fiabilité du cluster][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Activer un proxy inverse et/ou DNS
Les services se connectant entre eux, en général à l’intérieur d’un cluster, peuvent accéder directement aux points de terminaison d’autres services, car les nœuds d’un cluster se trouvent sur le même réseau local. Afin de faciliter la connexion entre les services, Service Fabric fournit des services supplémentaires : un [service DNS](service-fabric-dnsservice.md) et un [service de proxy inverse](service-fabric-reverseproxy.md).  Ces deux services peuvent être activés lors du déploiement d'un cluster.

Dans la mesure où de nombreux services, en particulier les services en conteneur, peuvent avoir un nom d’URL existant, il est pratique de pouvoir utiliser le protocole DNS standard (plutôt que le protocole Service de nommage), notamment dans les scénarios impliquant le développement et le transfert d’applications. C’est là précisément qu’intervient le service DNS. Il vous permet de mapper les noms DNS à un nom de service et, par conséquent, de résoudre les adresses IP des points de terminaison.

Le proxy inverse traite les services dans le cluster qui expose des points de terminaison HTTP (y compris HTTPS). Le proxy inverse simplifie l’appel d’autres services en fournissant un format d'URI spécifique.  Le proxy inverse gère également les étapes de résolution, connexion et nouvelle tentative requises par un service pour communiquer avec un autre.

## <a name="prepare-for-disaster-recovery"></a>Préparation à la récupération d’urgence
Pour fournir une haute disponibilité, il est essentiel que les services puissent survivre à tous les types d’échecs. Ceci est particulièrement important pour les échecs inattendus et hors de votre contrôle. L'article [Préparation à la récupération d’urgence](service-fabric-disaster-recovery.md)décrit certains modes d’échec courants qui peuvent aboutir à une situation critique s’ils ne sont pas modélisés et gérés correctement. Il traite également des atténuations de risques et des actions à entreprendre si un incident se produit.

## <a name="production-readiness-checklist"></a>Liste de vérification de disponibilité de la production
Votre application et le cluster sont prêts à accepter le trafic de production ? Avant de déployer votre cluster en production, passez en revue la [Check-list de préparation à la production](service-fabric-production-readiness-checklist.md). Assurez le bon fonctionnement de votre application et de votre cluster en passant en revue les éléments de cette check-list. Nous recommandons vivement de vérifier tous ces points avant de passer en production.

## <a name="next-steps"></a>Étapes suivantes
* [Créer un cluster Service Fabric exécutant Windows](service-fabric-best-practices-overview.md)
* [Créer un cluster Service Fabric exécutant Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster