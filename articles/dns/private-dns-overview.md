---
title: Qu’est-ce qu’Azure Private DNS ?
description: Vue d’ensemble du service d’hébergement DNS privé sur Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: aedace031eaedf2709993b5185979e8777821759
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444834"
---
# <a name="what-is-azure-private-dns"></a>Qu’est-ce qu’Azure Private DNS ?

> [!IMPORTANT]
> Le DNS privé Azure est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le DNS (Domain Name System) se charge de traduire (ou résoudre) un nom de service en une adresse IP.  Azure DNS est un service d’hébergement pour les domaines DNS qui offre une résolution de noms à l’aide de l’infrastructure Microsoft Azure. Outre les domaines DNS accessibles sur Internet, Azure DNS prend en charge les zones DNS privées.

Le DNS privé Azure fournit un service DNS fiable et sécurisé pour gérer et résoudre les noms de domaine dans un réseau virtuel sans nécessiter l’ajout de solution DNS personnalisée. Grâce aux zones DNS privées, vous pouvez utiliser vos propres noms de domaine personnalisés au lieu des noms fournis par Azure actuellement disponibles. L’utilisation de noms de domaine personnalisés vous aide à adapter votre architecture de réseau virtuel en fonction des besoins de votre organisation. Elle offre une résolution de noms pour les machines virtuelles au sein d’un réseau virtuel et entre plusieurs réseaux virtuels. De plus, vous pouvez configurer des noms de zones avec une vue divisée qui permet à des zones DNS publique et privée de partager le nom.

Pour résoudre les enregistrements d’une zone DNS privée de votre réseau virtuel, vous devez lier le réseau virtuel à la zone. Les réseaux virtuels liés disposent d’un accès complet et peuvent résoudre tous les enregistrements DNS publiés dans la zone privée. En outre, vous pouvez activer l’inscription automatique sur un lien de réseau virtuel. Si vous activez l’inscription automatique sur un lien de réseau virtuel, les enregistrements DNS pour les machines virtuelles sur ce réseau virtuel sont inscrites dans la zone privée. Quand l’inscription automatique est activée, Azure DNS met également à jour les enregistrements de zone chaque fois qu’une machine virtuelle est créée, change d’adresse IP ou est supprimée.

