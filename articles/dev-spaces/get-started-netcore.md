---
title: Créer un espace de développement Kubernetes dans le cloud à l’aide de .NET Core et de VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/26/2018
ms.topic: tutorial
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s
ms.openlocfilehash: e05dbc570836741a69ed229fc93eb32a7dfd01dd
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503181"
---
# <a name="get-started-on-azure-dev-spaces-with-net-core"></a>Bien démarrer avec l’utilisation d’Azure Dev Spaces conjointement à .NET Core

Dans ce guide, vous allez apprendre à :

- Créer un environnement Kubernetes dans Azure optimisé pour le développement : un _espace de développement_.
- Développer du code de façon itérative dans des conteneurs en utilisant VS Code et la ligne de commande.
- Développer et tester votre code de façon productive dans un environnement d’équipe.

> [!Note]
> **Si vous êtes bloqué**, consultez la section [Résolution des problèmes](troubleshooting.md).

## <a name="install-the-azure-cli"></a>Installer l’interface de ligne de commande Microsoft Azure
Azure Dev Spaces requiert une configuration d’ordinateur local minimale. La majeure partie de la configuration de votre espace de développement est stockée dans le cloud et peut être partagée avec d’autres utilisateurs. Commencez par télécharger et exécuter [l’interface de ligne de commande Azure (Azure CLI)](/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="sign-in-to-azure-cli"></a>Connectez-vous à Azure CLI
Connectez-vous à Azure. Saisissez la commande suivante dans une fenêtre de terminal :

```cmd
az login
```

> [!Note]
> Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Si vous avez plusieurs abonnements Azure...
Vous pouvez afficher vos abonnements en exécutant : 

```cmd
az account list
```
Recherchez l’abonnement qui a `isDefault: true` dans la sortie JSON.
Si ce n’est pas l’abonnement que vous souhaitez utiliser, vous pouvez modifier l’abonnement par défaut :

```cmd
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Créer un cluster Kubernetes activé pour Azure Dev Spaces

À l’invite de commandes, créez le groupe de ressources dans une [région qui prend en charge Azure Dev Spaces][supported-regions].

```cmd
az group create --name MyResourceGroup --location <region>
```

Créez un cluster Kubernetes avec la commande suivante :

```cmd
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

La création du cluster ne prend que quelques minutes.

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Configurer votre cluster AKS pour utiliser Azure Dev Spaces

Entrez la commande Azure CLI suivante en utilisant le groupe de ressources qui contient votre cluster AKS et le nom du cluster AKS. La commande configure votre cluster avec la prise en charge d’Azure Dev Spaces.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```
   
> [!IMPORTANT]
> Le processus de configuration d’Azure Dev Spaces supprime l’espace de noms `azds` dans le cluster, s’il existe.

## <a name="get-kubernetes-debugging-for-vs-code"></a>Obtenir un débogage Kubernetes pour VS Code
Des fonctionnalités puissantes comme le débogage Kubernetes sont disponibles pour les développeurs .NET Core et Node.js à l’aide de VS Code.

1. Installez [VS Code](https://code.visualstudio.com/Download) si vous ne l’avez pas encore fait.
1. Téléchargez et installez l’[extension VS Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Cliquez sur Installer une fois sur la page Place de marché de l’extension, puis à nouveau dans VS Code. 

## <a name="create-a-web-app-running-in-a-container"></a>Créer une application web s’exécutant dans un conteneur

Dans cette section, vous allez apprendre à créer une application web ASP.NET Core et à l’exécuter dans un conteneur dans Kubernetes.

### <a name="create-an-aspnet-core-web-app"></a>Créez une application web ASP.NET Core
Clonez ou téléchargez l’[exemple d’application Azure Dev Spaces](https://github.com/Azure/dev-spaces). Cet article utilise le code du répertoire *samples/dotnetcore/getting-started/webfrontend*.

## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Préparation de code pour le développement Docker et Kubernetes
Pour l’heure, vous disposez d’une application web de base qui peut s’exécuter localement. Vous allez maintenant la mettre en conteneur en créant des ressources qui définissent le conteneur de l’application et la façon dont il sera déployé dans Kubernetes. Azure Dev Spaces permet d’effectuer facilement cette tâche : 

1. Lancez VS Code et ouvrez le dossier `webfrontend`. (Vous pouvez ignorer les invites par défaut qui vous demandent d’ajouter des ressources de débogage ou de restaurer le projet.)
1. Ouvrez le terminal intégré dans VS Code (à partir du menu **Affichage > Terminal intégré**).
1. Exécutez cette commande (vérifiez que **webfrontend** est bien votre dossier actuel) :

    ```cmd
    azds prep --public
    ```

La commande `azds prep` d’Azure CLI génère des ressources Docker et Kubernetes avec les paramètres par défaut :
* `./Dockerfile` décrit l’image du conteneur de l’application et la façon dont le code source est généré et s’exécute dans le conteneur.
* Un [graphique Helm](https://docs.helm.sh) sous `./charts/webfrontend` décrit la façon dont le conteneur est déployé dans Kubernetes.

Pour l’instant, il n’est pas nécessaire de comprendre l’ensemble du contenu de ces fichiers. Il est cependant important de noter que **les mêmes ressources de configuration en tant que code Kubernetes et Docker peuvent être utilisées du développement jusqu’à la production, ce qui assure une meilleure cohérence dans les différents environnements.**
 
Un fichier nommé `./azds.yaml` est aussi généré par la commande `prep` ; il s’agit du fichier de configuration pour Azure Dev Spaces. Il complète les artefacts Docker et Kubernetes avec une configuration supplémentaire qui offre une expérience de développement itératif dans Azure.

## <a name="build-and-run-code-in-kubernetes"></a>Générer et exécuter du code dans Kubernetes
À présent, exécutons notre code. Dans la fenêtre de terminal, exécutez la commande ci-après à partir du **dossier de code racine**, webfrontend :

```cmd
azds up
```

En surveillant la sortie de la commande pendant que celle-ci s’exécute, vous remarquerez plusieurs choses :
- Le code source est synchronisé avec l’espace de développement dans Azure.
- Une image conteneur est créée dans Azure, comme spécifié par les ressources Docker dans votre dossier de code.
- La commande crée des objets Kubernetes qui utilisent l’image conteneur, comme spécifié par le graphique Helm dans votre dossier de code.
- Des informations relatives aux points de terminaison du conteneur s’affichent. Dans notre cas, nous attendons une URL HTTP publique.
- En supposant que les étapes ci-dessus se sont correctement déroulées, vous devriez commencer à voir la sortie `stdout` (et `stderr`) lorsque le conteneur démarre.

> [!Note]
> Ces étapes nécessitent davantage de temps lors de la première exécution de la commande `up` ; les exécutions suivantes s’effectuent plus rapidement.

### <a name="test-the-web-app"></a>Tester l’application web
Analysez la sortie de console pour trouver le message *Application démarrée*, qui confirme que la commande `up` est terminée :

```
Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Microsoft (R) Build Engine version 15.9.20+g88f5fadfbe for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  webfrontend -> /src/bin/Debug/netcoreapp2.2/webfrontend.dll
  webfrontend -> /src/bin/Debug/netcoreapp2.2/webfrontend.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:00.94
[...]
webfrontend-5798f9dc44-99fsd: Now listening on: http://[::]:80
webfrontend-5798f9dc44-99fsd: Application started. Press Ctrl+C to shut down.
```

Identifiez l’URL publique pour le service dans la sortie de la commande `up`. Elle se termine par `.azds.io`. Dans l’exemple ci-dessus, l’URL publique est `http://webfrontend.1234567890abcdef1234.eus.azds.io/`.

Pour voir votre application web, ouvrez l’URL publique dans un navigateur. En outre, notez que la sortie de `stdout` et `stderr` est diffusée dans la fenêtre de terminal *azds trace* lorsque vous interagissez avec votre application web. Vous verrez également des informations de suivi pour les requêtes HTTP lorsqu’elles passent par le système. Cela facilite le suivi des appels multiservice complexes pendant le développement. L’instrumentation ajoutée par Dev Spaces fournit ce suivi des requêtes.

![Fenêtre de terminal de azds trace](media/get-started-netcore/azds-trace.png)


> [!Note]
> En plus de l’URL publique, vous pouvez utiliser en guise d’alternative l’URL `http://localhost:<portnumber>` affichée dans la sortie de la console. Si vous utilisez l’URL localhost, le conteneur semble s’exécuter en local. En réalité, il s’exécute dans AKS. Azure Dev Spaces utilise la fonctionnalité de *redirection des ports* de Kubernetes pour mapper le port localhost sur le conteneur en cours d’exécution dans AKS. Cela facilite l’interaction avec le service à partir de votre machine locale.

### <a name="update-a-content-file"></a>Mettre à jour un fichier de contenu
Azure Dev Spaces vous permet non seulement d’obtenir un code s’exécutant dans Kubernetes, mais également de visualiser rapidement et de façon itérative la prise en compte des modifications de votre code dans un environnement Kubernetes dans le cloud.

1. Recherchez le fichier `./Views/Home/Index.cshtml` et procédez à une modification du code HTML. Par exemple, remplacez la [ligne 73 indiquant `<h2>Application uses</h2>`](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Views/Home/Index.cshtml#L73) par une chaîne telle que : 

    ```html
    <h2>Hello k8s in Azure!</h2>
    ```

1. Enregistrez le fichier . Quelques instants plus tard, la fenêtre de terminal affiche un message indiquant qu’un fichier du conteneur en cours d’exécution a été mis à jour.
1. Accédez à votre navigateur et actualisez la page. La page web doit afficher le code HTML mis à jour.

Que s’est-il passé ? Les modifications des fichiers de contenu, comme HTML et CSS, ne nécessitent aucune recompilation dans une application web .NET Core. Par conséquent, une commande `azds up` active synchronise automatiquement tous les fichiers de contenu modifiés dans le conteneur en cours d’exécution dans Azure, ce qui vous permet de visualiser immédiatement les modifications du contenu.

### <a name="update-a-code-file"></a>Mettre à jour un fichier de code
La mise à jour des fichiers de code nécessite un peu plus de travail, car une application .NET Core doit régénérer et produire les fichiers binaires d’application mis à jour.

1. Dans la fenêtre de terminal, appuyez sur `Ctrl+C` (pour arrêter `azds up`).
1. Ouvrez le fichier de code nommé `Controllers/HomeController.cs`, puis modifiez le message qui doit s’afficher sur la page À propos de : `ViewData["Message"] = "Your application description page.";`
1. Enregistrez le fichier .
1. Exécutez `azds up` dans la fenêtre de terminal. 

Cette commande régénère l’image conteneur et redéploie le graphique Helm. Pour visualiser la prise en compte de vos modifications de code dans l’application en cours d’exécution, accédez au menu À propos de figurant dans l’application web.

Toutefois, vous allez découvrir à la section suivante une *méthode encore plus rapide* pour développer du code. 

## <a name="debug-a-container-in-kubernetes"></a>Déboguer un conteneur dans Kubernetes

Dans cette section, vous utiliserez VS Code pour déboguer directement notre conteneur exécuté dans Azure. Vous apprendrez aussi à obtenir une boucle modification-exécution-test plus rapide.

![](media/common/edit-refresh-see.png)

> [!Note]
> **Si vous êtes bloqué**, consultez la section [Résolution des problèmes](troubleshooting.md) ou postez un commentaire sur cette page.

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Initialiser des ressources de débogage avec l’extension VS Code
Vous devez avant tout configurer votre projet de code pour permettre à VS Code de communiquer avec notre espace de développement dans Azure. L’extension VS Code pour Azure Dev Spaces fournit une commande d’assistance pour définir la configuration de débogage. 

Ouvrez la **Palette de commandes** (à partir du menu **Affichage | Palette de commandes**) et utilisez la saisie semi-automatique pour taper et sélectionnez cette commande : `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

Celle-ci ajoute la configuration de débogage pour Azure Dev Spaces sous le dossier `.vscode`. Cette commande ne doit pas être confondue avec la commande `azds prep`, qui configure le projet pour le déploiement.

![](media/common/command-palette.png)


### <a name="select-the-azds-debug-configuration"></a>Sélectionner la configuration de débogage AZDS
1. Pour ouvrir l’affichage de débogage, cliquez sur l’icône Débogage dans la **barre d’activités** située sur le côté de VS Code.
1. Sélectionnez **Lancer .NET Core (AZDS)** comme configuration de débogage active.

![](media/get-started-netcore/debug-configuration.png)

> [!Note]
> Si la palette de commandes ne présente aucune commande Azure Dev Spaces, vérifiez que vous avez installé l’extension VS Code pour Azure Dev Spaces. Assurez-vous que l’espace de travail que vous avez ouvert dans VS Code correspond au dossier contenant azds.yaml.


### <a name="debug-the-container-in-kubernetes"></a>Déboguer le conteneur dans Kubernetes
Pour déboguer votre code dans Kubernetes, appuyez sur **F5**.

Comme avec la commande `up`, le code est synchronisé avec l’espace de développement, et un conteneur est créé et déployé dans Kubernetes. Cette fois-ci, le débogueur est bien entendu joint au conteneur distant.

> [!Tip]
> La barre d’état VS Code devient orange, indiquant que le débogueur est attaché. Elle affiche également une URL interactive, que vous pouvez utiliser pour ouvrir votre site.

![](media/common/vscode-status-bar-url.png)

Définissez un point d’arrêt dans un fichier de code côté serveur, par exemple dans la fonction `About()` du fichier source `Controllers/HomeController.cs`. L’actualisation de la page du navigateur entraîne l’atteinte du point d’arrêt.

Vous disposez d’un accès complet aux informations de débogage, exactement comme si le code s’exécutait en local, notamment à la pile des appels, aux variables locales, aux informations sur les exceptions, etc.

### <a name="edit-code-and-refresh"></a>Modifier du code et actualiser la page
Lorsque le débogueur est actif, apportez une modification au code. Par exemple, modifiez le message de la page À propos de dans `Controllers/HomeController.cs`. 

```csharp
public IActionResult About()
{
    ViewData["Message"] = "My custom message in the About page.";
    return View();
}
```

Enregistrez le fichier, puis dans le **volet Actions de débogage**, cliquez sur le bouton **Redémarrer**. 

![](media/common/debug-action-refresh.png)

Plutôt que de régénérer et redéployer une image conteneur chaque fois que des modifications de code sont effectuées, cette opération nécessitant généralement un temps considérable, Azure Dev Spaces recompile le code de manière incrémentielle au sein du conteneur existant afin d’accélérer la boucle de modification/débogage.

Actualisez l’application web dans le navigateur, puis accédez à la page À propos de. Votre message personnalisé doit apparaître dans l’interface utilisateur.

**Vous disposez désormais d’une méthode d’itération rapide sur le code et de débogage directement dans Kubernetes.** Ensuite, vous verrez comment créer et appeler un second conteneur.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur le développement multiservice](multi-service-netcore.md)


[supported-regions]: about.md#supported-regions-and-configurations