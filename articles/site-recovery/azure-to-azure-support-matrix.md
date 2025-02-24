---
title: Matrice de prise en charge pour la récupération d’urgence de machines virtuelles Azure entre des régions Azure avec Azure Site Recovery | Microsoft Docs
description: Récapitule les conditions préalables et la prise en charge de la récupération d'urgence des machines virtuelles Azure d'une région vers une autre avec Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 06/27/2019
ms.author: raynew
ms.openlocfilehash: 55275144746dbc1a3ead7c7c12a6901ab6f9269e
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514134"
---
# <a name="support-matrix-for-replicating-azure-vms-from-one-region-to-another"></a>Matrice de support pour la réplication de machines virtuelles Azure d'une région vers une autre

Cet article récapitule la prise en charge et les conditions préalables lorsque vous définissez la récupération d’urgence de machines virtuelles Azure d’une région Azure vers une autre, avec le service [Azure Site Recovery](site-recovery-overview.md).


## <a name="deployment-method-support"></a>Prise en charge de la méthode de déploiement

**Déploiement** |  **Support**
--- | ---
**Portail Azure** | Pris en charge.
**PowerShell** | Pris en charge. [En savoir plus](azure-to-azure-powershell.md)
**API REST** | Pris en charge.
**INTERFACE DE LIGNE DE COMMANDE** | Non prise en charge pour le moment


## <a name="resource-support"></a>Prise en charge des ressources

**Action de ressource** | **Détails**
--- | --- | ---
**Déplacer des coffre entre plusieurs groupes de ressources** | Non pris en charge
**Déplacer le calcul/le stockage/les ressources réseau entre plusieurs groupes de ressources** | Non pris en charge.<br/><br/> Si vous déplacez une machine virtuelle ou des composants associés tels que le stockage/réseau après la réplication de la machine virtuelle, vous devez désactiver et réactiver la réplication pour la machine virtuelle.
**Répliquer des machines virtuelles Azure d’un abonnement à un autre pour la reprise d’activité** | Pris en charge à l’intérieur du même locataire Azure Active Directory.
**Migrer des machines virtuelles entre des régions dans les clusters géographiques pris en charge (dans un même abonnement et entre plusieurs abonnements)** | Pris en charge à l’intérieur du même locataire Azure Active Directory.
**Migrer des machines virtuelles au sein de la même région** | Non pris en charge.

## <a name="region-support"></a>Prise en charge de la région

Vous pouvez répliquer et restaurer des machines virtuelles entre deux régions appartenant au même cluster géographique. Les clusters géographiques sont définis en gardant à l’esprit la en souveraineté et la latence des données.


**Cluster géographique** | **Régions Azure**
-- | --
Amérique | Canada de l’Est, Canada du Centre, Sud du Centre des États-Unis, Ouest du Centre des États-Unis, États-Unis de l’Est, États-Unis de l’Est 2, États-Unis de l’Ouest, États-Unis de l’Ouest 2, États-Unis du Centre, Nord du Centre des États-Unis
Europe | Royaume-Uni Ouest, Royaume-Uni Sud, Europe Nord, Europe Ouest, France Centre, France Sud, Afrique du Sud Ouest,Afrique du Sud Nord
Asie | Inde du Sud, Centre de l’Inde, Asie du Sud-Est, Asie de l’Est, Japon de l’Est, Japon de l’Ouest, Centre de la Corée, Corée du Sud
Australie   | Australie Est, Australie Sud-Est, Australie Centre, Australie Centre 2
Azure Government    | Gouvernement des États-Unis - Virginie, Gouvernement des États-Unis - Iowa, Gouvernement des États-Unis - Arizona, Gouvernement des États-Unis - Texas, US DoD Est, US DoD Centre 
Allemagne | Centre de l’Allemagne, Nord-Est de l’Allemagne
Chine | Chine Est, Chine Nord, Chine Nord 2, Chine Est 2

>[!NOTE]
>
> - Pour **Brésil Sud**, vous pouvez répliquer et basculer vers ces régions : USA Centre Sud, USA Centre-Ouest, USA Est, USA Est 2, USA Ouest, USA Ouest 2 et USA Centre Nord.
> - Brésil Sud fait uniquement office de région source à partir de laquelle les machines virtuelles peuvent répliquer à l’aide de Site Recovery. Elle ne peut pas faire office de région cible et ce, en raison de problèmes de latence dus aux distances géographiques.
> - Vous pouvez travailler dans des régions pour lesquelles vous disposez d'un accès approprié.
> - Si la région où vous souhaitez créer un coffre ne s'affiche pas, assurez-vous que votre abonnement dispose d'un accès lui permettant de créer des ressources dans cette région.
> - Si vous ne parvenez pas à voir une région au sein d’un cluster géographique lorsque vous activez la réplication, assurez-vous que votre abonnement dispose des autorisations nécessaires pour créer des machines virtuelles dans cette région.



