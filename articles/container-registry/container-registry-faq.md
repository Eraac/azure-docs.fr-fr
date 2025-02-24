---
title: Azure Container Registry - Forum aux questions
description: Réponses aux questions fréquemment posées sur le service Azure Container Registry
services: container-registry
author: sajayantony
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: sajaya
ms.openlocfilehash: 2b835765bbd40ffbd4a5117f767a7ba163e41dda
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309285"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Forum aux questions sur Azure Container Registry

Cet article aborde les questions fréquemment posées et les problèmes connus liés à Azure Container Registry.

## <a name="resource-management"></a>Gestion des ressources

- [Puis-je créer un registre de conteneurs Azure à l’aide d’un modèle Resource Manager ?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Existe-t-il une analyse de sécurité des vulnérabilités pour les images dans ACR ?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Comment configurer Kubernetes avec Azure Container Registry ?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Comment obtenir des informations d’identification administrateur pour un registre de conteneurs ?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Comment obtenir des informations d’identification administrateur dans un modèle Resource Manager ?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [La suppression de la réplication échoue avec l’état Forbidden (Interdit), bien que la réplication soit supprimée à l’aide de l’interface de ligne de commande Azure ou d’Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Les règles de pare-feu sont correctement mises à jour, mais ne prennent pas effet](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Puis-je créer un registre de conteneurs Azure à l’aide d’un modèle Resource Manager ?

Oui. Voici [un modèle](https://github.com/Azure/azure-cli/blob/master/src/command_modules/azure-cli-acr/azure/cli/command_modules/acr/template.json) que vous pouvez utiliser pour créer un registre.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Existe-t-il une analyse de sécurité des vulnérabilités pour les images dans ACR ?

Oui. Consultez la documentation de [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) et [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Comment configurer Kubernetes avec Azure Container Registry ?

Consultez la documentation de [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) et les étapes concernant [Azure Kubernetes Service](container-registry-auth-aks.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Comment obtenir des informations d’identification administrateur pour un registre de conteneurs ?

> [!IMPORTANT]
> Le compte d’utilisateur administrateur est conçu pour permettre à un seul utilisateur d’accéder au registre, principalement à des fins de test. Nous ne recommandons pas de partager les informations d’identification du compte d’administrateur avec plusieurs utilisateurs. Une identité individuelle est recommandée pour les utilisateurs et principaux du service pour les scénarios sans périphérique de contrôle. Consultez [Vue d’ensemble de l’authentification](container-registry-authentication.md).

Avant d’obtenir les informations d’identification administrateur, vérifiez que l’utilisateur administrateur du registre est activé.

Pour obtenir les informations d’identification à l’aide de l’interface de ligne de commande Azure :

```azurecli
az acr credential show -n myRegistry
```

Utilisation d’Azure PowerShell :

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Comment obtenir des informations d’identification administrateur dans un modèle Resource Manager ?

> [!IMPORTANT]
> Le compte d’utilisateur administrateur est conçu pour permettre à un seul utilisateur d’accéder au registre, principalement à des fins de test. Nous ne recommandons pas de partager les informations d’identification du compte d’administrateur avec plusieurs utilisateurs. Une identité individuelle est recommandée pour les utilisateurs et principaux du service pour les scénarios sans périphérique de contrôle. Consultez [Vue d’ensemble de l’authentification](container-registry-authentication.md).

Avant d’obtenir les informations d’identification administrateur, vérifiez que l’utilisateur administrateur du registre est activé.

Pour obtenir le premier mot de passe :

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Pour obtenir le second mot de passe :

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>La suppression de la réplication échoue avec l’état Forbidden (Interdit), bien que la réplication soit supprimée à l’aide de l’interface de ligne de commande Azure ou d’Azure PowerShell

L’erreur se produit quand l’utilisateur dispose d’autorisations sur un registre, mais ne dispose pas d’autorisations de niveau Lecteur sur l’abonnement. Pour résoudre ce problème, attribuez des autorisations Lecteur sur l’abonnement à l’utilisateur :


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Les règles de pare-feu sont correctement mises à jour, mais ne prennent pas effet

La propagation des modifications apportées aux règles de pare-feu prend un certain temps. Une fois que vous avez changé les paramètres de pare-feu, veuillez patienter quelques minutes avant de vérifier le changement apporté.


## <a name="registry-operations"></a>Opérations du registre

- [Comment accéder à Docker Registry HTTP API V2 ?](#how-do-i-access-docker-registry-http-api-v2)
- [Comment supprimer tous les manifestes qui ne sont référencés par aucune étiquette dans un dépôt ?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Pourquoi l’utilisation du quota du registre ne diminue-t-elle pas après la suppression d’images ?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Comment valider les modifications de quotas de stockage ?](#how-do-i-validate-storage-quota-changes)
- [Comment m’authentifier auprès de mon registre lors de l’exécution de l’interface CLI dans un conteneur ?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Azure Container Registry offre-t-il une configuration limitée à TLS v1.2 et comment activer TLS v1.2 ?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Azure Container Registry prend-il en charge l’approbation de contenu ?](#does-azure-container-registry-support-content-trust)
- [Comment octroyer l’accès au tirage (pull) ou à l’envoi (push) d’images sans autorisation de gérer la ressource du registre ?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Comment activer le contrôle automatique des images pour un registre ?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Comment accéder à Docker Registry HTTP API V2 ?

ACR prend en charge Docker Registry HTTP API V2. Les API sont accessibles à l’adresse `https://<your registry login server>/v2/`. Exemple : `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Comment supprimer tous les manifestes qui ne sont référencés par aucune étiquette dans un dépôt ?

Si vous êtes sur bash :

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Pour PowerShell :

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Remarque : Vous pouvez ajouter `-y` dans la commande de suppression pour ignorer la confirmation.

Pour plus d’informations, consultez [Supprimer des images conteneur dans Azure Container Registry](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Pourquoi l’utilisation du quota du registre ne diminue-t-elle pas après la suppression d’images ?

Cette situation peut se produire si les couches sous-jacentes sont toujours référencées par d’autres images conteneur. Si vous supprimez une image sans aucune référence, l’utilisation du registre est mise à jour en quelques minutes.

### <a name="how-do-i-validate-storage-quota-changes"></a>Comment valider les modifications de quotas de stockage ?

Créez une image avec une couche de 1 Go à l’aide du fichier docker suivant. Ainsi, l’image a une couche qui n’est partagée par aucune autre image dans le registre.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Générez l’image et envoyez-la (push) à votre registre à l’aide de l’interface CLI docker.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Vous devez être en mesure de voir que l’utilisation du stockage a augmenté dans le portail Azure, ou vous pouvez interroger l’utilisation à l’aide de l’interface CLI.

```bash
az acr show-usage -n myregistry
```

Supprimez l’image à l’aide de l’interface de ligne de commande Azure ou du portail et vérifiez que l’utilisation a été mise à jour au bout de quelques minutes.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Comment m’authentifier auprès de mon registre lors de l’exécution de l’interface CLI dans un conteneur ?

Vous devez exécuter le conteneur Azure CLI en montant le socket Docker :

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

Dans le conteneur, installez `docker` :

```bash
apk --update add docker
```

Ensuite, authentifiez-vous auprès du registre :

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Azure Container Registry offre-t-il une configuration limitée à TLS v1.2 et comment activer TLS v1.2 ?

Oui. Activez TLS à l’aide de n’importe quel client docker récent (version 18.03.0 et ultérieures). 

### <a name="does-azure-container-registry-support-content-trust"></a>Azure Container Registry prend-il en charge l’approbation de contenu ?

Oui, vous pouvez utiliser des images approuvées dans Azure Container Registry, étant donné que [Docker Notary](https://docs.docker.com/notary/getting_started/) a été intégré et peut être activé. Pour plus d’informations, consultez [Approbation de contenu dans Azure Container Registry](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Où se trouve le fichier de l’empreinte ?

Sous `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata` :

* Les clés publiques et les certificats de tous les rôles (à l’exception des rôles de délégation) sont stockés dans `root.json`.
* Les clés publiques et les certificats du rôle de délégation sont stockés dans le fichier JSON de son rôle parent (par exemple `targets.json` pour le rôle `targets/releases`).

Il est recommandé de vérifier ces clés publiques et certificats une fois que le client Docker Notary a procédé à une vérification globale au moyen du framework TUF.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Comment octroyer l’accès au tirage (pull) ou à l’envoi (push) d’images sans autorisation de gérer la ressource du registre ?

ACR prend en charge les [rôles personnalisés](container-registry-roles.md) qui fournissent différents niveaux d’autorisations. Plus précisément, les rôles `AcrPull` et `AcrPush` permettent aux utilisateurs de tirer (pull) et/ou d’envoyer (push) des images sans l’autorisation de gérer la ressource du registre dans Azure.

* Portail Azure : Votre registre -> Contrôle d’accès (IAM) -> Ajouter (sélectionnez `AcrPull` ou `AcrPush` pour le rôle).
* Interface de ligne de commande Azure : Recherchez l’ID de ressource du registre en exécutant la commande suivante :

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Vous pouvez ensuite attribuer le rôle `AcrPull` ou `AcrPush` à un utilisateur (l’exemple suivant utilise `AcrPull`) :

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  Ou bien, attribuez le rôle à un principal de service identifié par son ID d’application :

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Le destinataire est alors en mesure de s’authentifier et d’accéder aux images dans le registre.

* Pour s’authentifier auprès du registre :
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Pour lister les dépôts :

  ```azurecli
  az acr repository list -n myRegistry
  ```

 Pour tirer (pull) une image :
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

À l’aide du seul rôle `AcrPull` ou `AcrPush`, le destinataire n’est pas autorisé à gérer la ressource du registre dans Azure. Par exemple, `az acr list` ou `az acr show -n myRegistry` n’affiche pas le registre.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Comment activer le contrôle automatique des images pour un registre ?

Le contrôle des images est une fonctionnalité d’évaluation d’ACR. Vous pouvez activer le mode de contrôle d’un registre afin que seules les images qui ont franchi avec succès la phase d’analyse de sécurité soient visibles par les utilisateurs normaux. Pour plus d’informations, consultez le [dépôt GitHub ACR](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics-and-health-checks"></a>Diagnostics et contrôles d’intégrité

- [Contrôler l’intégrité avec `az acr check-health`](#check-health-with-az-acr-check-health)
- [La commande docker pull échoue avec l’erreur : net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [La commande docker push réussit, mais la commande docker pull échoue avec l’erreur : unauthorized: authentication required](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [Activer et obtenir les journaux de débogage du démon docker](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Les nouvelles autorisations utilisateur peuvent ne pas entrer en vigueur dès la mise à jour](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Les informations d’authentification ne sont pas fournies dans le format correct sur les appels directs de l’API REST](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Pourquoi le portail Azure ne liste-t-il pas tous mes dépôts ou étiquettes ?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Comment collecter les traces http sur Windows ?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Contrôler l’intégrité avec `az acr check-health`

Pour résoudre les problèmes courants liés à l’environnement et au registre, consultez [Vérifier l’intégrité d’un registre de conteneurs Azure](container-registry-check-health.md).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>La commande docker pull échoue avec l’erreur : net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)

 - Si cette erreur est un problème temporaire, une nouvelle tentative réussit.
 - Si `docker pull` échoue en permanence, un problème affecte peut-être le démon Docker. Vous pouvez généralement résoudre le problème en redémarrant le démon Docker. 
 - Si vous continuez à voir ce problème après le redémarrage du démon Docker, il se peut que la machine ait un problème de connectivité réseau. Pour vérifier si le réseau d’ordre général sur la machine est sain, exécutez la commande suivante afin de tester la connectivité du point de terminaison. La version `az acr` minimale qui contient cette vérification de la connectivité est 2.2.9. Si vous utilisez une version antérieure, mettez à niveau votre interface de ligne de commande Azure.
 
   ```azurecli
    az acr check-health -n myRegistry
    ```
 - Vous devez toujours avoir un mécanisme de nouvelle tentative sur toutes les opérations du client Docker.

### <a name="docker-pull-is-slow"></a>La commande docker pull est lente
Utilisez [cet](http://www.azurespeed.com/Azure/Download) outil pour tester la vitesse de téléchargement de votre réseau de machines. Si le réseau de machines est lent, envisagez d’utiliser la machine virtuelle Azure dans la même région que votre registre. Vous pouvez ainsi généralement bénéficier d’une vitesse réseau plus rapide.

### <a name="docker-push-is-slow"></a>La commande docker push est lente
Utilisez [cet](http://www.azurespeed.com/Azure/Upload) outil pour tester la vitesse de chargement de votre réseau de machines. Si le réseau de machines est lent, envisagez d’utiliser la machine virtuelle Azure dans la même région que votre registre. Vous pouvez ainsi généralement bénéficier d’une vitesse réseau plus rapide.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>La commande docker push réussit, mais la commande docker pull échoue avec l’erreur : unauthorized: authentication required

Cette erreur peut se produire avec la version Red Hat du démon Docker, où `--signature-verification` est activé par défaut. Vous pouvez vérifier les options du démon Docker pour RHEL (Red Hat Enterprise Linux) ou Fedora en exécutant la commande suivante :

```bash
grep OPTIONS /etc/sysconfig/docker
```

Par exemple, Fedora 28 Server présente les options du démon docker suivantes :

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

Quand `--signature-verification=false` fait défaut, `docker pull` échoue avec une erreur similaire à la suivante :

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Pour résoudre l’erreur :
1. Ajoutez l’option `--signature-verification=false` au fichier de configuration du démon Docker `/etc/sysconfig/docker`. Par exemple :

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. Redémarrez le service du démon Docker en exécutant la commande suivante :

  ```bash
  sudo systemctl restart docker.service
  ```

Pour obtenir des informations détaillées sur `--signature-verification`, vous pouvez exécuter `man dockerd`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Activer et obtenir les journaux de débogage du démon docker  

Démarrez `dockerd` avec l’option `debug`. Tout d’abord, créez le fichier de configuration du démon Docker (`/etc/docker/daemon.json`) s’il n’existe pas et ajoutez l’option `debug` :

```json
{   
    "debug": true   
}
```

Ensuite, redémarrez le démon. Par exemple, avec Ubuntu 14.04 :

```bash
sudo service docker restart
```

Des informations détaillées sont disponibles dans la [documentation Docker](https://docs.docker.com/engine/admin/#enable-debugging). 

 * Vous pouvez générer les journaux à différents emplacements, selon votre système. Par exemple, pour Ubuntu 14.04, il s’agit de `/var/log/upstart/docker.log`.   
Consultez la [documentation Docker](https://docs.docker.com/engine/admin/#read-the-logs) pour plus d’informations.    

 * Concernant Docker pour Windows, les journaux sont générés sous %LOCALAPPDATA%/docker/. Il se peut toutefois qu’ils ne contiennent pas encore toutes les informations de débogage.   

   Pour accéder au journal complet du démon, vous devrez peut-être suivre quelques étapes supplémentaires :

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Maintenant, vous avez accès à tous les fichiers de la machine virtuelle en exécutant `dockerd`. Le journal se trouve à l’emplacement `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Les nouvelles autorisations utilisateur peuvent ne pas entrer en vigueur dès la mise à jour

Quand vous accordez de nouvelles autorisations (nouveaux rôles) à un principal de service, la modification peut ne pas prendre effet immédiatement. Il existe deux raisons possibles :

* Délai d’attribution de rôle Azure Active Directory. Normalement, l’attribution est rapide, mais elle peut prendre quelques minutes en raison du délai de propagation.
* Délai d’autorisation sur le serveur de jetons ACR. Cette opération peut prendre jusqu’à 10 minutes. Pour limiter ce délai, vous pouvez exécuter la commande `docker logout`, puis vous réauthentifier avec le même utilisateur après 1 minute :

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

ACR ne prend pas en charge la suppression de la réplication de base par les utilisateurs. La solution de contournement consiste à inclure l’opération de création de la réplication de base dans le modèle, mais à ignorer sa création en ajoutant `"condition": false` comme indiqué ci-dessous :

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Les informations d’authentification ne sont pas fournies dans le format correct sur les appels directs de l’API REST

Vous pouvez rencontrer une erreur `InvalidAuthenticationInfo`, en particulier en utilisant l’outil `curl` avec l’option `-L`, `--location` (pour suivre les redirections).
Par exemple, la récupération (fetch) de l’objet blob en utilisant `curl` avec l’option `-L` et l’authentification de base :

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

Peut entraîner la réponse suivante :

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

La cause racine est que certaines implémentations `curl` suivent les redirections avec des en-têtes de la demande d’origine.

Pour résoudre le problème, vous devez suivre les redirections manuellement sans les en-têtes. Récupérez les en-têtes de réponse avec l’option `-D -` de `curl` et extrayez l’en-tête`Location`  :

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Pourquoi le portail Azure ne liste-t-il pas tous mes dépôts ou étiquettes ? 

Si vous utilisez le navigateur Microsoft Edge/Internet Explorer, vous pouvez voir au maximum 100 dépôts ou étiquettes. Si votre registre a plus de 100 dépôts ou étiquettes, nous vous recommandons d’utiliser le navigateur Firefox ou Chrome pour les lister tous.

### <a name="how-do-i-collect-http-traces-on-windows"></a>Comment collecter les traces http sur Windows ?

#### <a name="prerequisites"></a>Prérequis

- Activez le déchiffrement de https dans Fiddler : <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Autorisez Docker à utiliser un proxy via l’interface utilisateur Docker : <https://docs.docker.com/docker-for-windows/#proxies>
- Veillez à revenir à la configuration antérieure une fois que vous avez terminé.  Docker ne fonctionne pas si ces options sont activées et que Fiddler n’est pas en cours d’exécution.

#### <a name="windows-containers"></a>Conteneurs Windows

Configurez le proxy Docker sur 127.0.0.1:8888.

#### <a name="linux-containers"></a>Conteneurs Linux

Recherchez l’adresse IP du commutateur virtuel de machine virtuelle Docker :

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Configurez le proxy Docker sur la sortie de la commande précédente et le port 8888 (par exemple 10.0.75.1:8888)

## <a name="tasks"></a>Tâches

- [Comment annuler des exécutions par lots ?](#how-do-i-batch-cancel-runs)
- [Comment inclure le dossier .git dans la commande az acr build ?](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>Comment annuler des exécutions par lots ?

Les commandes suivantes annulent toutes les tâches en cours d’exécution dans le registre spécifié.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Comment inclure le dossier .git dans la commande az acr build ?

Si vous passez un dossier source local à la commande `az acr build`, le dossier `.git` est exclu du package chargé par défaut. Vous pouvez créer un fichier `.dockerignore` avec le paramètre suivant. Il indique à la commande de restaurer tous les fichiers sous `.git` dans le package chargé. 

```
!.git/**
```

Ce paramètre s’applique également à la commande `az acr run`.

## <a name="cicd-integration"></a>Intégration CI/CD

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Actions GitHub](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Étapes suivantes

* [Découvrez-en plus](container-registry-intro.md) sur Azure Container Registry.