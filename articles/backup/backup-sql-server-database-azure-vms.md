---
title: Sauvegarder des bases de données SQL Server sur des machines virtuelles Azure | Microsoft Docs
description: Découvrir comment sauvegarder des bases de données SQL Server sur des machines virtuelles Azure
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: vijayts
ms.openlocfilehash: 9870acdebaa4bf700bb18b5fa1d3a26f6338a10b
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464984"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Sauvegarder des bases de données SQL Server sur des machines virtuelles Azure

Les bases de données SQL Server sont des charges de travail critiques nécessitant un faible objectif de point de récupération (RPO) et une conservation à long terme. Vous pouvez sauvegarder les bases de données SQL Server qui s’exécutent sur les machines virtuelles Azure en utilisant [Sauvegarde Azure](backup-overview.md).

Cet article explique comment sauvegarder dans un coffre Recovery Services de Sauvegarde Azure une base de données SQL Server s’exécutant sur une machine virtuelle Azure.

Cet article porte sur les points suivants :

> [!div class="checklist"]
> * Créer et configurer un coffre
> * Détecter des bases de données et configurer des sauvegardes
> * Configurer la protection automatique de bases de données


## <a name="prerequisites"></a>Prérequis

Pour pouvoir sauvegarder une base de données SQL Server, vérifiez les critères suivants :

