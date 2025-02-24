---
title: Créer une machine virtuelle Windows zonale avec le portail Azure | Microsoft Docs
description: Créer une machine virtuelle Windows dans une zone de disponibilité avec le portail Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 15dd81b6d222c811fe26ec693927355ebdeb47ee
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719031"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Créer une machine virtuelle Windows dans une zone de disponibilité avec le portail Azure

Cet article aborde l’utilisation du portail Azure pour créer une machine virtuelle dans une zone de disponibilité Azure. Une [zone de disponibilité](../../availability-zones/az-overview.md) est une zone physiquement séparée dans une région Azure. Utilisez les zones de disponibilité pour protéger vos applications et vos données dans l’éventualité peu probable d’une défaillance ou d’une perte d’un centre de données entier.

Pour utiliser une zone de disponibilité, créez votre machine virtuelle dans une [région Azure prise en charge](../../availability-zones/az-overview.md#services-support-by-region).

## <a name="sign-in-to-azure"></a>Connexion à Azure 

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.

2. Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**. 

3. Saisissez les informations de la machine virtuelle. Le nom d’utilisateur et le mot de passe que vous avez entrés vous serviront pour vous connecter à la machine virtuelle. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](faq.md#what-are-the-password-requirements-when-creating-a-vm). Choisissez un emplacement, par exemple USA Est 2, qui prend en charge des zones de disponibilité. Lorsque vous avez terminé, cliquez sur **OK**.

    ![Saisie des informations de base sur votre machine virtuelle dans le panneau du portail](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Choisissez une taille pour la machine virtuelle. Sélectionnez une taille recommandée, ou filtrez sur la base de fonctionnalités. Confirmez que la taille est disponible dans la zone que vous souhaitez utiliser.

    ![Sélectionnez une taille de machine virtuelle](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. Dans **Paramètres** > **Haute disponibilité**, sélectionnez une des zones numérotées dans la liste déroulante **Zone de disponibilité**, conservez les valeurs par défaut restantes et cliquez sur **OK**.

    ![Sélectionner une zone de disponibilité](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Dans la page de résumé, cliquez sur **Créer** pour démarrer le déploiement de la machine virtuelle.

7. La machine virtuelle sera épinglée au tableau de bord du portail Azure. Une fois le déploiement terminé, le récapitulatif de la machine virtuelle s’ouvre automatiquement.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Confirmer la zone du disque managé et de l’adresse IP

Lorsque la machine virtuelle est déployée dans une zone de disponibilité, un disque managé pour la machine virtuelle est créé dans la même zone de disponibilité. Par défaut, une adresse IP publique est également créée dans cette zone.

Vous pouvez confirmer les paramètres de zone pour ces ressources dans le portail.  

1. Cliquez sur **Groupes de ressources**, puis sur le nom du groupe de ressource de la machine virtuelle, par exemple *myResourceGroup*.

2. Cliquez sur le nom de la ressource de disque. La page **Vue d’ensemble** inclut des détails sur l’emplacement et sur la zone de disponibilité de la ressource.

    ![Zone de disponibilité du disque managé](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Cliquez sur le nom de la ressource de l’adresse IP publique. La page **Vue d’ensemble** inclut des détails sur l’emplacement et sur la zone de disponibilité de la ressource.

    ![Zone de disponibilité pour l’adresse IP](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer une machine virtuelle dans une zone de disponibilité. Apprenez-en davantage sur les [régions et la disponibilité](regions-and-availability.md) des machines virtuelles Azure.
