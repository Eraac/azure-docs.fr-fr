---
title: Authentification avec un registre de conteneurs Azure
description: Options d’authentification pour un registre de conteneurs Azure, y compris la connexion auprès d’une identité Azure Active Directory, au moyen de principaux de service et à l’aide d’informations d’identification d’administrateur facultatives.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 82fe80e098ee95c09c4a1400068ab813910e0e1a
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309829"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>S’authentifier avec un registre de conteneurs Docker

Plusieurs méthodes permettent de s’authentifier avec un registre de conteneurs Azure, chacune d’elles étant applicable à un ou plusieurs scénarios d’usage du registre.

Vous pouvez vous connecter directement à un registre par le biais d’une [connexion individuelle](#individual-login-with-azure-ad), sinon vos applications et orchestrateurs de conteneurs peuvent procéder à une authentification sans assistance, ou « headless » (administrée à distance), à l’aide d’un [principal de service](#service-principal) Azure Active Directory (Azure AD).

## <a name="individual-login-with-azure-ad"></a>Connexion individuelle avec Azure AD

Si vous utilisez directement votre registre, par exemple si vous extrayez des images et en envoyez depuis et vers une station de travail de développement, authentifiez-vous à l’aide de la commande [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) dans [Azure CLI](/cli/azure/install-azure-cli) :

```azurecli
az acr login --name <acrName>
```

Si vous vous connectez avec `az acr login`, l’interface CLI utilise le jeton créé lorsque vous avez exécuté [az login](/cli/azure/reference-index#az-login) pour authentifier en toute transparence votre session avec votre registre. Lorsque vous vous connectez à l'aide de cette méthode, vos informations d'identification sont mises en cache, et les commandes `docker` suivantes de votre session ne nécessitent ni nom d'utilisateur ni mot de passe. 

Pour l'accès au registre, le jeton utilisé par `az acr login` est valable 1 heure. Nous vous recommandons donc de toujours vous connecter au registre avant d'exécuter une commande `docker`. Si votre jeton arrive à expiration, vous pouvez l’actualiser en utilisant de nouveau la commande `az acr login` pour la réauthentification. 

L’utilisation de `az acr login` avec des identités Azure fournit un [accès en fonction du rôle](../role-based-access-control/role-assignments-portal.md). Pour certains scénarios, vous serez peut-être amené à vous connecter à un registre avec votre identité individuelle dans Azure AD. Pour les scénarios entre les services ou pour gérer les besoins d’un groupe de travail où vous ne souhaitez pas gérer l’accès individuel, vous pouvez également vous connecter avec un [identité managée pour les ressources Azure](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Principal du service

Si vous affectez un [principal du service](../active-directory/develop/app-objects-and-service-principals.md) à votre registre, votre application ou service peut l’utiliser pour une authentification headless (administrée à distance). Les principaux du service autorisent les [accès en fonction du rôle](../role-based-access-control/role-assignments-portal.md) à un registre, et vous pouvez affecter plusieurs principaux du service à un registre. Plusieurs principaux du service vous permettent de définir différents accès pour plusieurs applications.

Les rôles disponibles pour un registre de conteneurs sont les suivants :

* **AcrPull** : extraction

* **AcrPush**: extraction (pull) et envoi (push)

* **Propriétaire** : extraction, envoi et attribution de rôles à d’autres utilisateurs

Pour obtenir la liste complète des rôles, consultez [Autorisations et rôles Azure Container Registry](container-registry-roles.md).

Pour que les scripts CLI créent un ID d’application de principal de service avec le mot de passe associé et s’authentifient auprès d’un registre de conteneurs Azure, ou pour qu’ils utilisent un principal de service existant, consultez [Authentification Azure Container Registry avec des principaux de service](container-registry-auth-service-principal.md).

Les principaux de service activent une connectivité headless (administrée à distance) à un registre dans les scénarios d’extraction et d’envoi suivants :

  * *Pull* : déployer des conteneurs à partir d’un registre vers des systèmes d’orchestration, y compris Kubernetes, DC/OS et Docker Swarm. Vous pouvez également procéder à des extractions depuis les registres de conteneurs vers des services Azure connexes, tels que [Azure Kubernetes Service](container-registry-auth-aks.md), [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/), etc.

  * *Push* : générer des images conteneur et les envoyer (push) à un registre en utilisant des solutions d’intégration et de déploiement en continu, comme Azure Pipelines ou Jenkins.

Vous pouvez également vous connecter directement avec un principal du service. Lorsque vous exécutez la commande suivante, fournissez de manière interactive l'appID (nom d'utilisateur) et le mot de passe du principal du service dès que vous y êtes invité. Pour connaître les meilleures pratiques de gestion des informations d'identification, consultez la référence de la commande [docker login](https://docs.docker.com/engine/reference/commandline/login/) :

```
docker login myregistry.azurecr.io
```

Une fois que vous êtes connecté, Docker met les informations d’identification en cache. Vous n’avez donc pas besoin de vous souvenir de l’ID d’application.

> [!TIP]
> Vous pouvez régénérer le mot de passe d’un principal du service en exécutant la commande [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest).
>

## <a name="admin-account"></a>Compte d’administrateur

Chaque registre de conteneurs inclut un compte d’utilisateur administrateur, qui est désactivé par défaut. Vous pouvez activer le compte d'utilisateur administrateur et gérer ses informations d'identification à partir du portail Azure, ou par le biais de l'interface Azure CLI ou d'autres outils Azure.

> [!IMPORTANT]
> Le compte d’administrateur est conçu pour permettre à un seul utilisateur d’accéder au registre, principalement à des fins de test. Nous vous recommandons de partager les informations d’identification du compte d’administrateur avec plusieurs utilisateurs. Tous les utilisateurs qui s’authentifient avec le compte d’administrateur apparaissent sous la forme d’un seul utilisateur avec un accès par envoi et par extraction au registre. La modification ou la désactivation de ce compte désactive l’accès au registre pour tous les utilisateurs qui utilisent ces informations d’identification. Une identité individuelle est recommandée pour les utilisateurs et principaux du service pour les scénarios sans périphérique de contrôle.
>

Le compte d’administrateur reçoit deux mots de passe qui peuvent être régénérés. Deux mots de passe vous permettent de maintenir la connexion au registre en utilisant un mot de passe tandis que vous régénérez l’autre. Si le compte d'administrateur est activé, vous pouvez transmettre le nom d'utilisateur et l'un ou l'autre des mots de passe à la commande `docker login` lorsque vous y êtes invité pour une authentification de base auprès du registre. Par exemple :

```
docker login myregistry.azurecr.io 
```


Pour activer l’utilisateur administrateur pour un registre existant, vous pouvez utiliser le paramètre `--admin-enabled` de la commande [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) dans Azure CLI :

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Pour activer l’utilisateur administrateur dans le portail Azure, accédez au registre, sélectionnez **Clés d’accès** sous **PARAMÈTRES**, puis **Activer** sous **Utilisateur administrateur**.

![Activer l’IU de l’utilisateur administrateur dans le portail Azure][auth-portal-01]

## <a name="next-steps"></a>Étapes suivantes

* [Push your first image using the Azure CLI (Transmettre votre première image à l’aide d’Azure CLI)](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
