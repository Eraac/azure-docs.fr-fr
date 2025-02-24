---
title: Activer le chiffrement de disque pour les clusters Linux Azure Service Fabric | Microsoft Docs
description: Cet article décrit comment activer le chiffrement de disque pour les nœuds de cluster Azure Service Fabric dans Linux en utilisant Azure Resource Manager et Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: aljo
ms.openlocfilehash: 47b07188d1757708fb494c6a66e93379657e806a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258758"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Activer le chiffrement de disque pour les nœuds de cluster Azure Service Fabric dans Linux 
> [!div class="op_single_selector"]
> * [Chiffrement de disque pour Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Chiffrement de disque pour Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Dans ce didacticiel, vous allez apprendre à activer le chiffrement de disque sur les nœuds de cluster Azure Service Fabric dans Linux. Vous devez effectuer ces étapes pour chacun des types de nœuds et groupes de machines virtuelles identiques. Pour chiffrer les nœuds, nous allons utiliser les fonctionnalités d’Azure Disk Encryption sur les groupes de machines virtuelles identiques.

Le guide aborde les thèmes suivants :

* Concepts clés à connaître lors de l’activation du chiffrement de disque sur des groupes de machines virtuelles identiques de cluster Service Fabric dans Linux.
* Étapes à suivre avant d’activer le chiffrement de disque sur les nœuds de cluster Service Fabric dans Linux.
* Étapes à suivre pour activer le chiffrement de disque sur les nœuds de cluster Service Fabric dans Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

 **Inscription automatique**

La préversion du chiffrement de disque des groupes de machines virtuelles identiques nécessite une inscription automatique. Procédez comme suit :

1. Exécutez la commande suivante : 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Attendez 10 minutes environ jusqu’à ce que l’état indique *Inscrit*. Vous pouvez vérifier l’état en exécutant la commande suivante :
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. Créez un coffre de clés dans le même abonnement et la même région que le groupe identique. Sélectionnez ensuite la stratégie d’accès **EnabledForDiskEncryption** sur le coffre de clés à l’aide de sa cmdlet PowerShell. Vous pouvez également définir la stratégie à l’aide de l’interface utilisateur Key Vault dans le portail Azure à l’aide de la commande suivante :
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Installez la dernière version d’[Azure CLI](/cli/azure/install-azure-cli), qui inclut les nouvelles commandes de chiffrement.

3. Installez la dernière version du [Kit de développement logiciel (SDK) Azure d’Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Voici les cmdlets Azure Disk Encryption de groupe de machines virtuelles identiques pour activer ([set](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) le chiffrement, récupérer ([get](/powershell/module/az.compute/get-azvmssvmdiskencryption)) l’état du chiffrement et supprimer ([disable](/powershell/module/az.compute/disable-azvmssdiskencryption)) le chiffrement sur une instance de groupe identique.


| Commande | Version |  Source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 ou ultérieure | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 ou ultérieure | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 ou ultérieure | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 ou ultérieure | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 ou ultérieure | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 ou ultérieure | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Scénarios pris en charge pour le chiffrement de disque
* Le chiffrement de groupes de machines virtuelles identiques est pris en charge uniquement pour les groupes identiques créés avec des disques managés. Il ne l’est pas pour les groupes identiques de disques natifs (ou non managés).
* Le chiffrement et la désactivation du chiffrement sont pris en charge pour les volumes de système d’exploitation et de données dans des groupes de machines virtuelles identiques dans Linux. 
* Les opérations de réimageage et de mise à niveau de machine virtuelle de groupes de machines virtuelles identiques ne sont pas prises en charge dans la préversion actuelle.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Créer un cluster et activer le chiffrement de disque

Utilisez les commandes suivantes pour créer un cluster et activer le chiffrement de disque à l’aide d’un modèle Azure Resource Manager et d’un certificat auto-signé.

### <a name="sign-in-to-azure"></a>Connexion à Azure  

Connectez-vous à l’aide des commandes suivantes :

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Utiliser le modèle personnalisé que vous possédez déjà 

Si vous avez besoin de créer un modèle personnalisé, nous vous recommandons vivement d’utiliser l’un des modèles dans la page des [exemples de création de modèle Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). 

Si vous disposez déjà d’un modèle personnalisé, vérifiez bien que les trois paramètres liés au certificat dans le modèle et le fichier de paramètres portent les noms indiqués ci-après. Vérifiez également que les valeurs sont null comme indiqué ci-dessous :

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Étant donné que seul le chiffrement des disques de données est pris en charge pour les groupes de machines virtuelles identiques dans Linux, , vous devez ajouter le disque de données à l’aide du modèle Resource Manager. Mettez à jour votre modèle d’approvisionnement de disque de données comme suit :

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Voici la commande CLI équivalente. Modifiez les valeurs dans les instructions declare et remplacez-les par les valeurs appropriées. La CLI prend en charge tous les autres paramètres que la commande PowerShell précédente prend en charge.

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Monter un disque de données sur une instance Linux
Avant de procéder au chiffrement sur un groupe de machines virtuelles identiques, vérifiez que le disque de données ajouté est correctement monté. Connectez-vous à la machine virtuelle du cluster Linux et exécutez la commande **LSBLK**. La sortie doit indiquer ce disque de données ajouté dans la colonne **Point de montage**.


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Déployer une application sur un cluster Service Fabric dans Linux
Pour déployer une application sur votre cluster, suivez les étapes et les instructions dans [Démarrage rapide : Déployer des conteneurs Linux sur Service Fabric](service-fabric-quickstart-containers-linux.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Activer le chiffrement de disque pour le groupe de machines virtuelles identiques créé précédemment
Pour activer le chiffrement de disque pour les groupes de machines virtuelles identiques que vous avez créés dans les étapes précédentes, exécutez les commandes suivantes :
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Valider si le chiffrement de disque est activé pour un groupe de machines virtuelles identiques dans Linux
Pour obtenir l’état de l’ensemble d’un groupe de machines virtuelles identiques ou d’une instance d’un groupe identique, exécutez les commandes suivantes.
Vous pouvez également vous connecter à la machine virtuelle du cluster Linux et exécuter la commande **LSBLK**. La sortie doit indiquer le disque de données ajouté dans la colonne **Point de montage** et la colonne **Type** doit indiquer *Crypt*.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Désactiver le chiffrement de disque pour un groupe de machines virtuelles identiques dans un cluster Service Fabric
Désactivez le chiffrement de disque pour un groupe de machines virtuelles identiques en exécutant les commandes suivantes. Notez que la désactivation du chiffrement de disque s’applique à l’ensemble du groupe de machines virtuelles identiques et non par instance individuelle.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Étapes suivantes
À ce stade, vous devez avoir un cluster sécurisé et savoir comment activer et désactiver le chiffrement de disque pour des nœuds de cluster Service Fabric et des groupes de machines virtuelles identiques. Pour obtenir des recommandations similaires sur les nœuds de cluster Service Fabric dans Windows, consultez [Chiffrement de disque pour Windows](service-fabric-enable-azure-disk-encryption-windows.md). 