## <a name="cache-storage"></a>Stockage du cache

Le tableau suivant récapitule la prise en charge du compte de stockage du cache utilisé par Site Recovery lors de la réplication.

**Paramètre** | **Support** | **Détails**
--- | --- | ---
Comptes de stockage V2 à usage général (niveaux chaud et froid) | Pris en charge | L'utilisation de GPv2 est déconseillée, car les coûts de transaction pour V2 sont sensiblement plus élevés que pour les comptes de stockage V1.
Pare-feux du Stockage Azure pour réseaux virtuels  | Pris en charge | Si vous utilisez un compte de stockage de cache ou cible autorisé par le pare-feu, vérifiez que l’option « [Autoriser les services Microsoft approuvés](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) » est activée.


## <a name="replicated-machine-operating-systems"></a>Systèmes d’exploitation de machine répliquée

Site Recovery prend en charge la réplication de machines virtuelles Azure exécutant les systèmes d’exploitation répertoriés dans cette section.

### <a name="windows"></a>Windows

**Système d’exploitation** | **Détails**
--- | ---
Windows Server 2019 | Server Core, Server avec Expérience utilisateur
Windows Server 2016  | Server Core, Server avec Expérience utilisateur
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | Exécutant SP1 ou version ultérieure
Windows 10 (x64) |
Windows 8.1 (x64) |
Windows 8 (x64) |
Windows 7 (x64) | Exécutant SP1 ou version ultérieure (Windows 7 RTM n'est pas pris en charge)

#### <a name="linux"></a>Linux

**Système d’exploitation** | **Détails**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6  
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6
Serveur LTS Ubuntu 14.04 | [Versions du noyau prises en charge](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Serveur LTS Ubuntu 16.04 | [Version du noyau prise en charge](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Sur les serveurs Ubuntu utilisant l’authentification et la connexion basées sur un mot de passe, et le package cloud-init pour configurer des machines virtuelles cloud, la connexion basée sur un mot de passe peut être désactivée lors du basculement (en fonction de la configuration de cloudinit). La connexion basée sur un mot de passe peut être réactivée sur la machine virtuelle en réinitialisant le mot de passe dans le menu Support > Résolution des problèmes > Paramètres (de la machine virtuelle basculée sur le portail Azure).
Debian 7 | [Versions du noyau prises en charge](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Versions du noyau prises en charge](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Versions du noyau prises en charge)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> La mise à niveau des machines de réplication SP3 vers SP4 n’est pas prise en charge. Si une machine répliquée a été mise à niveau, vous devez désactiver la réplication et la réactiver après la mise à niveau.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5 <br/><br/> Exécutant le noyau compatible Red Hat ou le noyau Unbreakable Enterprise Kernel Release 3 (UEK3).


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versions du noyau Ubuntu prises en charge pour les machines virtuelles Azure

**Version release** | **Version du service Mobilité** | **Version du noyau** |
--- | --- | --- |
14.04 LTS | 9.25 | 3.13.0-24-generic à 3.13.0-169-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-146-generic,<br/>4.15.0-1023-azure à 4.15.0-1042-azure |
14.04 LTS | 9.24 | 3.13.0-24-generic à 3.13.0-167-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-143-generic,<br/>4.15.0-1023-azure à 4.15.0-1040-azure |
14.04 LTS | 9.23 | 3.13.0-24-generic à 3.13.0-165-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-142-generic,<br/>4.15.0-1023-azure à 4.15.0-1037-azure |
14.04 LTS | 9.22 | 3.13.0-24-generic à 3.13.0-164-generic,<br/>3.16.0-25-generic à 3.16.0-77-generic,<br/>3.19.0-18-generic à 3.19.0-80-generic,<br/>4.2.0-18-generic à 4.2.0-42-generic,<br/>4.4.0-21-generic à 4.4.0-140-generic,<br/>4.15.0-1023-azure à 4.15.0-1036-azure |
|||
LTS 16.04 | 9.25 | 4.4.0-21-generic à 4.4.0-146-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.15.0-13-generic à 4.15.0-48-generic<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure <br/>4.15.0-1012-azure à 4.15.0-1042-azure|
LTS 16.04 | 9.24 | 4.4.0-21-generic à 4.4.0-143-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.15.0-13-generic à 4.15.0-46-generic<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure <br/>4.15.0-1012-azure à 4.15.0-1040-azure|
LTS 16.04 | 9.23 | 4.4.0-21-generic à 4.4.0-142-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.15.0-13-generic à 4.15.0-45-generic<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure <br/>4.15.0-1012-azure à 4.15.0-1037-azure|
LTS 16.04 | 9.22 | 4.4.0-21-generic à 4.4.0-140-generic,<br/>4.8.0-34-generic à 4.8.0-58-generic,<br/>4.10.0-14-generic à 4.10.0-42-generic,<br/>4.11.0-13-generic à 4.11.0-14-generic,<br/>4.13.0-16-generic à 4.13.0-45-generic,<br/>4.15.0-13-generic à 4.15.0-43-generic<br/>4.11.0-1009-azure à 4.11.0-1016-azure,<br/>4.13.0-1005-azure à 4.13.0-1018-azure <br/>4.15.0-1012-azure à 4.15.0-1036-azure|

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Versions du noyau Debian prises en charge pour les machines virtuelles Azure

**Version release** | **Version du service Mobilité** | **Version du noyau** |
--- | --- | --- |
Debian 7 | 9.22, 9.23, 9.24, 9.25 | 3.2.0-4-amd64 à 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.25 | 3.16.0-4-amd64 à 3.16.0-8-amd64, 4.9.0-0.bpo.4-amd64 à 4.9.0-0.bpo.8-amd64 |
Debian 8 | 9.22, 9.23, 9.24 | 3.16.0-4-amd64 à 3.16.0-7-amd64, 4.9.0-0.bpo.4-amd64 à 4.9.0-0.bpo.8-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Versions du noyau SUSE Linux Enterprise Server 12 prises en charge pour les machines virtuelles Azure

**Version release** | **Version du service Mobilité** | **Version du noyau** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3,SP4) | 9.25 | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default à 4.4.121-92.104-default</br></br>SP3 4.4.73-5-default à 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-azure à 4.4.176-4.25-azure</br></br>SP4 4.12.14-94.41-default à 4.12.14-95.13-default</br>SP4 4.12.14-6.3-azure à 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3,SP4) | 9.24 | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default à 4.4.121-92.104-default</br></br>SP3 4.4.73-5-default à 4.4.176-94.88-default</br></br>SP4 4.12.14-94.41-default à 4.12.14-95.13-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3,SP4) | 9.23 | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default à 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default à 4.4.162-94.69-default</br></br>SP4 4.12.14-94.41-default à 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.22 | SP1 3.12.49-11-default à 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default à 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default à 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default à 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default à 4.4.162-94.72-default |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Machines répliquées - Stockage invité/système de fichiers Linux

