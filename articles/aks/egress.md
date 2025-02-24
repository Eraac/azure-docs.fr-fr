---
title: Adresse IP statique pour le trafic de sortie dans Azure Kubernetes Service (ACS)
description: Découvrez comment créer et utiliser une adresse IP publique statique pour le trafic de sortie dans un cluster Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 094a696a12025dcfd575ce3f035b12b4a04aba10
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615569"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>Utiliser une adresse IP publique statique pour le trafic de sortie dans Azure Kubernetes Service (AKS)

Par défaut, l’adresse IP de sortie d’un cluster Azure Kubernetes Service (AKS) est affectée aléatoirement. Cette configuration n’est pas idéale quand vous avez besoin d’identifier une adresse IP par exemple pour accéder à des services externes. Au lieu de cela, il peut être nécessaire d’affecter une adresse IP statique qui peut être placée en liste verte pour l’accès au service.

Cet article vous montre comment créer et utiliser une adresse IP publique statique pour le trafic de sortie dans un cluster AKS.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

Azure CLI 2.0.59 (ou une version ultérieure) doit également être installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre votre version à niveau, consultez  [Installation d’Azure CLI][install-azure-cli].

## <a name="egress-traffic-overview"></a>Vue d’ensemble du trafic de sortie

Le trafic sortant provenant d’un cluster AKS suit les [conventions d’Azure Load Balancer][outbound-connections]. Avant la création du premier service Kubernetes de type `LoadBalancer`, les nœuds d’agent d’un cluster AKS ne font partie d’aucun pool d’Azure Load Balancer. Dans cette configuration, les nœuds n’ont pas d’adresse IP publique au niveau de l’instance. Azure translate le flux sortant vers une adresse IP publique source qui n’est pas configurable ou déterministe.

Après la création d’un service Kubernetes de type `LoadBalancer`, les nœuds d’agent sont ajoutés à un pool d’équilibrage de charge Azure. Azure translate le flux sortant vers la première adresse IP publique configurée sur l’équilibreur de charge. Cette adresse IP publique est valide seulement pour la durée de vie de cette ressource. Si vous supprimez le service Load Balancer Kubernetes, l’adresse IP et l’équilibreur de charge associés sont également supprimés. Si vous voulez affecter une adresse IP spécifique ou conserver une adresse IP pour des services Kubernetes redéployés, vous pouvez créer et utiliser une adresse IP publique statique.

## <a name="create-a-static-public-ip"></a>Créer une adresse IP publique statique

Quand vous créez une adresse IP publique statique pour l’utiliser avec AKS, la ressource d’adresse IP doit être créée dans le groupe de ressources du **nœud**. Obtenez le nom du groupe de ressources avec la commande [az aks show][az-aks-show] et ajoutez le paramètre de requête `--query nodeResourceGroup`. L’exemple suivant obtient les le groupe de ressources du nœud pour le cluster AKS nommé *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup* :

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Créez maintenant une adresse IP publique statique avec la commande [az network public ip create][az-network-public-ip-create]. Spécifiez le nom de groupe de ressources du nœud obtenue dans la commande précédente, puis un nom pour la ressource d’adresse IP, par exemple *myAKSPublicIP* :

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

L’adresse IP apparaît, comme illustré dans l’exemple de sortie condensée suivante :

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
```

Vous pouvez obtenir ultérieurement l’adresse IP publique avec la commande [az network public-ip list][az-network-public-ip-list]. Spécifiez le nom du groupe de ressources du nœud, puis recherchez *ipAddress* comme indiqué dans l’exemple suivant :

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Créer un service avec l’adresse IP statique

Pour créer un service avec l’adresse IP publique statique, ajoutez la propriété `loadBalancerIP` et la valeur de l’adresse IP publique statique au manifeste YAML. Créez un fichier nommé `egress-service.yaml` et copiez-y le YAML suivant. Spécifiez votre propre adresse IP publique créée à l’étape précédente.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

Créez le service et le déploiement avec la commande `kubectl apply`.

```console
kubectl apply -f egress-service.yaml
```

Ce service configure une nouvelle adresse IP frontend sur l’équilibreur de charge Azure. Si vous n’avez pas d’autres adresses IP configurées, **tout** le trafic sortant doit désormais utiliser cette adresse. Lorsque plusieurs adresses sont configurées sur l’équilibreur de charge Azure, la sortie utilise la première adresse IP de cet équilibreur de charge.

## <a name="verify-egress-address"></a>Vérifier l’adresse de sortie

Pour vérifier que l’adresse IP publique statique est utilisée, vous pouvez utiliser un service de recherche DNS comme `checkip.dyndns.org`.

Démarrez et attachez à un pod *Debian* de base :

```console
kubectl run -it --rm aks-ip --image=debian --generator=run-pod/v1
```

Pour accéder à un site web à partir du conteneur, utilisez `apt-get` pour installer `curl` dans le conteneur.

```console
apt-get update && apt-get install curl -y
```

Utilisez maintenant curl pour accéder au site *checkip.dyndns.org*. L’adresse IP de sortie apparaît, comme illustré dans l’exemple de sortie suivant. Cette adresse IP correspond à l’adresse IP publique statique créée et définie pour le service d’équilibreur de charge :

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Étapes suivantes

Pour éviter de devoir gérer plusieurs adresses IP publiques sur l’équilibreur de charge Azure, vous pouvez à la place utiliser un contrôleur d’entrée. Les contrôleurs d’entrée offrent des avantages supplémentaires, comme l’arrêt de SSL/TLS, la prise en charge des réécritures d’URI et le chiffrement SSL/TLS en amont. Pour plus d’informations, consultez [Créer un contrôleur d’entrée de base dans AKS][ingress-aks-cluster].

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli