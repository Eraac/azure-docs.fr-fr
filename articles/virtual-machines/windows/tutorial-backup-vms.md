---
title: 'Didacticiel : sauvegarder des machines virtuelles Windows dans le portail Azure | Microsoft Docs'
description: Avec ce didacticiel, vous allez apprendre à utiliser le portail Azure pour protéger vos machines virtuelles Windows au moyen du service Sauvegarde Azure.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3db1884fb80ce28dc58f09687ff16cc1ce904679
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708091"
---
# <a name="tutorial-back-up-and-restore-files-for-windows-virtual-machines-in-azure"></a>Didacticiel : Sauvegarder et restaurer des fichiers pour des machines virtuelles Windows dans Azure

Vous pouvez protéger vos données en effectuant des sauvegardes à intervalles réguliers. Azure Backup crée des points de récupération stockés dans des coffres de récupération géoredondants. Quand vous effectuez une restauration à partir d’un point de récupération, vous pouvez restaurer la machine virtuelle entière ou des fichiers spécifiques. Cet article explique comment restaurer un fichier unique sur une machine virtuelle exécutant Windows Server et IIS. Si vous ne disposez d’aucune machine virtuelle, vous pouvez en créer une à l’aide du [démarrage rapide Windows](quick-create-portal.md). Ce didacticiel vous explique comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une sauvegarde de machine virtuelle
> * Planifier une sauvegarde quotidienne
> * Restaurer un fichier à partir d’une sauvegarde

## <a name="backup-overview"></a>Présentation de la sauvegarde

Lorsque le service Sauvegarde Azure lance une sauvegarde, il déclenche l’extension de sauvegarde pour prendre un instantané à un moment donné. Le service Sauvegarde Azure utilise l’extension _VMSnapshot_. L’extension est installée lors de la première sauvegarde de machine virtuelle si cette machine virtuelle est exécutée. Si la machine virtuelle n’est pas en cours d’exécution, le service Sauvegarde prend un instantané du stockage sous-jacent (car aucune écriture de l’application n’a lieu pendant l’arrêt de la machine virtuelle).

Lorsque de la prise d’un instantané de machines virtuelles Windows, le service Sauvegarde se coordonne avec le service VSS pour obtenir un instantané cohérent des disques de la machine virtuelle. Après que le service Sauvegarde Azure a pris l’instantané, les données sont transférées vers le coffre de sauvegarde. Pour optimiser l’efficacité, le service identifie et transfère uniquement les blocs de données qui ont été modifiés depuis la sauvegarde précédente.

Une fois le transfert de données terminé, l’instantané est supprimé et un point de récupération est créé.

## <a name="create-a-backup"></a>Création d'une sauvegarde
Créez une simple sauvegarde quotidienne planifiée dans un coffre Recovery Services. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
1. Dans le menu de gauche, sélectionnez **Machines virtuelles**. 
1. Dans la liste, sélectionnez la machine virtuelle que vous souhaitez sauvegarder.
1. Dans le panneau de la machine virtuelle, dans la section **Opérations**, cliquez sur **Sauvegarde**. Le panneau **Activer la sauvegarde** s’ouvre.
1. Dans **Coffre Recovery Services**, cliquez sur **Créer** et fournissez le nom du nouveau coffre. Un coffre est créé dans le même groupe de ressources et au même emplacement que la machine virtuelle.
1. Sous **Choisir une stratégie de sauvegarde**, conservez la valeur par défaut **(Nouveau) DailyPolicy**, puis cliquez sur **Activer la sauvegarde**.
1. Pour créer un point de récupération initial, dans le panneau **Sauvegarde**, cliquez sur **Sauvegarder maintenant**.
1. Dans le panneau **Sauvegarder maintenant**, cliquez sur l’icône de calendrier, utilisez le contrôle de calendrier pour choisir la durée de conservation du point de restauration, puis cliquez sur **OK**.
1. Dans le panneau **Sauvegarde** de votre machine virtuelle, vous voyez le nombre de points de restauration complets.


    ![Points de récupération](./media/tutorial-backup-vms/backup-complete.png)
    
La première sauvegarde prend environ 20 minutes. Une fois la sauvegarde terminée, passez à l’étape suivante de ce didacticiel.

## <a name="recover-a-file"></a>Récupérer un fichier