* Systèmes de fichiers : ext3, ext4, ReiserFS (Suse Linux Enterprise Server uniquement), XFS, BTRFS
* Gestionnaire de volume : LVM2
* Logiciel multichemin : Mappeur d’appareil


## <a name="replicated-machines---compute-settings"></a>Machines répliquées - Paramètres de calcul

**Paramètre** | **Support** | **Détails**
--- | --- | ---
Size | N’importe quelle taille de machine virtuelle Azure avec au moins 2 cœurs d’UC et 1 Go de RAM | Consultez [Tailles de machine virtuelle Azure](../virtual-machines/windows/sizes.md).
Groupes à haute disponibilité | Pris en charge | Si vous activez la réplication pour une machine virtuelle Azure avec les options par défaut, un groupe à haute disponibilité est créé automatiquement, selon les paramètres de la région source. Vous pouvez modifier ces paramètres.
Zones de disponibilité | Pris en charge |
HUB (Hybrid Use Benefit) | Pris en charge | Si la machine virtuelle source a une licence HUB activée, une machine virtuelle de basculement ou de test de basculement utilise également la licence HUB.
Groupes de machines virtuelles identiques (VMSS) | Non pris en charge |
Images de la galerie Azure - Publiées par Microsoft | Pris en charge | Prises en charge si la machine virtuelle s’exécute sur un système d’exploitation pris en charge.
Images de la galerie Azure - Publiées par un tiers | Pris en charge | Prises en charge si la machine virtuelle s’exécute sur un système d’exploitation pris en charge.
Images personnalisées - Publiées par un tiers | Pris en charge | Prises en charge si la machine virtuelle s’exécute sur un système d’exploitation pris en charge.
Machines virtuelles migrées à l’aide de Site Recovery | Pris en charge | Si un ordinateur physique une machine virtuelle VMware a été migré(e) vers Azure à l’aide de Site Recovery, vous devez désinstaller l’ancienne version du service Mobilité sur l’ordinateur et redémarrer celui-ci avant de le répliquer vers une autre région Azure.
Stratégies RBAC | Non pris en charge | Les stratégies de contrôle d’accès en fonction du rôle (RBAC) sur les machines virtuelles ne sont pas répliquées sur la machine virtuelle de basculement dans la région cible.
Extensions | Non pris en charge | Les extensions ne sont pas répliquées sur la machine virtuelle de basculement dans la région cible. Elles doivent être installées manuellement après le basculement.

