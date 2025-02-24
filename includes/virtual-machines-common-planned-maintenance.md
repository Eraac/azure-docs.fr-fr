---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 4/30/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c2931fa410cf92755a5df5b7129dcf93de900930
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177043"
---
Azure met régulièrement à jour sa plateforme pour améliorer la fiabilité, le niveau de performance et la sécurité de l’infrastructure hôte des machines virtuelles. L’objectif de ces mises à jour comprend l’application d’une mise à jour corrective aux composants logiciels de l’environnement d’hébergement, la mise à niveau des composants réseau et la désactivation du matériel. 

Les mises à jour affectent rarement les machines virtuelles hébergées. Lorsque les mises à jour ont un effet, Azure choisit alors la méthode ayant le moins d’impact pour l’exécution des mises à jour :

- Si une mise à jour sans redémarrage est possible, la machine virtuelle est en pause pendant la mise à jour de l’hôte ou fait l’objet d’une migration dynamique vers un hôte déjà mis à jour.

- Si la maintenance nécessite un redémarrage, vous êtes averti de la maintenance planifiée. Azure vous permet également de disposer d’un intervalle de temps durant lequel vous pouvez démarrer la maintenance vous-même, au moment qui vous convient. La période de maintenance manuelle est généralement de 30 jours, sauf si la maintenance est urgente. Azure investit dans des technologies qui permettent de réduire le nombre de cas où une maintenance de la plateforme exige que les machines virtuelles soient redémarrées. 

Cette page décrit la façon dont Azure effectue les deux types de maintenance. Pour plus d’informations sur les événements non planifiés (interruptions), consultez  [Gérer la disponibilité des machines virtuelles pour Windows](../articles/virtual-machines/windows/manage-availability.md) ou l’article correspondant pour [Linux](../articles/virtual-machines/linux/manage-availability.md).