Si vous supprimez un fichier ou y apportez des modifications accidentellement, vous pouvez utiliser la récupération de fichier pour restaurer le fichier à partir de votre coffre de sauvegarde. La récupération de fichier utilise un script qui s’exécute sur la machine virtuelle pour monter le point de récupération en tant que lecteur local. Ces lecteurs restent montés pendant 12 heures pour vous permettre de copier des fichiers à partir du point de récupération et de les restaurer sur la machine virtuelle.  

Dans cet exemple, vous découvrirez comment récupérer le fichier image qui est utilisé dans la page web par défaut pour IIS. 

1. Ouvrez un navigateur et connectez-vous à l’adresse IP de la machine virtuelle pour afficher la page IIS par défaut.

    ![Page web IIS par défaut](./media/tutorial-backup-vms/iis-working.png)

1. Connectez-vous à la machine virtuelle.
1. Sur la machine virtuelle, ouvrez **Explorateur de fichiers**, accédez à \inetpub\wwwroot et supprimez le fichier **iisstart.png**.
1. Sur votre ordinateur local, actualisez le navigateur pour confirmer que cette image sur la page IIS par défaut a disparu.

    ![Page web IIS par défaut](./media/tutorial-backup-vms/iis-broken.png)

1. Sur votre ordinateur local, ouvrez un nouvel onglet et accédez au [portail Azure](https://portal.azure.com).
1. Dans le menu de gauche, sélectionnez **Machines virtuelles** et cliquez sur la machine virtuelle dans la liste.
1. Dans le panneau de la machine virtuelle, dans la section **Opérations**, cliquez sur **Sauvegarde**. Le panneau **Sauvegarde** s’ouvre. 
1. Dans le menu en haut du panneau, sélectionnez **Récupération de fichier**. Le panneau **Récupération de fichier** s’affiche.
1. À l’**Étape 1 : Sélectionner un point de récupération**, sélectionnez un point de récupération dans la liste déroulante.
1. À l’**Étape 2 : Télécharger le script pour parcourir et restaurer des fichiers**, cliquez sur le bouton **Télécharger le fichier exécutable**. Copiez le mot de passe pour le fichier et enregistrez-le à un endroit sûr.
1. Sur votre ordinateur local, ouvrez **Explorateur de fichiers** et accédez à votre dossier**Téléchargements**, puis copiez le fichier .exe téléchargé. Le nom du fichier commence par le nom de votre machine virtuelle. 
1. Sur votre machine virtuelle (en utilisant la connexion RDP), collez le fichier .exe sur le Bureau. 
1. Accédez au bureau de votre machine virtuelle et double-cliquez sur le fichier .exe. Une invite de commandes démarre. Le programme monte le point de récupération sous forme de partage de fichiers auquel vous pouvez accéder. Après avoir créé le partage, entrez **q** pour fermer l’invite de commandes.
1. Sur votre machine virtuelle, ouvrez **Explorateur de fichiers** et accédez à la lettre de lecteur qui a été utilisée pour le partage de fichiers.
1. Accédez à \inetpub\wwwroot, copiez **iisstart.png** à partir du partage de fichier et collez-le dans \inetpub\wwwroot. Par exemple, copiez F:\inetpub\wwwroot\iisstart.png et collez-le dans c:\inetpub\wwwroot pour récupérer le fichier.
1. Sur votre ordinateur local, ouvrez l’onglet du navigateur avec lequel vous êtes connecté à l’adresse IP de la machine virtuelle affichant la page IIS par défaut. Appuyez sur CTRL + F5 pour actualiser la page du navigateur. Vous devriez maintenant voir que l’image a été restaurée.
1. Sur votre ordinateur local, revenez à l’onglet du navigateur du portail Azure et à l’**Étape 3 : Démonter les disques après la récupération** cliquez sur le bouton **Démonter les disques**. Si vous avez omis cette étape, la connexion au point de montage est automatiquement fermée après 12 heures. Une fois ces 12 heures écoulées, vous devez télécharger un nouveau script pour créer un point de montage.





## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer une sauvegarde de machine virtuelle
> * Planifier une sauvegarde quotidienne
> * Restaurer un fichier à partir d’une sauvegarde

Passez au didacticiel suivant pour en savoir plus sur la surveillance des machines virtuelles.

> [!div class="nextstepaction"]
> [Gouverner les machines virtuelles](tutorial-govern-resources.md)









