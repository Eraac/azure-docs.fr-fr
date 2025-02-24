---
title: Monter un volume Azure Files dans Azure Container Instances
description: Découvrir comment monter un volume Azure Files pour conserver l’état avec Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/08/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 25cac6a66baeb1587e4b5ba3f0923ca9c4394706
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325498"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Monter un partage de fichiers Azure dans Azure Container Instances

Par défaut, les conteneurs Azure Container Instances sont sans état. Si le conteneur se bloque ou s’arrête, son état est entièrement perdu. Pour conserver l’état au-delà de la durée de vie du conteneur, vous devez monter un volume à partir d’un stockage externe. Cet article explique comment monter un partage de fichiers Azure créé avec [Azure Files](../storage/files/storage-files-introduction.md) pour une utilisation avec Azure Container Instances. Azure Files offre des partages de fichiers managés dans le cloud qui sont accessibles via le protocole SMB (Server Message Block) standard. L'utilisation d'un partage de fichiers Azure avec Azure Container Instances offre des fonctionnalités de partage de fichiers similaires à l’utilisation d’un partage de fichiers Azure avec machines virtuelles Azure.

> [!NOTE]
> Le montage d’un partage de fichiers Azure est actuellement limité aux conteneurs Linux. Nous travaillons actuellement à proposer toutes ces fonctionnalités dans des conteneurs Windows. En attendant, nous vous invitons à découvrir les différences actuelles de la plateforme dans la [Vue d’ensemble](container-instances-overview.md#linux-and-windows-containers).

## <a name="create-an-azure-file-share"></a>Crée un partage de fichiers Azure

Avant d’utiliser un partage de fichiers Azure avec Azure Container Instances, vous devez le créer. Exécutez le script suivant pour créer un compte de stockage et y héberger le partage de fichiers, et le partage proprement dit. Comme le nom du compte de stockage doit être globalement unique, le script ajoute une valeur aléatoire à la chaîne de base.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create --name $ACI_PERS_SHARE_NAME --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>Obtenir des informations d’identification de stockage

Pour monter un partage de fichiers Azure en tant que volume dans Azure Container Instances, vous avez besoin de trois valeurs : le nom du compte de stockage, le nom du partage et la clé d’accès de stockage.

Si vous avez utilisé le script ci-dessus, le nom du compte de stockage a été stocké dans la variable $ACI_PERS_STORAGE_ACCOUNT_NAME. Pour afficher le nom du compte, entrez :

```console
echo $ACI_PERS_STORAGE_ACCOUNT_NAME
```

Le nom du partage étant déjà connu (c’est-à-dire *acishare* dans le script ci-dessus), il ne reste que la clé du compte de stockage, qui peut être trouvée à l’aide de la commande suivante :

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume---cli"></a>Déployer le conteneur et monter le volume - CLI

Pour monter un partage de fichiers Azure en tant que volume dans un conteneur en utilisant Azure CLI, spécifiez le point de montage du volume et le partage lorsque vous créez le conteneur avec [az container create][az-container-create]. Si vous avez accompli les étapes précédentes, vous pouvez monter le partage que vous avez créé avant à l’aide de la commande suivante pour créer un conteneur :

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

La valeur `--dns-name-label` doit être unique au sein de la région Azure dans laquelle vous créez l’instance de conteneur. Mettez à jour la valeur de la commande précédente si vous recevez un message d’erreur **Étiquette de nom DNS** lorsque vous exécutez la commande.

## <a name="manage-files-in-mounted-volume"></a>Gérer les fichiers dans le volume monté

Une fois que le conteneur démarre, vous pouvez utiliser l’application web simple déployée avec la commande Microsoft [aci-hellofiles][aci-hellofiles] image to create small text files in the Azure file share at the mount path you specified. Obtain the web app's fully qualified domain name (FQDN) with the [az container show][az-container-show] :

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn --output tsv
```

Après avoir enregistré le texte à l’aide de l’application, vous pouvez utiliser le [portal Azure][portal] or a tool like the [Microsoft Azure Storage Explorer][storage-explorer] pour récupérer et inspecter le fichier écrit sur le partage de fichiers.

## <a name="deploy-container-and-mount-volume---yaml"></a>Déployer le conteneur et monter le volume - YAML

Vous pouvez également déployer un groupe de conteneurs et monter un volume dans un conteneur avec l’interface Azure CLI et un [modèle YAML](container-instances-multi-container-yaml.md). Le déploiement avec un modèle YAML est la méthode privilégiée lorsqu’il s’agit de déployer des groupes de conteneurs, composés de conteneurs multiples.

Le modèle YAML suivant définit un groupe de conteneurs avec un seul conteneur créé avec l’image `aci-hellofiles`. Le conteneur monte le partage de fichiers Azure *acishare* créé précédemment en tant que volume. Lorsque cela est indiqué, entrez la clé de stockage et le nom du compte de stockage qui héberge le partage de fichiers. 

Comme dans l’exemple CLI, la valeur `dnsNameLabel` doit être unique au sein de la région Azure dans laquelle vous créez l’instance de conteneur. Si nécessaire, mettez à jour la valeur dans le fichier YAML.

```yaml
apiVersion: '2018-10-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Pour déployer grâce au modèle YAML, enregistrez le YAML précédent dans un fichier nommé `deploy-aci.yaml`, puis exécutez la commande [az container create][az-container-create] avec le paramètre `--file` :

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>Déployer le conteneur et monter le volume - Resource Manager

En plus du déploiement de l’interface CLI et de YAML, vous pouvez déployer un groupe de conteneurs et monter un volume dans un conteneur à l’aide d’un [modèle Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) Azure.

Tout d’abord, remplissez le tableau `volumes` dans la section `properties` du groupe de conteneurs du modèle. 

Ensuite, pour chaque conteneur dans lequel vous souhaitez monter le volume, renseignez le tableau `volumeMounts` dans la section `properties` de la définition de conteneur.

Le modèle Resource Manager suivant définit un groupe de conteneurs avec un conteneur montant un volume `aci-hellofiles`secret sur . Le conteneur monte le partage de fichiers Azure *acishare* créé précédemment en tant que volume. Lorsque cela est indiqué, entrez la clé de stockage et le nom du compte de stockage qui héberge le partage de fichiers. 

Comme dans les exemples précédents, la valeur `dnsNameLabel` doit être unique au sein de la région Azure dans laquelle vous créez l’instance de conteneur. Si nécessaire, mettez à jour la valeur dans le modèle.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

Pour déployer grâce au modèle Resource Manager, enregistrez le JSON précédent dans un fichier nommé `deploy-aci.json`, puis exécutez la commande [az container create][az-group-deployment-create] avec le paramètre `--template-file` :

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>Monter plusieurs volumes

Pour monter plusieurs volumes dans une instance de conteneur, vous devez effectuer le déploiement à l’aide d’un [modèle Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) ou d'un fichier YAML. Pour utiliser un modèle ou fichier YAML, fournissez les détails de partage et définissez les volumes en remplissant le tableau `volumes` dans la section `properties` du modèle. 

Par exemple, si vous avez créé deux partages de fichiers Azure nommés *share1* et *share2* dans le compte de stockage *myStorageAccount*, le tableau `volumes` dans un modèle Resource Manager ressemblerait à ceci :

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

Ensuite, pour chaque conteneur du groupe de conteneurs dans lequel vous souhaitez monter les volumes, remplissez le tableau `volumeMounts` dans la section `properties` de la définition de conteneur. Ainsi, nous obtenons les deux volumes montés, *myvolume1* et *myvolume2*, définis précédemment :

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment monter d’autres types de volumes dans Azure Container Instances :

* [Monter un volume emptyDir dans Azure Container Instances](container-instances-volume-emptydir.md)
* [Monter un volume gitRepo dans Azure Container Instances](container-instances-volume-gitrepo.md)
* [Monter un volume secret dans Azure Container Instances](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create