1. Identifiez ou créez un [coffre Recovery Services](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) dans la même région ou avec les mêmes paramètres régionaux que la machine virtuelle qui héberge l’instance SQL Server.
2. Vérifiez que la machine virtuelle dispose d’une [connectivité réseau](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
3. Assurez-vous que les bases de données SQL Server respectent les [instructions de nommage pour Sauvegarde Azure](#database-naming-guidelines-for-azure-backup).
4. Pour SQL 2008 et 2008 R2, [ajoutez une clé de Registre](#add-registry-key-to-enable-registration) pour activer l’inscription du serveur. Cette étape ne sera plus requise une fois que la fonctionnalité sera mise à la disposition générale.
5. Vérifiez que vous n’avez aucune autre solution de sauvegarde activée pour la base de données. Désactivez tous les autres sauvegardes SQL Server avant de sauvegarder la base de données.

> [!NOTE]
> Vous pouvez activer Sauvegarde Azure pour une machine virtuelle Azure et une base de données SQL Server s’exécutant sur la machine virtuelle sans conflit.


### <a name="establish-network-connectivity"></a>Établir la connectivité réseau

Pour toute opération, une machine virtuelle SQL Server requiert une connectivité pour accéder aux adresses IP publiques Azure. Les opérations de machine virtuelle (détection de bases de données, configuration de sauvegardes, sauvegardes planifiées, restauration des points de récupération, etc.) échouent en cas d’absence de connexion aux adresses IP publiques Azure.

Établissez la connectivité en utilisant l’une des options suivantes :

- **Autorisez les plages d’adresses IP du centre de données Azure**. Cette option autorise les [plages d’adresses IP](https://www.microsoft.com/download/details.aspx?id=41653) dans le téléchargement. Pour accéder à un groupe de sécurité réseau, utilisez l’applet de commande Set-AzureNetworkSecurityRule. Si vous êtes un destinataire sûr, dressez la liste des adresses IP spécifiques à une région ; vous devez également mettre à jour la liste des destinataires sûrs en indiquant la balise du service Azure Active Directory (Azure AD) pour activer l’authentification.

- **Autorisez l’accès à l’aide de balises de groupe de sécurité réseau**. Si vous utilisez des groupes de sécurité réseau pour restreindre la connectivité, cette option ajoute une règle à votre groupe de sécurité réseau qui permet l’accès sortant à Sauvegarde Azure en utilisant la balise AzureBackup. En plus de cette balise, vous avez également besoin des [règles](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) correspondantes pour Azure AD et Stockage Azure afin de permettre la connexion pour l’authentification et le transfert de données. La balise AzureBackup est actuellement disponible sur PowerShell uniquement. Pour créer une règle à l’aide de la balise AzureBackup :

    - Ajoutez les identifiants de compte Azure et mettez à jour les clouds nationaux<br/>
    `Add-AzureRmAccount`

    - Sélectionnez l’abonnement au groupe de sécurité réseau<br/>
    `Select-AzureRmSubscription "<Subscription Id>"`

     - Sélectionnez le groupe de sécurité réseau<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

    - Ajoutez la règle de trafic sortant autorisée pour la balise du service Sauvegarde Azure<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

  - Enregistrez le groupe de sécurité réseau<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`
- **Autorisez l’accès à l’aide de balises de Pare-feu Azure**. Si vous utilisez Pare-feu Azure, créez une règle d’application en utilisant la balise [FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags) d’AzureBackup. Cela autorise l’accès sortant à Sauvegarde Azure.
- **Déployez un serveur proxy HTTP pour le routage du trafic**. Lorsque vous sauvegardez une base de données SQL Server sur une machine virtuelle Azure, l’extension de sauvegarde sur la machine virtuelle utilise les API HTTPS pour envoyer des commandes de gestion à Sauvegarde Azure, et des données à Stockage Azure. L’extension de sauvegarde utilise également Azure AD pour l’authentification. Acheminez le trafic de l’extension de sauvegarde pour ces trois services via le proxy HTTP. Les extensions sont le seul composant configuré pour l’accès à l’internet public.

Les options de connectivité présentent les avantages et inconvénients suivants :

**Option** | **Avantages** | **Inconvénients**
--- | --- | ---
Autoriser les plages d’adresses IP | Aucun coût supplémentaire | Difficile à gérer, car les plages d’adresses IP changent au fil du temps <br/><br/> Fournit un accès à l’ensemble d’Azure et pas seulement à Stockage Azure
Utiliser les balises du service NSG | Plus faciles à gérer car les modifications apportées à la plage sont fusionnées automatiquement <br/><br/> Aucun coût supplémentaire <br/><br/> | Peut être utilisé uniquement avec les groupes de sécurité réseau <br/><br/> Fournit l’accès à l’ensemble du service
Utiliser les balises FQDN du Pare-feu Azure | Plus faciles à gérer car les noms de domaine complet sont managés automatiquement | Utilisabes avec Pare-feu Azure uniquement
Utiliser un proxy HTTP | Le contrôle granulaire dans le proxy sur les URL de stockage est autorisé <br/><br/> Un seul point d’accès Internet aux machines virtuelles <br/><br/> Non soumis aux modifications d’adresse IP Azure | Frais supplémentaires d’exécution de machine virtuelle avec le logiciel de serveur proxy

### <a name="database-naming-guidelines-for-azure-backup"></a>Instructions de dénomination des bases de données pour Sauvegarde Azure

Évitez d’utiliser les éléments suivants dans les noms de base de données :

  * Espaces au début et à la fin
  * Points d’exclamation (!) à la fin
  * Crochets de fermeture (])
  * Point-virgule « ; »
  * Barre oblique « / »

L’utilisation d’alias est possible, bien que déconseillée, pour les caractères non pris en charge. Pour plus d'informations, consultez la rubrique [Présentation du modèle de données du service de Table](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

### <a name="add-registry-key-to-enable-registration"></a>Ajoutez une clé de Registre pour activer l’inscription

1. Ouvrez Regedit
2. Créez le répertoire de Registre suivant : HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook (vous devrez créer la clé TestHook sous WorkloadBackup qui, lui-même, doit être créé sous Microsoft).
3. Sous le chemin d’accès du répertoire du Registre, créez une nouvelle valeur chaîne avec le nom chaîne **AzureBackupEnableWin2K8R2SP1** et la valeur : **True**

    ![Regedit pour l’activation de l’inscription](media/backup-azure-sql-database/reg-edit-sqleos-bkp.png)

Vous pouvez également automatiser cette étape en exécutant le fichier .reg avec la commande suivante :

```csharp
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WorkloadBackup\TestHook]
"AzureBackupEnableWin2K8R2SP1"="True"
```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Détecter les bases de données SQL Server

Comment détecter les bases de données en cours d’exécution sur une machine virtuelle :

1. Dans le [portail Azure](https://portal.azure.com), ouvrez le coffre Recovery Services que vous utilisez pour sauvegarder la base de données.

2. Dans le tableau de bord du **coffre Recovery Services**, cliquez sur **Sauvegarde**.

   ![Cliquer sur Sauvegarder pour ouvrir le menu Objectif de sauvegarde](./media/backup-azure-sql-database/open-backup-menu.png)

3. Dans **Objectif de sauvegarde**, définissez **Où s’exécute votre charge de travail ?** sur **Azure**.

4. Dans **Que souhaitez-vous sauvegarder ?** , sélectionnez **SQL Server dans une machine virtuelle Azure**.

    ![Sélectionnez SQL Server dans une machine virtuelle Azure pour la sauvegarde](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Dans **Objectif de sauvegarde** > **Découvrir les bases de données dans les machines virtuelles**, sélectionnez **Démarrer la détection** pour rechercher des machines virtuelles non protégées dans l’abonnement. La durée de cette recherche varie selon le nombre de machines virtuelles non protégées de l’abonnement.

   - Après la détection, les machines virtuelles non protégées doivent apparaître dans la liste, répertoriées par nom et groupe de ressources.
   - Si une machine virtuelle n’est pas répertoriée contrairement à ce que vous attendez, vérifiez si elle n’est pas déjà sauvegardée dans un coffre.
   - Plusieurs machines virtuelles peuvent avoir le même nom, mais appartenir à différents groupes de ressources.

     ![La sauvegarde est en attente au cours de la recherche pour des bases de données dans des machines virtuelles](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Dans la liste des machines virtuelles, sélectionnez la machine virtuelle exécutant la base de données SQL Server > **Découvrir les bases de données**.

7. Suivez la découverte des bases de données dans **Notifications**. La durée nécessaire à cette action dépend du nombre de bases de données sur la machine virtuelle. Lorsque les bases de données sélectionnées ont été détectées, un message de réussite s’affiche.

    ![Message Déploiement réussi](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. La sauvegarde Azure détecte toutes les bases de données SQL Server résidant sur la machine virtuelle. Lors de la découverte, les éléments suivants se produisent en arrière-plan :

    - Sauvegarde Azure inscrit la machine virtuelle auprès du coffre pour la sauvegarde de la charge de travail. Les bases de données présentes sur la machine virtuelle inscrite ne peuvent être sauvegardées que sur ce coffre.
    - Sauvegarde Azure installe l’extension AzureBackupWindowsWorkload sur la machine virtuelle. Aucun agent n’est installé sur une base de données SQL.
    - Sauvegarde Azure crée le compte de service NT Service\AzureWLBackupPluginSvc sur la machine virtuelle.
      - Toutes les opérations de sauvegarde et de restauration utilisent le compte de service.
      - NT Service\AzureWLBackupPluginSvc requiert des autorisations d’administrateur système SQL. SqlIaaSExtension est installé sur toutes les machines virtuelles SQL Server créées dans la Place de marché Azure. L’extension AzureBackupWindowsWorkload utilise l’extension SQLIaaSExtension pour obtenir automatiquement les autorisations requises.
    - Si vous n’avez pas créé la machine virtuelle à partir de la Place de marché ou si vous utilisez SQL 2008 et 2008 R2, SqlIaaSExtension n’est probablement pas installé sur la machine virtuelle, et l’opération de découverte échoue avec le message d’erreur UserErrorSQLNoSysAdminMembership. Pour résoudre ce problème, suivez les instructions de la section [Définir les autorisations de machine virtuelle](backup-azure-sql-database.md#set-vm-permissions).

        ![Sélectionner la machine virtuelle et la base de données](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurer une sauvegarde  

1. Dans **Objectif de sauvegarde** > **Étape 2 : Configurer la sauvegarde**, sélectionnez **Configurer la sauvegarde**.

   ![Sélectionner Configurer la sauvegarde](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. **Sélectionner les éléments à sauvegarder** répertorie tous les groupes de disponibilité inscrits et les instances SQL Server autonomes. Sélectionnez la flèche vers la gauche pour développer la liste de toutes les bases de données non protégées dans cette instance ou ce groupe de disponibilité Always On.  

    ![Affichage de toutes les instances SQL Server avec des bases de données autonomes](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Choisissez toutes les bases de données que vous souhaitez protéger, puis sélectionnez **OK**.

   ![Protection de la base de données](./media/backup-azure-sql-database/select-database-to-protect.png)

   Pour optimiser les charges de sauvegarde, la sauvegarde Azure définit le nombre maximal de bases de données à 50 dans une tâche de sauvegarde.

     * Pour protéger plus de 50 bases de données, configurez plusieurs sauvegardes.
     * Pour activer [](#enable-auto-protection) l’instance entière ou le groupe de disponibilité Always On. Dans la liste déroulante **AUTOPROTECT** (Protection automatique), sélectionnez **ON** (Activer), puis **OK**.

    > [!NOTE]
    > La fonctionnalité de [protection automatique](#enable-auto-protection) permet non seulement de protéger toutes les bases de données existantes en une seule étape, mais aussi de protéger automatiquement toutes les nouvelles bases de données ajoutées à cette instance ou ce groupe de disponibilité.  

4. Cliquez sur **OK** pour ouvrir **Stratégie de sauvegarde**.

    ![Activer la protection automatique pour le groupe de disponibilité Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Dans **Stratégie de sauvegarde**, choisissez une stratégie, puis sélectionnez  **OK**.

   - Sélectionner la stratégie par défaut HourlyLogBackup.
   - Choisir une stratégie de sauvegarde existante créée précédemment pour SQL.
   - Définir une nouvelle stratégie selon votre RPO et la durée de rétention.

     ![Sélectionner la stratégie de sauvegarde](./media/backup-azure-sql-database/select-backup-policy.png)

6. Dans  **Sauvegarder**, sélectionnez  **Activer la sauvegarde**.

    ![Activer la stratégie de sauvegarde choisie](./media/backup-azure-sql-database/enable-backup-button.png)

7. Vous pouvez suivre la progression de la configuration dans la zone  **Notifications**  du portail.

    ![Zone Notifications](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Créer une stratégie de sauvegarde

Une stratégie de sauvegarde définit le moment auquel les sauvegardes sont effectuées ainsi que leur durée de rétention.

- Une stratégie est créée au niveau du coffre.
- Plusieurs coffres peuvent utiliser la même stratégie de sauvegarde, mais vous devez appliquer la stratégie de sauvegarde à chaque coffre.
- Lorsque vous créez une stratégie de sauvegarde, une sauvegarde complète quotidienne est la valeur par défaut.
- Vous pouvez ajouter une sauvegarde différentielle, mais uniquement si vous configurez les sauvegardes complètes pour qu’elles aient lieu toutes les semaines.
- Apprenez-en davantage sur les [différents types de stratégies de sauvegarde](backup-architecture.md#sql-server-backup-types).

Pour créer une stratégie de sauvegarde :

1. Dans le coffre, sélectionnez **Stratégies de sauvegarde** > **Ajouter**.
2. Dans **Ajouter**, sélectionnez **SQL Server dans une machine virtuelle Azure** pour définir le type de stratégie.

   ![Choisissez un type de stratégie pour la nouvelle stratégie de sauvegarde](./media/backup-azure-sql-database/policy-type-details.png)

3. Dans **Nom de la stratégie**, entrez le nom de la nouvelle stratégie.
4. Dans **Stratégie de sauvegarde complète**, sélectionnez une **Fréquence de sauvegarde**. Choisissez **Quotidienne** ou **Hebdomadaire**.

   - Si vous sélectionnez **quotidienne**, sélectionnez l’heure et le fuseau horaire de début du travail de sauvegarde.
   - Si vous choisissez la fréquence **hebdomadaire**, sélectionnez le jour de la semaine, l’heure et le fuseau horaire indiquant le début du travail de sauvegarde.
   - Exécutez une sauvegarde complète, car vous ne pouvez pas désactiver l’option **Sauvegarde complète**.
   - Sélectionnez **Sauvegarde complète** pour afficher la stratégie.
   - Si vous choisissez des sauvegardes complètes quotidiennes, vous ne pouvez pas créer de sauvegardes différentielles.

     ![Champs de la nouvelle stratégie de sauvegarde](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Dans **DURÉE DE RÉTENTION**, toutes les options sont sélectionnées par défaut. Désactivez les limites des plages de rétention dont vous ne souhaitez pas, puis définissez les intervalles à utiliser.

    - La période de rétention minimale est de sept jours pour tous les types de sauvegardes (complète, différentielle et fichier journal).
    - Des points de récupération sont marqués pour la rétention et varient selon la durée de rétention. Par exemple, si vous sélectionnez une sauvegarde complète quotidienne, seule une sauvegarde complète est déclenchée chaque jour.
    - La sauvegarde d’un jour spécifique est marquée et conservée conformément à la durée et au paramètre de rétention hebdomadaire.
    - Les durées de rétention mensuelle et annuelle ont le même comportement.

       ![Paramètres d’intervalle de la durée de rétention](./media/backup-azure-sql-database/retention-range-interval.png)

6. Dans le menu de **stratégie Sauvegarde complète**, cliquez sur **OK** pour accepter les paramètres.
7. Pour ajouter une stratégie de sauvegarde différentielle, sélectionnez **Sauvegarde différentielle**.

   ![Paramètres d’intervalle des durées de rétention](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Ouvrir le menu de la stratégie de sauvegarde différentielle](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Dans la stratégie **Sauvegarde différentielle**, sélectionnez **Activer** pour ouvrir les contrôles de fréquence et de rétention.

    - Vous pouvez déclencher une sauvegarde différentielle une fois par jour uniquement.
    - Les sauvegardes différentielles peuvent être conservées jusqu’à 180 jours. Pour une durée de rétention supérieure, utilisez des sauvegardes complètes.

9. Sélectionnez **OK** pour enregistrer la stratégie et revenir au menu principal **Stratégie de sauvegarde**.

10. Pour ajouter une stratégie de sauvegarde de fichier journal, cliquez sur **Sauvegarde de fichier journal**.
11. Dans **Sauvegarde de fichier journal**, sélectionnez **Activer** et définissez les contrôles de fréquence et de rétention. Les sauvegardes de fichiers journaux peuvent se produire toutes les 15 minutes et être conservées jusqu’à 35 jours.
12. Sélectionnez **OK** pour enregistrer la stratégie et revenir au menu principal **Stratégie de sauvegarde**.

    ![Modifier la stratégie de sauvegarde de fichier journal](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. Dans le menu **Stratégie de sauvegarde**, choisissez s’il convient d’activer l’option **Compression de la sauvegarde SQL**,
    - qui est désactivée par défaut.
    - Sur le serveur principal, Sauvegarde Azure utilise la compression de sauvegarde native SQL.

14. Après avoir terminé les modifications apportées à la stratégie de sauvegarde, sélectionnez **OK**.


### <a name="modify-policy"></a>Modifier la stratégie
Dans la stratégie, modifiez la fréquence de sauvegarde ou la plage de rétention.

> [!NOTE]
> Toute modification de la période de rétention sera appliquée rétroactivement à tous les anciens points de récupération ainsi qu’aux nouveaux.

Dans le tableau de bord du coffre, accédez à **Gérer** > **Stratégies de sauvegarde** et choisissez la stratégie que vous souhaitez modifier.

  ![Gérer la stratégie de sauvegarde](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>Activer la protection automatique  

Vous pouvez activer la protection automatique pour sauvegarder automatiquement toutes les bases de données existantes et futures sur une instance SQL Server autonome ou à un groupe de disponibilité Always On.

- Il n’existe aucune limite sur le nombre de bases de données que vous pouvez sélectionner en une fois pour la protection automatique.
- Vous ne pouvez pas protéger ou exclure sélectivement des bases de données de la protection dans une instance lorsque vous activez la protection automatique.
- Si votre instance inclut déjà des bases de données protégées, elles restent protégées par leurs stratégies respectives même après que vous avez activé la protection automatique. Toutefois, toutes les bases de données non protégées ainsi que celles que vous ajoutez par la suite ont une seule stratégie que vous définissez sous  **Configurer la sauvegarde** après avoir sélectionné les bases de données. Toutefois, vous pouvez modifier ultérieurement la stratégie associée à une base de données protégée automatiquement.  

Pour activer la protection automatique :

  1. Dans **Éléments à sauvegarder**, sélectionnez l’instance pour laquelle vous souhaitez activer la protection automatique.
  2. Sélectionnez la liste déroulante sous **AUTOPROTECT** (Protection automatique), choisissez **ON** (Activer), puis sélectionnez **OK**.

      ![Activer la protection automatique sur le groupe de disponibilité](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. La sauvegarde est configurée pour toutes les bases de données et peut être suivie dans **Travaux de sauvegarde**.

Si vous devez désactiver la protection automatique, sélectionnez le nom d’instance sous **Configurer la sauvegarde**, puis sélectionnez **Désactiver la protection automatique** pour cette instance. Toutes les bases de données continueront à être sauvegardées, mais mes futures bases de données, en revanche, ne seront pas automatiquement protégées.

![Désactiver la protection automatique sur cette instance](./media/backup-azure-sql-database/disable-auto-protection.png)

 
## <a name="next-steps"></a>Étapes suivantes

Découvrez comment :

- [Restaurer des bases de données SQL Server sauvegardées](restore-sql-database-azure-vm.md)
- [Gérer les bases de données SQL Server sauvegardées](manage-monitor-sql-database-backup.md)
