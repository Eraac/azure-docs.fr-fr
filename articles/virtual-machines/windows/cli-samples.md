---
title: Exemples d’interface de ligne de commande Azure sur Windows | Microsoft Docs
description: Exemples d’interface de ligne de commande Azure sur Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f0fca7a36caa84d22e07dad01089209752efa6fa
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719061"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Exemples d’interface de ligne de commande Azure pour machines virtuelles Windows

Le tableau suivant contient des liens vers des scripts Bash créés à l’aide de l’interface de ligne de commande Azure pour déployer des machines virtuelles Windows.

| | |
|---|---|
|**Créer des machines virtuelles**||
| [Créer une machine virtuelle](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée une machine virtuelle Windows avec une configuration minimale. |
| [Créer une machine virtuelle entièrement configurée](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée un groupe de ressources, une machine virtuelle et toutes les ressources associées.|
| [Créer des machines virtuelles hautement disponibles](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée plusieurs machines virtuelles dans une configuration haute disponibilité avec équilibrage de charge. |
| [Créer une machine virtuelle et exécuter le script de configuration](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée une machine virtuelle et utilise l’extension de script personnalisé Azure pour installer IIS. |
| [Créer une machine virtuelle et exécuter la configuration DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée une machine virtuelle et utilise l’extension DSC Azure pour installer IIS. |
|**Gérer le stockage**||
| [Créer un disque géré à partir d’un disque dur virtuel](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée un disque managé à partir d’un disque dur virtuel spécialisé en tant que disque de système d’exploitation ou d’un disque dur virtuel de données en tant que disque de données.  |
| [Créer un disque géré à partir d’une capture instantanée](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée un disque géré à partir d’une capture instantanée. |
| [Copier le disque géré vers le même abonnement ou un abonnement différent](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Copie le disque géré vers le même abonnement ou un abonnement différent, mais dans la même région que le disque géré parent. 
| [Exporter une capture instantanée en tant que disque dur virtuel vers un compte de stockage](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporte une capture instantanée gérée en tant que disque dur virtuel vers un compte de stockage dans une région différente. |
| [Exporter le disque dur virtuel d’un disque managé vers un compte de stockage](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporte le disque dur virtuel sous-jacent d’un disque managé vers un compte de stockage situé dans une autre région. |
| [Copier la capture instantanée vers le même abonnement ou un abonnement différent](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Copie la capture instantanée vers le même abonnement ou un abonnement différent, mais dans la même région que la capture instantanée parente. |
|**Mettre en réseau des machines virtuelles**||
| [Sécuriser le trafic réseau entre les machines virtuelles](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée deux machines virtuelles, toutes les ressources associées, ainsi qu’un groupe de sécurité réseau interne et un groupe de sécurité réseau externe. |
|**Sécuriser les machines virtuelles**||
| [Chiffrer une machine virtuelle et des disques de données](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée une solution Key Vault, une clé de chiffrement et le principal de service, puis chiffre une machine virtuelle. |
|**Surveiller les machines virtuelles**||
| [Superviser une machine virtuelle avec Azure Monitor](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée une machine virtuelle, installe l’agent Log Analytics et inscrit la machine virtuelle auprès d’un espace de travail Log Analytics.  |
| | |
