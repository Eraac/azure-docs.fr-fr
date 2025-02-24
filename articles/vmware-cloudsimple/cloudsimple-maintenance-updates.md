---
title: Solution Azure VMware par CloudSimple – Maintenance et mises à jour CloudSimple
description: Décrit le processus de service CloudSimple pour les mises à jour et maintenance planifiée.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4dde358f10e9ac5054297ff68a0971404c0dc135
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65160244"
---
# <a name="cloudsimple-maintenance-and-updates"></a>Maintenance et mises à jour CloudSimple

L’environnement de cloud privé est conçu pour ne comporter aucun point de défaillance unique :

* Les clusters ESXi sont configurés avec la haute disponibilité de vSphere. Ils sont dimensionnés de façon à comporter au moins un nœud de secours dans une optique de résilience.
* Le stockage principal redondant est assuré par vSAN, qui a besoin d’au moins trois nœuds pour assurer une protection contre la défaillance unique. Il est possible de configurer vSAN de sorte qu’il offre une résilience supérieure pour les grands clusters.
* Les machines virtuelles vCenter, PSC et NSX Manager sont configurées avec la stratégie de stockage RAID 10 dans une optique de protection contre les défaillances de stockage. Les machines virtuelles sont protégées contre les défaillances de nœud/réseau par vSphere HA.
* Les hôtes ESXi comportent des cartes réseau et des ventilateurs redondants.
* Les commutateurs Tor et Spine sont configurés en paires haute disponibilité à des fins de résilience.

CloudSimple effectue un monitoring continu de la disponibilité des machines virtuelles suivantes et propose des contrats SLA de disponibilité :

* Hôtes ESXi
* vCenter
* PSC
* NSX Manager

CloudSimple assure également une supervision des pannes suivantes :

* Disques durs
* Ports NIC physiques
* Serveurs
* Ventilateurs
* Puissance
* Commutateurs
* Ports de commutateurs

En cas d’échec d’un disque ou d’un nœud, un nouveau nœud est automatiquement ajouté au cluster VMware affecté pour rétablir immédiatement son intégrité.

CloudSimple sauvegarde, gère et met à jour ces éléments VMware dans les clouds privés :

* ESXi
* vCenter Platform Services
* Controller
* vSAN
* NSX

## <a name="back-up-and-restore"></a>Sauvegarde et restauration

La sauvegarde CloudSimple inclut :

* des sauvegardes incrémentielles nocturnes de règles vCenter, PSC et DVS ;
* l’utilisation d’API natives vCenter pour sauvegarder les composants de la couche Application ;
* la sauvegarde automatique avant toute mise à jour ou mise à niveau des logiciels de gestion VMware ;
* le chiffrement de données à la source, par vCenter, avant le transfert de données via un canal chiffré TLS 1.2 vers Azure. Les données sont stockées dans un objet blob Azure, où elles sont répliquées dans différentes régions.

Pour demander une restauration, ouvrez une [Demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="maintenance"></a>Maintenance

CloudSimple effectue plusieurs types de maintenance planifiée.

### <a name="backendinternal-maintenance"></a>Maintenance de serveur principal/interne

Cette maintenance implique généralement de reconfigurer les ressources physiques ou d’installer des correctifs logiciels. Elle n’a pas d’incidence sur la consommation normale des ressources concernées. Grâce aux cartes réseau redondantes présentes sur chaque rack physique, le trafic réseau normal et les opérations de cloud privé ne sont pas touchés. Le seul impact possible sur les performances se produit si l’organisation s’attend à utiliser la totalité de la bande passante redondante pendant l’intervalle de maintenance.

### <a name="cloudsimple-portal-maintenance"></a>Maintenance du portail CloudSimple

Un temps d’arrêt limité des services est nécessaire lors de la mise à jour de l’infrastructure ou du plan de contrôle CloudSimple. Les intervalles de maintenance peuvent actuellement atteindre une fréquence mensuelle, qui devrait diminuer au fil du temps. CloudSimple assure la notification en cas de maintenance du portail et limite autant que possible l’intervalle. Pendant un intervalle de maintenance du portail, les services suivants continuent de fonctionner sans aucun impact :

* Applications et plan de gestion VMware
* Accès vCenter
* Tout ce qui concerne les réseaux et le stockage
* Tout le trafic Azure

### <a name="vmware-infrastructure-maintenance"></a>Maintenance de l’infrastructure VMware

Il est parfois nécessaire d’apporter des modifications à la configuration de l’infrastructure VMware.  La fréquence, actuellement mensuelle ou bimestrielle, devrait diminuer au fil du temps. Ce type de maintenance est généralement possible sans interruption de la consommation des services CloudSimple. Pendant un intervalle de maintenance VMware, les services suivants continuent de fonctionner sans aucun impact :

* Applications et plan de gestion VMware
* Accès vCenter
* Tout ce qui concerne les réseaux et le stockage
* Tout le trafic Azure

## <a name="updates-and-upgrades"></a>Mises à jour et mises à niveau

CloudSimple est responsable de la gestion du cycle de vie des logiciels VMware (ESXi vCenter, PSC et NSX) dans le cloud privé.

Il existe plusieurs mises à jour de logiciels :

* **Correctifs**. Mises à jour de sécurité ou correctifs de bogues publiés par VMware.
* **Mises à jour**. Changement de version mineure d’un composant de la pile VMware.
* **Mises à niveau**. Changement de version majeure d’un composant de la pile VMware.

CloudSimple teste les mises à jour de sécurité critiques dès qu’elles sont mises à disposition par VMware. Conformément au contrat SLA, CloudSimple déploie le correctif de sécurité dans les environnements de cloud privé sous une semaine.

CloudSimple propose des mises à jour de maintenance trimestrielles des composants logiciels VMware. Lorsqu’une nouvelle version majeure d’un logiciel de VMware est disponible, CloudSimple se coordonne avec les clients pour trouver une fenêtre de maintenance adaptée à la mise à niveau.

## <a name="next-steps"></a>Étapes suivantes

[Sauvegarder des machines virtuelles de charge de travail avec Veeam](https://docs.azure.cloudsimple.com/backup-workloads-veeam/).