---
title: Réinitialiser votre mot de passe Linux local sur les machines virtuelles Azure | Microsoft Docs
description: Introduction des étapes pour réinitialiser votre mot de passe Linux local sur la machine virtuelle Azure
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: d96d75f4f2623476f7af4e6eea930c1f2c503e3a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60306949"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Réinitialiser votre mot de passe Linux local sur les machines virtuelles Azure

Cet article présente plusieurs méthodes pour réinitialiser les mots de passe Linux locaux sur les machines virtuelles. Si le compte utilisateur a expiré ou si vous souhaitez simplement créer un nouveau compte, utilisez les méthodes suivantes pour créer un nouveau compte d’administrateur local pour retrouver l’accès à la machine virtuelle.

## <a name="symptoms"></a>Symptômes

Vous ne pouvez pas vous connecter à la machine virtuelle, et vous recevez un message vous informant que votre mot de passe est incorrect. De plus, vous ne pouvez pas utiliser VMAgent pour réinitialiser votre mot de passe sur le portail Azure.

## <a name="manual-password-reset-procedure"></a>Procédure de réinitialisation manuelle du mot de passe

1.  Supprimez la machine virtuelle et conservez les disques joints.

2.  Joignez le lecteur du système d’exploitation comme disque de données à une autre machine virtuelle temporaire, située au même endroit.

3.  Exécutez la commande SSH suivante dans la machine virtuelle temporaire pour devenir un super utilisateur.

    ```bash
    sudo su
    ```

4.  Exécutez **fdisk -l** ou consultez les journaux d’activité système pour trouver le disque joint. Recherchez le nom du lecteur à monter. Sur la machine virtuelle temporaire, cherchez dans le fichier journal correspondant.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Voici un exemple de sortie de la commande grep :

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Créer un point de montage nommé **tempmount**.

    ```bash
    mkdir /tempmount
    ```

6.  Monter le disque du système d’exploitation sur le point de montage. Vous devez généralement monter *sdc1* ou *sdc2*. Tout dépend de la partition d’hébergement dans le répertoire */etc* du disque de la machine défaillante.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Créez des copies des principaux fichiers d’informations d’identification avant d’apporter des modifications :

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Réinitialisez le mot de passe de l'utilisateur dont vous avez besoin :

    ```bash
    passwd <<USER>> 
    ```

9.  Déplacez les fichiers modifiés dans le bon dossier sur le disque de la machine défaillante.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Revenez à la racine et démontez le disque.

    ```bash
    cd /
    umount /tempmount
    ```

11. Détachez le disque du portail de gestion.

12. Recréez la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

* [Résoudre les problèmes de machine virtuelle Azure en attachant le disque du système d’exploitation à une autre machine virtuelle Azure](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI : guide pratique pour supprimer et redéployer une machine virtuelle à partir d’un disque dur virtuel](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
