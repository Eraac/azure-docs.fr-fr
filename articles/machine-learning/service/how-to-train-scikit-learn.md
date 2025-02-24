---
title: Entraîner et inscrire les modèles scikit-learn
titleSuffix: Azure Machine Learning service
description: Cet article vous explique comment entraîner et inscrire un modèle scikit-learn à l’aide d’Azure Machine Learning service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 06/30/2019
ms.custom: seodec18
ms.openlocfilehash: d2c9b104d1fe9333221bc20e7e23b436358c9ece
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514025"
---
# <a name="train-and-register-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>Entraîner et inscrire des modèles Scikit-learn à l’échelle avec Azure Machine Learning service

Cet article vous explique comment entraîner et inscrire un modèle Scikit-learn à l’aide d’Azure Machine Learning service. Il utilise le populaire [jeu de données Iris](https://archive.ics.uci.edu/ml/datasets/iris) pour classer des images de fleurs d’iris avec la classe [scikit-learn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) personnalisée.

Scikit-learn est une infrastructure de calcul open source couramment utilisée pour le Machine Learning. Avec Azure Machine Learning service, vous pouvez rapidement faire monter en charge des tâches de formation open source à l’aide de ressources de calcul cloud élastiques. Vous pouvez également suivre vos sessions de formation, les modèles de version, les modèles de déploiement et bien plus encore.

Que vous développiez un modèle Scikit-learn de A à Z ou importiez un modèle existant dans le cloud, Azure Machine Learning service peut vous aider à créer des modèles prêts pour la production.

## <a name="prerequisites"></a>Prérequis

Exécutez ce code sur l’un des environnements :
 - Machine virtuelle de Notebook Azure Machine Learning : pas d’installation ou de téléchargement nécessaire

    - Effectuez un [démarrage rapide du notebook informatique](quickstart-run-cloud-notebook.md) pour créer un serveur de notebook dédié dans lequel auront été préchargés le kit de développement logiciel (SDK) et l’exemple de référentiel.
    - Dans le dossier des exemples du serveur de notebook, recherchez un notebook terminé et développé en accédant à ce répertoire : le dossier **how-to-use-azureml > training > train-hyperparameter-tune-deploy-with-sklearn**.

 - Votre propre serveur de notebooks Jupyter

    - [Installer le kit de développement logiciel (SDK) Azure Machine Learning pour Python](setup-create-workspace.md#sdk)
    - [Créer un fichier de configuration d’espace de travail](setup-create-workspace.md#write-a-configuration-file)
    - [Télécharger l’exemple de fichier de script](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn) `train_iris.py`
    - Vous trouverez également une [version Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-sklearn.ipynb) complète de ce guide sur la page des exemples GitHub. Le notebook comprend une section plus détaillée couvrant le paramétrage des hyperparamètres intelligents et la récupération du meilleur modèle par les métriques primaires.

## <a name="set-up-the-experiment"></a>Configurer l’expérience

Cette section configure l’expérience de formation via le chargement des packages Python requis, l’initialisation d’un espace de travail, la création d’une expérience et le chargement des données et des scripts de formation.

### <a name="import-packages"></a>Importer des packages

Tout d’abord, importez les bibliothèques Python nécessaires.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Initialiser un espace de travail

L’[espace de travail Azure Machine Learning service](concept-workspace.md) est la ressource de niveau supérieur du service. Il vous fournit un emplacement centralisé dans lequel utiliser tous les artefacts que vous créez. Dans le kit de développement logiciel (SDK) Python, vous pouvez accéder aux artefacts de l’espace de travail en créant un objet [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).

Créez un objet d’espace de travail à partir du fichier `config.json` créé dans la [section Conditions préalables](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Création d'une expérience

Créer une expérience et un dossier pour stocker vos scripts de formation. Dans cet exemple, créez une expérience appelée « sklearn-iris ».

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>Charger le jeu de données et les scripts

Le [magasin de données](how-to-access-data.md) est l’emplacement où les données peuvent être stockées. Il est possible d’y accéder en montant ou en copiant des données dans la cible de calcul. Chaque espace de travail fournit un magasin de données par défaut. Chargez les données et les scripts de formation dans le magasin de données afin d’y accéder plus facilement pendant la formation.

1. Créez le répertoire pour vos données.

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. Chargez le jeu de données iris dans le magasin de données par défaut.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. Chargez le script de formation Scikit-learn : `train_iris.py`.

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>Créer ou obtenir une cible de calcul

Créez une cible de calcul sur laquelle vous exécuterez votre tâche Scikit-learn. Scikit-learn prend uniquement en charge les calculs d’UC à nœud unique.

Le code suivant crée un calcul managé Azure Machine Learning (AmlCompute) pour votre ressource de calcul de formation à distance. La création d’AmlCompute prend environ cinq minutes. Si un calcul AmlCompute portant ce nom est déjà dans votre espace de travail, ce code ignore le processus de création.

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Pour plus d’informations sur les cibles de calcul, consultez l’article [Qu’est-ce qu’une cible de calcul](concept-compute-target.md).

## <a name="create-a-scikit-learn-estimator"></a>Créer un estimateur Scikit-learn

L’[estimateur Scikit-learn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fournit un moyen pour lancer simplement la tâche de formation de Scikit-learn sur une cible de calcul. Il est implémenté via la classe [`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) qui peut être utilisé pour prendre en charge de la formation d’UC à nœud unique.

Si l’exécution de votre script de formation exige des packages PIP ou Conda supplémentaires, vous pouvez installer les packages sur l’image docker obtenue en transmettant leurs noms via les arguments `pip_packages` et `conda_packages`.

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```

## <a name="submit-a-run"></a>Envoyer une exécution

L’[objet d’exécution](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fournit l’interface à l’historique des exécutions pendant et après l’exécution de la tâche.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

Lorsque l’exécution est lancée, il effectue les étapes suivantes :

- **Préparation** : une image docker est créée en fonction de l’estimateur TensorFlow. L’image est chargée dans le registre de conteneurs de l’espace de travail et mise en cache pour des exécutions ultérieures. Les journaux sont également transmis en continu à l’historique des exécutions et peuvent être affichés afin de surveiller la progression.

- **Mise à l’échelle** : le cluster tente de monter en puissance si le cluster Batch AI nécessite plus de nœuds pour l’exécution que la quantité disponible actuellement.

- **Running** : tous les scripts dans le dossier de script sont chargés dans la cible de calcul, les magasins de données sont montés ou copiés, puis entry_script est exécuté. Les sorties issues de stdout et du dossier ./logs sont transmises en continu à l’historique des exécutions et peuvent être utilisées pour surveiller l’exécution.

- **Post-traitement** : Le dossier ./outputs de l’exécution est copiée dans l’historique des exécutions.

## <a name="save-and-register-the-model"></a>Télécharger et enregistrer le modèle

Une fois que vous avez entraîné le modèle, vous pouvez l’enregistrer et l’inscrire sur votre espace de travail. L’inscription du modèle vous permet de stocker vos modèles et de suivre leurs versions dans votre espace de travail afin de simplifier [la gestion et le déploiement des modèles](concept-model-management-and-deployment.md).

Ajoutez le code suivant à votre script de formation, train_iris.py, pour enregistrer le modèle. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Inscrivez le modèle sur votre espace de travail avec le code suivant.

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez entraîné et inscrit un modèle Scikit-learn sur Azure Machine Learning service.

* Pour savoir comment déployer un modèle, passez à notre article relatif aux [Modèles de déploiement](how-to-deploy-and-where.md).

* [Optimiser les hyperparamètres](how-to-tune-hyperparameters.md)

* [Effectuer le suivi des métriques d’exécution pendant l’entraînement](how-to-track-experiments.md)