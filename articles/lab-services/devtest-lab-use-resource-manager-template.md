---
title: Afficher et utiliser un modèle Azure Resource Manager de machine virtuelle | Microsoft Docs
description: Découvrez comment utiliser le modèle Azure Resource Manager à partir d’une machine virtuelle pour créer d’autres machines virtuelles
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 533770d98b146dea01e91e1249115c4b5c074b3c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62101561"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Créer des machines virtuelles à l’aide d’un modèle Azure Resource Manager 

Lorsque vous créez une machine virtuelle dans DevTest Labs via le [portail Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040), vous pouvez voir le modèle Azure Resource Manager avant d’enregistrer la machine virtuelle. Le modèle peut alors être utilisé comme base pour créer d’autres machines virtuelles lab avec les mêmes paramètres.

Cet article décrit les modèles Resource Manager utilisant une ou plusieurs machines virtuelles et vous montre comment afficher et enregistrer un modèle lors de la création d’une machine virtuelle.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Modèles Resource Manager à plusieurs machines virtuelles ou à machine virtuelle unique
Il existe deux façons de créer des machines virtuelles dans DevTest Labs avec un modèle Resource Manager : provisionner la ressource Microsoft.DevTestLab/labs/virtualmachines ou provisionner la ressource Microsoft.Compute/virtualmachines. Chaque méthode est utilisée dans des scénarios différents et nécessite des autorisations différentes.

- Les modèles Resource Manager qui utilisent un type de ressource Microsoft.DevTestLab/labs/virtualmachines (tel que déclaré dans la propriété « ressource » du modèle) peuvent configurer des machines virtuelles lab individuelles. Chaque machine virtuelle apparaît alors sous la forme d’un seul élément dans la liste de machines virtuelles DevTest Labs :

   ![Liste des machines virtuelles qui apparaissent comme un seul élément dans la liste de machines virtuelles DevTest Lab](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Ce type de modèle Resource Manager peut être provisionné via la commande Azure PowerShell **New-AzResourceGroupDeployment** ou via la commande Azure CLI **az group deployment create**. Il requiert des autorisations d’administrateur. Les utilisateurs assignés à un rôle d’utilisateur DevTest Labs ne peuvent donc pas effectuer le déploiement. 

- Les modèles Resource Manager qui utilisent un type de ressource Microsoft.Compute/virtualmachines peuvent configurer plusieurs machines virtuelles en tant qu’environnement unique dans la liste des machines virtuelles DevTest Labs :

   ![Liste des machines virtuelles qui apparaissent comme un seul élément dans la liste de machines virtuelles DevTest Lab](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Les machines virtuelles qui se trouvent dans le même environnement peuvent être gérées ensemble et partagent le même cycle de vie. Les utilisateurs affectés à un rôle d’utilisateur DevTest Labs peuvent créer des environnements à l’aide de ces modèles, si l’administrateur a configuré le laboratoire de cette façon.

Le reste de cet article présente les modèles Resource Manager qui utilisent Microsoft.DevTestLab/labs/virtualmachines. Ils sont utilisés par les administrateurs de laboratoire pour automatiser la création de machines virtuelles lab (par exemple, des machines virtuelles revendicables) ou la génération de l’image de référence (par exemple, Image Factory).

Les [Bonnes pratiques de création de modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) contiennent de nombreuses directives et suggestions pour vous aider à créer des modèles Azure Resource Manager fiables et faciles à utiliser.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Afficher et enregistrer un modèle Resource Manager de machine virtuelle
1. Suivez les étapes de la section [Créer votre première machine virtuelle dans un laboratoire](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) pour commencer à créer une machine virtuelle.
1. Entrez les informations requises pour votre machine virtuelle et ajoutez les artefacts que vous souhaitez pour cette machine virtuelle.
1. Au bas de la fenêtre Configurer les paramètres, choisissez **Afficher le modèle ARM**.

   ![Bouton Afficher le modèle ARM](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. Copiez et enregistrez le modèle Resource Manager à utiliser ultérieurement pour créer une autre machine virtuelle.

   ![Modèle Resource Manager à enregistrer pour une utilisation ultérieure](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Après avoir enregistré le modèle Resource Manager, vous devez mettre à jour la section Paramètres du modèle avant de pouvoir l’utiliser. Vous pouvez créer un paramètre .json qui personnalise simplement les paramètres en dehors du modèle Resource Manager. 

![Personnaliser les paramètres à l’aide d’un fichier JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Le modèle Resource Manager est maintenant préparé pour [créer une machine virtuelle](devtest-lab-create-environment-from-arm.md).

### <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [Créer des environnements à plusieurs machines virtuelles avec les modèles Resource Manager](devtest-lab-create-environment-from-arm.md).
* [Déployer un modèle Resource Manager pour créer une machine virtuelle](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Découvrez les autres modèles Resource Manager à démarrage rapide pour l’automatisation de DevTest Labs à partir du [dépôt DevTest Labs GitHub public](https://github.com/Azure/azure-quickstart-templates).
