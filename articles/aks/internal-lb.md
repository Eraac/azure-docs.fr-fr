---
title: Créer un équilibreur de charge interne dans Azure Kubernetes Service (AKS)
description: Découvrez comment créer et utiliser un équilibreur de charge interne pour exposer vos services avec Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 5842003d43d4268d0f663e8a57e40562a480e252
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615147"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Utiliser un équilibreur de charge interne avec Azure Kubernetes Service (AKS)

Pour restreindre l’accès à vos applications dans Azure Kubernetes Service (AKS), vous pouvez créer et utiliser un équilibreur de charge interne. Un équilibreur de charge interne rend un service Kubernetes accessible uniquement aux applications qui s’exécutent dans le même réseau virtuel que le cluster Kubernetes. Cet article explique comment créer et utiliser un équilibreur de charge interne avec Azure Kubernetes Service (AKS).

> [!NOTE]
> Azure Load Balancer se décline en deux références SKU : *De base* et *Standard*. Par défaut, la référence SKU *De base* est utilisée lorsqu’un manifeste de service est utilisé pour créer un équilibreur de charge sur AKS. Pour plus d’informations, consultez [Comparaison des références SKU de Load Balancer][azure-lb-comparison].

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

Azure CLI 2.0.59 (ou une version ultérieure) doit également être installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installer l’interface de ligne de commande Microsoft Azure][install-azure-cli].

Le principal de service du cluster AKS a besoin de l’autorisation de gérer les ressources réseau si vous utilisez un sous-réseau ou un groupe de ressources existant. De façon générale, attribuez le rôle *Contributeur de réseau* au principal de service sur les ressources déléguées. Pour plus d’informations sur les autorisations, consultez [Delegate access to other Azure resources][aks-sp] (Déléguer l’accès à d’autres ressources Azure).

## <a name="create-an-internal-load-balancer"></a>Créer un équilibrage de charge interne

Pour créer un équilibreur de charge interne, créez un manifeste de service nommé `internal-lb.yaml` avec le type de service *LoadBalancer* et l’annotation *azure-load-balancer-internal*, comme indiqué dans l’exemple suivant :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Déployez l’exemple d’équilibreur de charge interne avec la commande [kubectl apply][kubectl-apply] et spécifiez le nom de votre manifeste YAML :

```console
kubectl apply -f internal-lb.yaml
```

Un équilibreur de charge Azure est créé dans le groupe de ressources de nœud et connecté au même réseau virtuel que le cluster AKS.

Quand vous affichez les détails du service, l’adresse IP de l’équilibreur de charge interne apparaît dans la colonne *EXTERNAL-IP*. Dans ce contexte, *Externe* fait référence à l’interface externe de l’équilibreur de charge, pas au fait qu’il reçoit une adresse IP publique externe. Le passage de l’adresse IP de l’état *\<en attente\>* à une adresse IP interne réelle peut prendre une ou deux minutes, comme indiqué dans l’exemple suivant :

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Spécifier une adresse IP

Si vous souhaitez utiliser une adresse IP spécifique avec l’équilibreur de charge interne, ajoutez la propriété *loadBalancerIP* au manifeste YAML de l’équilibreur de charge. L’adresse IP spécifiée doit se trouver dans le même sous-réseau que le cluster AKS et ne doit pas déjà être assignée à une ressource.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

Une fois déployé et quand vous affichez les détails du service, l’adresse IP dans la colonne *EXTERNAL-IP* reflète votre adresse IP spécifiée :

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Utiliser des réseaux privés

Lorsque vous créez votre cluster AKS, vous pouvez configurer des paramètres de réseau avancés. Cette méthode vous permet de déployer le cluster sur un réseau virtuel Azure existant et ses sous-réseaux. L’un des scénarios possibles consiste à déployer votre cluster AKS sur un réseau privé connecté à votre environnement local, et à exécuter des services accessibles uniquement en interne. Pour plus d’informations, découvrez comment configurer vos propres sous-réseaux du réseau virtuel avec [Kubenet][use-kubenet] or [Azure CNI][advanced-networking].

Vous pouvez utiliser les étapes précédentes pour déployer un équilibreur de charge interne dans un cluster AKS qui utilise un réseau privé. L’équilibreur de charge est créé dans le même groupe de ressources que votre cluster AKS, mais il est connecté à votre réseau privé virtuel et à son sous-réseau, comme indiqué dans l’exemple suivant :

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Vous devrez peut-être accorder au principal de service de votre cluster AKS le rôle de *Contributeur de réseaux* pour le groupe de ressources où sont déployées vos ressources de réseau virtuel Azure. Affichez le principal de service avec [az aks show][az-aks-show], par exemple : `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Pour créer une attribution de rôle, utilisez la commande [az role assignment create][az-role-assignment-create].

## <a name="specify-a-different-subnet"></a>Spécifier un autre sous-réseau

Pour spécifier un sous-réseau pour votre équilibreur de charge, ajoutez l’annotation *azure-load-balancer-internal-subnet* à votre service. Le sous-réseau spécifié doit se trouver dans le même réseau virtuel que votre cluster AKS. Une fois déployé, l’adresse *EXTERNAL-IP* de l’équilibreur de charge fait partie du sous-réseau spécifié.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>Supprimer l’équilibreur de charge

Lorsque tous les services qui utilisent l’équilibreur de charge interne ont été supprimés, l’équilibreur de charge lui-même est supprimé.

Vous pouvez aussi directement supprimer un service à l’image de toute ressource Kubernetes, tel que `kubectl delete service internal-app`, supprimant par la même occasion l’équilibreur de charge Azure sous-jacent.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les services Kubernetes dans la [documentation des services Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
