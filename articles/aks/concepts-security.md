---
title: 'Concepts : sécurité dans AKS (Azure Kubernetes Service)'
description: Découvrez la sécurité dans AKS (Azure Kubernetes Service), notamment la communication entre les maîtres et les nœuds, les stratégies réseau et les secrets Kubernetes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 1d100f17130594ace6169f5840915c88435cb9a8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615771"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Concepts de sécurité pour les applications et les clusters dans AKS (Azure Kubernetes Service)

Pour protéger vos données clientes quand vous exécutez des charges de travail d’applications dans AKS (Azure Kubernetes Service), vous devez prendre en compte la sécurité de votre cluster. Kubernetes inclut des composants de sécurité tels que les *stratégies réseau* et les *secrets*. Azure ajoute des composants tels que les groupes de sécurité réseau et les mises à niveau de cluster orchestrées. Ces composants de sécurité sont combinés afin que votre cluster AKS exécute les dernières versions de Kubernetes et mises à jour de sécurité du système d’exploitation, et que le trafic vers les pods et l’accès aux informations d’identification sensibles soient sécurisés.

Cet article présente les concepts fondamentaux qui sécurisent vos applications dans AKS :

- [Sécurité des composants maîtres](#master-security)
- [Sécurité des nœuds](#node-security)
- [Mise à niveau des clusters](#cluster-upgrades)
- [Sécurité du réseau](#network-security)
- [Secrets Kubernetes](#kubernetes-secrets)

## <a name="master-security"></a>Sécurité du maître

Dans AKS, les composants maîtres Kubernetes font partie du service managé fourni par Microsoft. Chaque cluster AKS a son propre maître Kubernetes dédié monolocataire pour fournir le serveur d’API, le planificateur, etc. Ce maître est managé et maintenu par Microsoft.

Par défaut, le serveur d’API Kubernetes utilise une adresse IP publique avec nom de domaine complet (FQDN). Vous pouvez contrôler l’accès au serveur d’API avec des contrôles d’accès en fonction du rôle Kubernetes et Azure Active Directory. Pour plus d’informations, consultez [Intégration d’Azure AD à AKS][aks-aad].

## <a name="node-security"></a>Sécurité des nœuds

Les nœuds AKS sont des machines virtuelles Azure dont vous assurez la gestion et la maintenance. Les nœuds Linux exécutent une distribution Ubuntu optimisée à l’aide du runtime de conteneur Moby. Les nœuds Windows Server (actuellement en préversion dans AKS) exécutent une version Windows Server 2019 optimisée et utilisent également le runtime de conteneur Moby. Quand un cluster AKS est créé ou fait l’objet d’un scale-up, les nœuds sont déployés automatiquement avec les dernières configurations et mises à jour de sécurité du système d’exploitation.

La plateforme Azure applique automatiquement les correctifs de sécurité du système d’exploitation aux nœuds Linux chaque nuit. Si une mise à jour de la sécurité du système d’exploitation Linux nécessite un redémarrage de l’hôte, ce redémarrage n’est pas effectué automatiquement. Vous pouvez redémarrer manuellement les nœuds Linux. Une autre approche courante consiste à utiliser [Kured][kured], an open-source reboot daemon for Kubernetes. Kured runs as a [DaemonSet][aks-daemonsets] et à rechercher sur chaque nœud un fichier indiquant qu’un redémarrage est nécessaire. Les redémarrages sont gérés au sein du cluster à l’aide du même [processus d’isolation et de drainage](#cordon-and-drain) que celui appliqué pour la mise à niveau du cluster.

Pour les nœuds Windows Server (actuellement en préversion dans AKS), Windows Update ne s’exécute pas et d’applique pas automatiquement les dernières mises à jour. Suivez une planification régulière basée sur le cycle de mise à jour de Windows et votre propre processus de validation pour effectuer une mise à niveau sur le ou les pools de nœuds Windows Server dans votre cluster AKS. Ce processus de mise à niveau crée des nœuds qui exécutent la dernière image et les derniers correctifs de Windows Server, puis supprime les anciens nœuds. Pour plus d’informations sur ce processus, consultez [Mettre à niveau un pool de nœuds dans AKS][nodepool-upgrade].

Les nœuds sont déployés sur un sous-réseau de réseau virtuel privé, sans aucune adresse IP publique affectée. Pour des raisons de gestion et de résolution des problèmes, SSH est activé par défaut. Cet accès SSH n’est disponible qu’au moyen de l’adresse IP interne.

Pour fournir le stockage, les nœuds utilisent Azure Disques managés. Pour la plupart des tailles de nœud de machine virtuelle, il s’agit de disques Premium assortis de disques SSD hautes performances. Les données stockées sur les disques managés sont automatiquement chiffrées au repos au sein de la plateforme Azure. Pour améliorer la redondance, ces disques sont également répliqués de manière sécurisée au sein du centre de données Azure.

Les environnements Kubernetes, dans AKS ou ailleurs, ne sont pas encore totalement sûrs pour une utilisation multi-locataire hostile. Des fonctionnalités de sécurité supplémentaires, telles que les *stratégies de sécurité Pod*, et des contrôles d’accès en fonction du rôle (RBAC) plus détaillés pour les nœuds rendent les attaques plus difficiles. Mais lors de l’exécution de charges de travail multi-locataires hostiles, seul un hyperviseur garantira véritablement la sécurité. Le domaine de sécurité de Kubernetes devient le cluster, et non un nœud individuel. Pour ces types de charges de travail multi-locataires hostiles, vous devez utiliser des clusters physiquement isolés. Pour plus d’informations sur les méthodes d’isolation des charges de travail, consultez [Meilleures pratiques relatives à l’isolation de clusters dans AKS][cluster-isolation],

## <a name="cluster-upgrades"></a>Mise à niveau des clusters

Pour des raisons de conformité et de sécurité, ou pour que soient utilisées les dernières fonctionnalités, Azure fournit des outils pour orchestrer la mise à niveau d’un cluster et de composants AKS. Cette orchestration de la mise à niveau inclut les composants maîtres et agents Kubernetes. Vous pouvez afficher la [liste des versions Kubernetes disponibles](supported-kubernetes-versions.md) pour votre cluster AKS. Pour démarrer le processus de mise à niveau, vous spécifiez une de ces versions disponibles. Ensuite, Azure isole et draine de manière sécurisée chaque nœud AKS et effectue la mise à niveau.

### <a name="cordon-and-drain"></a>Isolation et drainage

Pendant le processus de mise à niveau, les nœuds AKS sont chacun isolés du cluster afin que de nouveaux pods ne soient pas planifiés sur ces nœuds. Les nœuds sont ensuite drainés et mis à niveau comme suit :

- Un nouveau nœud est déployé dans le pool de nœuds. Ce nœud exécute la dernière image et les derniers correctifs du système d’exploitation.
- Un des nœuds existants est identifié pour être mis à niveau. Les pods sur ce nœud sont arrêtés normalement et planifiés sur les autres nœuds du pool de nœuds.
- Ce nœud existant est supprimé du cluster AKS.
- Le nœud suivant dans le cluster est isolé et drainé au moyen du même processus jusqu’à ce que tous les nœuds soient remplacés dans le cadre du processus de mise à niveau.

Pour plus d’informations, consultez [Mettre à niveau un cluster AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Sécurité du réseau

Pour la connectivité et la sécurité avec les réseaux locaux, vous pouvez déployer votre cluster AKS sur des sous-réseaux de réseau virtuel Azure existants. Ces réseaux virtuels peuvent avoir une connexion Express Route ou VPN de site à site Azure à votre réseau local. Des contrôleurs d’entrée Kubernetes peuvent être définis avec des adresses IP privées internes afin que les services ne soient accessibles que via cette connexion réseau interne.

### <a name="azure-network-security-groups"></a>Groupes de sécurité réseau Azure

Pour filtrer le flux du trafic dans les réseaux virtuels, Azure utilise des règles de groupe de sécurité réseau. Ces règles définissent les plages d’adresses IP source et de destination, les ports et les protocoles qui se voient autoriser ou refuser l’accès aux ressources. Des règles par défaut sont créées pour autoriser le trafic TLS vers le serveur d’API Kubernetes. Quand vous créez des services avec des équilibreurs de charge, des mappages de ports ou des routes d’entrée, AKS modifie automatiquement le groupe de sécurité réseau afin que le trafic transite de manière appropriée.

## <a name="kubernetes-secrets"></a>Secrets Kubernetes

Un *secret* Kubernetes permet d’injecter des données sensibles dans des pods, telles que les clés ou les informations d’identification d’accès. Tout d’abord, vous créez un secret à l’aide de l’API Kubernetes. Quand vous définissez votre pod ou déploiement, un secret spécifique peut être demandé. Les secrets sont fournis uniquement aux nœuds dont un pod planifié a besoin d’un secret. En outre, le secret est stocké dans un volume *tmpfs*, au lieu d’être écrit sur le disque. Quand le dernier pod sur un nœud qui requiert un secret est supprimé, ce dernier est supprimé du volume tmpfs du nœud. Les secrets sont stockés dans un espace de noms donné et ne sont accessibles qu’aux pods se trouvant dans cet espace de noms.

L’utilisation de secrets réduit la quantité d’informations sensibles définies dans le manifeste YAML des pods ou services. Au lieu de cela, vous demandez le secret stocké sur le serveur d’API Kubernetes dans le cadre de votre manifeste YAML. Ainsi, l’accès au secret n’est accordé qu’au pod concerné. Remarque : les fichiers manifeste secrets bruts contiennent les données secrètes au format base64 (consultez la [documentation officielle][secret-risks] pour plus d’informations). Ce fichier doit donc être considéré comme des informations sensibles et ne doit jamais être validé dans le contrôle de code source.

## <a name="next-steps"></a>Étapes suivantes

Pour vous familiariser avec la sécurisation de vos clusters AKS, consultez [Mettre à niveau un cluster AKS][aks-upgrade-cluster].

Pour connaître les meilleures pratiques associées, consultez [Meilleures pratiques relatives aux mises à jour et à la sécurité du cluster dans AKS][operator-best-practices-cluster-security].

Pour plus d’informations sur les concepts fondamentaux de Kubernetes et d’AKS, consultez les articles suivants :

- [Clusters et charges de travail Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Identité Kubernetes/AKS][aks-concepts-identity]
- [Réseaux virtuels Kubernetes/AKS][aks-concepts-network]
- [Stockage Kubernetes/AKS][aks-concepts-storage]
- [Mise à l’échelle Kubernetes/AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: azure-ad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
