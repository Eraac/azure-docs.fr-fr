---
title: 'Tutoriel : Utiliser Azure Key Vault avec une machine virtuelle Windows dans Python | Microsoft Docs'
description: Dans ce tutoriel, vous allez configurer une application ASP.NET Core pour lire un secret dans votre coffre de clés.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: cdc540f2f6fa834a97c4c405276414f29672e5c7
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876667"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Didacticiel : Utiliser Azure Key Vault avec une machine virtuelle Windows dans Python

Azure Key Vault vous permet de protéger des secrets tels que les clés API et les chaînes de connexion de base de données nécessaires pour accéder à vos applications, services et ressources.

Dans ce tutoriel, vous allez vous procurer une application console pour lire des informations d’Azure Key Vault. Pour ce faire, vous allez utiliser des identités managées pour des ressources Azure. 

Ce didacticiel explique les procédures suivantes :

> [!div class="checklist"]
> * Création d’un coffre de clés
> * Ajoutez un secret au coffre de clés.
> * Récupération d’un secret à partir du coffre de clés.
> * Créez une machine virtuelle Azure.
> * Activez une identité managée.
> * Attribuez des autorisations à l’identité de machine virtuelle.

Avant de continuer, lisez les [concepts de base de Key Vault](key-vault-whatis.md#basic-concepts). 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Prérequis

Pour Windows, Mac et Linux :
  * [Git](https://git-scm.com/downloads)
  * Ce didacticiel nécessite que vous exécutiez l’interface Azure CLI localement. Vous devez avoir installé Azure CLI 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau l’interface CLI, consultez l’article [Installation d’Azure CLI 2.0](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>À propos de Managed Service Identity (MSI)

Azure Key Vault stocke les informations d’identification de manière sécurisée, de façon à ce qu’elles n’apparaissent pas dans votre code. Toutefois, vous devez vous authentifier auprès d’Azure Key Vault pour récupérer vos clés. Pour vous authentifier auprès de Key Vault, vous avez besoin d’informations d’identification. Il s’agit d’un dilemme de démarrage classique. Managed Service Identity (MSI) résout ce problème en fournissant une _identité de démarrage_.

Lorsque vous activez MSI pour un service Azure, par exemple, Machines virtuelles Azure, Azure App Service ou Azure Functions, Azure crée un [principal de service](key-vault-whatis.md#basic-concepts). MSI procède ainsi pour l’instance du service dans Azure Active Directory (Azure AD) et injecte les informations d’identification du principal de service dans cette instance. 

![MSI](media/MSI.png)

Ensuite, pour obtenir un jeton d’accès, votre code appelle un service de métadonnées local disponible dans la ressource Azure. Pour s’authentifier auprès d’un service Azure Key Vault, votre code utilise alors le jeton qu’il obtient du point de terminaison local MSI_ENDPOINT. 

## <a name="log-in-to-azure"></a>Connexion à Azure

Pour vous connecter à Azure à l’aide de l’interface CLI, entrez :

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). 

Sélectionnez un nom de groupe de ressources et renseignez l’espace réservé. L’exemple suivant crée un groupe de ressources dans l’emplacement USA Ouest :

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Vous allez utiliser ce nouveau groupe de ressources tout au long du tutoriel.

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

Pour créer un coffre de clés dans le groupe de ressources que vous avez créé à l’étape précédente, fournissez les informations suivantes :

* Nom du coffre de clés : chaîne de 3 à 24 caractères qui ne peut contenir que des chiffres (0-9), des lettres (a-z, A-Z) et des traits d’union (-).
* Nom de groupe ressources
* Localisation : **USA Ouest**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

## <a name="add-a-secret-to-the-key-vault"></a>Ajouter un secret au coffre de clés

Nous allons ajouter un secret pour montrer comment cela fonctionne. Il peut s’agir d’une chaîne de connexion SQL ou de toute autre information que vous devez sécuriser et garder à disposition pour votre application.

Pour créer un secret dans le coffre de clés appelé **AppSecret**, tapez la commande suivante :

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ce secret stocke la valeur **MySecret**.

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle
Vous pouvez créer une machine virtuelle à l’aide de l’une des méthodes suivantes :

* [L’interface de ligne de commande Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli)
* [PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)
* [Le portail Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-an-identity-to-the-vm"></a>Affecter une identité à la machine virtuelle
Dans cette étape, vous créez une identité affectée par le système pour la machine virtuelle en exécutant la commande suivante dans Azure CLI :

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Notez l’identité affectée par le système qui est affichée dans le code suivant. La sortie de la commande ci-dessus doit être la suivante : 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Attribuer des autorisations à l’identité de machine virtuelle
À présent, vous pouvez attribuer à votre coffre de clés les autorisations d’identité créées précédemment en exécutant la commande suivante :

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Connexion à la machine virtuelle

Pour vous connecter à la machine virtuelle, suivez les instructions de l’article [Se connecter à une machine virtuelle Azure exécutant Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="create-and-run-a-sample-python-app"></a>Créer et exécuter l’exemple d’application Python

La section suivante inclut un exemple de fichier nommé *Sample.py*. Il utilise la bibliothèque [requests](http://docs.python-requests.org/en/master/) pour effectuer des appels HTTP GET.

## <a name="edit-samplepy"></a>Modifier Sample.py

Après avoir créé *Sample.py*, ouvrez le fichier et copiez le code dans cette section. 

Le code comprend deux étapes :
1. Récupérer un jeton du point de terminaison MSI local sur la machine virtuelle.  
  Cela extrait également un jeton d’Azure AD.
1. Passez le jeton au coffre de clés et récupérez votre secret. 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Vous pouvez afficher la valeur de secret en exécutant le code suivant : 

```
python Sample.py
```

Le code précédent montre comment effectuer des opérations avec Azure Key Vault dans une machine virtuelle Windows. 

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand ils ne sont plus nécessaires, supprimez la machine virtuelle et le coffre de clés.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [API REST Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
