---
title: Prise en charge de l’utilisation d’Azure Site Recovery avec Sauvegarde Azure | Microsoft Docs
description: Fournit une vue d’ensemble de la façon dont Azure Site Recovery et Sauvegarde Azure peuvent être utilisés conjointement.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: sideeksh
ms.openlocfilehash: e902f70225ec0eb0caa98f7e19a16c87220cb6f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61035761"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Prise en charge de l’utilisation de Site Recovery avec Sauvegarde Azure

Cet article résume la prise en charge de l’utilisation conjointe du [service Site Recovery](site-recovery-overview.md) avec le [service Sauvegarde Azure](https://docs.microsoft.com/azure/backup/backup-overview).

**Action** | **Prise en charge de Site Recovery** | **Détails**
--- | --- | ---
**Déployer les services ensemble** | Pris en charge | Les services sont interopérables et peuvent être configurés conjointement.
**Sauvegarde/restauration de fichier** | Pris en charge | Lorsque les fonctions de sauvegarde et de réplication sont activées pour une machine virtuelle, et que les sauvegardes sont effectuées, il n’existe aucun problème de restauration de fichiers sur les machines virtuelles ou le groupe de machines virtuelles côté source. La réplication se poursuit comme d’habitude, sans que l’intégrité de la réplication s’en trouve modifiée.
**Sauvegarde/restauration de disque** | Aucune prise en charge actuelle | Si vous restaurez un disque sauvegardé, vous devez à nouveau désactiver et réactiver la réplication pour la machine virtuelle.
**Sauvegarde/restauration de machine virtuelle** | Aucune prise en charge actuelle | Si vous sauvegardez ou restaurez une machine virtuelle ou un groupe de machines virtuelles, vous devez désactiver et réactiver la réplication pour ces machines virtuelles.  


