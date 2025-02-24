---
title: 'Didacticiel : Utiliser Azure Database Migration Service pour effectuer une migration hors connexion de SQL Server vers une base de données unique/mise en pool dans Azure SQL Database | Microsoft Docs'
description: Apprenez à effectuer une migration hors connexion d’une instance locale de SQL Server vers une base de données unique ou mise en pool dans Azure SQL Database à l’aide du service Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/09/2019
ms.openlocfilehash: c4d2e70d0f200f3a31948cfaa43def8e1b699878
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706878"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-offline-using-dms"></a>Didacticiel : Migrer SQL Server vers une base de données unique ou mise en pool dans Azure SQL Database hors connexion à l’aide de DMS

Vous pouvez utiliser Azure Database Migration Service pour migrer les bases de données d’une instance SQL Server locale vers [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/). Dans ce tutoriel, vous migrez la base de données **Adventureworks2012** restaurée sur une instance locale de SQL Server 2016 (ou une version ultérieure) vers une base de données unique ou mise en pool dans Azure SQL Database à l’aide du service Azure Database Migration Service.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
>
> - Évaluer votre base de données locale à l’aide de l’Assistant Migration des données.
> - Migrer l’exemple de schéma à l’aide de l’Assistant Migration des données.
> - Créer une instance Azure Database Migration Service.
> - Créer un projet de migration en utilisant Azure Database Migration Service.
> - Exécuter la migration.
> - Surveiller la migration.
> - Télécharger un rapport de migration.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Cet article décrit une migration hors connexion de SQL Server vers une base de données unique ou une base de données mise en pool dans Azure SQL Database. Pour une migration en ligne, consultez [Migrer SQL Server vers Azure SQL Database en ligne à l’aide de DMS](tutorial-sql-server-azure-sql-online.md).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

