---
title: Configurer le GPU pour Windows Virtual Desktop Preview - Azure
description: Comment activer le rendu et le codage avec accélération GPU dans Windows Virtual Desktop Preview.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: b6a4811f685803ecdc079a690d550618c071c4a6
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620196"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-preview"></a>Configurer l’accélération GPU pour Windows Virtual Desktop Preview

Windows Virtual Desktop Preview prend en charge le rendu et le codage avec accélération GPU pour des performances d’applications et une scalabilité améliorées. L’accélération GPU est particulièrement importante pour les applications graphiques.

Suivez les instructions de cet article pour créer une machine virtuelle Azure optimisée pour le GPU, ajoutez-la à votre pool d’hôtes et configurez-la pour utiliser l’accélération GPU à des fins de codage et de rendu. Cet article suppose que vous avez déjà un locataire Windows Virtual Desktop configuré.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Sélectionner une taille de machine virtuelle optimisée pour le GPU

Azure offre plusieurs [tailles de machine virtuelle optimisées pour le GPU](/azure/virtual-machines/windows/sizes-gpu). Le bon choix pour votre pool d’hôtes dépend de plusieurs facteurs, notamment vos charges de travail d’applications spécifiques, la qualité souhaitée de l’expérience utilisateur et le coût. En général, les GPU plus volumineux et performants offrent une meilleure expérience utilisateur selon une densité d’utilisateurs donnée.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Créer un pool d’hôtes, provisionner votre machine virtuelle et configurer un groupe d’applications

