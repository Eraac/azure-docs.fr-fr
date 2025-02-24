---
title: Support pour les conteneurs
titleSuffix: Azure Cognitive Services
description: Découvrez comment les conteneurs Docker peuvent faire bénéficier vos données de Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: d9f226213215f66b53eb1ef248fd47f7b6dfee5a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705376"
---
# <a name="container-support-in-azure-cognitive-services"></a>Prise en charge des conteneurs dans Azure Cognitive Services

La prise en charge des conteneurs dans Azure Cognitive Services permet aux développeurs d’utiliser les mêmes API riches que celles disponibles dans Azure, et permet une flexibilité sur l’emplacement de déploiement et de stockage des services fournis avec les [conteneurs Docker](https://www.docker.com/what-container). La prise en charge des conteneurs est désormais disponible en préversion pour quelques solutions Azure Cognitive Services, dont des composants de :

* [Détecteur d’anomalies][ad-containers]
* [Vision par ordinateur][cv-containers]
* [Visage][fa-containers]
* [Form Recognizer][fr-containers]
* [Language Understanding (LUIS)][lu-containers]
* [API Speech Service][sp-containers]
* [Analyse de texte][ta-containers]

<!--
* [Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
-->

La mise en conteneur est une méthode de distribution de logiciels dans laquelle une application ou un service, y compris les dépendances et la configuration, est packagé en tant qu’image conteneur. L’image conteneur peut être déployée sur un hôte conteneur avec peu ou pas de modifications. Les conteneurs sont isolés les uns des autres et l’encombrement du système d’exploitation sous-jacent est inférieur à celui d’une machine virtuelle. Vous pouvez instancier des conteneurs à partir d’images conteneurs pour les tâches à court terme et les supprimer quand vous n’en avez plus besoin.

La vidéo suivante montre l’utilisation d’un conteneur Cognitive Services.

[![Démonstration d’un conteneur pour Cognitive Services](./media/index/containers-video-image.png)](https://azure.microsoft.com/resources/videos/containers-support-of-cognitive-services)

Les ressources Cognitive Services sont disponibles sur [Microsoft Azure](https://azure.microsoft.com). Connectez-vous au [portail Azure](https://portal.azure.com/) pour créer et explorer les ressources Azure pour ces services.

## <a name="features-and-benefits"></a>Fonctionnalités et avantages

- **Contrôle des données** : permettre aux clients de choisir où ces services Cognitive Services traitent leurs données. Ceci est essentiel pour les clients qui ne peut pas envoyer les données dans le cloud mais ont besoin d’accéder à la technologie de Cognitive Services. Cohérence de la prise en charge dans les environnements hybrides : entre les données, la gestion, l’identité et la sécurité.
- **Contrôle des mises à jour du modèle** : Fournir aux clients une flexibilité dans le contrôle de version et la mise à jour des modèles déployés dans leurs solutions.
- **Architecture portable** : Permettre la création d’une architecture d’application portable qui peut être déployée dans le cloud, localement et en périphérie. Les conteneurs peuvent être déployés directement sur [Azure Kubernetes Service](../aks/index.yml), sur [Azure Container Instances](../container-instances/index.yml) ou sur un cluster [Kubernetes](https://kubernetes.io/) déployé sur [Azure Stack](/azure-stack/operator). Pour plus d’informations, consultez [Déployer Kubernetes sur Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Débit élevé / faible latence** : fournir aux clients la possibilité de mettre à l’échelle pour des exigences de débit élevé et de faible latence en permettant l’exécution de Cognitive Services physiquement proche de leur logique d’application et de leurs données. Les conteneurs ne limitent pas les transactions par seconde (TPS) et peuvent être configurés pour effectuer un scale-up ou un scale-out afin de gérer la demande si vous fournissez les ressources matérielles nécessaires. 

## <a name="containers-in-azure-cognitive-services"></a>Conteneurs dans Azure Cognitive Services

Les conteneurs Azure Cognitive Services fournissent l’ensemble suivant de conteneurs Docker, chacun contenant un sous-ensemble de fonctionnalités à partir de services dans Azure Cognitive Services :

| de diffusion en continu | Niveau tarifaire pris en charge | Conteneur | Description |
|---------|----------|----------|-------------|
|[Détecteur d’anomalies][ad-containers] |F0, S0|**Détecteur d’anomalies** |L’API Détecteur d’anomalies vous permet de surveiller et de détecter des anomalies dans vos données de série chronologique grâce à l’apprentissage automatique.<br>[Demander l’accès](https://aka.ms/adcontainer)|
|[Vision par ordinateur][cv-containers] |F0, S1|**Reconnaître le texte** |Extraire un texte imprimé à partir d’images d’objets divers avec différents arrière-plans et surfaces, tels que des reçus, des affiches et des cartes de visite.<br/><br/>**Important :** Le conteneur Reconnaître le texte ne fonctionne qu’en anglais pour le moment.<br>[Demander l’accès](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Visage][fa-containers] |F0, S0|**Visage** |Détecter les visages humains sur des images et identifier les attributs, y compris les caractéristiques (par exemple, le nez et les yeux), le sexe, l’âge et d’autres caractéristiques du visage prévues par la machine. En plus de la détection, le conteneur Visage peut vérifier si deux visages sur la même image ou sur des images différentes sont identiques à l’aide d’un score de confiance, ou de comparer des visages par rapport à une base de données pour voir si un visage similaire ou identique existe déjà. Il peut également regrouper des visages similaires à l’aide de caractéristiques visuelles partagées.<br>[Demander l’accès](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Form Recognizer][fr-containers] |F0, S0|**Form Recognizer** |Form Understanding applique la technologie de Machine Learning pour identifier et extraire des tables et des paires clé-valeur à partir de formulaires.<br>[Demander l’accès](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**LUIS** ([image](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Charge un modèle Language Understanding entraîné ou publié, également connu sous le nom d’application LUIS, dans un conteneur docker et fournit l’accès aux prédictions de requête à partir des points de terminaison d’API du conteneur. Vous pouvez collecter les journaux d’activité de requêtes du conteneur et les charger sur le [portail LUIS](https://www.luis.ai) pour améliorer la précision de prédiction de l’application.|
|[API Speech Service][sp-containers] |F0, S0|**Reconnaissance vocale** |Retranscrit une voix en un texte de façon continue et en temps réel.<br>[Demander l’accès](https://aka.ms/speechcontainerspreview/)|
|[API Speech Service][sp-containers] |F0, S0|**Synthèse vocale** |Convertit le texte en paroles naturelles.<br>[Demander l’accès](https://aka.ms/speechcontainerspreview/)|
|[Analyse de texte][ta-containers] |F0, S|**Extraction de phrases clés** ([image](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Extraire des expressions clés pour identifier les points principaux. Par exemple, pour le texte d’entrée « Le repas était délicieux et le personnel adorable », l’API renvoie les principaux points de discussion : « repas » et « personnel adorable ». |
|[Analyse de texte][ta-containers]|F0, S|**Détection de la langue** ([image](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Détecter la langue (parmi 120 langues maximum) dans laquelle le texte d’entrée est rédigé et générer un code de langue unique pour chaque document envoyé dans la demande. Le code de langue est associé à un score indiquant la puissance de l’analyse. |
|[Analyse de texte][ta-containers]|F0, S|**Analyse des sentiments** ([image](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analyser le texte brut pour obtenir des indices sur un sentiment positif ou négatif. Cette API renvoie un score de sentiment de 0 à 1 pour chaque document, où 1 correspond au sentiment le plus positif. Les modèles d’analyse sont préformés à l’aide d’un corps complet de technologies de texte et de Langage naturel de Microsoft. Pour les [langues sélectionnées](./text-analytics/language-support.md), l’API peut analyser tout texte brut que vous fournissez, lui attribuer un score, et renvoyer directement les résultats à l’application appelante. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Certains conteneurs sont également pris en charge dans des clés de ressources Cognitive Services d’[**offre tout-en-un**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne). Vous pouvez créer une ressource Cognitive Services tout-en-un et utiliser la même clé de facturation entre les services pris en charge pour les services suivants :

* Vision par ordinateur
* Face
* LUIS
* Analyse de texte

## <a name="container-availability-in-azure-cognitive-services"></a>Disponibilité des conteneurs dans Azure Cognitive Services

Les conteneurs Azure Cognitive Services sont accessibles publiquement via votre abonnement Azure, et les images conteneur Docker peuvent être extraites à partir de Microsoft Container Registry ou de Docker Hub. Vous pouvez utiliser la commande [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur à partir du référentiel approprié.

> [!IMPORTANT]
> Actuellement, vous devez effectuer un processus d’inscription pour accéder aux conteneurs suivants, dans laquelle vous remplissez et soumettez un questionnaire comportant des questions sur vous, votre entreprise et le cas d’utilisation pour lequel vous souhaitez implémenter les conteneurs. Une fois que l’accès et des informations d’identification vous sont fournis, vous pouvez alors extraire les images conteneur à partir d’un Registre de conteneurs privé hébergé par Azure Container Registry.
> * [Détecteur d’anomalies](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Visage](Face/face-how-to-install-containers.md)
> * [Form Recognizer](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Reconnaître le texte](Computer-vision/computer-vision-how-to-install-containers.md)
> * [Reconnaissance vocale et Synthèse vocale](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Prérequis

Vous devez respecter les prérequis suivants avant d’utiliser des conteneurs Azure Cognitive Services :

**Moteur Docker** : le moteur Docker doit être installé localement. Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) et [Windows](https://docs.docker.com/docker-for-windows/). Sur Windows, Docker doit être configuré pour prendre en charge les conteneurs Linux. Les conteneurs Docker peuvent également être déployés directement sur [Azure Kubernetes Service](../aks/index.yml) ou sur [Azure Container Instances](../container-instances/index.yml).

Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation.

**Connaissance de Microsoft Container Registry et de Docker** : vous devez avoir une compréhension élémentaire des concepts de Microsoft Container Registry et de Docker, notamment les registres, dépôts, conteneurs et images conteneur, ainsi qu’une maîtrise des commandes `docker` de base.

Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).

Les conteneurs individuels peuvent aussi avoir leurs propres exigences, notamment sur le serveur et l’allocation de mémoire.

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [recettes de conteneur](containers/container-reuse-recipe.md) que vous pouvez utiliser avec Cognitive Services.

Installer et explorer les fonctionnalités fournies par les conteneurs dans Azure Cognitive Services :

* [Conteneurs Détecteur d’anomalies][ad-containers]
* [Conteneurs Vision par ordinateur][cv-containers]
* [Conteneurs Visage][fa-containers]
* [Conteneurs Form Recognizer][fr-containers]
* [Conteneurs Language Understanding (LUIS)][lu-containers]
* [Conteneurs API Speech Service][sp-containers]
* [Conteneurs Analyse de texte][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->


[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md