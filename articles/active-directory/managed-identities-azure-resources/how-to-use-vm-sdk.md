---
title: Comment utiliser des identités managées pour ressources Azure sur une machine virtuelle Azure avec des kits de développement logiciel (SDK) Azure
description: Exemples de code pour l’utilisation de kits de développement logiciel (SDK) Azure avec une machine virtuelle Azure qui a des identités managées pour ressources Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00c86562e0fdb4e6d62d44088b7aba08e45e22a4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60293232"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Comment utiliser des identités managées pour ressources Azure sur une machine virtuelle Azure avec des kits de développement logiciel (SDK) Azure 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Cet article fournit une liste d’exemples de kits de développement logiciel (SDK), qui illustrent l’utilisation de leur prise en charge respective du Kit de développement logiciel (SDK) pour des identités managées pour ressources Azure.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Les exemples de code/script dans cet article partent du principe que le client est exécuté sur une machine virtuelle disposant d’identités managées pour ressources Azure activées. Utilisez la fonctionnalité « Se connecter » de machine virtuelle dans le portail Azure, pour vous connecter à distance à votre machine virtuelle. Pour plus d’informations sur l’activation d’identités managées pour ressources Azure sur une machine virtuelle, voir [Configurer des identités managées pour ressources Azure sur une machine virtuelle en utilisant le portail Azure](qs-configure-portal-windows-vm.md), ou l’une des variantes de cet article (en utilisant PowerShell, CLI, un modèle ou un Kit de développement logiciel (SDK) Azure). 

## <a name="sdk-code-samples"></a>Exemples de code de kit de développement logiciel

| Kit SDK             | Exemple de code |
| --------------- | ----------- |
| .NET            | [Déployer un modèle Azure Resource Manager à partir d’une machine virtuelle Windows en utilisant des identités managées pour ressources Azure](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Appeler des services Azure à partir d’une machine virtuelle Linux en utilisant des identités managées pour ressources Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Gérer des ressources en utilisant des identités managées pour ressources Azure](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Utiliser des identités managées pour ressources Azure afin de s’authentifier simplement à partir d’une machine virtuelle](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Gérer les ressources à partir d’une machine virtuelle avec des identités managées pour ressources Azure activées](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Kits de développement logiciel Azure](https://azure.microsoft.com/downloads/) pour obtenir la liste complète des ressources des kits de développement Azure, y compris les téléchargements de bibliothèque, la documentation et bien plus encore.
- Pour activer les identités managées pour ressources Azure sur une machine virtuelle Azure, voir [Configurer des identités managées pour ressources Azure sur une machine virtuelle en utilisant le portail Azure](qs-configure-portal-windows-vm.md).








