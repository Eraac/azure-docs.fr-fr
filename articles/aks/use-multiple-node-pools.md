---
title: Utiliser plusieurs pools de nœuds dans Azure Kubernetes Service (AKS)
description: Découvrez comment créer et gérer plusieurs pools de nœuds pour un cluster dans Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/17/2019
ms.author: iainfou
ms.openlocfilehash: 48fdb251fa0302c2755281644a804c74ae80a63e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491542"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Préversion - Créer et gérer plusieurs pools de nœuds pour un cluster dans Azure Kubernetes Service (AKS)

Dans Azure Kubernetes Service (AKS), les nœuds d’une même configuration sont regroupés dans des *pools de nœuds*. Ces pools de nœuds contiennent les machines virtuelles sous-jacentes qui exécutent vos applications. Le nombre initial de nœuds et leur taille (référence SKU) sont définis quand vous créez un cluster AKS, opération qui crée un *pool de nœuds par défaut*. Pour prendre en charge les applications qui ont des exigences de calcul ou de stockage différentes, vous pouvez créer des pools de nœuds supplémentaires. Par exemple, utilisez ces pools de nœuds supplémentaires pour fournir des GPU pour les applications nécessitant beaucoup de ressources système ou pour accéder à un stockage SSD hautes performances.

Cet article vous montre comment créer et gérer plusieurs pools de nœuds dans un cluster AKS. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire.

> [!IMPORTANT]
> Les fonctionnalités d’évaluation AKS sont en libre-service et font l’objet d’un abonnement. Elles sont fournies pour que notre communauté puisse faire part de ses commentaires et des bogues éventuels. En préversion, ces fonctionnalités ne sont pas destinées à une utilisation en production. Les fonctionnalités en préversion publique font l’objet d’un support relatif. L’assistance des équipes de support technique AKS est disponible pendant les heures de bureau du fuseau horaire Heure du Pacifique uniquement. Pour obtenir des informations supplémentaires, veuillez lire les articles de support suivants :
>
> * [Stratégies de support AKS][aks-support-policies]
> * [FAQ du support Azure][aks-faq]

## <a name="before-you-begin"></a>Avant de commencer

L’interface de ligne de commande Azure (Azure CLI) version 2.0.61 ou une version ultérieure doit avoir été installée et configurée. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installer l’extension CLI de préversion d’aks

Pour utiliser plusieurs pools de nœuds, vous avez besoin de l’extension CLI *aks-preview* version 0.4.1 ou ultérieure. Installez l’extension Azure CLI *aks-preview* à l’aide de la commande [az extension add][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update]:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-multiple-node-pool-feature-provider"></a>Inscrire le fournisseur de fonctionnalités à plusieurs pools de nœuds

Pour créer un cluster AKS capable d’utiliser plusieurs pools de nœuds, commencez par activer deux indicateurs de fonctionnalité sur votre abonnement. Les clusters de pools multinœuds utilisent un groupe de machines virtuelles identiques pour gérer le déploiement et la configuration des nœuds Kubernetes. Inscrivez les indicateurs de fonctionnalité *MultiAgentpoolPreview* et *VMSSPreview* à l’aide de la commande [az feature register][az-feature-register], comme indiqué dans l’exemple suivant :

> [!CAUTION]
> Lorsque vous inscrivez une fonctionnalité sur un abonnement, vous ne pouvez actuellement pas désinscrire cette fonctionnalité. Après avoir activé des fonctionnalités d’évaluation, des valeurs par défaut peuvent être utilisées pour tous les clusters AKS créés ultérieurement dans l’abonnement. N’activez pas les fonctionnalités d’évaluation sur les abonnements de production. Utilisez un abonnement distinct pour tester les fonctionnalités d’évaluation et recueillir des commentaires.

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Tous les clusters AKS que vous créez après avoir inscrit *MultiAgentpoolPreview* utilisent cette expérience de cluster en préversion. Pour continuer à créer des clusters standard et entièrement supportés, n’activez pas les fonctionnalités en préversion sur les abonnements de production. Utilisez abonnement Azure de test ou de développement pour tester les fonctionnalités en préversion.

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vous pouvez vérifier l'état de l'inscription à l'aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Limites

