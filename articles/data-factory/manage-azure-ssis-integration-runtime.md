---
title: Reconfigurer le runtime d’intégration Azure-SSIS | Microsoft Docs
description: Découvrez comment reconfigurer le runtime d’intégration Azure-SSIS dans Azure Data Factory après l’avoir déjà configuré.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 3c1178a20debc36fbdbbd374eaf9adb6005a93a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66152351"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Reconfigurer le runtime d’intégration Azure-SSIS
Cet article décrit comment reconfigurer un runtime d’intégration Azure-SSIS existant. Consultez [Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md) pour créer un runtime d’intégration (IR) Azure-SSIS dans Azure Data Factory.  

## <a name="data-factory-ui"></a>IU de la fabrique de données 
Vous pouvez utiliser l’interface utilisateur de Data Factory pour arrêter, modifier/reconfigurer ou supprimer un runtime d’intégration Azure-SSIS. 

1. Dans **l’interface utilisateur de Data Factory**, passez dans l’onglet **Modifier**. Pour lancer l’interface utilisateur de Data Factory, cliquez sur **Créer et surveiller** dans la page d’accueil de votre fabrique de données.
2. Dans le volet gauche, cliquez sur **Connexions**.
3. Dans le volet droit, basculez vers les **Runtimes d’intégration**. 
4. Vous pouvez utiliser les boutons de la colonne Actions pour **arrêter**, **modifier** ou **supprimer** le runtime d’intégration. Le bouton **Code** dans la colonne **Actions** vous permet d’afficher la définition JSON associée au runtime d’intégration.  
    
    ![Actions pour le runtime d’intégration Azure-SSIS](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Pour reconfigurer un runtime d’intégration Azure-SSIS
1. Arrêtez le runtime d’intégration en cliquant sur **Arrêter** dans la colonne **Actions**. Pour actualiser l’affichage de liste, cliquez sur **Actualiser** dans la barre d’outils. Une fois le runtime d’intégration arrêté, vous constatez que la première action vous permet de démarrer le runtime d’intégration. 

    ![Actions pour le runtime d’intégration Azure-SSIS après l’arrêt](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Modifiez/reconfigurez le runtime d’intégration en cliquant sur le bouton **Modifier** dans la colonne **Actions**. Dans la fenêtre **Programme d’installation du runtime d’intégration**, changez les paramètres (par exemple la taille du nœud, le nombre de nœuds ou le nombre maximal d’exécutions en parallèle par nœud). 
3. Pour redémarrer le runtime d’intégration, cliquez sur le bouton **Démarrer** dans la colonne **Actions**.     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Une fois que vous avez configuré et démarré une instance du runtime d’intégration Azure-SSIS, vous pouvez la reconfigurer en exécutant plusieurs cmdlets PowerShell `Stop`  -  `Set`  -  `Start`, les unes à la suite des autres. Par exemple, le script PowerShell suivant modifie le nombre de nœuds alloués à l’instance du runtime d’intégration Azure-SSIS et lui en attribue cinq.

### <a name="reconfigure-an-azure-ssis-ir"></a>Reconfigurer un runtime d’intégration Azure-SSIS

1. Tout d’abord, arrêtez le runtime d’intégration Azure-SSIS à l’aide de l’applet de commande [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime). Cette commande libère tous ses nœuds et arrête la facturation.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Ensuite, reconfigurez Azure-SSIS Integration Runtime à l’aide de l’applet de commande [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime). L’exemple de commande suivant augmente la taille du runtime d’intégration Azure-SSIS en le faisant passer à cinq nœuds.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Démarrez ensuite le runtime d’intégration Azure-SSIS à l’aide de l’applet de commande [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime). Cette commande a pour effet d’allouer tous ses nœuds à l’exécution des packages SSIS.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Supprimer un runtime d’intégration Azure-SSIS
1. Tout d’abord, répertoriez tous les runtimes d’intégration Azure-SSIS dans votre fabrique de données.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Puis, arrêtez tous les runtimes d’intégration Azure-SSIS dans votre fabrique de données.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Ensuite, supprimez un à un tous les runtimes d’intégration Azure-SSIS dans votre fabrique de données.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Enfin, supprimez votre fabrique de données.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Si vous aviez créé un groupe de ressources, supprimez-le.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le runtime Azure-SSIS, voir les rubriques suivantes : 

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) (Runtime d’intégration Azure-SSIS). Cet article fournit des informations conceptuelles sur les runtimes d’intégration en général, y compris sur le runtime d’intégration Azure-SSIS. 
- [Didacticiel : deploy SSIS packages to Azure](tutorial-create-azure-ssis-runtime-portal.md) (Déployer des packages SSIS vers Azure). Cet article fournit des instructions détaillées pour créer un runtime d’intégration Azure-SSIS qui utilise une base de données Azure SQL pour héberger le catalogue SSIS. 
- [Guide pratique : Créer un runtime d’intégration Azure-SSIS](create-azure-ssis-integration-runtime.md). Cet article s’appuie sur le tutoriel et fournit des instructions sur la façon d’utiliser Azure SQL Database Managed Instance et de joindre le runtime d’intégration à un réseau virtuel. 
- [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](join-azure-ssis-integration-runtime-virtual-network.md). Cet article fournit des informations conceptuelles sur la façon d’attacher un runtime d’intégration Azure-SSIS à un réseau virtuel Azure. Il décrit également les étapes nécessaires pour utiliser le portail Azure afin de configurer le réseau virtuel de sorte que le runtime d’intégration Azure-SSIS puisse le rejoindre. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article explique comment récupérer des informations sur un runtime d’intégration Azure-SSIS ainsi que des descriptions d’état dans les informations renvoyées. 
 