![Vue d’ensemble de DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> La meilleure pratique consiste à ne pas utiliser de domaine *.local* pour votre zone DNS privée. Certains systèmes d’exploitation ne le permettent pas.

## <a name="benefits"></a>Avantages

Un DNS privé Azure offre les avantages suivants :

* **Évite d’avoir à utiliser des solutions DNS personnalisées**. Auparavant, un grand nombre de clients devaient créer des solutions DNS personnalisées pour gérer les zones DNS dans leur réseau virtuel. Vous pouvez désormais gérer les zones DNS à l’aide de l’infrastructure native d’Azure, ce qui supprime la lourde tâche de créer et gérer des solutions DNS personnalisées.

* **Prise en charge de tous les types d’enregistrements DNS courants**. Azure DNS prend en charge les enregistrements A, AAAA, CNAME, MX, PTR, SOA, SRV et TXT.

* **Gestion automatique des enregistrements de noms d’hôte**. En plus d’héberger vos enregistrements DNS personnalisés, Azure gère automatiquement les enregistrements de noms d’hôte pour les machines virtuelles dans les réseaux virtuels spécifiés. Dans ce scénario, vous pouvez optimiser les noms de domaine que vous utilisez sans avoir à créer de solutions DNS personnalisées ni modifier les applications.

* **Résolution de noms d’hôte entre réseaux virtuels**. Contrairement aux noms d’hôte fournis par Azure, les zones DNS privé peuvent être partagées entre des réseaux virtuels. Cette fonctionnalité simplifie les scénarios de détection de services et réseaux croisés, tels que l’homologation de réseaux virtuels.

* **Outils familiers et expérience utilisateur**. Pour réduire la courbe d’apprentissage, ce service offre utilise des outils Azure DNS bien établis (portail Azure, Azure PowerShell, Azure CLI, modèles Azure Resource Manager et API REST).

* **Prise en charge DNS avec découpage d’horizon**. Azure DNS vous permet de créer des zones portant le même nom capables de résoudre des résultats différents au sein d’un réseau virtuel et à partir de l’Internet public. Un scénario classique de DNS avec découpage d’horizon consiste à fournir une version dédiée d’un service pour une utilisation au sein du réseau virtuel.

* **Disponible dans toutes les régions Azure**. Les zones privées Azure DNS sont disponibles dans toutes les régions Azure dans le cloud public Azure.

## <a name="capabilities"></a>Fonctionnalités

Azure DNS offre les fonctionnalités suivantes :

* **Inscription automatique de machines virtuelles à partir d’un réseau virtuel lié à une zone privée avec l’inscription automatique activée**. Les machines virtuelles sont inscrites (ajoutées) à la zone privée en tant qu’enregistrements A pointant vers leurs adresses IP privées. Lors de la suppression d’une machine virtuelle figurant dans un lien de réseau virtuel pour lequel l’inscription automatique est activée, Azure supprime aussi automatiquement l’enregistrement DNS correspondant de la zone privée liée.

* **Prise en charge de la résolution DNS sur plusieurs réseaux virtuels liés à la zone privée**. Pour la résolution DNS entre réseaux virtuels, il n’existe aucune dépendance explicite ayant pour effet d’appairer ces réseaux. En revanche, vous pouvez appairer des réseaux virtuels à d’autres fins (par exemple, pour le trafic HTTP).

* **Prise en charge de la recherche DNS inversée dans l’étendue du réseau virtuel**. La recherche DNS inversée d’une adresse IP privée dans le réseau virtuel assigné à une zone privée retourne le nom de domaine complet qui inclut le nom de l’enregistrement/hôte, ainsi que le nom de la zone comme suffixe.

## <a name="known-issues"></a>Problèmes connus
Voici les bogues et problèmes connus dans la préversion :
* Si vous supprimez un réseau virtuel lié à une zone DNS privée, cela n’a pas pour effet de supprimer les liens vers la zone DNS privée. Si vous recréez le réseau virtuel avec les mêmes nom et groupe de ressources, puis tentez de le lier à nouveau à une zone DNS privée, l’opération échoue. Pour contourner ce problème, créez le réseau virtuel dans un autre groupe de ressources ou sous un autre nom dans le même groupe de ressources.
* Si vous déplacez un réseau virtuel vers un autre groupe de ressources ou abonnement, les liens vers la zone DNS privée ne sont pas actualisés. La résolution de noms pour le réseau virtuel déplacé continue de fonctionner mais, lorsque vous affichez les liens de réseau virtuel de la zone DNS privée, les anciens ID ARM du réseau virtuel apparaissent.
* À l’heure actuelle, les réseaux virtuels liés hébergés dans les zones Émirats arabes unis Nord, Émirats arabes unis Centre, Afrique du Sud Ouest, Afrique du Sud Nord, Canada Est et France Sud peuvent être défaillants et vous pouvez rencontrer des problèmes de résolution DNS intermittents. 


## <a name="other-considerations"></a>Autres points à considérer

Azure DNS présente les limites suivantes :

* Un réseau virtuel ne peut être lié qu’à une seule zone privée, car l’inscription automatique des enregistrements DNS de machine virtuelle est activée. Vous pouvez toutefois lier plusieurs réseaux virtuels à une seule zone DNS.
* Un DNS inversé ne fonctionne que pour un espace d’adressage IP privé sur le réseau virtuel lié
* Un DNS inversé pour une adresse IP privée de réseau virtuel lié retourne « internal.cloudapp.net » en tant que suffixe par défaut pour la machine virtuelle. Pour les réseaux virtuels liés à une zone privée avec l’inscription automatique activée, un DNS inversé pour une adresse IP privée renvoie 2 noms de domaine complets, l’un avec le suffixe par défaut *internal.cloudapp.net*, et l’autre avec le suffixe de la zone privée.
* Actuellement, le transfert conditionnel n’est pas pris en charge en mode natif. Pour activer la résolution entre réseaux Azure et locaux. Consultez [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
 
## <a name="pricing"></a>Tarifs

Pour obtenir des informations sur les prix, voir la [tarification Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment créer une zone privée dans Azure DNS à l’aide [d’Azure PowerShell](./private-dns-getstarted-powershell.md) ou [d’Azure CLI](./private-dns-getstarted-cli.md).

* Passez en revue certains [scénarios de zones privées](./private-dns-scenarios.md) courants qui peuvent être réalisés avec des zones privées dans Azure DNS.

* Pour trouver des réponses aux questions fréquemment posées concernant les zones privées dans Azure DNS, notamment le comportement spécifique auquel vous attendre pour certains types d’opérations, voir le [FAQ sur les DNS privés](./dns-faq-private.md).

* Pour plus d’informations sur les zones et enregistrements DNS, consultez [Vue d’ensemble des enregistrements et zones DNS](dns-zones-records.md).

* Découvrez certaines des autres [fonctionnalités de réseau](../networking/networking-overview.md) clés d’Azure.
