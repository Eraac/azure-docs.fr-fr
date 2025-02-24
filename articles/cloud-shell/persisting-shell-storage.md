---
title: Conserver des fichiers pour Bash dans Azure Cloud Shell | Microsoft Docs
description: Procédure pas à pas de conservation des fichiers dans Azure Cloud Shell avec Bash.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: 0aa00af543a3d21db9b8ad0ed808a8bff0b534e1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60200181"
---
[!INCLUDE [PersistingStorage-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Fonctionnement du stockage Cloud Shell 
Cloud Shell conserve les fichiers à l’aide des deux méthodes suivantes : 
* Création d’une image disque de votre répertoire `$Home` pour conserver la totalité du contenu figurant dans le répertoire. Cette image disque est enregistrée dans votre partage de fichiers spécifié en tant que `acc_<User>.img` à l’emplacement `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` et synchronise automatiquement les modifications. 
* Montage du partage de fichiers spécifié en tant que `clouddrive` dans votre répertoire `$Home` pour l’interaction directe avec le partage de fichiers. `/Home/<User>/clouddrive` est mappé à `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Tous les fichiers figurant dans votre répertoire `$Home`, tels que les clés SSH, sont conservés dans l’image disque utilisateur qui est stockée dans votre partage de fichiers monté. Appliquez les bonnes pratiques lors de la conservation d’informations dans votre répertoire `$Home` et votre partage de fichiers monté.

## <a name="bash-specific-commands"></a>Commandes Bash spécifiques

### <a name="use-the-clouddrive-command"></a>Utiliser la commande `clouddrive`
Avec Bash dans Cloud Shell, vous pouvez exécuter une commande appelée `clouddrive`, qui vous permet de mettre à jour manuellement le partage de fichiers qui est monté dans Cloud Shell.
![Utilisation de la commande clouddrive](media/persisting-shell-storage/clouddrive-h.png)

### <a name="mount-a-new-clouddrive"></a>Monter un nouveau clouddrive

#### <a name="prerequisites-for-manual-mounting"></a>Prérequis pour le montage manuel
Vous pouvez mettre à jour le partage de fichiers qui est associé à Cloud Shell à l’aide de la commande `clouddrive mount`.

Si vous montez un partage de fichiers existant, les comptes de stockage doivent se trouver dans la région Cloud Shell de votre choix. Récupérez l’emplacement en exécutant la commande `env` à partir de Bash et en vérifiant `ACC_LOCATION`.

#### <a name="the-clouddrive-mount-command"></a>La commande `clouddrive mount`

> [!NOTE]
> Si vous montez un nouveau partage de fichiers, une nouvelle image utilisateur est créée pour votre répertoire `$Home`. L’image précédente `$Home` est conservée dans le partage de fichier précédent.

Exécutez la commande `clouddrive mount` avec les paramètres suivants :

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Pour afficher plus de détails, exécutez `clouddrive mount -h`, comme illustré ici :

![Exécution de la commande clouddrive mount](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Démontage de clouddrive
Vous pouvez démonter un partage de fichiers monté sur Cloud Shell à tout moment. Dans la mesure où Cloud Shell requiert l’utilisation d’un partage de fichiers monté, vous serez invité à créer et monter un autre partage de fichiers lors de la prochaine session.

1. Exécutez `clouddrive unmount`.
2. Acceptez et confirmez les invites.

Votre partage de fichiers continue d’exister, tant que vous ne le supprimez pas manuellement. Cloud Shell ne fera plus de recherche pour ce partage de fichiers lors des sessions ultérieures. Pour afficher plus de détails, exécutez `clouddrive unmount -h`, comme illustré ici :

![Exécution de la commande clouddrive unmount](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> L’exécution de cette commande ne va pas supprimer de ressources. Toutefois, la suppression manuelle d’un groupe de ressources, d’un compte de stockage ou d’un partage de fichiers mappé à Cloud Shell efface votre image disque du répertoire `$Home`, ainsi que tous les autres fichiers présents dans votre partage de fichiers. Il est impossible d’annuler cette opération.

### <a name="list-clouddrive"></a>Liste `clouddrive`
Pour détecter le partage de fichiers monté comme `clouddrive`, exécutez la commande `df`. 

Le chemin de fichier vers clouddrive affiche le nom de votre compte de stockage et le partage de fichiers dans l’URL. Par exemple, `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```
## <a name="powershell-specific-commands"></a>Commandes PowerShell spécifiques

### <a name="list-clouddrive-azure-file-shares"></a>Répertorier les partages de fichiers Azure `clouddrive`
La cmdlet `Get-CloudDrive` récupère les informations du partage de fichiers Azure actuellement monté par le `clouddrive` dans Cloud Shell. <br>
![Exécution de Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Démonter `clouddrive`
Vous pouvez démonter un partage de fichiers Azure monté sur Cloud Shell à tout moment. En cas de suppression du partage de fichiers Azure, vous serez invité à créer et à monter un nouveau partage de fichiers Azure lors de la prochaine session.

La cmdlet `Dismount-CloudDrive` démonte un partage de fichiers Azure à partir du compte de stockage actuel. Le démontage du `clouddrive` met fin à la session active. L’utilisateur sera invité à créer et monter un nouveau partage de fichiers Azure lors de la prochaine session.
![Exécution de Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Remarque : Si vous devez définir une fonction dans un fichier et l'appeler à partir des cmdlets PowerShell, l’opérateur point doit être inclus. Par exemple : . .\MyFunctions.ps1

## <a name="next-steps"></a>Étapes suivantes
[Démarrage rapide de Bash dans Cloud Shell](quickstart.md) <br>
[Démarrage rapide de PowerShell dans Cloud Shell](quickstart-powershell.md) <br>
[En savoir plus sur le stockage de fichiers Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[En savoir plus sur les balises de stockage](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
