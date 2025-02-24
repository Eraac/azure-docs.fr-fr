---
title: Tutoriel - Réexpédier Azure Data Box Heavy | Microsoft Docs
description: Découvrir comment réexpédier votre Azure Data Box Heavy à Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 84db33e4c7ac612353c590ac9d2904ac3bc48d38
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592392"
---
# <a name="tutorial-return-azure-data-box-heavy-and-verify-data-upload-to-azure"></a>Didacticiel : Retourner l’appareil Azure Data Box Heavy et vérifier le chargement des données dans Azure


Ce tutoriel vous explique comment retourner l’appareil Azure Data Box Heavy et vérifier le chargement des données dans Azure.

Ce didacticiel fournit des informations sur les sujets suivants :

> [!div class="checklist"]
> * Prérequis
> * Préparer l’expédition
> * Expédier le Data Box Heavy à Microsoft
> * Vérifier le chargement des données dans Azure
> * Effacer les données du Data Box Heavy

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez les points suivants :

- Vous avez terminé le [Tutoriel : Copier des données sur Azure Data Box et procéder à une vérification](data-box-heavy-deploy-copy-data.md).
- Les tâches de copie sont terminées. La préparation de l’expédition ne peut pas s’effectuer si les tâches de copie sont en cours d’exécution.

## <a name="prepare-to-ship"></a>Préparer l’expédition

[!INCLUDE [data-box-heavy-prepare-to-ship](../../includes/data-box-heavy-prepare-to-ship.md)]

## <a name="ship-data-box-heavy-back"></a>Réexpédier le Data Box Heavy

1. Vérifiez que l’appareil est hors tension et que tous les câbles sont débranchés. Enroulez et fixez les 4 cordons d’alimentation sur le plateau accessible à l’arrière de l’appareil.
2. L’appareil est livré par chargement partiel via FedEx aux États-Unis et DHL dans l’Union européenne

    1. Contactez le service des [Opérations Data Box](mailto:DataBoxOps@microsoft.com) pour l’informer de l’enlèvement et obtenir l’étiquette de réexpédition.
    2. Appelez le numéro local pour votre transporteur pour planifier l’enlèvement.
    3. Assurez-vous que l’étiquette d’expédition est bien visible à l’extérieur du colis.
    4. Vérifiez que les anciennes étiquettes de l’expédition précédente sont retirées de l’appareil.
3. Une fois que le Data Box Heavy a été récupéré et scanné par le transporteur, l’état de la commande dans le portail affiche **Collecté**. Un ID de suivi s’affiche également.

## <a name="verify-data-upload-to-azure"></a>Vérifier le chargement des données dans Azure

Lorsque Microsoft reçoit et analyse l’appareil, l’état de la commande est mis à jour sur **Reçue**. L’appareil subit une vérification physique afin de détecter des dommages ou des signes d’altération potentiels.

Une fois la vérification terminée, le Data Box Heavy est connecté au réseau du centre de données Azure. La copie des données démarre automatiquement. Selon la taille des données, l’opération de copie peut prendre de quelques heures à quelques jours. Vous pouvez suivre la progression du travail de copie dans le portail.

Une fois la copie terminée, la commande passe à l’état **Completed (Terminée)** .

Vérifiez que vos données ont été chargées dans Azure avant de les supprimer de la source. Vos données peuvent se trouver aux emplacements suivants :

- Votre ou vos comptes de stockage Azure. Lorsque vous copiez les données sur Data Box, selon le type, les données sont chargées vers l’un des chemins d’accès ci-après dans votre compte Stockage Azure.

  - Pour les objets blob de blocs et de pages : `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Pour les fichiers Azure : `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Vous pouvez également accéder à votre compte de stockage Azure dans le Portail Azure et naviguer à partir de cet emplacement.

- Votre ou vos groupes de ressources spécifiés pour les disques managés. Quand vous créez des disques managés, les disques durs virtuels sont chargés en tant qu’objets blob de pages, puis convertis en disques managés. Les disques managés sont attachés aux groupes de ressources spécifiés au moment de la création de la commande. 

    - Si la copie vers des disques managés dans Azure a réussi, vous pouvez accéder aux **détails de la commande** dans le portail Azure et noter les noms des groupes de ressources spécifiés pour les disques managés.

        ![Identifier les groupes de ressources spécifiés pour les disques managés](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Accédez au groupe de ressources noté précédemment pour trouver vos disques managés.

        ![Disque managé attaché à des groupes de ressources](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Si vous avez copié un VHDX, ou un VHD dynamique ou de différenciation, le VHDX/VHD est chargé dans le compte de stockage de préproduction comme un objet blob de pages, mais la conversion du VHD en disque managé échoue. Accédez à votre compte de stockage de préproduction (**Compte de stockage > Objets blob**), puis sélectionnez le conteneur approprié (SSD Standard, HDD Standard ou SSD Premium). Les disques durs virtuels sont chargés en tant qu’objets blob de pages dans votre compte de stockage de préproduction.

## <a name="erasure-of-data-from-data-box-heavy"></a>Effacer les données du Data Box Heavy
 
Une fois que le chargement des données vers Azure est terminé, la Data Box efface les données de ses disques, conformément aux [instructions du standard NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). Une fois l’effacement terminé, vous pouvez [télécharger l’historique des commandes](data-box-portal-admin.md#download-order-history).

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a apporté des connaissances concernant Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
> * Prérequis
> * Préparer l’expédition
> * Expédier le Data Box Heavy à Microsoft
> * Vérifier le chargement des données dans Azure
> * Effacer les données du Data Box Heavy

Passez à l’article suivant pour apprendre à gérer le Data Box Heavy par le biais de l’interface utilisateur web locale.

> [!div class="nextstepaction"]
> [Administrer l’appareil Azure Data Box à l’aide de l’interface utilisateur web locale](./data-box-local-web-ui-admin.md)


