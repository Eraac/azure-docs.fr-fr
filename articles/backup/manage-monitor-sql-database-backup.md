---
title: Gérer et surveiller des bases de données SQL Server sur une machine virtuelle Azure sauvegardée par le service Sauvegarde Azure | Microsoft Docs
description: Cet article décrit comment gérer et surveiller des bases de données SQL Server s’exécutant sur une machine virtuelle Azure.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: raynew
ms.openlocfilehash: 0a864382e54f5c8641aa6da2369eb914011c712c
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464869"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gérer et surveiller des bases de données SQL Server sauvegardées

Cet article décrit les tâches courantes de gestion et de surveillance des bases de données SQL Server s’exécutant sur une machine virtuelle Azure et sauvegardées dans un coffre Recovery Services par le service [Sauvegarde Azure](backup-overview.md). Vous apprendrez à surveiller les travaux et les alertes, à arrêter et à réactiver la protection de la base de données, à exécuter des travaux de sauvegarde, ainsi qu’à désinscrire une machine virtuelle des sauvegardes.

Si vous n’avez pas encore configuré de sauvegardes pour vos bases de données SQL Server, voir [Sauvegarder des bases de données SQL Server sur des machines virtuelles Azure](backup-azure-sql-database.md).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Surveiller les travaux de sauvegarde manuelles sur le portail

Sauvegarde Azure affiche tous les travaux déclenchés manuellement sur le portail **Travaux de sauvegarde**. Les travaux que vous voyez sur ce portail incluent les opérations de découverte, d’inscription, de sauvegarde et de restauration de base de données.

