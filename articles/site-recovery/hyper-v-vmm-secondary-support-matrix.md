---
title: Matrice de prise en charge pour la récupération d’urgence de machines virtuelles Hyper-V dans des clouds VMM vers un site secondaire avec Azure Site Recovery | Microsoft Docs
description: Résume la prise en charge de la réplication des machines virtuelles Hyper-V dans des clouds VMM vers un site secondaire avec Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: e8b8f9856fe7e0fa591ceb42aab97e92642b6098
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399363"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Matrice de prise en charge pour la récupération d’urgence de machines virtuelles Hyper-V vers un site secondaire

Cet article résume ce qui est pris en charge lorsque vous utilisez le service [Azure Site Recovery](site-recovery-overview.md) pour répliquer des machines virtuelles Hyper-V gérées dans des clouds System Center Virtual Machine Manager (VMM) vers un site secondaire. Si vous souhaitez répliquer des machines virtuelles Hyper-V vers Azure, passez en revue [cette matrice de prise en charge](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Vous pouvez uniquement répliquer vers un site secondaire lorsque vos hôtes Hyper-V sont gérés dans des clouds VMM.

  

## <a name="host-servers"></a>Serveurs hôtes

**Système d’exploitation** | **Détails**
--- | ---
Windows Server 2012 R2 | Les serveurs doivent exécuter les dernières mises à jour.
Windows Server 2016 |  Les clouds VMM 2016 qui combinent des hôtes Windows Server 2016 et 2012 R2 ne sont actuellement pas pris en charge.<br/><br/> Les déploiements qui ont été mis à niveau à partir de System Center 2012 R2 VMM 2012 R2 vers System Center 2016 ne sont pas pris en charge actuellement.


## <a name="replicated-vm-support"></a>Prise en charge de la machine virtuelle répliquée

Le tableau suivant récapitule la prise en charge du système d’exploitation pour les machines répliquées avec Site Recovery. Toute charge de travail peut être exécutée sur le système d’exploitation pris en charge.

**Version de Windows** | **Hyper-V (avec VMM)**
--- | ---
Windows Server 2016 | Tout système d’exploitation invité [pris en charge par Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) sur Windows Server 2016 
Windows Server 2012 R2 | Tout système d’exploitation invité [pris en charge par Hyper-V](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) sur Windows Server 2012 R2

## <a name="linux-machine-storage"></a>Stockage de machine Linux

Seules les machines Linux avec le stockage suivant peuvent être répliquées :

- Système de fichiers (EXT3, ETX4, ReiserFS, XFS).
- Logiciel multichemin-device Mapper.
- Gestionnaire de volume (LVM2).
- Les serveurs physiques avec stockage de contrôleur HP CCISS ne sont pas pris en charge.
- Le système de fichiers ReiserFS n’est pris en charge que sur SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Configuration du réseau - machines virtuelles hôtes/invitées

**Configuration** | **Pris en charge**  
--- | --- 
Hôte - Association de cartes réseau | OUI 
Hôte -VLAN | OUI 
Hôte - IPv4 | OUI 
Hôte - IPv6 | Non 
Machine virtuelle invitée - Association de cartes réseau | Non
Machine virtuelle invitée - IPv4 | OUI
Machine virtuelle invitée - IPv6 | Non
Machine virtuelle invitée - Windows/Linux - Adresse IP statique | OUI
Machine virtuelle invitée - Plusieurs cartes réseau | OUI


## <a name="storage"></a>Stockage

### <a name="host-storage"></a>Stockage hôte

**Stockage (hôte)** | **Pris en charge**
--- | --- 
NFS | N/A
SMB 3.0 |  OUI
SAN (ISCSI) | OUI
Chemins d’accès multiples (MPIO) | OUI

### <a name="guest-or-physical-server-storage"></a>Stockage sur serveur physique ou invité

**Configuration** | **Pris en charge**
--- | --- | 
VMDK |  N/A
VHD/VHDX | Oui (jusqu’à 16 disques)
Machine virtuelle de 2e génération | OUI
Disque de cluster partagé | Non
Disque chiffré | Non
UEFI| N/A
NFS | Non
SMB 3.0 | Non
RDM | N/A
Disque > 1 To | OUI
Volume avec disque à bandes > 1 To<br/><br/> LVM | OUI
Espaces de stockage | OUI
Ajout/suppression de disque à chaud | Non
Exclure le disque | OUI
Chemins d’accès multiples (MPIO) | OUI

## <a name="vaults"></a>Coffres

**Action** | **Pris en charge**
--- | --- 
Déplacer les coffres entre plusieurs groupes de ressources (dans ou entre les différents abonnements) |  Non
Déplacer le stockage, les réseaux, les machines virtuelles Azure entre des groupes de ressources (dans ou entre les différents abonnements) | Non

## <a name="azure-site-recovery-provider"></a>Fournisseur Azure Site Recovery

Le fournisseur coordonne les communications entre les serveurs VMM. 

**La plus récente** | **Mises à jour**
--- | --- 
5.1.19 ([disponible sur le portail](https://aka.ms/downloaddra)) | [Fonctionnalités et correctifs récents](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>Étapes suivantes

[Répliquer des machines virtuelles Hyper-V résidant dans des clouds VMM vers un site secondaire](tutorial-vmm-to-vmm.md)

