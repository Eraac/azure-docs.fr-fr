---
title: Exécuter Linux sur des nœuds de calcul de machine virtuelle - Azure Batch | Microsoft Docs
description: Découvrez comment traiter vos charges de travail de calcul parallèles sur des pools de machines virtuelles Linux dans Azure Batch.
services: batch
documentationcenter: python
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b4b381ff1f68935084e3dd30865cf539d4abbd16
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323528"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Configurer des nœuds de calcul Linux dans des pools Batch

Vous pouvez utiliser Azure Batch pour exécuter des charges de travail de calcul parallèles sur les machines virtuelles Linux et Windows. Cet article explique comment créer des pools de nœuds de calcul Linux dans le service Batch à l’aide de bibliothèques clientes [Batch Python][py_batch_package] and [Batch .NET][api_net].

> [!NOTE]
> Les packages d’applications sont pris en charge sur tous les pools Batch créés après le 5 juillet 2017. Ils sont pris en charge sur les pools Batch créés entre le 10 mars 2016 et le 5 juillet 2017 uniquement si le pool a été créé à l’aide d’une configuration de service cloud. Les pools Batch créés avant le 10 mars 2016 ne prennent pas en charge les packages d’applications. Pour plus d’informations sur l’utilisation de packages d’applications pour déployer vos applications sur vos nœuds Batch, consultez [Déployer des applications sur les nœuds avec des packages d’applications Batch](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Configuration de la machine virtuelle
Lorsque vous créez un pool de nœuds de calcul dans Batch, vous avez deux options pour sélectionner la taille du nœud et le système d’exploitation : Configuration des services cloud et Configuration de la machine virtuelle.

**configuration des services cloud** fournit *uniquement*des nœuds de calcul Windows. Les tailles de nœud de calcul disponibles sont répertoriées dans [Tailles de services cloud](../cloud-services/cloud-services-sizes-specs.md), et les systèmes d’exploitation disponibles sont répertoriés dans [Versions du SE invité et matrice de compatibilité du Kit de développement logiciel (SDK) Azure](../cloud-services/cloud-services-guestos-update-matrix.md). Lorsque vous créez un pool contenant des nœuds Services cloud Azure, vous spécifiez la taille du nœud et la famille de systèmes d’exploitation, décrites dans les articles mentionnés ci-dessus. Pour les pools de nœuds de calcul Windows, les services Cloud Services sont le plus couramment utilisés.

**Virtual Machine Configuration** fournit des images Linux et Windows pour les nœuds de calcul. Les tailles de nœud de calcul disponibles sont répertoriées dans [Tailles des machines virtuelles dans Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) et [Tailles des machines virtuelles dans Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Lorsque vous créez un pool contenant des nœuds de la Configuration de la machine virtuelle, vous devez spécifier la taille des nœuds ainsi que la référence de l’image de la machine virtuelle et la référence de l’agent de nœud du Batch à installer sur les nœuds.

### <a name="virtual-machine-image-reference"></a>Référence de l’image de la machine virtuelle
Le service Batch utilise des [groupes de machines virtuelles identiques](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pour fournir des nœuds de calcul dans la configuration de la machine virtuelle. Vous pouvez spécifier une image à partir de la [Place de marché Azure][vm_marketplace] ou fournir une image personnalisée que vous avez préparée. Pour plus d’informations sur les images personnalisées, consultez [Créer un pool avec une image personnalisée](batch-custom-images.md).

Lorsque vous configurez une référence d’image de machine virtuelle, vous spécifiez les propriétés d’une image de machine virtuelle. Vous avez besoin des propriétés suivantes lorsque vous créez une référence d’image de machine virtuelle :

| **Propriétés de référence d’image** | **Exemple** |
| --- | --- |
| Publisher |Canonical |
| Offer |UbuntuServer |
| SKU |14.04.4-LTS |
| Version |latest |

> [!TIP]
> Vous trouverez plus d’informations sur ces propriétés et sur la manière de répertorier des images Marketplace dans [Parcourir et sélectionner des images de machines virtuelles Linux dans Azure avec l’interface CLI ou PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Notez que toutes les images Marketplace ne sont pas compatibles avec Batch pour le moment. Pour plus d’informations, consultez la rubrique [Référence de l’agent de nœud](#node-agent-sku)ci-dessous.
>
>

### <a name="node-agent-sku"></a>Référence de l’agent de nœud
L’agent de nœud de Batch est un programme qui s’exécute sur chaque nœud dans le pool et fournit l’interface de commande et de contrôle entre le nœud et le service Batch. Il existe différentes implémentations de l’agent de nœud pour différents systèmes d’exploitation, connues sous le nom de références. Essentiellement, lorsque vous créez une configuration de machine virtuelle, vous spécifiez d’abord la référence de l’image de la machine virtuelle, puis spécifiez l’agent de nœud à installer sur l’image. En règle générale, chaque référence d’agent de nœud est compatible avec plusieurs images de machine virtuelle. Voici quelques exemples de références d’agent de nœud :

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> Toutes les images de machine virtuelle disponibles sur Marketplace ne sont pas compatibles avec les agents de nœud Batch actuellement disponibles. Utilisez les SDK Batch pour répertorier les références d’agent de nœud disponibles ainsi que les images de machine virtuelle avec lesquelles ils sont compatibles. Pour plus d’informations et des exemples montrant comment récupérer une liste d’images valides lors de l’exécution, consultez la [liste des images de machine virtuelle](#list-of-virtual-machine-images).
>
>

## <a name="create-a-linux-pool-batch-python"></a>Création d’un pool Linux : Python Batch
L’extrait de code suivant illustre un exemple d’utilisation de la [bibliothèque cliente Microsoft Azure Batch pour Python][py_batch_package] to create a pool of Ubuntu Server compute nodes. Reference documentation for the Batch Python module can be found at [azure.batch package][py_batch_docs] dans Lire la documentation.

Cet extrait de code crée une méthode [ImageReference][py_imagereference] explicitly and specifies each of its properties (publisher, offer, SKU, version). In production code, however, we recommend that you use the [list_node_agent_skus][py_list_skus] pour déterminer et sélectionner parmi les combinaisons d’images et de références SKU d’agent de nœud disponibles lors de l’exécution.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="14.04.2-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Comme nous l’avons indiqué, il est recommandé de procéder ainsi au lieu de créer la méthode [ImageReference][py_imagereference] explicitly, you use the [list_node_agent_skus][py_list_skus] afin de sélectionner de manière dynamique une combinaison d’image Marketplace/agent de nœud actuellement prise en charge. L’extrait de code Python suivant illustre l’utilisation de cette méthode.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(
    agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id=ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Création d’un pool Linux : .NET Batch
L’extrait de code suivant montre un exemple d’utilisation de [batch.NET][nuget_batch_net] client library to create a pool of Ubuntu Server compute nodes. You can find the [Batch .NET reference documentation][api_net] sur docs.microsoft.com.

L’extrait de code suivant utilise la méthode [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] pour sélectionner des combinaisons d’images Marketplace et de références d’agent de nœud actuellement pris en charge dans la liste. Cette technique est souhaitable car la liste des combinaisons prises en charge peut changer de temps à autre. En règle générale, les combinaisons prises en charge sont ajoutées.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Bien que l’extrait de code ci-dessus utilise la méthode [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] pour répertorier et sélectionner des combinaisons d’images et de références d’agent de nœud prises en charge de manière dynamique (recommandé), vous pouvez également configurer explicitement une combinaison [ImageReference][net_imagereference] :

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>liste des images de machine virtuelle
Le tableau suivant répertorie les images de machine virtuelle Marketplace compatibles avec les agents de nœud Batch au moment où cet article a été mis à jour. Il est important de noter que cette liste n’est pas définitive, car des images et des agents de nœud peuvent être ajoutés ou supprimés à tout moment. Nous recommandons l’utilisation de [list_node_agent_skus][py_list_skus] (Python) or [ListNodeAgentSkus][net_list_skus] (Batch .NET) par vos services et applications Batch pour déterminer et sélectionner parmi les références actuellement disponibles.

> [!WARNING]
> La liste suivante peut changer à tout moment. Utilisez toujours les méthodes de **création d’une liste de références d’agents de nœud** disponibles dans les API Batch pour répertorier les références (SKU) de machine virtuelle et d’agent de nœud compatibles lorsque vous exécutez vos travaux Batch.
>
>

| **Publisher** | **Offer** | **Référence d’image** | **Version** | **ID de référence de l’agent de nœud** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| lot | rendering-centos73 | rendu | latest | batch.node.centos 7 |
| lot | rendering-windows2016 | rendu | latest | batch.node.windows amd64 |
| Canonical | UbuntuServer | 16.04-LTS | latest | batch.node.ubuntu 16.04 |
| Canonical | UbuntuServer | 14.04.5-LTS | latest | batch.node.ubuntu 14.04 |
| Credativ | Debian | 9 | latest | batch.node.debian 9 |
| Credativ | Debian | 8 | latest | batch.node.debian 8 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | latest | batch.node.centos 7 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | latest | batch.node.windows amd64 |
| microsoft-azure-batch | centos-container | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | centos-container-rdma | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | ubuntu-server-container | 16-04-lts | latest | batch.node.ubuntu 16.04 |
| microsoft-azure-batch | ubuntu-server-container-rdma | 16-04-lts | latest | batch.node.ubuntu 16.04 |
| MicrosoftWindowsServer | WindowsServer | 2016-centre-de-données | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-centre-de-données-avec-conteneurs | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1-smalldisk | latest | batch.node.windows amd64 |
| OpenLogic | CentOS | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.3 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | latest | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7.4 | latest | batch.node.centos 7 |
| SUSE | SLES-HPC | 12-SP2 | latest | batch.node.opensuse 42.1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>Se connecter à des nœuds Linux via SSH
Pendant le développement ou lors de la résolution des problèmes, il peut s’avérer nécessaire de se connecter aux nœuds de votre pool. Contrairement aux nœuds de calcul Windows, vous ne pouvez pas utiliser le protocole RDP (Remote Desktop Protocol) pour se connecter à des nœuds Linux. Au lieu de cela, le service Batch autorise l’accès SSH sur chaque nœud de connexion à distance.

L’extrait de code Python suivant crée un utilisateur sur chaque nœud d’un pool, nécessaire à la connexion à distance. Il imprime ensuite les informations de connexion SSH pour chaque nœud.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Voici un exemple de sortie du code ci-dessus pour un pool contenant quatre nœuds Linux :

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Au lieu d’un mot de passe, vous pouvez spécifier une clé publique SSH lorsque vous créez un utilisateur sur un nœud. Dans le kit de développement logiciel (SDK) Python, utilisez le paramètre **ssh_public_key** sur le paramètre [ComputeNodeUser][py_computenodeuser]. In .NET, use the [ComputeNodeUser][net_computenodeuser].[SshPublicKey][net_ssh_key].

## <a name="pricing"></a>Tarifs
Azure Batch est basé sur la technologie d’Azure Cloud Services et des machines virtuelles Azure. Le service Batch lui-même est proposé gratuitement, ce qui signifie que vous payez uniquement les ressources de calcul utilisées par vos solutions Batch. Si vous sélectionnez **Configuration des services cloud**, vous êtes facturé en fonction de la structure de [tarification des services cloud][cloud_services_pricing]. Si vous sélectionnez la **Configuration de la machine virtuelle**, vous êtes facturé en fonction de la structure de [tarification des machines virtuelles][vm_pricing]. 

Si vous déployez des applications sur vos nœuds Batch à l’aide de [packages d’application](batch-application-packages.md), vous êtes également facturé pour les ressources Stockage Azure que vos packages d’application consomment. En général, les coûts Stockage Azure sont minimes. 

## <a name="next-steps"></a>Étapes suivantes

Les [exemples de code Python][github_samples_py] du référentiel in the [azure-batch-samples][github_samples] sur GitHub comprennent des scripts illustrant l’exécution d’opérations Batch communes telles que la création de pools, de travaux et de tâches. Le fichier [Lisez-moi][github_py_readme] qui accompagne les exemples de code Python contient des informations sur l’installation des packages nécessaires.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure-sdk-for-python.readthedocs.io/batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: https://docs.microsoft.com/python/api/azure.batch.models.computenodeuser
[py_imagereference]: https://docs.microsoft.com/python/api/azure.mgmt.batch.models.imagereference
[py_list_skus]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
