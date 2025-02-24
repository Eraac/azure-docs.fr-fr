---
title: Configurer votre environnement de développement sur Mac OS X afin de travailler avec Azure Service Fabric | Microsoft Docs
description: Installez le runtime, le kit de développement logiciel et créez un cluster de développement local. Une fois la configuration terminée, vous serez prêt à générer des applications sur Mac OS X.
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: chackdan
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: linux
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: suhuruli
ms.openlocfilehash: 84d1f52b5fb8f18d3578bad28930f74534b1409f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60947587"
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurer votre environnement de développement sur Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Vous pouvez générer des applications Azure Service Fabric pour qu’elles s’exécutent sur les clusters Linux à l’aide de Mac OS X. Ce document explique comment configurer votre Mac pour le développement.

## <a name="prerequisites"></a>Prérequis
Azure Service Fabric n’est pas exécuté en mode natif sur Mac OS X. Pour exécuter un cluster Service Fabric local, une image de conteneur Docker préconfigurée est fournie. Avant de commencer, vous avez besoin des éléments suivants :

* Au moins 4 Go de RAM.
* La dernière version de [Docker](https://www.docker.com/).

>[!TIP]
>
>Pour installer Docker sur votre Mac, suivez les étapes indiquées dans la [documentation de Docker](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install). Après l’installation, [vérifiez votre installation](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>Créer un conteneur local et configurer Service Fabric
Pour configurer un conteneur Docker local et y exécuter un cluster Service Fabric, procédez comme suit :

1. Mettez à jour la configuration du démon Docker sur votre ordinateur hôte avec les paramètres suivants, puis redémarrez le démon Docker : 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Vous pouvez mettre à jour ces paramètres directement dans le fichier daemon.json dans votre chemin d’installation de Docker. Vous pouvez modifier directement les paramètres de configuration du démon dans Docker. Sélectionnez l’**icône de Docker**, puis sélectionnez **Préférences** > **Démon** > **Avancé**.
    
    >[!NOTE]
    >
    >Modifier le démon directement dans Docker est recommandé, car l’emplacement du fichier daemon.json peut varier d’une machine à l’autre. Par exemple, ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >

    >[!TIP]
    >Nous recommandons d’augmenter la quantité de ressources allouées à Docker lorsque vous testez des applications volumineuses. Pour cela, sélectionnez **l’icône de Docker**, puis choisissez **Avancé** pour ajuster le nombre de cœurs et la mémoire.

2. Dans le nouveau répertoire, créez un fichier nommé `Dockerfile` pour créer votre image Service Fabric :

    ```Dockerfile
    FROM microsoft/service-fabric-onebox
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >Vous pouvez adapter ce fichier pour ajouter des programmes supplémentaires ou des dépendances dans votre conteneur.
    >Par exemple, l’ajout de `RUN apt-get install nodejs -y` permet de prendre en charge les applications `nodejs` comme exécutables invités.
    
    >[!TIP]
    > Par défaut, cela extraira l’image avec la dernière version de Service Fabric. Pour des révisions particulières, visitez la page [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

3. Pour créer votre image réutilisable à partir de `Dockerfile`, ouvrez un terminal et `cd` vers le répertoire contenant votre `Dockerfile`, puis exécutez :

    ```bash 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Cette opération prend un certain temps, mais ne doit être effectuée qu’une seule fois.

4. Vous pouvez maintenant démarrer rapidement une copie locale de Service Fabric, chaque fois que nécessaire, en exécutant :

    ```bash 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Donnez un nom à votre instance de conteneur de sorte qu’elle puisse être gérée plus facilement. 
    >
    >Si votre application écoute sur certains ports, ceux-ci doivent être spécifiés à l’aide de balises `-p` supplémentaires. Par exemple, si votre application écoute sur le port 8080, ajoutez la balise `-p` suivante :
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. Le cluster prendra quelques instants pour démarrer. Lorsqu’il s’exécute, vous pouvez afficher les journaux d’activité à l’aide de la commande suivante ou accéder au tableau de bord pour afficher l’intégrité des clusters [http://localhost:19080](http://localhost:19080) :

    ```bash 
    docker logs sftestcluster
    ```



6. Pour arrêter et nettoyer le conteneur, utilisez la commande suivante. Toutefois, nous allons utiliser ce conteneur à l’étape suivante.

    ```bash 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Limites connues 
 
 Voici les limitations connues d’un cluster local qui s’exécute dans un conteneur sous Mac : 
 
 * Le service DNS ne s’exécute pas et n’est pas pris en charge [Problème n° 132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Configurer la CLI Service Fabric (sfctl) sur votre ordinateur Mac

Suivez les instructions de [la CLI Service Fabric](service-fabric-cli.md#cli-mac) pour installer la CLI Service Fabric (`sfctl`) sur votre ordinateur Mac.
Les commandes CLI prennent en charge l’interaction avec des entités Service Fabric, y compris des clusters, des applications et des services.

1. Pour vous connecter au cluster avant de déployer des applications, exécutez la commande ci-dessous. 

```bash
sfctl cluster select --endpoint http://localhost:19080
```

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>Créer votre application sur votre Mac à l’aide de Yeoman

Service Fabric fournit des outils de génération de modèles automatique qui vous aident à créer une application Service Fabric depuis un terminal à l’aide du générateur de modèles Yeoman. Procédez comme suit pour vous assurer que le générateur de modèles Yeoman Service Fabric est en état de fonctionnement sur votre machine :

1. Node.js et le gestionnaire de package de nœud (NPM) doivent être installés sur votre Mac. Le logiciel peut être installé à l’aide de [HomeBrew](https://brew.sh/), comme suit :

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Installez le générateur de modèles [Yeoman](https://yeoman.io/) sur votre machine à partir de NPM :

    ```bash
    npm install -g yo
    ```
3. Installez le générateur Yeoman que vous préférez en suivant les étapes indiquées dans la [documentation](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables) de prise en main. Pour créer des applications Service Fabric à l’aide de Yeoman, procédez comme suit :

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Après avoir installé ces générateurs, vous devez être en mesure de créer un exécutable invité ou des services de conteneurs en exécutant respectivement `yo azuresfguest` ou `yo azuresfcontainer`.

5. Pour générer une application Java Service Fabric sur votre Mac, JDK version 1.8 et Gradle doivent être installés sur la machine hôte. Le logiciel peut être installé à l’aide de [HomeBrew](https://brew.sh/), comme suit : 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

    >[!TIP]
    > Vérifiez que la bonne version du kit JDK est installée. 

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>Déployer votre application sur votre Mac à partir du terminal

Lorsque vous avez créé et généré votre application Service Fabric, vous pouvez déployer votre application à l’aide de la [CLI Service Fabric](service-fabric-cli.md#cli-mac) :

1. Connectez-vous au cluster Service Fabric qui est en cours d’exécution à l’intérieur de l’instance de conteneur sur votre Mac :

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Depuis votre répertoire de projet, exécutez le script d’installation :

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Configurer le développement de .NET Core 2.0

Installez le [Kit de développement logiciel (SDK) .NET Core 2.0 pour Mac](https://www.microsoft.com/net/core#macos) afin de démarrer [la création d’applications Service Fabric C#](service-fabric-create-your-first-linux-application-with-csharp.md). Les packages pour les applications .NET Core 2.0 Service Fabric sont hébergés sur NuGet.org, qui est actuellement en préversion.

## <a name="install-the-service-fabric-plug-in-for-eclipse-on-your-mac"></a>Installer le plug-in Service Fabric pour Eclipse sur votre Mac

Azure Service Fabric fournit un plug-in pour Eclipse Neon (ou une version ultérieure) pour l’environnement de développement intégré Java. Le plug-in simplifie le processus de création, de génération et de déploiement des services Java. Pour installer ou mettre à jour le plug-in Service Fabric pour Eclipse vers la dernière version, procédez comme [suit](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse). Les autres étapes de la [documentation de Service Fabric pour Eclipse](service-fabric-get-started-eclipse.md) sont également applicables : générer une application, ajouter un service à une application, désinstaller une application, etc.

La dernière étape consiste à instancier le conteneur avec un chemin d’accès qui est partagé avec votre hôte. Le plug-in requiert ce type d’instanciation pour utiliser le conteneur Docker sur votre Mac. Par exemple :

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox microsoft/service-fabric-onebox
```

Les attributs sont définis comme suit :
* `/Users/sayantan/work/workspaces/mySFWorkspace` est le chemin d’accès qualifié complet de l’espace de travail sur votre Mac.
* `/tmp/mySFWorkspace` est le chemin d’accès à l’intérieur du conteneur auquel l’espace de travail doit être mappé.

>[!NOTE]
> 
>Si vous avez un nom/chemin d’accès différent pour votre espace de travail, mettez à jour ces valeurs dans la commande `docker run`.
> 
>Si vous démarrez le conteneur avec un nom autre que `sfonebox`, mettez à jour la valeur du nom dans le fichier testclient.sh de votre application Java pour Service Fabric.
>

## <a name="next-steps"></a>Étapes suivantes
<!-- Links -->
* [Create and deploy your first Service Fabric Java application on Linux using Yeoman (Créer et déployer votre première application Java Service Fabric sur Linux à l’aide de Yeoman)](service-fabric-create-your-first-linux-application-with-java.md)
* [Créer et déployer votre première application Java Service Fabric sur Linux à l’aide du plug-in Service Fabric pour Eclipse](service-fabric-get-started-eclipse.md)
* [Création d’un cluster Service Fabric dans Azure à partir du portail Azure](service-fabric-cluster-creation-via-portal.md)
* [Créer un cluster Service Fabric à l’aide d’Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Modéliser une application dans Service Fabric](service-fabric-application-model.md)
* [Utilisez l’interface de ligne de commande Service Fabric pour gérer vos applications](service-fabric-application-lifecycle-sfctl.md)
* [Préparer un environnement de développement Linux sur Windows](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
