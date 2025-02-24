---
title: Sauvegarder une machine virtuelle Azure à partir des paramètres de la machine virtuelle avec le service Sauvegarde Azure
description: Découvrir comment sauvegarder une machine virtuelle Azure avec le service Sauvegarde Azure
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: raynew
ms.openlocfilehash: 994762098027f7051591b8bf89bfa5cb7c380373
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465277"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Sauvegarder une machine virtuelle Azure à partir des paramètres de la machine virtuelle

Cet article explique comment sauvegarder des machines virtuelles Azure avec le service [Sauvegarde Azure](backup-overview.md). Pour sauvegarder des machines virtuelles Azure, vous avez le choix entre deux méthodes :

- Une seule machine virtuelle Azure : les instructions de cet article décrivent comment sauvegarder une machine virtuelle Azure directement à partir des paramètres de la machine virtuelle.
- Plusieurs machines virtuelles Azure : vous pouvez configurer un coffre Recovery Services et configurer la sauvegarde pour plusieurs machines virtuelles Azure. Pour ce scénario, suivez les instructions contenues dans [cet article](backup-azure-arm-vms-prepare.md).



## <a name="before-you-start"></a>Avant de commencer

1. [Découvrez](backup-architecture.md#how-does-azure-backup-work) le fonctionnement de la sauvegarde et [vérifiez](backup-support-matrix.md#azure-vm-backup-support) les conditions de prise en charge.
2. [Consultez une vue d’ensemble](backup-azure-vms-introduction.md) de la sauvegarde de machines virtuelles Azure.

### <a name="azure-vm-agent-installation"></a>Installation de l’agent de machine virtuelle Azure

Pour sauvegarder des machines virtuelles Azure, Sauvegarde Azure installe une extension sur l’agent de machine virtuelle en cours d’exécution sur la machine. Si votre machine virtuelle a été créée à partir d’une image de la Place de marché Azure, l’agent s’exécute. Dans certains cas, par exemple si vous créez une machine virtuelle personnalisée ou migrez une machine à partir d’un emplacement local, vous devrez peut-être installer l’agent manuellement.

- Si vous n’avez pas besoin d’installer l’agent de machine virtuelle manuellement, suivez les instructions fournies pour les machines virtuelles [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux).
- Une fois que l’agent est installé et que vous activez la sauvegarde, Sauvegarde Azure installe l’extension de sauvegarde sur l’agent. L’extension est mise à jour et corrigée sans aucune intervention de l’utilisateur.

## <a name="back-up-from-azure-vm-settings"></a>Sauvegarder à partir des paramètres de la machine virtuelle Azure


1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Cliquez sur **Tous les services**, puis tapez **Machines virtuelles** dans le filtre. Cliquez ensuite sur **Machines virtuelles**.
3. Dans la liste des machines virtuelles, sélectionnez celle à sauvegarder.
4. Dans le menu de la machine virtuelle, cliquez sur **Sauvegarder**.
5. Dans **Coffre Recovery Services**, effectuez les étapes suivantes :
   - Si vous disposez déjà d’un coffre, cliquez sur **Sélectionner** et choisissez un coffre.
   - Si vous n’avez pas de coffre, cliquez sur **Créer**. Nommez le coffre. Il est créé dans la même région et le même groupe de ressources que la machine virtuelle. Vous ne pouvez pas modifier ces paramètres quand vous activez la sauvegarde directement à partir des paramètres de la machine virtuelle.

   ![Assistant Activer la sauvegarde](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. Dans **Choisir une stratégie de sauvegarde**, effectuez les étapes suivantes :

   - Conservez la stratégie par défaut. La machine virtuelle est sauvegardée une fois par jour à l’heure spécifiée, et les sauvegardes sont conservées dans le coffre pendant 30 jours.
   - Sélectionnez une stratégie de sauvegarde existante si vous en avez une.
   - Créez une stratégie et définissez ses paramètres.  

   ![Sélectionner la stratégie de sauvegarde](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Cliquez sur **Activer la sauvegarde**. La stratégie de sauvegarde est alors associée à la machine virtuelle.

    ![Bouton Activer la sauvegarde](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Vous pouvez suivre la progression de la configuration dans les notifications du portail.
9. Une fois le travail terminé, cliquez sur **Sauvegarder** dans le menu de la machine virtuelle. La page affiche l’état de la sauvegarde de la machine virtuelle, des informations sur les points de récupération, les travaux en cours d’exécution et les alertes émises.

   ![État de la sauvegarde](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Une fois la sauvegarde activée, une sauvegarde initiale s’exécute. Vous pouvez démarrer la sauvegarde initiale immédiatement ou attendre qu’elle démarre selon la planification de la sauvegarde.
    - Tant que la sauvegarde initiale n’est pas terminée, la zone **État de la dernière sauvegarde** indique **Avertissement (Sauvegarde initiale en attente)** .
    - Pour savoir quand la prochaine sauvegarde planifiée va s’exécuter, cliquez sur le nom de la stratégie de sauvegarde.


> [!NOTE]
> Pour stocker l’instantané, le service Sauvegarde Azure crée un groupe de ressources distinct (autre que le groupe de ressources de la machine virtuelle). Son nom est au format **AzureBackupRG_géographie_numéro** (par exemple, AzureBackupRG_northeurope_1). Les données dans ce groupe de ressources sont conservées pendant la durée en jours spécifiée dans la section « Conserver l’instantané de récupération instantanée » de la stratégie Sauvegarde de machines virtuelles Azure. L’application d’un verrou à ce groupe de ressources peut entraîner des échecs de sauvegarde.<br>
Ce groupe de ressources doit également être exclu des restrictions de nom/étiquette, car une stratégie de restriction peut y empêcher la création de collections de points de ressources, entraînant de nouveau des échecs de sauvegarde.


## <a name="run-a-backup-immediately"></a>Exécuter une sauvegarde immédiatement

1. Pour exécuter une sauvegarde immédiatement, dans le menu de la machine virtuelle, cliquez sur **Sauvegarder** > **Sauvegarder maintenant**.

    ![Exécuter la sauvegarde](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. Dans **Sauvegarder maintenant**, utilisez le contrôle de calendrier pour sélectionner la durée de conservation du point de récupération, puis cliquez sur **OK**.

    ![Jour de fin de la conservation de la sauvegarde](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Les notifications du portail vous informent que le travail de sauvegarde a été déclenché. Pour suivre la progression de la sauvegarde, cliquez sur **Afficher tous les travaux**.




## <a name="back-up-from-the-recovery-services-vault"></a>Sauvegarder à partir du coffre Recovery Services

Suivez les instructions de cet article pour activer la sauvegarde des machines virtuelles Azure en configurant un coffre Recovery Services Sauvegarde Azure et en activant la sauvegarde dans le coffre.

## <a name="next-steps"></a>Étapes suivantes

- Si vous rencontrez des difficultés avec les procédures décrites dans cet article, consultez le [guide de résolution des problèmes](backup-azure-vms-troubleshoot.md).
- [Découvrez-en plus](backup-azure-manage-vms.md) sur la gestion de vos sauvegardes.