![Portail Travaux de sauvegarde](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Le portail **Travaux de sauvegarde** n’affiche pas les travaux de sauvegarde planifiée. Utilisez SQL Server Management Studio pour surveiller les travaux de sauvegarde planifiés, comme décrit dans la section suivante.
>

Pour plus d’informations sur les scénarios de surveillance, voir [Surveillance dans le portail Azure](backup-azure-monitoring-built-in-monitor.md) et [Surveillance à l’aide d’Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).  


## <a name="view-backup-alerts"></a>Afficher les alertes de sauvegarde

Comme les sauvegardes de fichiers journaux interviennent toutes les 15 minutes, la surveillance des tâches de sauvegarde peut s'avérer fastidieuse. Le service Sauvegarde Azure facilite la surveillance en envoyant des alertes par courrier électronique. Les alertes par courrier électronique sont :

- Déclenchées pour tous les échecs de sauvegarde.
- Consolidées au niveau de la base de données par code d’erreur.
- Envoyées uniquement pour le premier échec de sauvegarde d’une base de données.

Pour surveiller les alertes de sauvegarde de base de données :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Sur le tableau de bord du coffre, sélectionnez **Alertes et événements**.

   ![Sélectionner Alertes et événements](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. Dans **Alertes et événements**, sélectionnez **Alertes de sauvegarde**.

   ![Sélectionner Alertes de sauvegarde](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Arrêter la protection d’une base de données SQL Server

Pour arrêter la sauvegarde d'une base de données SQL Server, deux méthodes sont disponibles :

* Arrêter tous les travaux de sauvegarde à venir et supprimer tous les points de récupération.
* Arrêter tous les travaux de sauvegarde à venir, mais conserver les points de récupération.

Si vous choisissez de conserver les points de récupération, gardez à l’esprit les considérations suivantes :

* Tous les points de récupération resteront intacts, toutes les opérations de nettoyage s’arrêteront à l’arrêt de la protection avec données conservées.
* Vous serez facturé pour l’instance protégée et le stockage utilisé. Pour plus d’informations, voir [Tarification Sauvegarde Azure](https://azure.microsoft.com/pricing/details/backup/).
* Si vous supprimez une source de données sans arrêter les sauvegardes, les nouvelles sauvegardes échouent.

Pour arrêter la protection de la base de données :

1. Dans le tableau de bord du coffre, sélectionnez **Éléments de la sauvegarde**.

2. Sous **Type de gestion des sauvegardes**, sélectionnez **SQL dans une machine virtuelle Azure**.

    ![Sélectionner SQL in Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Sélectionnez la base de données dont vous souhaitez arrêter la protection.

    ![Sélectionner la protection de la base de données a arrêter](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. Dans le menu de la base de données, sélectionnez **Arrêter la sauvegarde**.

    ![Sélectionner Arrêter la sauvegarde](./media/backup-azure-sql-database/stop-db-button.png)


5. Dans le menu **Arrêter la sauvegarde**, indiquez si vous souhaitez conserver ou supprimer les données. Vous pouvez également indiquer une raison ou formuler un commentaire.

    ![Conserver ou supprimer des données dans le menu Arrêter la sauvegarde](./media/backup-azure-sql-database/stop-backup-button.png)

6. Sélectionnez **Arrêter la sauvegarde**.


> [!NOTE]
>
Pour plus d’informations sur l’option de suppression de données, voir le FAQ ci-dessous :
* [Si je supprime une base de données d’une instance protégée automatiquement, qu’advient-il des sauvegardes ?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
* [Si j’arrête une opération de sauvegarde d’une base de données protégée automatiquement, comment se comportera-t-elle ?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>


## <a name="resume-protection-for-a-sql-database"></a>Rétablir la protection d’une base de données SQL

Lorsque vous arrêtez la protection de la base de données SQL, si vous sélectionnez l’option **Conserver les données de sauvegarde**, vous pourrez réactiver la protection ultérieurement. Si vous ne conservez pas les données de sauvegarde, vous ne pourrez pas réactiver la protection.

Pour réactiver la protection d’une base de données SQL :

1. Ouvrez l’élément de la sauvegarde, puis sélectionnez **Reprendre la sauvegarde**.

    ![Sélectionner Reprendre la sauvegarde pour relancer la protection de la base de données](./media/backup-azure-sql-database/resume-backup-button.png)

2. Dans le menu **Stratégie de sauvegarde**, sélectionnez une stratégie, puis sélectionnez **Enregistrer**.

## <a name="run-an-on-demand-backup"></a>Exécuter une sauvegarde à la demande

Vous pouvez exécuter différents types de sauvegardes à la demande :

* Sauvegarde complète
* Sauvegarde de copie uniquement
* Sauvegarde différentielle
* Sauvegarde du journal

Bien que vous deviez spécifier la durée de rétention de l’option Copier uniquement la sauvegarde complète, la plage de rétention des autres types de sauvegardes est automatiquement définie sur 30 jours à compter de l’heure actuelle. <br/>
Pour plus d’informations, voir [Types de sauvegardes SQL Server](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Supprimer l'inscription d'une instance SQL Server

Désinscrivez une instance SQL Server après avoir désactivé la protection, mais avant de supprimer le coffre :

1. Sur le tableau de bord du coffre, sous **Gestion**, sélectionnez **Infrastructure de sauvegarde**.  

   ![Choisir Infrastructure de sauvegarde](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Sous **Management Servers** (Serveurs d’administration), sélectionnez **Protected Servers** (serveurs protégés).

   ![Sélectionner Serveurs protégés](./media/backup-azure-sql-database/protected-servers.png)

3. Dans **Serveurs protégés**, sélectionnez le serveur dont vous souhaitez annuler l'inscription. Si vous souhaitez supprimer le coffre, vous devez annuler l’inscription de tous les serveurs.

4. Cliquez sur le serveur protégé, puis sélectionnez **Désinscrire**.

   ![Sélectionner Supprimer](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Réinscrire une extension sur la machine virtuelle SQL Server

Parfois, l’extension de charge de travail sur la machine virtuelle peut être affectée pour une raison ou une autre. Dans ce cas, toutes les opérations déclenchées sur la machine virtuelle commencent à échouer. Vous devrez alors réinscrire l’extension sur la machine virtuelle. L’opération **Réinscrire** a pour effet de réinstaller l’extension de sauvegarde de charge de travail sur la machine virtuelle pour que les opérations se poursuivent.  <br>

Il est conseillé d’utiliser cette option avec prudence car, une fois déclenchée sur une machine virtuelle avec une extension déjà saine, cette opération provoque le redémarrage de l’extension. Cela peut entraîner l’échec de tous les travaux en cours. Avant de déclencher l’opération de réinscription, vérifiez la présence d’un ou plusieurs des [symptômes](backup-sql-server-azure-troubleshoot.md#re-registration-failures).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir [Résoudre les problèmes de sauvegarde des bases de données SQL Server](backup-sql-server-azure-troubleshoot.md).