Dans une machine virtuelle, vous pouvez recevoir une notification concernant une maintenance à venir [à l’aide de Scheduled Events pour Windows](../articles/virtual-machines/windows/scheduled-events.md) ou pour [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Pour plus d’informations sur la procédure de gestion de la maintenance planifiée, consultez [Gestion des notifications de maintenance planifiée pour Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou l’article correspondant pour [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Maintenance ne nécessitant aucun redémarrage

Comme indiqué précédemment, la plupart des mises à jour de la plateforme n’affectent pas les machines virtuelles cliente. Lorsqu’une mise à jour sans impact n’est pas possible, Azure choisit alors le mécanisme ayant le moins d’impact pour les machines virtuelles clientes. 

La plupart des maintenances ayant un impact mettent la machine virtuelle en pause pendant moins de 10 secondes. Dans certains cas, Azure utilise des mécanismes de maintenance avec préservation de la mémoire. Ces mécanismes interrompent la machine virtuelle pendant 30 secondes au maximum et conservent la mémoire dans la RAM. Une fois la machine virtuelle redémarrée, son horloge est synchronisée automatiquement. 

La maintenance avec préservation de la mémoire fonctionne avec plus de 90 % des machines virtuelles Azure. Elle ne fonctionne pas pour les modèles de série G, M, N et H. Azure utilise de plus en plus les technologies de migration dynamique et améliore les mécanismes de maintenance avec préservation de la mémoire pour réduire la durée de la mise en pause.  

Ces opérations de maintenance qui ne nécessitent pas de redémarrage sont appliquées à un domaine d’erreur à la fois. Elles sont interrompues si elles reçoivent des signaux d’avertissement relatifs à l’intégrité. 

Ces types de mise à jour peuvent affecter certaines applications. Lorsque la machine virtuelle fait l’objet d’une migration dynamique vers un autre hôte, certaines charges de travail sensibles peuvent subir une légère détérioration des performances au cours des quelques minutes qui précèdent la mise en pause de la machine virtuelle. Afin de préparer la maintenance de la machine virtuelle et de réduire l’impact sur la maintenance d’Azure, essayez d’[utiliser Scheduled Events pour Windows](../articles/virtual-machines/windows/scheduled-events.md) ou [Linux](../articles/virtual-machines/linux/scheduled-events.md) sur de telles applications. Azure travaille sur les fonctionnalités de contrôle de maintenance pour ces applications sensibles. 

### <a name="live-migration"></a>Migration dynamique

La migration dynamique est une opération qui ne nécessite aucun redémarrage et qui conserve la mémoire pour la machine virtuelle. Elle entraîne une pause ou un blocage qui dure généralement moins de 5 secondes. Toutes les machines virtuelles infrastructure as a service (IaaS) sont éligibles pour la migration dynamique, à l’exception des machines de série G, M, N et H. Les machines virtuelles éligibles représentent plus de 90 % des machines virtuelles IaaS déployées sur la flotte Azure. 

La plateforme Azure démarre la migration dynamique dans les scénarios suivants :
- Maintenance planifiée
- Défaillance matérielle
- Optimisations de l’allocation

Certains scénarios de maintenance planifiée utilisent la migration dynamique. Vous pouvez utiliser Scheduled Events pour connaître à l’avance la date de démarrage des opérations de migration dynamique.

La migration dynamique peut également servir à déplacer des machines virtuelles lorsque les algorithmes d’Azure Machine Learning prédisent une défaillance matérielle imminente ou lorsque vous souhaitez optimiser les allocations de machines virtuelles. Pour plus d’informations sur la modélisation prédictive détectant les instances de matériel détérioré, consultez [Améliorer la résilience des machines virtuelles Azure grâce à la migration dynamique et au Machine Learning prédictif](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Les notifications de migration dynamique s’affichent sur le portail Azure, dans les journaux Monitor et Service Health ainsi que dans Scheduled Events si vous utilisez ces services.

## <a name="maintenance-that-requires-a-reboot"></a>Maintenance nécessitant un redémarrage

Dans les rares cas où les machines virtuelles doivent redémarrer dans le cadre d’une maintenance planifiée, vous recevez une notification à l’avance. Une maintenance planifiée comprend deux phases : celle de libre-service et celle de maintenance planifiée.

Lors de la *phase de libre-service*, qui dure généralement quatre semaines, vous démarrez la maintenance sur vos machines virtuelles. Puisqu’il s’agit d’un libre-service, vous pouvez interroger chaque machine virtuelle pour afficher leur état et vérifier le résultat de votre dernière requête de maintenance.

Quand vous démarrez la maintenance libre-service, votre machine virtuelle est redéployée sur un nœud déjà mis à jour. À cause du redémarrage de la machine virtuelle, le disque temporaire est perdu et les adresses IP dynamiques associées à l’interface réseau virtuelle sont mises à jour.

Si vous rencontrez une erreur lors de la maintenance en libre-service, l’opération s’interrompt, la machine virtuelle n’est pas mise à jour et vous aurez la possibilité de retenter la maintenance en libre-service. 

Lorsque la phase de libre-service se termine, la *phase de maintenance planifiée* commence. Pendant cette phase, vous pouvez toujours interroger la phase de maintenance, mais vous ne pouvez pas démarrer la maintenance vous-même.

Pour plus d’informations sur la gestion des maintenances nécessitant un redémarrage, consultez [Gestion des notifications de maintenance planifiée pour Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou l’article correspondant pour [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considérations relatives à la disponibilité lors de la maintenance planifiée 

Si vous décidez d’attendre jusqu’à la phase de maintenance planifiée, plusieurs éléments sont à prendre en compte si vous voulez maintenir une haute disponibilité de vos machines virtuelles. 

#### <a name="paired-regions"></a>Régions jumelées

Chaque région Azure est jumelée à une autre région de la même zone géographique. Elles constituent une paire de régions. Durant la phase de maintenance planifiée, Azure met uniquement à jour les machines virtuelles d’une seule des régions d’une paire de régions. Par exemple, durant la mise à jour de la machine virtuelle dans la région USA Centre Nord, Azure ne met à jour simultanément aucune machine virtuelle dans la région USA Centre Sud. En revanche, les autres régions, Europe Nord par exemple, peuvent faire l’objet d’une maintenance en même temps que la région USA Est. Comprendre le fonctionnement des paires régionales peut vous aider à mieux répartir vos machines virtuelles entre les régions. Pour en savoir plus, consultez [Paires régionales Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Groupes à haute disponibilité et groupes identiques

Lorsque vous déployez une charge de travail sur des machines virtuelles Azure, vous pouvez créer des machines virtuelles dans un *groupe à haute disponibilité* afin de fournir une haute disponibilité pour votre application. À l’aide des groupes à haute disponibilité, vous pouvez vous assurer qu’au moins une machine virtuelle est disponible en cas de panne ou d’événements de maintenance nécessitant un redémarrage.

Au sein d’un groupe à haute disponibilité, les machines virtuelles individuelles sont réparties sur un maximum de 20 domaines de mise à jour (UD). Durant la maintenance planifiée, un seul UD est mis à jour à un moment donné. Les UD ne sont pas systématiquement mis à jour de façon séquentielle. 

Les *groupes de machines virtuelles identiques* sont des ressources de calcul Azure que vous pouvez utiliser pour déployer et gérer un ensemble de machines virtuelles identiques en tant que ressource unique. Le groupe identique est automatiquement déployé dans les domaines de mise à jour, comme les machines virtuelles dans un groupe à haute disponibilité. Comme pour les groupes à haute disponibilité, un seul UD est mis à jour à un moment donné dans les groupes identiques durant la maintenance planifiée.

Pour plus d’informations sur la configuration de vos machines virtuelles pour la haute disponibilité, consultez  [Gérer la disponibilité de vos machines virtuelles pour Windows](../articles/virtual-machines/windows/manage-availability.md) ou l’article correspondant pour [Linux](../articles/virtual-machines/linux/manage-availability.md).
