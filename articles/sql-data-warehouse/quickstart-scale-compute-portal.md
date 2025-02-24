---
title: 'Démarrage rapide : Effectuer un scale-out du calcul dans Azure SQL Data Warehouse – Portail Azure | Microsoft Docs'
description: Mettez à l’échelle le calcul dans Azure SQL Data Warehouse sur le Portail Azure. Montez en charge le calcul pour améliorer les performances, ou réduisez-le pour diminuer les coûts.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: Anthony.vanGemert
ms.reviewer: jrasnick
ms.openlocfilehash: 7c3e48cc57613b107e82cb0fbcb7d69aceb6cf0a
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479275"
---
## <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Démarrage rapide : Mettre à l’échelle le calcul dans Azure SQL Data Warehouse sur le Portail Azure

Mettez à l’échelle le calcul dans Azure SQL Data Warehouse sur le Portail Azure. [Augmentez le calcul](sql-data-warehouse-manage-compute-overview.md) pour améliorer les performances, ou réduisez-le pour diminuer les coûts. 

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Avant de commencer

Vous pouvez mettre à l’échelle un entrepôt de données que vous possédez déjà, ou utiliser la section [Démarrage rapide : Créer et connecter – Portail](create-data-warehouse-portal.md) pour créer un entrepôt de données nommé **mySampleDataWarehouse**.  Ce guide de démarrage rapide met à l’échelle **mySampleDataWarehouse**.

>[!Note]
>Votre entrepôt de données doit être en ligne pour la mise à l’échelle. 

## <a name="scale-compute"></a>Mise à l’échelle des ressources de calcul

Les ressources de calcul SQL Data Warehouse peuvent être mises à l’échelle en augmentant ou diminuant les unités DWU (Data Warehouse Unit). Le guide de démarrage rapide [Créer et connecter – Portail] (create-data-warehouse-portal.md) a permis de créer **mySampleDataWarehouse** et de l’initialiser avec 400 DWU. Les étapes suivantes ajustent les DWU de **mySampleDataWarehouse**.

Pour modifier les unités Data Warehouse Unit :

1. Cliquez sur **Entrepôts de données SQL Data Warehouse** sur la page de gauche du portail Azure.
2. Sélectionnez **mySampleDataWarehouse** sur la page **Entrepôts de données SQL Data Warehouse**. L’entrepôt de données s’ouvre.
3. Cliquez sur **Scale**.

    ![Cliquez sur Mettre à l’échelle.](media/quickstart-scale-compute-portal/click-scale.png)

2. Dans le panneau Mettre à l’échelle, déplacez le curseur vers la gauche ou vers la droite pour modifier le paramètre DWU.

    ![Déplacez le curseur](media/quickstart-scale-compute-portal/scale-dwu.png)

3. Cliquez sur **Enregistrer**. Un message de confirmation s’affiche. Cliquez sur **Oui** pour confirmer ou sur **Non** pour annuler.

    ![Cliquez sur Enregistrer.](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>Étapes suivantes
Vous avez désormais appris à mettre à l’échelle le calcul pour votre entrepôt de données. Pour en savoir plus sur Azure SQL Data Warehouse, continuez avec le didacticiel de chargement des données.

> [!div class="nextstepaction"]
>[Charger des données dans un entrepôt SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
