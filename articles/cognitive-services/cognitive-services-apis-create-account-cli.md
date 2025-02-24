---
title: Créer un compte Cognitive Services à l’aide d’Azure CLI
titlesuffix: Azure Cognitive Services
description: Création d’un compte d’API Cognitive Services à l’aide d’Azure CLI.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: acafc2c42c2946632496b646d001c58d6b48c2a6
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657707"
---
# <a name="create-a-cognitive-services-account-using-the-azure-command-line-interfacecli"></a>Créer un compte Cognitive Services à l’aide d’Azure CLI

Dans ce guide de démarrage rapide, vous allez apprendre à vous inscrire à Azure Cognitive Services et à créer un compte disposant d’un abonnement monoservice ou multiservice, à l’aide d’[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Ces services sont représentés par des [ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) Azure qui vous permettent de vous connecter à une ou plusieurs des API Cognitive Services.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure valide. [Créez un compte](https://azure.microsoft.com/free/) gratuitement.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>Installer Azure CLI et se connecter 

Installez [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Pour vous connecter à votre installation locale de l’interface CLI, exécutez la commande [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) :

```console
az login
```

Vous pouvez également utiliser le bouton vert **Essayer** pour exécuter ces commandes dans votre navigateur.
 
## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Créer un groupe de ressources Azure Cognitive Services

Vos abonnements à Cognitive Services sont représentés par des ressources Azure. Chaque compte Cognitive Services (et ses ressources Azure associées) doivent appartenir à un groupe de ressources Azure.

### <a name="choose-your-resource-group-location"></a>Choisir l’emplacement de votre groupe de ressources

Pour créer une ressource, il vous faut un emplacement Azure disponible pour votre abonnement. Vous pouvez récupérer une liste des emplacements disponibles avec la commande [az account list-locations](/cli/azure/account#az-account-list-locations). La plupart des Services Cognitive est accessible depuis plusieurs emplacements. Choisissez l’emplacement le plus proche de vous, ou consultez les emplacements disponibles pour le service.

> [!IMPORTANT]
> * Retenez votre emplacement Azure, car vous en aurez besoin lors de l’appel à Azure Cognitive Services.
> * La disponibilité de certains services Cognitive Services peut varier selon la région. Pour plus d’informations, consultez les [Produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).  

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Après avoir choisi un emplacement Azure, créez un groupe de ressources avec Azure CLI par le biais de la commande [az group create](/cli/azure/group#az-group-create).

Dans l’exemple ci-dessous, remplacez l’emplacement Azure `westus2` par l’un des emplacements disponibles pour votre abonnement.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Créer une ressource Cognitive Services

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Choisissez un service cognitif et son niveau tarifaire

Lorsque vous créez une nouvelle ressource, vous devez connaître le « type » du service que vous souhaitez utiliser, ainsi que le [niveau tarifaire](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou référence SKU) souhaité. Ces informations et d’autres vous serviront de paramètres lors de la création de la ressource.

> [!NOTE]
> De nombreux services cognitifs ont un niveau gratuit, que vous pouvez utiliser pour tester le service. Pour utiliser le niveau gratuit, utilisez `F0` en tant que référence SKU de votre ressource.

### <a name="vision"></a>Vision

| de diffusion en continu                    | Type                      |
|----------------------------|---------------------------|
| Vision par ordinateur            | `ComputerVision`          |
| Custom Vision - Prédiction | `CustomVision.Prediction` |
| Custom Vision - Formation   | `CustomVision.Training`   |
| API Visage                   | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

### <a name="search"></a>Recherche

| de diffusion en continu            | Type                  |
|--------------------|-----------------------|
| Suggestion automatique Bing   | `Bing.Autosuggest.v7` |
| Recherche personnalisée Bing | `Bing.CustomSearch`   |
| Recherche d’entité Bing | `Bing.EntitySearch`   |
| Bing Search        | `Bing.Search.v7`      |
| Vérification orthographique Bing   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Speech

| de diffusion en continu            | Type                 |
|--------------------|----------------------|
| Services Speech    | `SpeechServices`     |
| Reconnaissance vocale | `SpeakerRecognition` |

### <a name="language"></a>Langage

| de diffusion en continu            | Type                |
|--------------------|---------------------|
| Compréhension de formulaire | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Analyse de texte     | `TextAnalytics`     |
| Traduction de texte   | `TextTranslation`   |

### <a name="decision"></a>Décision

| de diffusion en continu           | Type               |
|-------------------|--------------------|
| Le détecteur d’anomalies  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |

Vous trouverez une liste des « types » de services cognitifs disponibles avec la commande [az cognitiveservices account list-kinds](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-list-kinds) :

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Ajouter une ressource à votre groupe de ressources

Pour créer une ressource Cognitive Services et s’y abonner, utilisez la commande [az cognitiveservices account create](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create). Cette commande ajoute une nouvelle ressource facturable au groupe de ressources créé précédemment. Lorsque vous créez votre nouvelle ressource, vous devez connaître le « type » du service que vous souhaitez utiliser, ainsi que son niveau tarifaire (ou référence SKU) et un emplacement Azure :

Vous pouvez créer une ressource F0 (gratuite) pour détecteur d’anomalies, nommé `anomaly-detector-resource` avec la commande ci-dessous.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

## <a name="get-the-keys-for-your-subscription"></a>Obtenir les clés pour votre abonnement

Pour vous connecter à votre installation locale de l’interface CLI, utilisez la commande [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login).

```console
az login
```

Utilisez la commande [az cognitiveservices account keys list](https://docs.microsoft.com/cli/azure/cognitiveservices/account/keys?view=azure-cli-latest#az-cognitiveservices-account-keys-list) pour obtenir les clés pour votre ressource Cognitive Service.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources liées au groupe de ressources.

Pour supprimer le groupe de ressources et les ressources associées, y compris le nouveau compte de stockage, utilisez la commande az group delete.

```azurecli-interactive
az group delete --name storage-resource-group
```

## <a name="see-also"></a>Voir aussi

* [Authentifier des requêtes auprès d’Azure Cognitive Services](authentication.md)
* [Qu’est-ce qu’Azure Cognitive Services ?](Welcome.md)
* [Prise en charge en langage naturel](language-support.md)
* [Prise en charge des conteneurs Docker](cognitive-services-container-support.md)
