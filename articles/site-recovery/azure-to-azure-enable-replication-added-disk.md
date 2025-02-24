---
title: Activer la réplication d'un disque ajouté à une machine virtuelle Azure répliquée par Azure Site Recovery | Microsoft Docs
description: Cet article explique comment activer la réplication d'un disque ajouté à une machine virtuelle Azure activée pour la récupération d'urgence avec Azure Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: asgang
ms.openlocfilehash: 4a262a3a0c32516988890a6afc6eef34d8655c89
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671870"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Activer la réplication d'un disque ajouté à une machine virtuelle


Cet article explique comment activer la réplication de disques de données ajoutés à une machine virtuelle Azure déjà activée pour la récupération d'urgence dans une autre région à l'aide d'[Azure Site Recovery](site-recovery-overview.md).

L'activation de la réplication d'un disque ajouté à une machine virtuelle est prise en charge sur les machines virtuelles Azure dotées de disques managés.

Lorsque vous ajoutez un nouveau disque à une machine virtuelle Azure qui est répliquée dans une autre région Azure, voici ce qui se produit :

-   L'intégrité de la réplication de la machine virtuelle affiche un avertissement, et une note apparaît sur le portail pour vous informer qu'un ou plusieurs disques peuvent être protégés.
-   Si vous activez la protection pour les disques ajoutés, l'avertissement disparaît après la réplication initiale du disque.
-   Si vous choisissez de ne pas activer la réplication du disque, vous pouvez masquer l'avertissement.

![Nouveau disque ajouté](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Avant de commencer

Cet article suppose que vous avez déjà configuré la récupération d'urgence sur la machine virtuelle à laquelle vous ajoutez le disque. Si ce n'est pas le cas, suivez le [didacticiel Récupération d'urgence Azure vers Azure](azure-to-azure-tutorial-enable-replication.md). 

## <a name="enable-replication-for-an-added-disk"></a>Activer la réplication pour un disque ajouté 

Pour activer la réplication d'un disque ajouté, procédez comme suit :

1. Dans le coffre > **Éléments répliqués**, cliquez sur la machine virtuelle à laquelle vous avez ajouté le disque.
2. Cliquez sur **Disques**, puis sélectionnez le disque de données pour lequel vous voulez activer la réplication (ces disques présentent un état **Non protégé**).
3.  Dans **Détails du disque**, cliquez sur **Activer la réplication**.

    ![Activer la réplication d'un disque ajouté](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Une fois le travail d'activation de la réplication exécuté et la réplication initiale terminée, l'avertissement relatif à l'intégrité de la réplication du disque disparaît.



# <a name="next-steps"></a>Étapes suivantes

[En savoir plus](site-recovery-test-failover-to-azure.md) sur l’exécution d’un test de basculement.
