---
title: Conditions d’intégration pour SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Conditions d’intégration pour SAP HANA sur Azure (grandes instances).
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
ms.date: 01/31/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2f596f44acfd51b1e2449bc77eed6add0d9d90b0
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707392"
---
# <a name="onboarding-requirements"></a>Conditions d’intégration

Cette liste indique la configuration requise pour l’exécution de SAP HANA sur Azure (plus grandes instances).

**Microsoft Azure**

- Un abonnement Azure qui peut être lié à SAP HANA sur Azure (grandes instances).
- Contrat de support Premier Microsoft. Pour plus d’informations sur l’exécution de SAP dans Azure, consultez [SAP Support Note #2015553 – SAP on Microsoft Azure: Support prerequisites](https://launchpad.support.sap.com/#/notes/2015553) (Remarque 2015553 sur la prise en charge de SAP sur Microsoft Azure : conditions préalables à la prise en charge). Si vous utilisez des unités de grande instance HANA incluant 384 UC et plus, vous devez également étendre le contrat de support Premier pour inclure Azure Rapid Response.
- Reconnaissance des références de la grande instance HANA dont vous avez besoin après avoir effectué un exercice de redimensionnement avec SAP.

**Connectivité réseau**

- ExpressRoute entre le système local et Azure : pour connecter votre centre de données local à Azure, assurez-vous de commander au moins une connexion de 1 Gbit/s auprès de votre ISP. La connectivité entre les unités de grande instance HANA et Azure utilise également la technologie ExpressRoute. Cette connexion ExpressRoute entre les unités de grande instance HANA et Azure est incluse dans le prix des unités de grande instance HANA ; ce prix comprend également tous les frais d'entrée et de sortie de données de ce circuit ExpressRoute spécifique. En tant que client, aucuns frais supplémentaires ne vous sont donc facturés au-delà de votre lien ExpressRoute entre votre système local et Azure.

**Système d’exploitation**

- Licences pour SUSE Linux Enterprise Server 12 pour les applications SAP.

   > [!NOTE] 
   > Le système d’exploitation livré par Microsoft n’est pas enregistré avec SUSE. Il n’est pas connecté à une instance de l’outil de gestion des abonnements.

- L’outil de gestion des abonnements SUSE Linux déployé dans Azure sur une machine virtuelle. Cet outil offre la capacité pour SAP HANA sur Azure (grandes instances) d’être inscrit et respectivement mis à jour par SUSE. (Il n’existe aucun accès à internet dans le centre de données de la grande instance HANA.) 
- Licences pour Red Hat Enterprise Linux 6.7 ou 7.x pour SAP HANA.

   > [!NOTE]
   > Le système d’exploitation livré par Microsoft n’est pas enregistré avec Red Hat. Il n’est pas connecté à une instance de l’outil de gestion des abonnements Red Hat.

- Gestionnaire des abonnements Red Hat déployé dans Azure sur une machine virtuelle. Le gestionnaire des abonnements Red Hat permet à SAP HANA sur Azure (grandes instances) d’être inscrit et respectivement mis à jour par Red Hat. (Il n’existe aucun accès direct à internet à partir de l’abonné déployé sur le tampon de grande instance Azure.)
- Le système SAP requiert également un contrat de support avec votre fournisseur Linux. Cette exigence n’est pas supprimée par la solution de la grande instance HANA ni par le fait de l’exécution de Linux dans Azure. Contrairement à certaines images de la galerie Linux Azure, les frais de service ne sont *pas* inclus dans l’offre de solution de la grande instance HANA. Vous êtes tenu de respecter les exigences de SAP en matière de contrat de support avec le distributeur Linux. 
   - Pour SUSE Linux, recherchez les conditions requises des contrats de support dans [SAP Note #1984787 - SUSE Linux Enterprise Server 12: Installation notes](https://launchpad.support.sap.com/#/notes/1984787) (Note SAP 1984787 - SUSE Linux Enterprise Server 12 : notes relatives à l’installation) et [SAP Note #1056161 - SUSE priority support for SAP applications](https://launchpad.support.sap.com/#/notes/1056161) (Note SAP 1056161 : support prioritaire SUSE des applications SAP).
   - Pour Red Hat Linux, vous devez disposer des bons niveaux d’abonnement, y compris la prise en charge et les mises à jour du service des systèmes d’exploitation de la grande instance HANA. Red Hat recommande la solution d’abonnement Red Hat Enterprise Linux pour SAP. Voir https://access.redhat.com/solutions/3082481. 

Pour consulter la matrice de prise en charge des différentes versions SAP HANA avec les différentes versions Linux, reportez-vous à [Remarque SAP n° 2235581](https://launchpad.support.sap.com/#/notes/2235581).

Pour la matrice de compatibilité des versions de système d’exploitation et de microprogramme/pilote HLI, consultez [Mise à niveau du système d’exploitation pour HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> Pour les unités de Type II, seule la version SLES 12 SP2 du système d’exploitation est actuellement prise en charge. 


**Base de données**

- Licences et composants d’installation logiciels pour SAP HANA (édition Enterprise ou Platform).

**Applications**

- Licences et composants d’installation logiciels pour toutes les applications SAP se connectant à SAP HANA et associées aux contrats de support SAP.
- Licences et composants d’installation logiciels pour toutes les applications autres que SAP utilisées avec SAP HANA sur Azure (grandes instances) et associées aux contrats de support.

**Compétences**

- Expérience et connaissances relatives à Azure IaaS et ses composants.
- Expérience et connaissances du mode de déploiement d’une charge de travail SAP dans Azure.
- Personnel certifié pour l’installation de SAP HANA.
- Compétences en architecture SAP pour concevoir la haute disponibilité et la récupération d’urgence autour de SAP HANA.

**SAP**

- Vous devez être un client SAP et disposer d’un contrat de support auprès de SAP.
- En particulier pour les implémentations de références SKU de grande instance HANA de classe Type II, consultez SAP pour connaître les versions de SAP HANA et les configurations éventuelles sur du matériel monté en puissance de grande taille.

**Étapes suivantes**
- Voir [Architecture SAP HANA (grandes instances) sur Azure](hana-architecture.md)