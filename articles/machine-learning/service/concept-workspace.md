---
title: Qu’est-ce qu’un espace de travail ?
titleSuffix: Azure Machine Learning service
description: L’espace de travail est la ressource de niveau supérieur du service Azure Machine Learning. Il conserve un historique de toutes les exécutions d’entraînement, y compris les journaux d’activité, les métriques, les sorties et un instantané de vos scripts. Vous utilisez ces informations pour déterminer l’exécution d’entraînement produisant le meilleur modèle.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 912c064fb5ca4e7ca311f60ed04a0122809cb0ff
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442369"
---
# <a name="what-is-an-azure-machine-learning-service-workspace"></a>Qu’est-ce qu’un espace de travail Azure Machine Learning service ?

L’espace de travail est la ressource de niveau supérieur pour Azure Machine Learning service. Il fournit un emplacement centralisé dans lequel exploiter tous les artefacts que vous créez lorsque vous utilisez Azure Machine Learning service.  L’espace de travail conserve un historique de toutes les exécutions d’entraînement, y compris les journaux d’activité, les métriques, les sorties et un instantané de vos scripts. Vous utilisez ces informations pour déterminer quelle exécution d’entraînement produit le meilleur modèle.  

Une fois que vous disposez d’un modèle qui vous convient, inscrivez-le avec l’espace de travail. Ainsi, grâce au modèle inscrit et aux scripts de scoring, vous pouvez déployer sur Azure Container Instances, Azure Kubernetes Service ou sur un tableau FPGA (field programmable gate array) comme point de terminaison HTTP basé sur REST. Vous pouvez également déployer le modèle en tant que module sur un appareil Azure IoT Edge.

## <a name="taxonomy"></a>Taxonomie 

Le diagramme suivant représente une taxonomie de l’espace de travail :

[![Taxonomie de l’espace de travail](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Le diagramme montre les composants d’un espace de travail suivants :

+ Un espace de travail peut contenir des [machines virtuelles de notebook](quickstart-run-cloud-notebook.md), des ressources cloud configurées avec l’environnement Python nécessaire pour exécuter Azure Machine Learning.
+ Les [rôles d’utilisateur](how-to-assign-roles.md) vous permettent de partager votre espace de travail avec d’autres utilisateurs, équipes ou projets.
+ Les [cibles de calcul](concept-azure-machine-learning-architecture.md#compute-targets) vous permettent d’exécuter vos expériences.
+ Lorsque vous créez l’espace de travail, les [ressources associées](#resources) sont également créées à votre place.
+ Les [expériences](concept-azure-machine-learning-architecture.md#experiments) sont des exécutions d’entraînement qui vous permettent de créer vos modèles.  Vous pouvez créer et exécuter des expériences avec
    + Le [kit de développement logiciel (SDK) Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + La section relative aux [expériences Machine Learning automatisées (préversion)](how-to-create-portal-experiments.md) dans le portail Azure.
    + L’[interface visuelle (préversion)](ui-concept-visual-interface.md).
+ Les [pipelines](concept-azure-machine-learning-architecture.md#ml-pipelines) sont des flux de travail réutilisables destinés à l’entraînement et la reformation de votre modèle.
+ Les [jeux de données](concept-azure-machine-learning-architecture.md#datasets-and-datastores) facilitent la gestion des données que vous utilisez pour l’entraînement des modèles et la création de pipelines.
+ Une fois que vous avez un modèle que vous souhaitez déployer, vous créez un modèle inscrit.
+ Utilisez le modèle inscrit et un script de scoring pour créer un [déploiement](concept-azure-machine-learning-architecture.md#deployment).

## <a name="tools-for-workspace-interaction"></a>Outils pour l’interaction avec l’espace de travail

Vous pouvez interagir avec votre espace de travail comme suit :

+ Sur le web :
    + [Portail Azure](https://portal.azure.com)
    + [Interface visuelle (préversion)](ui-concept-visual-interface.md)
+ Dans Python à l’aide du [kit de développement logiciel (SDK)](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) Azure Machine Learning
+ Sur la ligne de commande avec l’[extension CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli) Azure Machine Learning :

## <a name="machine-learning-with-a-workspace"></a>Machine Learning avec un espace de travail

Les tâches de Machine Learning lisent et/ou écrivent des artefacts sur votre espace de travail. 

+ Exécuter une expérience pour entraîner un modèle : écrit les résultats de l’exécution d’expérience sur l’espace de travail.
+ Utiliser le Machine Learning automatisé pour entraîner un modèle : écrit les résultats de l’entraînement sur l’espace de travail.
+ Inscrivez un modèle dans l’espace de travail.
+ Déployer un modèle : utilise le modèle inscrit pour créer un déploiement.
+ Créez et exécutez des flux de travail réutilisables.
+ Affichez les artefacts de Machine Learning (expériences, pipelines, modèles, déploiements, etc.).
+ Suivez et supervisez les modèles.

## <a name="workspace-management"></a>Gestion de l’espace de travail

Vous pouvez également effectuer les tâches de gestion de l’espace de travail suivantes :

| Tâche de gestion de l’espace de travail   | Portail              | Kit SDK        | Interface de ligne de commande        |
|---------------------------|------------------|------------|------------|
| Créer un espace de travail        | **&check;**     | **&check;** | **&check;** |
| Créer et gérer des ressources de calcul    | **&check;**   | **&check;** |  **&check;**   |
| Gérer les accès à l’espace de travail    | **&check;**   | |  **&check;**    |
| Création d’une machine virtuelle Notebook | **&check;**   | |     |

Découvrez le service en [créant un espace de travail](setup-create-workspace.md).

## <a name="resources"></a> Ressources associées

Lorsque vous créez un nouvel espace de travail, celui-ci crée automatiquement plusieurs ressources Azure qui sont utilisées par l’espace de travail :

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) : Enregistre les conteneurs docker que vous utilisez pendant la formation et lorsque vous déployez un modèle. Pour réduire les coûts, ACR est **chargé en différé** jusqu’à la création des images de déploiement.
+ [Compte Stockage Azure](https://azure.microsoft.com/services/storage/) : Utilisé comme magasin de données par défaut pour l’espace de travail.  Les notebooks Jupyter utilisés avec vos machines virtuelles de notebook sont également stockés ici.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) : Stocke les informations de supervision concernant les modèles.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) : Stocke les secrets qui sont utilisés par les cibles de calcul, ainsi que d’autres informations sensibles dont a besoin l’espace de travail.

> [!NOTE]
> En plus de créer de nouvelles versions, vous pouvez utiliser les services Azure existants.

## <a name="next-steps"></a>Étapes suivantes

Pour prendre en main le service Azure Machine Learning, consultez :

+ [Vue d’ensemble d’Azure Machine Learning service](overview-what-is-azure-ml.md)
+ [Créer un espace de travail](setup-create-workspace.md)
+ [Gérer un espace de travail](how-to-manage-workspace.md)
+ [Tutoriel : Effectuer l'apprentissage d’un modèle](tutorial-train-models-with-aml.md)
