---
title: Bonnes pratiques de l’opérateur - Isolation de clusters dans Azure Kubernetes Service (AKS)
description: Découvrir les bonnes pratiques de l’opérateur relatives à l’isolation de clusters dans Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mlearned
ms.openlocfilehash: 8150e184f0c7533d5a6e7e4847bf126206f5e6c6
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614928"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Bonnes pratiques relatives à l’isolation de clusters dans Azure Kubernetes Service (AKS)

Quand vous gérez des clusters dans Azure Kubernetes Service (AKS), il est souvent nécessaire d’isoler les équipes et les charges de travail. AKS vous offre une certaine flexibilité dans la manière d’exécuter les clusters multilocataires et d’isoler les ressources. Pour maximiser votre investissement dans Kubernetes, il est important de comprendre et d’implémenter ces fonctionnalités de multilocation et d’isolation.

Cet article traite des bonnes pratiques d’isolation pour les opérateurs de clusters. Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Préparer des clusters multilocataires et la séparation des ressources
> * Utiliser l’isolement logique ou physique dans vos clusters AKS

## <a name="design-clusters-for-multi-tenancy"></a>Concevoir des clusters pour la multilocation

Kubernetes fournit des fonctionnalités qui vous permettent d’isoler logiquement les équipes et les charges de travail dans le même cluster. L’objectif est de fournir le moins de privilèges et de les limiter aux ressources dont chaque équipe a besoin. Un [espace de noms][k8s-namespaces] dans Kubernetes crée une limite d’isolation logique. Parmi les autres fonctionnalités et considérations liées à l’isolation et à la multilocation dans Kubernetes, citons les suivantes :

* **Planification** : inclut l’utilisation de fonctionnalités de base telles que les quotas de ressources et les budgets de perturbation de pod. Pour plus d’informations sur ces fonctionnalités, consultez [Bonnes pratiques relatives aux fonctionnalités de base du planificateur dans AKS][aks-best-practices-scheduler].
  * Le planificateur comprend également des fonctionnalités plus avancées, notamment les teintes (taints) et tolérances (tolerations), les sélecteurs de nœud ainsi que l’affinité ou l’anti-affinité des nœuds et des pods. Pour plus d’informations sur ces fonctionnalités, consultez [Bonnes pratiques relatives aux fonctionnalités avancées du planificateur dans AKS][aks-best-practices-advanced-scheduler].
* **Réseau** : inclut l’utilisation de stratégies réseau pour contrôler le flux du trafic à destination et en provenance des pods.
* **Authentification et autorisation** : incluent l’utilisation du contrôle d’accès en fonction du rôle (RBAC) et de l’intégration Azure Active Directory (AD), les identités de pod et les secrets dans Azure Key Vault. Pour plus d’informations sur ces fonctionnalités, consultez [Bonnes pratiques relatives à l’authentification et à l’autorisation dans AKS][aks-best-practices-identity].
* **Conteneurs** : incluent les stratégies de sécurité des pods, les contextes de sécurité des pods ainsi que l’analyse d’images et de runtimes à la recherche de vulnérabilités. Implique également l’utilisation d’App Armor ou de Seccomp (Secure Computing) pour restreindre l’accès du conteneur au nœud sous-jacent.

## <a name="logically-isolate-clusters"></a>Isoler logiquement des clusters

**Guide de bonne pratique** : utilisez une isolation logique pour séparer les équipes et les projets. Essayez de réduire le nombre de clusters AKS physiques que vous déployez pour isoler les équipes ou les applications.

Grâce à l’isolation logique, un même cluster AKS peut être utilisé pour plusieurs charges de travail, équipes ou environnements. Les [espaces de noms][k8s-namespaces] Kubernetes forment la limite d’isolation logique pour les charges de travail et les ressources.

![Isolation logique d’un cluster Kubernetes dans AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

La séparation logique de clusters fournit généralement une densité de pods supérieure à celle résultant de l’isolation physique. L’excédent de capacité de calcul en état d’inactivité dans le cluster est moins important. En combinant cette approche à l’outil Kubernetes de mise à l’échelle automatique de cluster (« autoscaler »), vous pouvez effectuer un scale-up ou un scale-down des nœuds pour répondre aux demandes. Cette bonne pratique concernant la mise à l’échelle automatique vous permet d’exécuter uniquement le nombre de nœuds nécessaires et de réduire les coûts.

Les environnements Kubernetes, dans AKS ou ailleurs, ne sont pas totalement sûrs pour une utilisation multi-locataire hostile. Des fonctionnalités de sécurité supplémentaires, telles que la *stratégie de sécurité Pod*, et des contrôles d'accès en fonction du rôle (RBAC) plus détaillés pour les nœuds rendent les attaques plus difficiles. Mais lors de l'exécution de charges de travail multi-locataires hostiles, seul un hyperviseur garantira véritablement la sécurité. Le domaine de sécurité de Kubernetes devient le cluster, et non un nœud individuel. Pour ces types de charges de travail multi-locataires hostiles, vous devez utiliser des clusters physiquement isolés.

## <a name="physically-isolate-clusters"></a>Isoler physiquement des clusters

**Guide de bonne pratique** : réduisez l’utilisation de l’isolation physique pour chaque équipe ou déploiement d’application. Utilisez plutôt l’isolation *logique*, comme indiqué dans la section précédente.

Une approche courante en matière d’isolation de cluster consiste à utiliser des clusters AKS séparés physiquement. Dans ce modèle d’isolation, les équipes ou charges de travail se voient affecter leur propre cluster AKS. Cette approche apparaît souvent comme la plus simple pour isoler des charges de travail ou des équipes, mais elle ajoute des efforts de gestion et des frais supplémentaires. Vous devez en effet gérer ces clusters, en particulier fournir un accès et affecter des autorisations à chacun d’eux. Tous les nœuds individuels vous sont également facturés.

![Isolation physique de clusters Kubernetes individuels dans AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Les clusters séparés physiquement offrent généralement une faible densité de pods. Du fait que chaque équipe ou charge de travail a son propre cluster AKS, le cluster est fréquemment surprovisionné avec des ressources de calcul. Souvent, un petit nombre de pods est planifié sur ces nœuds. Toute capacité inutilisée sur les nœuds ne peut pas être exploitée par d’autres équipes pour des applications ou des services en cours de développement. Cet excédent de ressources engendre des frais supplémentaires pour les clusters séparés physiquement.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, nous avons traité de l’isolation des clusters. Pour plus d’informations sur les opérations liées aux clusters dans AKS, consultez les bonnes pratiques suivantes :

* [Fonctionnalités de base du planificateur Kubernetes][aks-best-practices-scheduler]
* [Fonctionnalités avancées du planificateur Kubernetes][aks-best-practices-advanced-scheduler]
* [Authentification et autorisation][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