## <a name="replicated-machines---disk-actions"></a>Machines répliquées - Actions de disque

**Action** | **Détails**
-- | ---
Redimensionner le disque sur la machine virtuelle répliquée | Pris en charge
Ajouter un disque à une machine virtuelle répliquée | Pris en charge

## <a name="replicated-machines---storage"></a>Machines répliquées - Stockage

Ce tableau récapitule la prise en charge du disque du système d’exploitation, du disque de données et du disque temporaire de la machine virtuelle Azure.

- Il est important d’observer les limites des disques de machine virtuelle et les cibles des machines virtuelles [Linux](../virtual-machines/linux/disk-scalability-targets.md) et [Windows](../virtual-machines/windows/disk-scalability-targets.md) pour éviter tout problème de performances.
- Si vous déployez avec les paramètres par défaut, Site Recovery crée automatiquement les disques et les comptes de stockage en fonction des paramètres de la source.
- Si vous les personnalisez, veillez à respecter les instructions.

**Composant** | **Support** | **Détails**
--- | --- | ---
Taille maximale du disque du système d’exploitation | 2048 GB | [En savoir plus](../virtual-machines/windows/managed-disks-overview.md) sur les disques de machines virtuelles.
Disque temporaire | Non pris en charge | Le disque temporaire est toujours exclu de la réplication.<br/><br/> Ne conservez pas de données persistantes sur le disque temporaire. [Plus d’informations](../virtual-machines/windows/managed-disks-overview.md)
Taille maximale du disque de données | 4095 Go |
Taille minimale du disque de données | Aucune restriction pour les disques non managés. 2 Go pour les disques managés | 
Nombre maximal de disques de données | Jusqu’à 64, en adéquation avec la prise en charge pour une taille spécifique de machine virtuelle Azure | [En savoir plus](../virtual-machines/windows/sizes.md) sur les tailles de machines virtuelles.
Taux de modification du disque de données | 10 Mbits/s maximum par disque pour le stockage Premium. 2 Mbits/s maximum par disque pour le stockage Standard. | Si le taux moyen de modification des données sur le disque est en permanence supérieur à la valeur maximale, la réplication ne pourra pas suivre.<br/><br/>  Toutefois, si la valeur maximale est dépassée de manière sporadique, la réplication peut suivre, mais les points de récupération pourraient être légèrement différés.
Disque de données - Compte de stockage Standard | Pris en charge |
Disque de données - Compte de stockage Premium | Pris en charge | Si une machine virtuelle a des disques répartis sur des comptes de stockage Standard et Premium, vous pouvez sélectionner un compte de stockage cible différent pour chaque disque afin d’être sûr d’avoir la même configuration de stockage dans la région cible.
Disque managé - Standard | Pris en charge dans les régions Azure dans lesquelles Azure Site Recovery est pris en charge. |
Disque managé - Premium | Pris en charge dans les régions Azure dans lesquelles Azure Site Recovery est pris en charge. |
SSD Standard | Pris en charge |
Redondance | LRS et GRS sont pris en charge.<br/><br/> ZRS n’est pas pris en charge.
Stockage à froid et à chaud | Non pris en charge | Les disques de machine virtuelle ne sont pas pris en charge sur le stockage à froid et à chaud
Espaces de stockage | Pris en charge |
Chiffrement au repos (SSE) | Pris en charge | SSE est le paramètre par défaut sur les comptes de stockage.   
Azure Disk Encryption (ADE) pour système d’exploitation Windows | Prise en charge des machines virtuelles activées pour le [chiffrement avec Azure AD app](https://aka.ms/ade-aad-app) |
Azure Disk Encryption (ADE) pour système d’exploitation Linux | Non pris en charge |
Ajout à chaud | Pris en charge | L'activation de la réplication pour un disque de données que vous ajoutez à une machine virtuelle Azure répliquée est prise en charge pour les machines virtuelles utilisant des disques managés.
Retrait de disque à chaud | Non pris en charge | Si vous retirez un disque de données de la machine virtuelle, vous devez désactiver la réplication puis la réactiver pour la machine virtuelle.
Exclure le disque | Pris en charge. Vous devez utiliser [Powershell](azure-to-azure-exclude-disks.md) pour configurer. |  Les disques temporaires sont exclus par défaut.
Espaces de stockage direct  | Pris en charge pour les points de récupération cohérents d’incident. Les points de récupération cohérents d’incident ne sont pas pris en charge. |
Serveur de fichiers avec montée en puissance parallèle  | Pris en charge pour les points de récupération cohérents d’incident. Les points de récupération cohérents d’incident ne sont pas pris en charge. |
LRS | Prise en charge |
GRS | Prise en charge |
RA-GRS | Prise en charge |
ZRS | Non pris en charge |
Stockage à froid et à chaud | Non pris en charge | Les disques de machine virtuelle ne sont pas pris en charge sur le stockage à froid et à chaud.
Pare-feux du Stockage Azure pour réseaux virtuels  | Pris en charge | Si vous limitez l’accès au réseau virtuel aux comptes de stockage, activez l'option [Autoriser les services Microsoft de confiance](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Comptes de stockage V2 à usage général (niveaux chaud et froid) | OUI | Augmentation significative des coûts de transaction par rapport aux comptes de stockage V1 à usage général

>[!IMPORTANT]
> Pour éviter les problèmes de performances, assurez-vous de vous respecter les valeurs d'évolutivité et de performances des disques VM pour les machines virtuelles [Linux](../virtual-machines/linux/disk-scalability-targets.md) ou [Windows](../virtual-machines/windows/disk-scalability-targets.md). Si vous utilisez les paramètres par défaut, Site Recovery crée les disques et comptes de stockage requis en fonction de la configuration de la source. Si vous personnalisez et sélectionnez vos propres paramètres, suivez les cibles de scalabilité et de performances de disque de vos machines virtuelles sources.

## <a name="limits-and-data-change-rates"></a>Limites et taux de changement des données

Le tableau suivant récapitule les limites de Site Recovery.

- Ces limites sont basées sur nos tests, mais ne couvrent pas toutes les combinaisons d’E/S d’application possibles.
- Les résultats réels varient en fonction de la combinaison d’E/S de votre application.
- Il existe deux limites à prendre en compte, le taux d’activité de données par disque et le taux d’activité de données par machine virtuelle.
- Par exemple, si nous utilisons le disque Premium P20 comme décrit dans le tableau ci-dessous, Site Recovery peut gérer un taux d’activité des données de 5 Mo/s par disque pour cinq disques maximum par machine virtuelle, en raison de la limite de taux d’activité total des données de 25 Mo/s par machine virtuelle.

**Cible de stockage** | **E/S moyennes de disque source** |**Activité des données moyenne de disque source** | **Total de l’activité des données de disque source par jour**
---|---|---|---
Stockage Standard | 8 Ko | 2 Mo/s | 168 Go par disque
Disque Premium P10 ou P15 | 8 Ko  | 2 Mo/s | 168 Go par disque
Disque Premium P10 ou P15 | 16 Ko | 4 Mo/s |  336 Go par disque
Disque Premium P10 ou P15 | 32 Ko ou plus | 8 Mo/s | 672 Go par disque
Disque Premium P20 ou P30 ou P40 ou P50 | 8 Ko    | 5 Mo/s | 421 Go par disque
Disque Premium P20 ou P30 ou P40 ou P50 | 16 Ko ou plus |20 Mo/s | 1 684 Go par disque

## <a name="replicated-machines---networking"></a>Machines répliquées - Mise en réseau
**Paramètre** | **Support** | **Détails**
--- | --- | ---
Carte d’interface réseau | Nombre maximal pris en charge pour une taille de machine virtuelle Azure spécifique | Les cartes réseau sont créées lors de la création de la machine virtuelle pendant le basculement.<br/><br/> Le nombre de cartes réseau sur la machine virtuelle de basculement dépend du nombre de cartes réseau que possède la machine virtuelle source au moment de l’activation de la réplication. Si vous ajoutez ou supprimez une carte réseau après l’activation de la réplication, cela n’affecte pas le nombre de cartes réseau sur la machine virtuelle répliquée après le basculement. Notez qu'il n'est pas garanti que l'ordre des cartes réseau après basculement soit le même que l’ordre d’origine.
Équilibreur de charge Internet | Pris en charge | Associez l’équilibreur de charge préconfiguré à l’aide d’un script Azure Automation dans un plan de récupération.
Équilibreur de charge interne | Pris en charge | Associez l’équilibreur de charge préconfiguré à l’aide d’un script Azure Automation dans un plan de récupération.
Adresse IP publique | Pris en charge | Associez une adresse IP publique existante à la carte réseau. Ou, créez une adresse IP publique et associez-la à la carte réseau à l’aide d’un script Azure Automation dans un plan de récupération.
Groupe de sécurité réseau (NSG) sur la carte réseau | Pris en charge | Associez le groupe de sécurité réseau à la carte réseau à l’aide d’un script Azure Automation dans un plan de récupération.
Groupe de sécurité réseau (NSG) sur le sous-réseau | Pris en charge | Associez le groupe de sécurité réseau au sous-réseau à l’aide d’un script Azure Automation dans un plan de récupération.
Adresse IP (statique) réservée | Pris en charge | Si la carte réseau sur la machine virtuelle source a une adresse IP statique et que le sous-réseau cible a la même adresse IP disponible, celle-ci est affectée à la machine virtuelle de basculement.<br/><br/> Si le sous-réseau cible n’a pas la même adresse IP disponible, l’une des adresses IP disponibles sur le sous-réseau est réservée à la machine virtuelle.<br/><br/> Vous pouvez également spécifier une adresse IP fixe et un sous-réseau dans **Éléments répliqués** > **Paramètres** > **Calcul et réseau** > **Interfaces réseau**.
Adresse IP dynamique | Pris en charge | Si la carte réseau sur la machine virtuelle source a l’adressage IP dynamique, la carte réseau sur la machine virtuelle de basculement est également dynamique par défaut.<br/><br/> Vous pouvez remplacer cela par une adresse IP fixe si nécessaire.
Plusieurs adresses IP | Non pris en charge | Lorsque vous basculez une machine virtuelle équipée d'une carte réseau avec plusieurs adresses IP, seule l’adresse IP principale de la carte réseau de la région source est conservée. Pour attribuer plusieurs adresses IP, vous pouvez ajouter des machines virtuelles à un [plan de récupération](recovery-plan-overview.md) et joindre un script pour attribuer des adresses IP supplémentaires à ce plan. Vous pouvez également procéder à la modification manuellement ou à l'aide d'un script après basculement. 
Traffic Manager     | Pris en charge | Vous pouvez préconfigurer Traffic Manager pour que le trafic soit acheminé vers le point de terminaison dans la région source de manière régulière et vers le point de terminaison dans la région cible en cas de basculement.
Azure DNS | Pris en charge |
Système DNS personnalisé  | Pris en charge |
Proxy non authentifié | Pris en charge | [En savoir plus](site-recovery-azure-to-azure-networking-guidance.md)    
Proxy authentifié | Non pris en charge | Si la machine virtuelle utilise un proxy authentifié pour la connectivité sortante, elle ne peut pas être répliquée à l’aide d’Azure Site Recovery.    
Connexion VPN de site à site à un environnement local<br/><br/>(avec ou sans ExpressRoute)| Pris en charge | Vérifiez que les itinéraires définis par l’utilisateur et les groupes de sécurité réseau sont configurés de telle sorte que le trafic Site Recovery ne soit pas acheminé vers l’infrastructure locale. [En savoir plus](site-recovery-azure-to-azure-networking-guidance.md)    
Connexion de réseau virtuel à réseau virtuel | Pris en charge | [En savoir plus](site-recovery-azure-to-azure-networking-guidance.md)  
Points de terminaison de service de réseau virtuel | Pris en charge | Si vous limitez l’accès au réseau virtuel aux comptes de stockage, assurez-vous que les services Microsoft de confiance sont autorisés à accéder au compte de stockage.
Mise en réseau accélérée | Pris en charge | L’accélération réseau doit être activée sur la machine virtuelle source. [Plus d’informations](azure-vm-disaster-recovery-with-accelerated-networking.md)



## <a name="next-steps"></a>Étapes suivantes
- Lisez la [mise en réseau pour la réplication des machines virtuelles Azure](site-recovery-azure-to-azure-networking-guidance.md).
- Déployez la récupération d’urgence en [répliquant des machines virtuelles Azure](site-recovery-azure-to-azure.md).
