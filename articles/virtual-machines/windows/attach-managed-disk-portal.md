---
title: Attacher un disque de données géré à une machine virtuelle Windows - Azure | Microsoft Docs
description: Comment attacher un disque de données managé à une machine virtuelle Windows à l’aide du portail Azure.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e3641960131d23bf5a8e5b2310a09e7a4dbd70b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64680400"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Attacher un disque de données managé à une machine virtuelle Windows à l’aide du portail Azure

Cet article montre comment attacher un nouveau disque de données managé à une machine virtuelle Windows à l’aide du portail Azure. La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher. Pour plus d’informations, consultez [Tailles des machines virtuelles](sizes.md).


## <a name="add-a-data-disk"></a>Ajouter un disque de données

1. Dans le menu de gauche du [portail Azure](https://portal.azure.com), sélectionnez **Machines virtuelles**.
2. Sélectionnez une machine virtuelle dans la liste.
3. Dans la page **Machine virtuelle**, sélectionnez **Disques**.
4. Dans la page **Disques**, sélectionnez **Ajouter un disque de données**.
5. Dans la liste déroulante du nouveau disque, sélectionnez **Créer un disque**.
6. Sur la page **Créer un disque managé**, tapez le nom du disque et ajustez les autres paramètres si nécessaire. Sélectionnez **Créer** lorsque vous avez terminé.
7. Dans la page **Disques**, sélectionnez **Enregistrer** pour enregistrer la nouvelle configuration de disque de la machine virtuelle.
8. Une fois qu’Azure l’a créé et attaché à la machine virtuelle, le nouveau disque est répertorié dans les paramètres de disque de la machine virtuelle, sous **Disques de données**.


## <a name="initialize-a-new-data-disk"></a>Initialisation d’un nouveau disque de données

1. Connectez-vous à la machine virtuelle.
1. Sélectionnez le menu **Démarrer** de Windows dans la machine virtuelle en cours d’exécution et entrez **diskmgmt.msc** dans la zone de recherche. La console **Gestion des disques** console s’ouvre.
2. L’outil Gestion des disques détermine que votre nouveau disque n’est pas initialisé et affiche la fenêtre **Initialiser le disque**.
3. Vérifiez que le nouveau disque est sélectionné, puis sélectionnez **OK** pour l’initialiser.
4. Le nouveau disque apparaît comme **non alloué**. Cliquez avec le bouton droit n’importe où sur le disque, puis sélectionnez **Nouveau volume simple**. La fenêtre **Assistant Création d’un volume simple** s’ouvre.
5. Exécutez l’Assistant en conservant tous les paramètres par défaut. Quand vous avez terminé, sélectionnez **Terminer**.
6. Fermez **Gestion des disques**.
7. Une fenêtre contextuelle s’affiche et vous demande de formater le nouveau disque pour que vous puissiez l’utiliser. Sélectionnez **Formater le disque**.
8. Dans la fenêtre **Formater un nouveau disque**, vérifiez les paramètres, puis sélectionnez **Démarrer**.
9. Un avertissement vous informe que le formatage des disques efface toutes les données. Sélectionnez **OK**.
10. Une fois le formatage terminé, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez également [attacher un disque de données à l’aide de PowerShell](attach-disk-ps.md).
- Si votre application doit utiliser le lecteur *D:* pour stocker des données, vous pouvez [changer la lettre de lecteur du disque temporaire Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
