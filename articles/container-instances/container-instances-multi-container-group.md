---
title: Tutoriel - Déployer un groupe de plusieurs conteneurs dans Azure Container Instances - modèle
description: Dans ce tutoriel, vous allez apprendre à déployer un groupe comportant plusieurs conteneurs dans Azure Container Instances à l’aide d’un modèle Resource Manager avec Azure CLI.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f769beda1654dc9f58ecff733741fb1ab9118031
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66152302"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Didacticiel : Déployer un groupe de plusieurs conteneurs avec un modèle Resource Manager

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Azure Container Instances prend en charge le déploiement de plusieurs conteneurs sur un seul hôte à l’aide d’un [groupe de conteneurs](container-instances-container-groups.md). Un groupe de conteneurs est utile lors de la création d’une annexe d’application pour la journalisation, la surveillance ou toute autre configuration dans laquelle un service a besoin d’un deuxième processus associé.

Dans ce tutoriel, vous suivez les étapes pour exécuter une configuration d’annexe simple de deux conteneurs en déployant un modèle Resource Manager à l’aide d’Azure CLI. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Configurer un modèle de groupe de plusieurs conteneurs
> * Déployer le groupe de conteneurs
> * Afficher les journaux des conteneurs

Un modèle Resource Manager peut être facilement adapté pour les scénarios dans lesquels vous avez besoin de déployer des ressources de service Azure supplémentaires (par exemple, un partage Azure Files ou un réseau virtuel) avec le groupe de conteneurs. 

> [!NOTE]
> Les groupes à plusieurs conteneurs sont actuellement restreints aux conteneurs Linux. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>Configurer un modèle

Commencez par copier le JSON suivant dans un nouveau fichier nommé `azuredeploy.json`. Dans Azure Cloud Shell, vous pouvez utiliser Visual Studio Code pour créer le fichier dans votre répertoire de travail :

```
code azuredeploy.json
```

Ce modèle Resource Manager définit un groupe de conteneurs qui comprend deux conteneurs, une adresse IP publique et deux ports exposés. Le premier conteneur du groupe exécute une application web accessible sur Internet. Le deuxième conteneur, l’annexe, adresse une requête HTTP à l’application web principale via le réseau local du groupe.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
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
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
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
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

Pour utiliser un registre d’image de conteneur privé, ajoutez au document JSON un objet du format suivant. Pour un exemple d’implémentation de cette configuration, consultez la documentation de [référence sur le modèle ACI Resource Manager][template-reference].

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>Déployer le modèle

Créez un groupe de ressources avec la commande [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Déployez ensuite le modèle avec la commande [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

Après quelques secondes, vous devriez recevoir une réponse initiale d’Azure.

## <a name="view-deployment-state"></a>Afficher l’état du déploiement

Pour afficher l’état du déploiement, utilisez la commande [az container show][az-container-show] suivante :

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Pour voir l’application en cours d’exécution, accédez à son adresse IP dans votre navigateur. Par exemple, l’adresse IP est `52.168.26.124` dans cet exemple de sortie :

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Afficher les journaux d’activité du conteneur

Consultez la sortie du journal d’un conteneur à l’aide de la commande [az container logs][az-container-logs]. L’argument `--container-name` spécifie le conteneur à partir duquel extraire les journaux d’activité. Dans cet exemple, le conteneur `aci-tutorial-app` est spécifié.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Sortie :

```bash
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Pour afficher les journaux du conteneur annexe, exécutez une commande similaire, en spécifiant le conteneur `aci-tutorial-sidecar`.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Sortie :

```bash
Every 3s: curl -I http://localhost                          2019-03-21 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 21 Mar 2019 20:36:41 GMT
Connection: keep-alive
```

Comme vous pouvez le voir, l’annexe envoie régulièrement une requête HTTP à l’application web principale via le réseau local du groupe pour s’assurer qu’il fonctionne. Cet exemple d’annexe peut être étendu pour déclencher une alerte suite à la réception d’un code de réponse HTTP autre que `200 OK`.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez utilisé un modèle Resource Manager pour déployer un groupe de plusieurs conteneurs dans Azure Container Instances. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Configurer un modèle de groupe de plusieurs conteneurs
> * Déployer le groupe de conteneurs
> * Afficher les journaux des conteneurs

Pour obtenir d’autres exemples de modèles, consultez [Azure Resource Manager templates for Azure Container Instances](container-instances-samples-rm.md) (Modèles Resource Manager pour Azure Container Instances).

Vous pouvez également spécifier un groupe de plusieurs conteneurs à l’aide un [fichier YAML](container-instances-multi-container-yaml.md). En raison de la nature plus concise du format YAML, le déploiement avec un fichier YAML est judicieux lorsque le déploiement comprend uniquement les instances de conteneur.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
