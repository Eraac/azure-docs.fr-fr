---
title: Mise à niveau du système d’exploitation pour SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Effectuer la mise à niveau du système d’exploitation pour SAP HANA sur Azure (grandes instances)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5583a633c64943185f874e1c0ff80f654010aa53
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710012"
---
# <a name="operating-system-upgrade"></a>Mise à niveau du système d’exploitation
Ce document décrit en détail les mises à niveau du système d’exploitation sur les grandes instances HANA.

>[!NOTE]
>La mise à niveau du système d’exploitation est la responsabilité des clients. Le support opérations de Microsoft peut vous orienter vers les endroits clés à surveiller pendant la mise à niveau. Consultez également le fournisseur de votre système d’exploitation avant de planifier une mise à niveau.

Au moment du provisionnement de l’unité HLI, l’équipe des opérations de Microsoft installe le système d’exploitation. Au fil du temps, il vous appartient d’effectuer la maintenance du système d’exploitation sur l’unité HLI : mise à jour corrective, réglage, mise à niveau, etc.

Avant d’apporter des modifications majeures au système d’exploitation (par exemple, une mise à niveau de SP1 vers SP2), vous devez contacter l’équipe des opérations de Microsoft en ouvrant un ticket de support.

Veuillez inclure sur votre ticket :

* Votre ID d’abonnement HLI.
* Le nom de votre serveur.
* Le niveau de correctif que vous envisagez d'appliquer.
* La date à laquelle vous envisagez cette modification. 

Nous vous conseillons d'ouvrir ce ticket au moins une semaine avant la date de mise à niveau souhaitée pour permettre à l'équipe de vérifier si une mise à niveau du microprogramme est requise dans le panneau de votre serveur.


Pour consulter la matrice de prise en charge des différentes versions SAP HANA avec les différentes versions Linux, reportez-vous à [Remarque SAP n° 2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Problèmes connus

Voici quelques problèmes affectant couramment la mise à niveau :
- Dans la référence SKU de classe de type II, le logiciel SFS est supprimé après la mise à niveau du système d’exploitation. Vous devez réinstaller le logiciel SFS compatible après la mise à niveau du système d’exploitation.
- Une version antérieure des pilotes de cartes Ethernet (ENIC et FNIC) est restaurée. Vous devez réinstaller la version compatible des pilotes après la mise à niveau.

## <a name="next-steps"></a>Étapes suivantes
- Pour la classe de références SKU de type I de sauvegarde du système d’exploitation, consultez [Sauvegarde et restauration](hana-overview-high-availability-disaster-recovery.md).
- Pour la classe de références SKU de type II, consultez [Sauvegarde du système d’exploitation pour références SKU de type II](os-backup-type-ii-skus.md).