Créez un nouveau pool d’hôtes à l’aide d’une machine virtuelle de la taille sélectionnée. Pour obtenir des instructions, consultez [Tutoriel : Créer un pool d’hôtes avec la Place de marché Azure](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Windows Virtual Desktop Preview prend en charge le rendu et le codage avec accélération GPU dans les systèmes d’opération suivants :

* Windows 10 version 1511 ou ultérieure
* Windows Server 2016 ou version ultérieure

Vous devez également configurer un groupe d’applications ou utilisez le groupe d’applications bureau par défaut (nommé « Groupe d’applications bureau ») qui est créé automatiquement lorsque vous créez un nouveau pool d’hôtes. Pour obtenir des instructions, consultez [Tutoriel : Gérer des groupes d’applications pour Windows Virtual Desktop Preview](/azure/virtual-desktop/manage-app-groups).

>[!NOTE]
>Windows Virtual Desktop Preview prend uniquement en charge le type de groupe d’applications « Bureau » pour les pools d’hôtes prenant en charge le GPU. Les groupes d’applications de type « RemoteApp » ne sont pas pris en charge pour les pools d’hôtes prenant en charge le GPU.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Installer des pilotes graphiques pris en charge dans votre machine virtuelle

Pour tirer parti des fonctionnalités GPU des machines virtuelles série N Azure dans Windows Virtual Desktop Preview, vous devez installer des pilotes graphiques NVIDIA. Suivez les instructions dans [Installer des pilotes GPU NVIDIA sur des machines virtuelles série N exécutant Windows](/azure/virtual-machines/windows/n-series-driver-setup) pour installer des pilotes, manuellement ou à l’aide de [l’Extension de pilote GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows).

Notez que seuls les [pilotes GRID NVIDIA](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) distribués par Azure sont pris en charge pour Windows Virtual Desktop Preview.

Après l’installation du pilote, le redémarrage de la machine virtuelle est nécessaire. Utilisez les étapes de vérification dans les instructions ci-dessus pour confirmer que les pilotes graphiques ont été correctement installés.

## <a name="configure-gpu-accelerated-app-rendering"></a>Configurer le rendu d’application avec accélération GPU

Par défaut, le rendu des applications et des bureaux exécutés dans des configurations multi-session est effectué avec le processeur et n’exploite pas le GPU disponible. Configurer la stratégie de groupe pour l’hôte de session afin d’activer le rendu avec accélération GPU :

1. Connectez-vous au bureau de la machine virtuelle à l’aide d’un compte avec des privilèges d’administrateur local.
2. Ouvrez le menu Démarrer, puis tapez « gpedit.msc » pour ouvrir l’Éditeur de stratégie de groupe.
3. Naviguez dans l’arborescence vers **Configuration de l’ordinateur** > **Modèles d’administration** > **Composants Windows** > **Services Bureau à distance** > **Hôte de session Bureau à distance** > **Environnement de session à distance**.
4. Sélectionnez la stratégie **Utiliser l’adaptateur graphique matériel par défaut pour toutes les sessions Services Bureau à distance** et définissez-la sur **Activé** pour activer le rendu GPU dans la session à distance.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Configurer le codage d’image avec accélération GPU

Le Bureau à distance code tous les graphiques rendus par les applications et les bureaux (que le rendu ait été fait avec le GPU ou l’UC) pour la transmission aux clients Bureau à distance. Par défaut, le Bureau à distance n’exploite pas les GPU disponibles pour ce codage. Configurez la stratégie de groupe pour l’hôte de session afin d’activer le codage d’image avec accélération GPU. Continuez les étapes ci-dessus :

1. Sélectionnez la stratégie **Prioriser le mode H.264/AVC 444 Graphics pour les connexions Bureau à distance** et définissez-la sur **Activé** pour forcer le codec H.264/AVC 444 dans la session à distance.
2. Sélectionnez la stratégie **Configurer le codage matériel H.264/AVC pour les connexions Bureau à distance** et définissez-la sur **Activé** pour activer le codage de matériel pour AVC/H.264 dans la session à distance.

    >[!NOTE]
    >Dans Windows Server 2016, définissez l’option **Préférer le codage matériel AVC** sur **Toujours essayer**.

3. Maintenant que les stratégies de groupe ont été modifiées, forcez une mise à jour de la stratégie de groupe. Ouvrez l’invite de commandes et tapez :

    ```batch
    gpupdate.exe /force
    ```

4. Déconnectez-vous de la session Bureau à distance.

## <a name="verify-gpu-accelerated-app-rendering"></a>Vérifier le rendu d’application avec accélération GPU

Pour vérifier que les applications utilisent le GPU pour le rendu, essayez l’une des opérations suivantes :

* Utilisez l’utilitaire `nvidia-smi` comme décrit dans [Vérifier l’installation du pilote](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) pour vérifier l’utilisation du GPU lors de l’exécution de vos applications.
* Dans les versions de système d’exploitation prises en charge, vous pouvez utiliser le Gestionnaire des tâches pour vérifier l’utilisation du GPU. Sélectionnez le GPU dans l’onglet « Performances » pour voir si les applications utilisent le GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Vérifier le codage d’image avec accélération GPU

Pour vérifier que le Bureau à distance utilise le codage avec accélération GPU :

1. Connectez-vous au bureau de la machine virtuelle à l’aide du client Windows Virtual Desktop.
2. Lancer l’Observateur d’événements et accédez au nœud suivant : **Journaux des applications et services** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreTS**  >  **Opérationnel**
3. Pour déterminer si de codage avec accélération GPU est utilisé, recherchez l’ID d’événement 170. Si vous voyez « Encodeur matériel AVC activé : 1 » alors le codage GPU est utilisé.
4. Pour déterminer si le mode AVC 444 est utilisé, recherchez l’ID d’événement 162. Si vous voyez « AVC disponible : 1 Profil initial : 2048 » alors AVC 444 est utilisé.

## <a name="next-steps"></a>Étapes suivantes

Grâce à ces instructions, vous pouvez commencer à utiliser l’accélération GPU sur une machine virtuelle hôte de session unique. Considérations supplémentaires pour l’activation de l’accélération GPU sur un plus grand pool d’hôtes :

* Envisagez d’utiliser [l’Extension du pilote GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) pour simplifier l’installation du pilote et les mises à jour sur plusieurs machines virtuelles.
* Envisagez d’utiliser la stratégie de groupe Active Directory pour simplifier la configuration de la stratégie de groupe sur plusieurs machines virtuelles. Pour plus d’informations sur le déploiement de la stratégie de groupe dans le domaine Active Directory, consultez [Utilisation d’objets stratégie de groupe](https://go.microsoft.com/fwlink/p/?LinkId=620889).