- Téléchargez et installez [SQL Server 2016 ou une version ultérieure](https://www.microsoft.com/sql-server/sql-server-downloads).
- Activez le protocole TCP/IP, qui est désactivé par défaut pendant l’installation de SQL Server Express, en suivant les instructions de l’article [Activer ou désactiver un protocole réseau de serveur](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Créez une base de données unique (ou mise en pool) dans Azure SQL Database, en suivant les indications de l’article [Créer une base de données unique dans Azure SQL Database à l’aide du Portail Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Si vous utilisez SQL Server Integration Services (SSIS) pour migrer la base de données de catalogue de vos projets ou de vos packages SSIS (SSISDB) entre SQL Server et Azure SQL Database, la base de données SSISDB de destination sera créée et gérée automatiquement lorsque vous provisionnerez SSIS dans Azure Data Factory (ADF). Pour plus d’informations sur la migration des packages SSIS, consultez l’article [Migrer des packages SQL Server Integration Services vers Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).
  
- Téléchargez et installez [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou version ultérieure.
- Créez un réseau virtuel Azure (VNet) pour le service Azure Database Migration Service à l’aide du modèle de déploiement Azure Resource Manager, qui fournit une connectivité de site à site à vos serveurs sources locaux via [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou une passerelle [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Pour plus d’informations sur la création d’un réseau virtuel, consultez la [documentation sur le réseau virtuel](https://docs.microsoft.com/azure/virtual-network/), en particulier les articles sur le démarrage rapide, qui fournissent des informations pas à pas.

    > [!NOTE]
    > Pendant la configuration du réseau virtuel, si vous utilisez ExpressRoute avec le peering réseau à Microsoft, ajoutez ces [points de terminaison](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de service au sous-réseau où doit être provisionné le service :
    > - Point de terminaison de base de données cible (un point de terminaison SQL ou Cosmos DB, par exemple)
    > - Point de terminaison de stockage
    > - Point de terminaison Service Bus
    >
    > Cette configuration est nécessaire, car Azure Database Migration Service ne dispose pas d’une connectivité Internet.

- Vérifiez que les règles du groupe de sécurité réseau de votre réseau virtuel ne bloquent pas les ports de communication entrants suivants d’Azure Database Migration Service : 443, 53, 9354, 445, 12000. Pour plus d’informations sur le filtrage du trafic de groupe de sécurité réseau de réseau virtuel Azure, consultez l’article [Filtrer le trafic réseau avec les groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Configurez votre [pare-feu Windows pour accéder au moteur de base de données](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Ouvrez votre Pare-feu Windows pour permettre à Azure Database Migration Service d’accéder au serveur SQL Server source, par défaut le port TCP 1433.
- Si vous exécutez plusieurs instances nommées de SQL Server avec des ports dynamiques, vous pouvez activer le service SQL Browser et autoriser l’accès au port UDP 1434 à travers vos pare-feu, de sorte qu’Azure Database Migration Service puisse se connecter à une instance nommée sur votre serveur source.
- Lorsque vous utilisez une appliance de pare-feu devant vos bases de données sources, vous devrez peut-être ajouter des règles de pare-feu pour permettre à Azure Database Migration Service d’accéder aux bases de données sources pour la migration.
- Créez une [règle de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) IP de niveau serveur pour le serveur Azure SQL Database afin de permettre au service Azure Database Migration Service d’accéder aux bases de données cibles. Fournissez la plage de sous-réseau du réseau virtuel utilisé pour Azure Database Migration Service.
- Assurez-vous que les informations d’identification utilisées pour se connecter à une instance SQL Server source disposent des autorisations [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Assurez-vous que les informations d’identification utilisées pour se connecter à l’instance Azure SQL Database cible disposent des autorisations CONTROL DATABASE concernant les bases de données Azure SQL cibles.

## <a name="assess-your-on-premises-database"></a>Évaluer votre base de données locale

Avant de pouvoir migrer les données d’une instance locale de SQL Server vers une base de données unique ou mise en pool dans Azure SQL Database, vous devez évaluer la base de données SQL Server pour détecter les problèmes de blocage susceptibles d’empêcher la migration. Une fois que vous avez téléchargé et installé l’Assistant Migration de données v3.3 ou version ultérieure, suivez les étapes décrites dans l’article [Évaluation de la migration de SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) pour évaluer la base de données locale. Un résumé de la procédure est présenté ci-dessous :

1. Dans l’Assistant Migration des données, sélectionnez l’icône Nouveau (+), puis sélectionnez le type de projet **Évaluation**.
2. Spécifiez un nom de projet, dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server** puis, dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database**, puis sélectionnez **Créer** pour créer le projet.

    Quand vous évaluez la base de données SQL Server source qui doit faire l’objet d’une migration vers une base de données unique ou mise en pool dans Azure SQL Database, vous pouvez choisir l’un des types de rapport d’évaluation suivants, ou les deux :

   - Vérifier la compatibilité de la base de données
   - Vérifier la parité de fonctionnalité

    Les deux types de rapports sont sélectionnés par défaut.

3. Dans l’Assistant Migration des données, dans l’écran **Options**, sélectionnez **suivant**.
4. Dans l’écran **Sélectionner les sources**, dans la boîte de dialogue **Se connecter à un serveur**, fournissez les détails de connexion à votre SQL Server, puis sélectionnez **Se connecter**.
5. Dans la boîte de dialogue **Add sources (Ajouter des sources)** , sélectionnez **AdventureWorks2012** et **Ajouter**, puis **Start Assessment (Démarrer l’évaluation)** .

    > [!NOTE]
    > Si vous utilisez SSIS, sachez que l’Assistant Migration de données ne prend pas en charge l’évaluation de la SSISDB source. Toutefois, les projets ou packages SSIS seront évalués/validés lors de leur redéploiement vers la SSISDB de destination qui est hébergée par Azure SQL Database. Pour plus d’informations sur la migration des packages SSIS, consultez l’article [Migrer des packages SQL Server Integration Services vers Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

    Une fois l’évaluation terminée, les résultats s’affichent comme illustré dans le graphique suivant :

    ![Évaluer la migration des données](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    En ce qui concerne les bases de données uniques ou les bases de données mises en pool dans Azure SQL Database, les évaluations vous permettent d’identifier les problèmes de parité des fonctionnalités ainsi que les problèmes de blocage de migration pour le déploiement sur une base de données unique ou mise en pool.

    - La catégorie de **parité de fonctionnalité SQL Server** fournit un ensemble complet de recommandations, d’approches alternatives disponibles dans Azure et de procédures d’atténuation pour vous aider à planifier les efforts de vos projets de migration.
    - La catégorie de **problèmes de compatibilité** indique les fonctionnalités partiellement ou non prises en charge reflétant les éventuels problèmes de compatibilité susceptibles de bloquer la migration des bases de données SQL Server locales vers Azure SQL Database. Des recommandations sont également fournies pour vous aider à résoudre ces problèmes.

6. Passez en revue les résultats d’évaluation en ce qui concerne les problèmes de blocage de migration et les problèmes de parité de fonctionnalité en sélectionnant les options spécifiques.

## <a name="migrate-the-sample-schema"></a>Migrer l’exemple de schéma

Une fois que vous êtes à l’aise avec l’évaluation et que vous pensez que la base de données sélectionnée est un candidat viable pour la migration vers une base de données unique ou mise en pool dans Azure SQL Database, utilisez DMA pour migrer le schéma vers Azure SQL Database.

> [!NOTE]
> Avant de créer un projet de migration dans Data Migration Assistant, vérifiez que vous avez bien provisionné une base de données Azure SQL comme indiqué dans les prérequis. Dans ce tutoriel, le nom de la base de données Azure SQL devrait être **AdventureWorksAzure**, mais vous pouvez lui attribuer un autre nom si vous le souhaitez.
> [!IMPORTANT]
> Si vous utilisez SSIS, sachez que l’Assistant Migration de données ne prend pas en charge la migration de la SSISDB source. Toutefois, vous pouvez redéployer vos projets ou packages SSIS vers la SSISDB de destination qui est hébergée par Azure SQL Database. Pour plus d’informations sur la migration des packages SSIS, consultez l’article [Migrer des packages SQL Server Integration Services vers Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Pour migrer le schéma **AdventureWorks2012** vers une base de données unique ou une base de données mise en pool Azure SQL Database, effectuez ce qui suit :

1. Dans Data Migration Assistant, sélectionnez l’icône Nouveau (+), puis sous **Type de projet**, sélectionnez **Migration**.
2. Spécifiez un nom de projet, dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server** puis, dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database**.
3. Sous **Étendue de la migration**, sélectionnez **Schéma uniquement**.

    Une fois les étapes précédentes effectuées, l’interface de l’Assistant Migration des données doit apparaître comme indiqué dans le graphique suivant :

    ![Créer un projet d’Assistant Migration des données](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Sélectionnez **Créer** pour créer le projet.
5. Dans l’Assistant Migration des données, spécifiez les détails de connexion source de votre SQL Server, sélectionnez **Se connecter**, puis sélectionnez la base de données **AdventureWorks2012**.

    ![Détails de connexion source de l’Assistant Migration des données](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Sélectionnez **Suivant**, sous **Se connecter au serveur cible**, spécifiez les détails de connexion cible de la base de données Azure SQL, sélectionnez **Se connecter**, puis sélectionnez la base de données **AdventureWorksAzure** que vous avez pré-approvisionnée dans la base de données Azure SQL.

    ![Détails de connexion cible de l’Assistant Migration des données](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Sélectionnez **Suivant** pour accéder à l’écran **Sélectionner des objets** dans lequel vous pouvez spécifier les objets de schéma dans la base de données **AdventureWorks2012** à déployer sur Azure SQL Database.

    Par défaut, tous les objets sont sélectionnés.

    ![Générer des scripts SQL](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Sélectionnez **Générer un script SQL** pour créer les scripts SQL, puis recherchez d’éventuelles erreurs dans les scripts.

    ![Script de schéma](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Sélectionnez **Déployer le schéma** pour déployer sur Azure SQL Database puis, une fois le schéma déployé, recherchez d’éventuelles anomalies sur le serveur cible.

    ![Déployer le schéma](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Inscrire le fournisseur de ressources Microsoft.DataMigration

1. Connectez-vous au portail Azure, sélectionnez **Tous les services**, puis **Abonnements**.

   ![Afficher les abonnements au portail](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Sélectionnez l’abonnement dans lequel vous souhaitez créer l’instance Azure Database Migration Service, puis sélectionnez **Fournisseurs de ressources**.

    ![Afficher les fournisseurs de ressources](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Recherchez migration, puis à droite de **Microsoft.DataMigration**, sélectionnez **Inscrire**.

    ![S’inscrire auprès du fournisseur de ressources](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Créer une instance

1. Dans le portail Azure, sélectionnez **+ Créer une ressource**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Service** dans la liste déroulante.

    ![Place de marché Azure](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Dans l’écran **Azure Database Migration Service**, sélectionnez **Créer**.

    ![Créer une instance Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. Dans l’écran **Créer un service de migration**, spécifiez un nom pour le service, l’abonnement, et un réseau virtuel nouveau ou existant.

4. Sélectionnez l’emplacement dans lequel vous souhaitez créer l’instance Azure Database Migration Service.

5. Sélectionnez un réseau virtuel existant ou créez-en un.

    Le réseau virtuel fournit à Azure Database Migration Service un accès au serveur SQL Server source et à l’instance Azure SQL Database cible.

    Pour plus d’informations sur la création d’un réseau virtuel dans le portail Azure, consultez l’article [Créer un réseau virtuel au moyen du portail Azure](https://aka.ms/DMSVnet).

6. Sélectionnez un niveau tarifaire.

    Pour plus d’informations sur les coûts et les niveaux de tarification, consultez la [page de tarification](https://aka.ms/dms-pricing).

    ![Configurer des paramètres d’instance Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. Sélectionnez **Créer** pour créer le service.

## <a name="create-a-migration-project"></a>Créer un projet de migration

Une fois le service créé, recherchez-le dans le portail Azure, ouvrez-le, puis créez un projet de migration.

1. Dans le portail Azure, sélectionnez **Tous les services**, recherchez Azure Database Migration Service, puis sélectionnez **Azure Database Migration Services**.

     ![Rechercher toutes les instances Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Dans l’écran **Azure Database Migration Services**, recherchez le nom de l’instance Azure Database Migration Service que vous avez créée, puis sélectionnez l’instance.

    ![Rechercher votre instance Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

3. Sélectionnez **+ Nouveau projet de migration**.
4. Dans l’écran **Nouveau projet de migration**, spécifiez un nom pour le projet. Dans la zone de texte **Type de serveur source**, sélectionnez **SQL Server**. Dans la zone de texte **Type de serveur cible**, sélectionnez **Azure SQL Database**. Enfin, dans la zone de texte **Choisir un type d’activité**, sélectionnez **Migration de données hors connexion**. 

    ![Créer un projet Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Sélectionnez **Créer et exécuter une activité** pour créer le projet et exécuter l’activité de migration.

## <a name="specify-source-details"></a>Spécifier les détails de la source

1. Dans l’écran **Détails de la source de migration**, spécifiez les détails de connexion de l’instance SQL Server source.

    Veillez à utiliser un nom de domaine complet pour le nom de l’instance SQL Server source. Vous pouvez également utiliser l’adresse IP quand la résolution de noms DNS est impossible.

2. Si vous n’avez pas installé de certificat de confiance sur votre serveur source, cochez la case **Faire confiance au certificat de serveur**.

    Quand aucun certificat de confiance n’est installé, SQL Server génère un certificat auto-signé lorsque l’instance est démarrée. Ce certificat permet de chiffrer les informations d’identification des connexions clientes.

    > [!CAUTION]
    > Les connexions SSL chiffrées à l’aide d’un certificat auto-signé n’offrent pas de sécurité renforcée. Elles sont vulnérables aux attaques de l’intercepteur. Vous ne devez pas compter sur SSL utilisant des certificats auto-signés dans un environnement de production ou sur des serveurs connectés à Internet.

   ![Détails de la source](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > Si vous utilisez SSIS, sachez que Database Migration Service ne prend pas en charge la migration de la SSISDB source. Toutefois, vous pouvez redéployer vos projets ou packages SSIS vers la SSISDB de destination qui est hébergée par Azure SQL Database. Pour plus d’informations sur la migration des packages SSIS, consultez l’article [Migrer des packages SQL Server Integration Services vers Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

## <a name="specify-target-details"></a>Spécifier les détails de la cible

1. Sélectionnez **Enregistrer** puis, dans l’écran **Détails de la cible de migration**, spécifiez les détails de connexion du serveur Azure SQL Database cible, à savoir l’instance Azure SQL Database préprovisionnée sur laquelle le schéma **AdventureWorks2012** a été déployé à l’aide de l’Assistant Migration de données.

    ![Sélectionner la cible](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Sélectionnez **Enregistrer**, puis dans l’écran **Mapper aux bases de données cibles**, mappez les bases de données source et cible pour la migration.

    Si la base de données cible porte le même nom que la base de données source, Azure Database Migration Service sélectionne la base de données cible par défaut.

    ![Mapper aux bases de données cibles](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Sélectionnez **Enregistrer**, dans l’écran **Sélectionner des tables**, développez la liste des tables et passez en revue la liste des champs concernés.

    Notez qu’Azure Database Migration Service sélectionne automatiquement toutes les tables sources vides qui existent sur l’instance Azure SQL Database cible. Si vous souhaitez migrer à nouveau les tables qui contiennent déjà des données, vous devez sélectionner explicitement les tables sur ce panneau.

    ![Sélectionner des tables](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4. Sélectionnez **Enregistrer**, dans l’écran **Résumé de la migration**, dans la zone de texte **Nom de l’activité**, spécifiez un nom pour l’activité de migration.

5. Développez la section **Option de validation** pour afficher l’écran **Choisir l’option de validation**, puis indiquez s’il faut valider les bases de données migrées pour la **comparaison de schémas**, la **cohérence des données** et **l’exactitude des requêtes**.

    ![Choisir l’option de validation](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

6. Sélectionnez **Enregistrer**, passez en revue le résumé pour vérifier que les détails de la source et de la cible correspondent à ce que vous avez spécifié précédemment.

    ![Résumé de la migration](media/tutorial-sql-server-to-azure-sql/dms-run-migration2.png)

## <a name="run-the-migration"></a>Exécuter la migration

- Sélectionnez **Exécuter la migration**.

    La fenêtre d’activité de migration s’affiche et l’**état** de l’activité est **En attente**.

    ![État de l’activité](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Surveiller la migration

1. Dans l’écran d’activité de migration, sélectionnez **Actualiser** pour mettre à jour l’affichage jusqu’à voir **l’état** de la migration s’afficher comme **Completed** (Terminé).

    ![État d’activité terminé](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. Une fois la migration terminée, sélectionnez **Télécharger le rapport** pour obtenir un rapport mentionnant les détails associés au processus de migration.

3. Vérifiez les bases de données cibles sur le serveur Azure SQL Database cible.

### <a name="additional-resources"></a>Ressources supplémentaires

- Atelier pratique [Migration de SQL à l’aide d’Azure Data Migration Service (DMS)](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587).
- Pour plus d’informations sur les limitations et problèmes connus concernant les migrations en ligne vers Azure SQL Database, consultez l’article sur les [problèmes connus et solutions de contournement des migrations en ligne Azure SQL Database](known-issues-azure-sql-online.md).
- Pour plus d’informations sur Azure Database Migration Service, consultez l’article [Qu’est-ce qu’Azure Database Migration Service ?](https://docs.microsoft.com/azure/dms/dms-overview).
- Pour plus d’informations sur Azure SQL Database, consultez l’article [Qu’est-ce que le service Azure SQL Database ?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
