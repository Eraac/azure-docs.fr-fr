---
title: Fonctionnement de Visual Studio Code avec Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: Fonctionnement de Visual Studio Code avec Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
ms.openlocfilehash: 0d80643b366b6d7313f24e73258056e492eb56fc
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297870"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Fonctionnement de Visual Studio Code avec Azure Dev Spaces

Vous pouvez utiliser Visual Studio Code et l’[extension Azure Dev Spaces][azds-extension] pour préparer, exécuter et déboguer vos services avec Azure Dev Spaces. Avec Visual Studio Code et l’extension Azure Dev Spaces, vous pouvez :

* Générez des ressources pour l’exécution et le débogage de services dans AKS
* Exécutez vos services Java, Node.js et .NET Core dans un espace de développement
* Déboguez directement vos services Java, Node.js et .NET Core en cours d’exécution dans un espace de développement

## <a name="generate-assets"></a>Générer des ressources

Visual Studio Code et l’extension Azure Dev Spaces génèrent les ressources suivantes pour votre projet :

* Dockerfile pour les applications Java à l’aide de Maven, des applications Node.js et des applications .NET Core
* Graphiques Helm pour presque n’importe quel langage avec un Dockerfile
* Un fichier `azds.yaml`, c’est-à-dire le [fichier de configuration Azure Dev Spaces][azds-yaml] pour votre projet
* Un `.vscode` dossier avec la configuration de lancement de Visual Studio Code de votre projet pour les applications Java à l’aide de Maven, des applications Node.js et des applications .NET Core

Le Dockerfile, le graphique Helm, et les fichiers `azds.yaml` sont les mêmes ressources générées pendant l’exécution de `azds prep`. Ces fichiers peuvent également être utilisés en dehors du code de Visual Studio pour exécuter votre projet dans AKS, telles que l’exécution de `azds up`. Le dossier `.vscode` est utilisé uniquement par le code de Visual Studio pour exécuter votre projet dans AKS à partir de Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Exécutez votre service dans AKS

Une fois que vous générez les ressources pour votre projet, vous pouvez exécuter vos services Java, Node.js et .NET Core dans un espace de développement existant à partir de Visual Studio Code. Dans la page *Déboguer* de Visual Studio Code, vous pouvez appeler la configuration de lancement à partir du répertoire `.vscode` pour exécuter votre projet.

Vous devez créer votre cluster AKS et activer Azure Dev Spaces dans votre cluster en dehors de Visual Studio Code. Par exemple, vous pouvez utiliser Azure CLI ou le portail Azure pour effectuer cette configuration. Vous pouvez réutiliser les Dockerfiles existant, les graphiques Helm, et les fichiers `azds.yaml` créés en dehors de Visual Studio Code, tels que les ressources générées en exécutant `azds prep`. Si vous réutilisez les ressources générées en dehors de Visual Studio Code, vous devez toujours avoir un répertoire `.vscode`. Ce répertoire `.vscode` peut être régénérée par Visual Studio code et l’extension Azure Dev Spaces et ne remplacera pas vos ressources existantes.

Pour les projets .NET Core, vous devez avoir l’[extension C#][csharp-extension] installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension] installée ainsi que [Maven installé et configuré][maven] pour exécuter votre service Java à partir de Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Déboguez votre service dans AKS

Une fois que vous lancez votre projet, vous pouvez déboguer vos services Java, Node.js et .NET Core en cours d’exécution dans un espace de développement directement à partir de Visual Studio Code. La configuration de lancement dans le répertoire `.vscode` fournit les informations de débogage supplémentaires pour un service en cours d’exécution avec débogage activé dans un espace de développement. Visual Studio Code se lie également au processus de débogage dans le conteneur en cours d’exécution dans vos espaces de développement, ce qui vous permet de définir des points d’arrêt, inspecter des variables et effectuer d’autres opérations de débogage.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Utilisez Visual Studio Code avec Azure Dev Spaces

Vous pouvez voir que Visual Studio Code et l’extension Azure Dev Spaces fonctionne avec Azure Dev Spaces dans les démarrages rapides suivants :

* [Développer avec Java][quickstart-java]
* [Développer avec .NET][quickstart-netcore]
* [Développer avec Node.js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
