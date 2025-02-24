---
title: Technologies de déploiement dans Azure Functions | Microsoft Docs
description: Découvrez les différentes façons de déployer du code sur Azure Functions.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 47d8bf33fd686942326db3b1cc606978bf47a1bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594394"
---
# <a name="deployment-technologies-in-azure-functions"></a>Technologies de déploiement dans Azure Functions

Vous pouvez utiliser plusieurs technologies différentes pour déployer le code de votre projet Azure Functions sur Azure. Cet article présente une liste exhaustive des technologies existantes, indique la disponibilité de ces technologies selon l’environnement d’exécution d’Azure Functions, explique le fonctionnement de chaque méthode et fournit des recommandations sur le choix de la meilleure méthode à utiliser dans divers scénarios. Les différents outils qui prennent en charge le déploiement sur Azure Functions sont adaptés à la technologie sous-jacente à leur contexte.

## <a name="deployment-technology-availability"></a>Disponibilité des technologies de déploiement

> [!IMPORTANT]
> Azure Functions prend en charge le développement local multiplateforme ainsi que l’hébergement sur Windows et Linux. Trois plans d’hébergement sont actuellement disponibles : [Consommation](functions-scale.md#consumption-plan), [Premium](functions-scale.md#premium-plan) et [Dédié (Azure App Service)](functions-scale.md#app-service-plan). Chaque plan a des comportements différents. Les technologies de déploiement ne sont pas toutes disponibles pour chaque environnement d’exécution d’Azure Functions.

| Technologie de déploiement | Consommation Windows | Windows Premium (préversion) | Dédié (Windows)  | Consommation Linux (préversion) | Dédié (Linux) |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| URL du package externe<sup>1</sup> |✔|✔|✔|✔|✔|
| Zip Deploy |✔|✔|✔| |✔|
| Conteneur Docker | | | | |✔|
| Web Deploy |✔|✔|✔| | |
| Contrôle de code source |✔|✔|✔| |✔|
| Git local<sup>1</sup> |✔|✔|✔| |✔|
| Synchronisation cloud<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| Modification dans le portail |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup> Cette technologie de déploiement nécessite une [synchronisation manuelle des déclencheurs](#trigger-syncing).  
<sup>2</sup> La modification dans le portail est disponible uniquement pour les déclencheurs HTTP et de minuteur quand Azure Functions est exécuté sur Linux dans le plan Dédié.

## <a name="key-concepts"></a>Concepts clés

Certains concepts clés sont essentiels pour comprendre le fonctionnement des déploiements dans Azure Functions.

### <a name="trigger-syncing"></a>Synchronisation des déclencheurs

Quand vous changez l’un de vos déclencheurs, l’infrastructure Functions doit en être informée. La synchronisation s’effectue automatiquement pour la plupart des technologies de déploiement. Toutefois, dans certains cas, vous devez synchroniser manuellement les déclencheurs. Quand vous déployez des mises à jour en référençant une URL de package externe, un Git local, une synchronisation cloud ou FTP, vous devez synchroniser manuellement les déclencheurs. Choisissez l’une de ces trois méthodes de synchronisation des déclencheurs :

* Redémarrez votre application de fonction dans le portail Azure.
* Envoyez une requête HTTP POST sur `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` en utilisant la [clé principale](functions-bindings-http-webhook.md#authorization-keys).
* Envoyez une requête HTTP POST sur `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Remplacez les espaces réservés par votre ID d’abonnement, le nom de votre groupe de ressources et le nom de votre application de fonction.

## <a name="deployment-technology-details"></a>Description des technologies de déploiement 

Les méthodes de déploiement décrites ci-après sont disponibles dans Azure Functions.

### <a name="external-package-url"></a>URL du package externe

Vous pouvez utiliser une URL de package externe pour référencer un fichier de package (.zip) distant qui contient votre application de fonction. Le fichier est téléchargé depuis l’URL fournie et l’application s’exécute en mode [Exécuter à partir du package](run-functions-from-deployment-package.md).

>__Comment l’utiliser ?__ Ajoutez `WEBSITE_RUN_FROM_PACKAGE` à vos paramètres d’application. La valeur de ce paramètre doit être une URL, correspondant à l’emplacement du fichier de package à exécuter. Vous pouvez ajouter des paramètres soit [dans le portail](functions-how-to-use-azure-function-app-settings.md#settings), soit [à l’aide d’Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Si vous employez le Stockage Blob Azure, utilisez un conteneur privé associé à une [signature d’accès partagé](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) pour permettre à Functions d’accéder au package. Chaque fois que l’application redémarre, elle extrait une copie du contenu. Votre référence doit être valide pendant la durée de vie de l’application.

>__Quand l’utiliser ?__ L’URL de package externe est la seule méthode de déploiement prise en charge quand Azure Functions est exécuté sur Linux dans le plan Consommation (préversion). Si vous mettez à jour le fichier de package référencé par une application de fonction, vous devez [synchroniser manuellement les déclencheurs](#trigger-syncing) pour indiquer à Azure que votre application a été modifiée.

### <a name="zip-deploy"></a>Zip Deploy

Utilisez Zip Deploy pour envoyer (push) un fichier .zip contenant votre application de fonction sur Azure. Vous pouvez éventuellement spécifier que votre application démarre en mode [Exécuter à partir du package](run-functions-from-deployment-package.md).

>__Comment l’utiliser ?__ Effectuez le déploiement à l’aide de votre outil client habituel : [VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure) ou [Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Pour déployer manuellement un fichier .zip sur votre application de fonction, suivez les instructions dans [Déployer à partir d’un fichier .zip ou d’une URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).
>
>Quand vous effectuez le déploiement à l’aide de Zip Deploy, vous pouvez définir votre application pour s’exécuter en mode [Exécuter à partir du package](run-functions-from-deployment-package.md). Pour définir le mode Exécuter à partir du package, affectez au paramètre d’application `WEBSITE_RUN_FROM_PACKAGE` la valeur `1`. Nous vous recommandons le déploiement zip. Il accélère les temps de chargement de vos applications, et il s’agit de la méthode par défaut pour VS Code, Visual Studio et Azure CLI.

>__Quand l’utiliser ?__ Zip Deploy est la technologie de déploiement recommandée quand Azure Functions est exécuté sur Windows, ou quand Azure Functions est exécuté sur Linux dans le plan Dédié.

### <a name="docker-container"></a>Conteneur Docker

Vous pouvez déployer une image conteneur Linux qui contient votre application de fonction.

>__Comment l’utiliser ?__ Créez une application de fonction Linux dans le plan Dédié et spécifiez l’image conteneur à partir de laquelle exécuter l’application. Il existe deux méthodes pour le faire :
>
>* Créer une application de fonction Linux dans un plan Azure App Service dans le portail Azure. Pour **Publier**, sélectionnez **Image Docker**, puis configurez le conteneur. Entrez l’emplacement d’hébergement de l’image.
>* Créer une application de fonction Linux dans un plan App Service par le biais d’Azure CLI. Pour savoir comment procéder, consultez [Créer une fonction sur Linux en utilisant une image personnalisée](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Pour effectuer le déploiement sur une application existante à l’aide d’un conteneur personnalisé, utilisez la commande [`func deploy`](functions-run-local.md#publish) dans [Azure Functions Core Tools](functions-run-local.md).

>__Quand l’utiliser ?__ Utilisez l’option Conteneur Docker si vous voulez contrôler davantage l’environnement Linux dans lequel s’exécute votre application de fonction. Cette technologie de déploiement est disponible uniquement quand Azure Functions est exécuté sur Linux dans un plan App Service.

### <a name="web-deploy-msdeploy"></a>Web Deploy (MSDeploy)

Web Deploy crée un package et déploie vos applications Windows sur n’importe quel serveur IIS, notamment vos applications de fonction qui s’exécutent sur Windows dans Azure.

>__Comment l’utiliser ?__ Utilisez [Visual Studio Tools pour Azure Functions](functions-create-your-first-function-visual-studio.md). Décochez la case **Exécuter à partir du fichier de package (recommandé)** .
>
>Vous pouvez aussi télécharger [Web Deploy 3.6](https://www.iis.net/downloads/microsoft/web-deploy) et appeler `MSDeploy.exe` directement.

>__Quand l’utiliser ?__ Web Deploy est pris en charge et ne pose aucun problème, mais la technologie privilégiée est [Zip Deploy avec l’option Exécuter à partir du package activée](#zip-deploy). Pour en savoir plus, consultez le [guide de développement Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Contrôle de code source

Utilisez le contrôle de code source pour connecter votre application de fonction à un dépôt git. Une mise à jour du code dans ce dépôt déclenche le déploiement. Pour plus d’informations, consultez le [Wiki Kudu](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Comment l’utiliser ?__ Utilisez le Centre de déploiement dans le portail Azure Functions pour configurer la publication à partir du contrôle de code source. Pour plus d’informations, consultez [Déploiement continu pour Azure Functions](functions-continuous-deployment.md).

>__Quand l’utiliser ?__ L’utilisation du contrôle de code source est la méthode recommandée pour les équipes qui travaillent en collaboration sur leurs applications de fonction. Il s’agit d’une bonne option si vous avez des pipelines de déploiement plus complexes.

### <a name="local-git"></a>Git local

Vous pouvez utiliser Git local pour envoyer (push) le code de votre ordinateur local vers Azure Functions à l’aide de Git.

>__Comment l’utiliser ?__ Suivez les instructions dans [Déploiement Git local vers Azure App Service](../app-service/deploy-local-git.md).

>__Quand l’utiliser ?__ En général, nous vous recommandons d’utiliser une méthode de déploiement différente. Si vous publiez à partir d’un Git local, vous devez [synchroniser manuellement les déclencheurs](#trigger-syncing).

### <a name="cloud-sync"></a>Synchronisation cloud

Utilisez la synchronisation cloud pour synchroniser votre contenu sur Dropbox et OneDrive avec Azure Functions.

>__Comment l’utiliser ?__ Suivez les instructions fournies dans [Synchroniser le contenu à partir d’un dossier cloud](../app-service/deploy-content-sync.md).

>__Quand l’utiliser ?__ En général, nous vous recommandons d’utiliser les autres méthodes de déploiement. Si vous publiez avec la synchronisation cloud, vous devez [synchroniser manuellement les déclencheurs](#trigger-syncing).

### <a name="ftp"></a>FTP

Vous pouvez utiliser FTP pour transférer les fichiers directement vers Azure Functions.

>__Comment l’utiliser ?__ Suivez les instructions dans [Déployer du contenu avec FTP/S](../app-service/deploy-ftp.md).

>__Quand l’utiliser ?__ En général, nous vous recommandons d’utiliser les autres méthodes de déploiement. Si vous publiez avec FTP, vous devez [synchroniser manuellement les déclencheurs](#trigger-syncing).

### <a name="portal-editing"></a>Modification dans le portail

Dans l’éditeur du portail, vous pouvez modifier directement les fichiers dans votre application de fonction (en effectuant le déploiement essentiellement dès que vous enregistrez vos modifications).

>__Comment l’utiliser ?__ Pour avoir la possibilité de modifier vos fonctions dans le portail Azure, vous devez avoir [créé les fonctions dans le portail](functions-create-first-azure-function.md). Pour garantir l’existence d’une seule source de confiance, l’utilisation d’une autre méthode de déploiement rend votre fonction accessible en lecture seule et empêche la poursuite de la modification dans le portail. Pour revenir à un état où vous pouvez modifier vos fichiers dans le portail Azure, vous pouvez rétablir manuellement le mode d’édition à `Read/Write` et supprimer tous les paramètres d’application relatifs au déploiement (comme `WEBSITE_RUN_FROM_PACKAGE`). 

>__Quand l’utiliser ?__ Le portail est un excellent moyen de vous familiariser avec Azure Functions. Toutefois, pour un travail de développement plus complexe, il est recommandé d’utiliser les outils clients suivants :
>
>* [Bien démarrer avec VS Code](functions-create-first-function-vs-code.md)
>* [Bien démarrer avec Azure Functions Core Tools](functions-run-local.md)
>* [Bien démarrer avec Visual Studio](functions-create-your-first-function-visual-studio.md)

Le tableau suivant présente les systèmes d’exploitation et les langages qui prennent en charge la modification dans le portail :

| | Consommation Windows | Windows Premium (préversion) | Dédié (Windows) | Consommation Linux (préversion) | Dédié (Linux) |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| C# | | | | | |
| Script C# |✔|✔|✔| |✔<sup>*</sup>|
| F# | | | | | |
| Java | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>*</sup>|
| Python (préversion) | | | | | |
| PowerShell (préversion) |✔|✔|✔| | |
| TypeScript (Node.js) | | | | | |

<sup>*</sup> La modification dans le portail est disponible uniquement pour les déclencheurs HTTP et de minuteur quand Azure Functions est exécuté sur Linux dans le plan Dédié.

## <a name="deployment-slots"></a>Emplacements de déploiement

Quand vous déployez votre application de fonction sur Azure, vous pouvez choisir un autre emplacement de déploiement que directement l’emplacement de production. Pour plus d’informations sur les emplacements de déploiement, consultez [Emplacements Azure App Service](../app-service/deploy-staging-slots.md).

### <a name="deployment-slots-levels-of-support"></a>Niveaux de prise en charge des emplacements de déploiement

Il existe deux niveaux de prise en charge des emplacements de déploiement :

* **Disponibilité générale** : entièrement pris en charge et approuvé pour la production.
* **Préversion** : pas encore pris en charge, mais le statut de disponibilité générale est prévu à l’avenir.

| Plan d’hébergement/système d’exploitation | Niveau de prise en charge |
| --------------- | ------ |
| Consommation Windows | PRÉVERSION |
| Windows Premium (préversion) | PRÉVERSION |
| Dédié (Windows) | Disponibilité générale |
| Consommation Linux | Non pris en charge |
| Dédié (Linux) | Disponibilité générale |

## <a name="next-steps"></a>Étapes suivantes

Lisez les articles suivants pour en savoir plus sur le déploiement de vos applications de fonction : 

+ [Déploiement continu pour Azure Functions](functions-continuous-deployment.md)
+ [Livraison continue à l’aide d’Azure DevOps](functions-how-to-azure-devops.md)
+ [Déploiements zip pour Azure Functions](deployment-zip-push.md)
+ [Exécuter des fonctions Azure à partir d’un fichier de package](run-functions-from-deployment-package.md)
+ [Automatiser le déploiement de ressources pour votre application de fonction dans Azure Functions](functions-infrastructure-as-code.md)