Les limitations suivantes s’appliquent lorsque vous créez et gérez les clusters AKS prenant en charge plusieurs pools de nœuds :

* Plusieurs pools de nœud sont disponibles seulement pour les clusters créés une fois que vous avez correctement inscrit les fonctionnalités *MultiAgentpoolPreview* et *VMSSPreview* pour votre abonnement. Vous ne pouvez pas ajouter ni gérer de pools de nœuds avec un cluster AKS existant créé avant l’enregistrement de ces fonctionnalités.
* Vous ne pouvez pas supprimer le premier pool de nœuds.
* Le module complémentaire de routage d’application HTTP ne peut pas être utilisé.
* Vous ne pouvez pas ajouter/mettre à jour/supprimer des pools de nœuds en utilisant un modèle Resource Manager existant comme avec la plupart des opérations. Au lieu de cela, [utilisez un modèle Resource Manager distinct](#manage-node-pools-using-a-resource-manager-template) pour apporter des modifications aux pools de nœuds dans un cluster AKS.

Même si cette fonctionnalité est en préversion préliminaire, les limitations supplémentaires suivantes s’appliquent :

* Le cluster AKS peut avoir un maximum de huit pools de nœuds.
* Le cluster AKS peut avoir un maximum de 400 nœuds dans ces huit pools de nœuds.
* Tous les pools de nœuds doivent résider dans le même sous-réseau.

## <a name="create-an-aks-cluster"></a>Créer un cluster AKS

Pour commencer, créez un cluster AKS avec un pool de nœuds unique. L’exemple suivant utilise la commande [az group create][az-group-create] pour créer un groupe de ressources nommé *myResourceGroup* dans la région *eastus*. Un cluster AKS nommé *myAKSCluster* est alors créé à l’aide de la commande [az aks create][az-aks-create]. Un paramètre *--kubernetes-version* de valeur *1.12.6* est utilisé pour montrer comment mettre à jour un pool de nœuds dans une étape suivante. Vous pouvez spécifier une [version Kubernetes prise en charge][supported-versions].

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --generate-ssh-keys \
    --kubernetes-version 1.12.6
```

La création du cluster ne prend que quelques minutes.

Lorsque le cluster est prêt, utilisez la commande [az aks get-credentials][az-aks-get-credentials] pour obtenir les informations d’identification du cluster à utiliser avec `kubectl` :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Ajouter un pool de nœuds

Le cluster créé à l’étape précédente dispose d’un pool de nœuds unique. Nous allons ajouter un deuxième pool de nœuds à l’aide de la commande [az aks node pool add][az-aks-nodepool-add]. L’exemple suivant crée un pool de nœuds nommé *mynodepool* qui exécute *3* nœuds :

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

Pour afficher l’état de vos pools de nœuds, utilisez la commande [az aks node pool list][az-aks-nodepool-list] et spécifiez votre groupe de ressources et le nom du cluster :

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster -o table
```

L’exemple de sortie suivant montre que *mynodepool* a été créé avec trois nœuds dans le pool de nœuds. Lorsque le cluster AKS a été créé à l’étape précédente, un pool *nodepool1* par défaut a été créé avec un nombre de nœuds de *1*.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

> [!TIP]
> Si aucun paramètre *OrchestratorVersion* ou *VmSize* n’est spécifié lorsque vous ajoutez un pool de nœuds, les nœuds sont créés en fonction des valeurs par défaut pour le cluster AKS. Dans cet exemple, il s’agissait de la version Kubernetes *1.12.6* et de la taille de nœud *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Mettre à niveau un pool de nœuds

Lorsque votre cluster AKS a été créé dans la première étape, un paramètre `--kubernetes-version` de *1.12.6* a été spécifié. Nous allons mettre à niveau *mynodepool* vers Kubernetes *1.12.7*. Utilisez la commande [az aks node pool upgrade][az-aks-nodepool-upgrade] pour mettre à niveau le pool de nœuds, comme illustré dans l’exemple suivant :

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.12.7 \
    --no-wait
```

Listez de nouveau l’état de vos pools de nœuds à l’aide de la commande [az aks node pool list][az-aks-nodepool-list]. L’exemple suivant montre que *mynodepool* est dans l’état *Upgrading (Mise à niveau)* vers *1.12.7* :

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.7                 100             Linux     Upgrading            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Il faut quelques minutes pour mettre à niveau les nœuds vers la version spécifiée.

En guise de bonne pratique, vous devez mettre à niveau tous les pools de nœuds dans un cluster AKS vers la même version de Kubernetes. La possibilité de mettre à niveau les pools de nœuds individuels vous permet d’effectuer une mise à niveau propagée et de planifier des pods entre les pools de nœuds pour maintenir la disponibilité des applications.

## <a name="scale-a-node-pool"></a>Mettre à l'échelle un pool de nœuds

Lorsque vos exigences de charge de travail d’application changent, vous pouvez être amené à mettre à l’échelle le nombre de nœuds dans un pool de nœuds. Le nombre de nœuds peut être augmenté ou diminué.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Pour mettre à l’échelle le nombre de nœuds dans un pool de nœuds, utilisez la commande [az aks node pool scale][az-aks-nodepool-scale]. L’exemple suivant met à l’échelle le nombre de nœuds dans *mynodepool* sur *5* :

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Listez de nouveau l’état de vos pools de nœuds à l’aide de la commande [az aks node pool list][az-aks-nodepool-list]. L’exemple suivant montre que *mynodepool* est dans l’état *Upgrading (Mise à niveau)* avec une nouvelle valeur de *5* nœuds :

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Scaling              myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

L’opération de mise à l’échelle peut prendre quelques minutes.

## <a name="delete-a-node-pool"></a>Supprimer un pool de nœuds

Si vous n’avez plus besoin d’un pool, vous pouvez le supprimer et supprimer les nœuds des machines virtuelles sous-jacentes. Pour supprimer un pool de nœuds, utilisez la commande [az aks node pool delete][az-aks-nodepool-delete] et spécifiez le nom du pool de nœuds. L’exemple suivant supprime le pool *mynodepool* créé au cours des étapes précédentes :

> [!CAUTION]
> Il n’existe aucune option de récupération pour les pertes de données qui peuvent se produire lorsque vous supprimez un pool de nœuds. Si des pods ne peuvent pas être planifiés sur d’autres pools de nœuds, ces applications ne sont pas disponibles. Veillez à ne pas supprimer un pool de nœuds lorsque des applications en cours d’utilisation n’ont pas de sauvegardes de données ou la possibilité de s’exécuter sur d’autres pools de nœuds dans votre cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

L’exemple de sortie suivant de la commande [az aks node pool list][az-aks-nodepool-list] montre que *mynodepool* est dans l’état *Deleting (Suppression)*  :

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Deleting             myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

La suppression des nœuds et du pool de nœuds prend quelques minutes.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Spécifier une taille de machine virtuelle pour un pool de nœuds

Dans les exemples précédents de création d’un pool de nœuds, une taille de machine virtuelle par défaut a été utilisée pour les nœuds créés dans le cluster. Un scénario plus courant consiste à créer des pools de nœuds avec des fonctionnalités et des tailles de machine virtuelle différentes. Par exemple, vous pouvez créer un pool de nœuds contenant des nœuds avec de grandes quantités de mémoire ou de processeur, ou un pool de nœuds fournissant une prise en charge GPU. Dans l’étape suivante, vous allez [utiliser des teintes et des tolérances](#schedule-pods-using-taints-and-tolerations) pour indiquer au planificateur Kubernetes comment limiter l’accès aux pods qui peuvent s’exécuter sur ces nœuds.

Dans l’exemple suivant, créez un pool de nœuds basé sur GPU qui utilise la taille de machine virtuelle *Standard_NC6*. Ces machines virtuelles sont alimentées par la carte NVIDIA Tesla K80. Pour obtenir des informations sur les tailles de machine virtuelle disponibles, consultez [Tailles des machines virtuelles Linux dans Azure][vm-sizes].

Créez un pool de nœuds de nouveau à l’aide de la commande [az aks node pool add][az-aks-nodepool-add]. Cette fois, spécifiez le nom *gpunodepool* et utilisez le paramètre `--node-vm-size` pour spécifier la taille *Standard_NC6* :

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

L’exemple de sortie suivant de la commande [az aks node pool list][az-aks-nodepool-list] montre que *gpunodepool* crée (*Creating*) des nœuds avec la taille *VmSize* spécifiée :

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name         OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  -----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  1        110        gpunodepool  1.12.6                 100             Linux     Creating             myResourceGroupPools  Standard_NC6
VirtualMachineScaleSets  1        110        nodepool1    1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Il faut quelques minutes pour que *gpunodepool* soit créé avec succès.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Planifier des pods à l’aide de teintes et de tolérances

Vous avez maintenant deux pools de nœuds dans votre cluster, le pool de nœuds par défaut initialement créé et le pool de nœuds basé sur GPU. Utilisez la commande [kubectl get nodes][kubectl-get] pour voir les nœuds figurant dans votre cluster. L’exemple de sortie suivant montre un seul nœud dans chaque pool de nœuds :

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.12.6
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.12.6
```

Le planificateur Kubernetes peut utiliser des teintes et des tolérances pour restreindre les charges de travail qui peuvent s’exécuter sur des nœuds.

* Quand une **teinte** est appliquée à un nœud, seuls des pods spécifiques peuvent être planifiés sur le nœud.
* Une **tolérance** est ensuite appliquée à un pod pour lui permettre de *tolérer* la teinte d’un nœud.

Pour plus d’informations sur la façon d’utiliser les fonctionnalités de Kubernetes planifiées avancées, consultez [Bonnes pratiques relatives aux fonctionnalités avancées du planificateur dans AKS][taints-tolerations].

Dans cet exemple, appliquez une teinte à votre nœud basé sur GPU à l’aide de la commande [kubectl taint node][kubectl-taint]. Spécifiez le nom de votre nœud basé sur GPU à partir de la sortie de la commande `kubectl get nodes` précédente. La teinte est appliquée en tant que *clé:valeur*, puis comme option de planification. L’exemple suivant utilise la paire *sku=gpu* et définit des pods qui ont la capacité *NoSchedule* :

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

L’exemple de manifeste YAML de base suivant utilise une tolérance pour autoriser le planificateur Kubernetes à exécuter un pod NGINX sur le nœud basé sur GPU. Pour obtenir un exemple plus approprié mais long d’exécution d’une tâche Tensorflow par rapport au jeu de données MNIST, consultez [Utiliser des GPU pour les charges de travail nécessitant beaucoup de ressources système sur AKS][gpu-cluster].

Créez un fichier nommé `gpu-toleration.yaml` et copiez-y l’exemple de code YAML suivant :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Planifiez le pod à l’aide de la commande `kubectl apply -f gpu-toleration.yaml` :

```console
kubectl apply -f gpu-toleration.yaml
```

Il faut quelques secondes pour planifier le pod et extraire l’image NGINX. Utilisez la commande [kubectl describe pod][kubectl-describe] pour voir l’état du pod. L’exemple de sortie condensée suivant montre que la tolérance *sku=gpu:NoSchedule* est appliquée. Dans la section des événements, le planificateur a affecté le pod au nœud basé sur GPU *aks-gpunodepool-28993262-vmss000000* :

```console
$ kubectl describe pod mypod

[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

Seuls les pods qui ont cette teinte appliquée peuvent être planifiés sur les nœuds dans *gpunodepool*. N’importe quel autre pod sera planifié dans le pool de nœuds *nodepool1*. Si vous créez des pools de nœuds supplémentaires, vous pouvez utiliser des teintes et des tolérances supplémentaires pour limiter les pods qui peuvent être planifiés sur ces ressources de nœud.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Gérer les pools de nœuds à l’aide d’un modèle Resource Manager

Lorsque vous utilisez un modèle Azure Resource Manager pour créer et gérer des ressources, vous pouvez généralement mettre à jour les paramètres dans votre modèle et le redéployer pour mettre à jour la ressource. Avec les pools de nœuds dans AKS, le profil de pool de nœuds initial ne peut pas être mis à jour une fois que le cluster AKS a été créé. Ce comportement signifie que vous ne pouvez pas mettre à jour un modèle Resource Manager existant, apporter un changement aux pools de nœuds et redéployer. Au lieu de cela, vous devez créer un modèle Resource Manager distinct qui mette à jour uniquement les pools d’agents pour un cluster AKS existant.

Créez un modèle tel que `aks-agentpools.json` et collez l’exemple de manifeste suivant. Cet exemple de modèle configure les paramètres suivants :

* Met à jour le pool d’agents *Linux* nommé *myagentpool* pour exécuter trois nœuds.
* Définit les nœuds dans le pool de nœuds pour exécuter Kubernetes version *1.12.8*.
* Définit la taille de nœud en tant que *Standard_DS2_v2*.

Modifiez ces valeurs en fonction des besoins pour mettre à jour, ajouter ou supprimer des pools de nœuds :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of your existing AKS cluster."
      }
    },
    "location": {
      "type": "string",
      "metadata": {
        "description": "The location of your existing AKS cluster."
      }
    },
    "agentPoolName": {
      "type": "string",
      "defaultValue": "myagentpool",
      "metadata": {
        "description": "The name of the agent pool to create or update."
      }
    },
    "vnetSubnetId": {
      "type": "string",
      "defaultValue": "",
      "metadata": {
        "description": "The Vnet subnet resource ID for your existing AKS cluster."
      }
    }
  },
  "variables": {
    "apiVersion": {
      "aks": "2019-04-01"
    },
    "agentPoolProfiles": {
      "maxPods": 30,
      "osDiskSizeGB": 0,
      "agentCount": 3,
      "agentVmSize": "Standard_DS2_v2",
      "osType": "Linux",
      "vnetSubnetId": "[parameters('vnetSubnetId')]"
    }
  },
  "resources": [
    {
      "apiVersion": "2019-04-01",
      "type": "Microsoft.ContainerService/managedClusters/agentPools",
      "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
      "location": "[parameters('location')]",
      "properties": {
            "maxPods": "[variables('agentPoolProfiles').maxPods]",
            "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
            "count": "[variables('agentPoolProfiles').agentCount]",
            "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
            "osType": "[variables('agentPoolProfiles').osType]",
            "storageProfile": "ManagedDisks",
      "type": "VirtualMachineScaleSets",
            "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
            "orchestratorVersion": "1.12.8"
      }
    }
  ]
}
```

Déployez ce modèle à l’aide de la commande [az group deployment create][az-group-deployment-create], comme indiqué dans l’exemple suivant. Vous êtes invité à fournir le nom et l’emplacement du cluster AKS existant :

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

La mise à jour de votre cluster AKS peut prendre quelques minutes selon les paramètres de pool de nœuds et les opérations que vous définissez dans votre modèle Resource Manager.

## <a name="clean-up-resources"></a>Supprimer des ressources

Dans cet article, vous avez créé un cluster AKS qui inclut des nœuds basés sur GPU. Pour réduire les coûts inutiles, vous voudrez peut-être supprimer le pool *gpunodepool* ou le cluster AKS tout entier.

Pour supprimer le pool de nœuds basé sur GPU, utilisez la commande [az aks nodepool delete][az-aks-nodepool-delete] comme indiqué dans l’exemple suivant :

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Pour supprimer le cluster lui-même, utilisez la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources AKS :

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment créer et gérer plusieurs pools de nœuds dans un cluster AKS. Pour plus d’informations sur la façon de contrôler les pods entre les pools de nœuds, consultez [Bonnes pratiques relatives aux fonctionnalités avancées du planificateur dans AKS][operator-best-practices-advanced-scheduler].

Pour créer et utiliser des pools de nœuds de conteneur Windows Server, consultez [Créer un conteneur Windows Server dans AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
