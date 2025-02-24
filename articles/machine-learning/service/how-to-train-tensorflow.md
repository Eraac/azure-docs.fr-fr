---
title: Former et enregistrer des modèles TensorFlow
titleSuffix: Azure Machine Learning service
description: Cet article vous explique comment entraîner et inscrire un modèle TensorFlow à l’aide d’Azure Machine Learning service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: 67263df319063cdf21dadea257dcab05ba0d5f7b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839995"
---
# <a name="train-and-register-tensorflow-models-at-scale-with-azure-machine-learning-service"></a>Entraîner et inscrire des modèles TensorFlow à l’échelle avec Azure Machine Learning service

Cet article vous explique comment entraîner et inscrire un modèle TensorFlow à l’aide d’Azure Machine Learning service. Il utilise le jeu de données populaire [MNIST](http://yann.lecun.com/exdb/mnist/) pour classer les nombres manuscrits en s’appuyant sur un réseau neuronal profond construit à l’aide de la [bibliothèque Python TensorFlow](https://www.tensorflow.org/overview).

TensorFlow est une infrastructure de calcul open source couramment utilisée pour créer des réseaux neuronaux profonds (DNN). Avec Azure Machine Learning service, vous pouvez rapidement faire monter en charge des tâches de formation open source à l’aide de ressources de calcul cloud élastiques. Vous pouvez également suivre vos sessions de formation, les modèles de version, les modèles de déploiement et bien plus encore.

Que vous développiez un modèle TensorFlow de A à Z ou importiez un [modèle existant](how-to-deploy-existing-model.md) dans le cloud, Azure Machine Learning service peut vous aider à créer des modèles prêts pour la production.

## <a name="prerequisites"></a>Prérequis

Exécutez ce code sur l’un de ces environnements :

 - Machine virtuelle de Notebook Azure Machine Learning : pas d’installation ou de téléchargement nécessaire

     - Suivez le [guide de démarrage rapide des notebooks cloud](quickstart-run-cloud-notebook.md) pour créer un serveur de notebooks dédié et préchargé avec le kit SDK et l’exemple de référentiel.
    - Dans le dossier des exemples du serveur de notebook, recherchez un notebook terminé et développé en accédant à ce répertoire : le dossier **how-to-use-azureml > training-with-deep-learning > train-hyperparameter-tune-deploy-with-tensorflow**. 
 
 - Votre propre serveur de notebooks Jupyter

     - [Installer le kit de développement logiciel (SDK) Azure Machine Learning pour Python](setup-create-workspace.md#sdk)
    - [Créer un fichier de configuration d’espace de travail](setup-create-workspace.md#write-a-configuration-file)
    - [Télécharger les exemples de fichiers de script](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` et `utils.py`
     
    Vous trouverez également une [version Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) complète de ce guide sur la page des exemples GitHub. Le notebook inclut des sections développées couvrant l’optimisation des hyperparamètres intelligents, les modèles de déploiement et les widgets de notebook.

## <a name="set-up-the-experiment"></a>Configurer l’expérience

Cette section configure l’expérience d’apprentissage via le chargement des packages Python requis, l’initialisation d’un espace de travail, la création d’une expérience et le chargement des données et des scripts d’apprentissage.

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

Créez une expérience et un dossier pour stocker vos scripts de formation. Dans cet exemple, créez une expérience appelée « tf-mnist ».

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Charger le jeu de données et les scripts

Le [magasin de données](how-to-access-data.md) est l’emplacement où les données peuvent être stockées. Il est possible d’y accéder en montant ou en copiant des données dans la cible de calcul. Chaque espace de travail fournit un magasin de données par défaut. Chargez les données et les scripts de formation dans le magasin de données afin d’y accéder plus facilement pendant la formation.

1. Téléchargez le jeu de données MNIST localement.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Chargez le jeu de données MNIST dans le magasin de données par défaut.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Chargez le script d’apprentissage TensorFlow `tf_mnist.py` et le fichier d’assistance `utils.py`.

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Créer une cible de calcul

Créez une cible de calcul sur laquelle vous exécuterez votre tâche TensorFlow. Dans cet exemple, créez un cluster de calcul Azure Machine Learning compatible avec le GPU.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Pour plus d’informations sur les cibles de calcul, consultez l’article [Qu’est-ce qu’une cible de calcul](concept-compute-target.md).

## <a name="create-a-tensorflow-estimator"></a>Créer un estimateur TensorFlow

[L’estimateur TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) fournit un moyen simple de lancer un travail d’entraînement TensorFlow sur une cible de calcul.

L’estimateur TensorFlow est implémenté via la classe générique [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py), qui peut être utilisée pour prendre en charge n’importe quelle infrastructure. Pour plus d’informations sur l’entraînement des modèles à l’aide de l’estimateur générique, consultez [Entraîner des modèles avec Azure Machine Learning à l’aide de l’estimateur](how-to-train-ml-models.md)

Si l’exécution de votre script de formation exige des packages PIP ou Conda supplémentaires, vous pouvez installer les packages sur l’image docker obtenue en transmettant leurs noms via les arguments `pip_packages` et `conda_packages`.

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Envoyer une exécution

L’[objet d’exécution](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) fournit l’interface à l’historique des exécutions pendant et après l’exécution de la tâche.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Lorsque l’exécution est lancée, il effectue les étapes suivantes :

- **Préparation** : une image docker est créée en fonction de l’estimateur TensorFlow. L’image est chargée dans le registre de conteneurs de l’espace de travail et mise en cache pour des exécutions ultérieures. Les journaux sont également transmis en continu à l’historique des exécutions et peuvent être affichés afin de surveiller la progression.

- **Mise à l’échelle** : le cluster tente de monter en puissance si le cluster Batch AI nécessite plus de nœuds pour l’exécution que la quantité disponible actuellement.

- **Running** : tous les scripts dans le dossier de script sont chargés dans la cible de calcul, les magasins de données sont montés ou copiés, puis entry_script est exécuté. Les sorties issues de stdout et du dossier ./logs sont transmises en continu à l’historique des exécutions et peuvent être utilisées pour surveiller l’exécution.

- **Post-traitement** : le dossier ./outputs de l’exécution est copié dans l’historique des exécutions.

## <a name="register-or-download-a-model"></a>Inscrire ou télécharger un modèle

Une fois que vous avez entraîné le modèle, vous pouvez l’inscrire sur votre espace de travail. L’inscription du modèle vous permet de stocker vos modèles et de suivre leurs versions dans votre espace de travail afin de simplifier [la gestion et le déploiement des modèles](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

Vous pouvez aussi télécharger une copie locale du modèle à l’aide de l’objet d’exécution. Dans le script de formation `mnist-tf.py`, un objet de sauvegarde TensorFlow conserve le modèle dans un dossier local (local dans la cible de calcul). Vous pouvez utiliser l’objet d’exécution pour télécharger une copie.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Entraînement distribué

L’estimateur [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) prend également en charge la formation distribuée sur les clusters de l’UC et du GPU. Vous pouvez facilement exécuter des tâches distribuées TensorFlow et le service Azure Machine Learning gère l’orchestration à votre place.

Le service Azure Machine Learning prend en charge deux méthodes d’entraînement distribué dans TensorFlow :

- Entraînement distribué [MPI](https://www.open-mpi.org/) qui utilise le framework [Horovod](https://github.com/uber/horovod)
- L’entraînement [distribué TensorFlow](https://www.tensorflow.org/deploy/distributed) natif à l’aide de la méthode de serveur de paramètres

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) est une infrastructure open source pour la formation distribuée, développée par Uber. Elle offre un accès facile vers des tâches distribuées TensorFlow du GPU.

Pour utiliser Horovod, spécifiez un objet [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) pour le paramètre `distributed_training` dans le constructeur TensorFlow. Ce paramètre s’assure que la bibliothèque Horovod est installée et que vous pouvez l’utiliser dans votre script de formation.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```

### <a name="parameter-server"></a>Serveur de paramètres

Vous pouvez également exécuter l’entraînement [natif distribué TensorFlow](https://www.tensorflow.org/deploy/distributed), qui utilise le modèle de serveur de paramètres. Dans cette méthode, vous effectuez un entraînement sur un cluster de serveurs de paramètres et de workers. Les workers calculent les dégradés pendant l’entraînement, alors que les serveurs de paramètres agrègent les dégradés.

Pour utiliser la méthode serveur de paramètres, spécifiez un objet [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) pour le paramètre `distributed_training` dans le constructeur TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tfconfig"></a>Définir des spécifications de cluster « TF_CONFIG »

Vous avez également besoin des adresses réseau et des ports du cluster pour [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec). Par conséquent, Azure Machine Learning définit la variable d’environnement `TF_CONFIG`.

La variable d’environnement `TF_CONFIG` est une chaîne JSON. Voici un exemple de cette variable pour un serveur de paramètres :

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Pour l’API de haut niveau [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) de TensorFlow, TensorFlow analyse la variable `TF_CONFIG` et crée les spécifications du cluster pour vous.

Pour les API de base de TensorFlow pour l’entraînement, analysez la variable `TF_CONFIG` et créer le `tf.train.ClusterSpec` dans votre code d’apprentissage.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez entraîné et inscrit un modèle TensorFlow. Pour savoir comment déployer un modèle vers un modèle compatible GPU, passez à notre article sur le déploiement de modèle GPU.

[Procédure de déploiement pour l’inférence avec des GPU](how-to-deploy-inferencing-gpus.md)
[Comment surveiller avec Tensorboard](how-to-monitor-tensorboard.md)
