---
title: Vérifier l’intégrité d’un registre dans Azure Container Registry
description: Découvrez comment exécuter une commande de diagnostic rapide pour identifier des problèmes courants lors de l’utilisation d’un registre de conteneurs Azure, notamment la configuration locale de Docker et la connectivité au registre
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3511655d220ee85ce6b5744612e5d6fddafbe877
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309729"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Vérifier l’intégrité d’un registre de conteneurs Azure

Lorsque vous utilisez un registre de conteneurs Azure, vous pouvez parfois rencontrer des problèmes. Par exemple, vous ne pourrez peut-être pas extraire une image de conteneur en raison d’un problème avec Docker dans votre environnement local. Il est également possible qu’un problème de réseau vous empêche de vous connecter au registre. 

La première étape du diagnostic consiste à exécuter la command to get information about the health of the environment and optionally access to a target registry. This command is available in Azure CLI version 2.0.67 or later. If you need to install or upgrade, see [Install Azure CLI][azure-cli], [az acr check-health][az-acr-check-health].

## <a name="run-az-acr-check-health"></a>Exécuter az acr check-health

Les exemples suivants montrent différentes façons d’exécuter la commande `az acr check-health`.

> [!NOTE]
> Si vous exécutez la commande dans Azure Cloud Shell, l’environnement local n’est pas vérifié. Toutefois, vous pouvez vérifier l’accès à un registre cible.

### <a name="check-the-environment-only"></a>Vérifier uniquement l’environnement

Pour vérifier le démon Docker local, la version de l’interface CLI ainsi que la configuration du client Helm, exécutez la commande sans paramètre supplémentaire :

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Vérifier l’environnement et un registre cible

Pour vérifier l’accès à un registre et vérifier l’environnement local, transmettez le nom d’un registre cible. Par exemple :

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Envoie de rapports d’erreurs

La commande enregistre des informations dans la sortie standard. Si un problème est détecté, il fournit un code d’erreur et une description. Pour plus d’informations sur les codes et les solutions possibles, consultez la [référence sur l’erreur](container-registry-health-error-reference.md).

Par défaut, la commande s’arrête chaque fois qu’elle détecte une erreur. Vous pouvez également exécuter la commande afin qu’elle fournisse la sortie pour toutes les vérifications d’intégrité, même si des erreurs sont détectées. Ajoutez le paramètre `--ignore-errors` comme indiqué dans les exemples suivants :

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Exemple de sortie :

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les codes d’erreur retournés par la commande [az acr check-health][az-acr-check-health], consultez la [référence sur l’erreur de vérification d’intégrité](container-registry-health-error-reference.md).

Consultez la section [FAQ](container-registry-faq.md) pour voir les questions fréquemment posées et les autres problèmes connus relatifs à Azure Container Registry.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
