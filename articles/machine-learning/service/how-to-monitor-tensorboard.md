---
title: Visualiser des expériences avec TensorBoard
titleSuffix: Azure Machine Learning service
description: Lancez TensorBoard pour visualiser les historiques des exécutions d’expériences et identifier les zones potentielles pour l’optimisation des hyperparamètres et le réentraînement.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: maxluk
ms.author: maxluk
ms.date: 06/28/2019
ms.openlocfilehash: fde2b6d1d298e89227951c376d584452fbff2679
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707063"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualiser des exécutions d’expériences et des métriques avec TensorBoard et le service Azure Machine Learning

Dans cet article, vous allez apprendre à consulter vos exécutions d’expériences et métriques dans TensorBoard à l’aide du [package `tensorboard`](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) dans le kit SDK du service Azure Machine Learning principal. Une fois que vous avez examiné vos exécutions d’expériences, vous pouvez optimiser et réentraîner vos modèles Machine Learning.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) est une suite d’applications web pour l’examen et la compréhension de la structure et des performances de vos expériences.

La façon dont vous lancez TensorBoard avec des expériences Azure Machine Learning varie selon le type d’expérience :
+ Si votre expérience génère en mode natif des fichiers journaux qui sont consommables par TensorBoard, comme les expériences PyTorch, Chainer et TensorFlow, vous pouvez [lancer directement TensorBoard](#direct) à partir de l’historique des exécutions des expériences. 

+ Pour les expériences qui ne génèrent pas en mode natif des fichiers consommables par TensorBoard, comme les expériences Scikit-learn ou Azure Machine Learning, utilisez [la méthode `export_to_tensorboard()`](#export) pour exporter les historiques des exécutions en tant que journaux TensorBoard et lancer TensorBoard à partir de là. 

## <a name="prerequisites"></a>Prérequis

* Pour lancer TensorBoard et afficher les historiques des exécutions de vos expériences, la journalisation doit avoir été activée pour celles-ci afin d’effectuer le suivi des métriques et performances.  

* Le code dans cette procédure peut être exécuté dans l’un des environnements suivants : 

    * Machine virtuelle Notebook Azure Machine Learning : pas de téléchargement ni d’installation nécessaire

        * Suivez le [guide de démarrage rapide des notebooks cloud](quickstart-run-cloud-notebook.md#create-notebook) pour créer un serveur de notebooks dédié et préchargé avec le kit SDK et l’exemple de dépôt.

        * Dans le dossier des exemples du serveur de notebooks, recherchez deux notebooks terminés et développés en accédant à ce répertoire : **how-to-use-azureml > training-with-deep-learning**.
        * export-run-history-to-run-history.ipynb
        * tensorboard.ipynb

    * Votre propre serveur de notebooks Jupyter
      * Utilisez l’article [Créer un espace de travail](setup-create-workspace.md) pour
          * [Installer le kit SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) avec le `tensorboard` supplémentaire
          * Créer un espace de travail et son fichier de configuration (config.json)
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Option 1 : Voir directement l’historique des exécutions dans TensorBoard

Cette option fonctionne pour les expériences qui génèrent en mode natif des fichiers journaux consommables par TensorBoard, comme les expériences PyTorch, Chainer et TensorFlow. Si ce n’est pas le cas de votre expérience, utilisez [la méthode `export_to_tensorboard()`](#export) à la place.

L’exemple de code suivant utilise l’[expérience de démonstration MNIST](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) à partir du dépôt TensorFlow dans une cible de calcul distante, la capacité de calcul Azure Machine Learning. Nous entraînons ensuite notre modèle avec l’[estimateur TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) personnalisé du SDK, puis démarrons TensorBoard sur cette expérience TensorFlow, autrement dit une expérience qui génère en mode natif des fichiers d’événements TensorBoard.

### <a name="set-experiment-name-and-create-project-folder"></a>Définir le nom de l’expérience et créer le dossier de projet

Ici, nous nommons l’expérience et créons son dossier. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Télécharger le code de l’expérience de démonstration TensorFlow

Le dépôt TensorFlow comporte une démonstration MNIST avec une instrumentation TensorBoard complète. Nous n’allons pas (et n’avons pas besoin de) modifier le code de cette démonstration pour qu’il fonctionne avec le service Azure Machine Learning. Dans le code suivant, nous téléchargeons le code MNIST et l’enregistrons dans le dossier de l’expérience nouvellement créé.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
Dans tout le fichier de code MNIST, mnist_with_summaries.py, notez qu’il existe des lignes qui appellent `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()`, etc. Ces méthodes regroupent, consignent et balisent des métriques clés de vos expériences dans l’historique des exécutions. La méthode `tf.summary.FileWriter()` est particulièrement importante, car elle sérialise les données à partir de vos métriques d’expériences consignées, ce qui permet à TensorBoard de générer des visualisations à partir de celles-ci.

 ### <a name="configure-experiment"></a>Configurer une expérience

Dans la section suivante, nous configurons notre expérience et les répertoires pour les journaux et les données. Ces journaux sont chargés sur le service d’artefact, auquel TensorBoard accède par la suite.

>[!Note]
> Pour cet exemple TensorFlow, vous devez installer TensorFlow sur votre ordinateur local. De plus, le module TensorBoard (autrement dit, celui inclus avec TensorFlow) doit être accessible au noyau de ce notebook, car l’ordinateur local est l’outil d’exécution de TensorBoard.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Créer un cluster pour votre expérience
Nous créons un cluster AmlCompute pour cette expérience, mais vos expériences peuvent être créées dans n’importe quel environnement et vous êtes toujours en mesure de lancer TensorBoard sur l’historique des exécutions des expériences. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>Envoyer l’exécution avec l’estimateur TensorFlow

L’estimateur TensorFlow fournit un moyen simple de lancer un travail d’entraînement TensorFlow sur une cible de calcul. Il est implémenté par le biais de la classe [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) générique, qui peut être utilisée pour prendre en charge n’importe quel framework. Pour plus d’informations sur l’entraînement des modèles à l’aide de l’estimateur générique, consultez [Entraîner des modèles avec Azure Machine Learning à l’aide de l’estimateur](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Lancer TensorBoard

Vous pouvez lancer TensorBoard pendant ou après l’exécution. Dans la section suivante, nous créons une instance d’objet TensorBoard, `tb`, qui prend l’historique des exécutions des expériences chargé dans le `run`, puis lance TensorBoard avec la méthode `start()`. 
  
Comme le [constructeur TensorBoard](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) accepte un tableau d’exécutions, veillez à le transmettre sous la forme d’un tableau à un seul élément.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Option 2 : Exporter l’historique en tant que journal à consulter dans TensorBoard

Le code suivant configure un exemple d’expérience, commence le processus de journalisation à l’aide des API d’historique des exécutions Azure Machine Learning et exporte l’historique des exécutions des expériences dans les journaux consommables par TensorBoard pour la visualisation. 

### <a name="set-up-experiment"></a>Configurer l’expérience

Le code suivant configure une nouvelle expérience et nomme le répertoire d’exécution `root_run`. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Ici, nous chargeons le jeu de données diabetes, un petit jeu de données intégré qui est fourni avec Scikit-learn, et le divisons en jeux d’entraînement et de test.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Exécuter l’expérience et consigner les métriques

Pour ce code, nous entraînons un modèle de régression linéaire et consignons des métriques clés, le coefficient alpha `alpha` et l’erreur quadratique moyenne `mse` dans l’historique des exécutions.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Exporter les exécutions vers TensorBoard

Avec la méthode [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) du kit SDK, nous pouvons exporter l’historique des exécutions de notre expérience Azure Machine Learning dans les journaux TensorBoard pour pouvoir les voir via TensorBoard.  

Dans le code suivant, nous créons le dossier `logdir` dans notre répertoire de travail actuel. Ce dossier est l’endroit où nous allons exporter notre historique des exécutions des expériences ainsi que les journaux du répertoire `root_run`, puis marquer cette exécution comme terminée. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 Vous pouvez également exporter une exécution spécifique vers TensorBoard en spécifiant le nom de l’exécution `export_to_tensorboard(run_name, logdir)`

Démarrez et arrêtez TensorBoard. Une fois notre historique des exécutions pour cette expérience exporté, nous pouvons lancer TensorBoard avec la méthode [start()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-). 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Lorsque vous avez terminé, veillez à appeler la méthode [stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) de l’objet TensorBoard. Sinon, TensorBoard continue de s’exécuter tant que vous n’arrêtez pas le noyau du notebook. 

```python
tb.stop()
```

## <a name="next-steps"></a>Étapes suivantes

Dans cette procédure, vous avez créé deux expériences et appris à lancer TensorBoard sur leurs historiques des exécutions afin d’identifier les zones potentielles d’optimisation et de réentraînement. 

* Si vous êtes satisfait de votre modèle, consultez notre article [Guide pratique pour déployer un modèle](how-to-deploy-and-where.md). 
* Apprenez-en davantage sur l’[optimisation des hyperparamètres](how-to-tune-hyperparameters.md). 
