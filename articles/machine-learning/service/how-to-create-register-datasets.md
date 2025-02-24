---
title: Créer des jeux de données pour accéder aux données avec azureml-datasets
titleSuffix: Azure Machine Learning service
description: Apprenez à créer des jeux de données à partir de différentes sources et à inscrire des jeux de données avec votre espace de travail
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/21/2019
ms.openlocfilehash: a879fa17244977277dab3e2e66c5888a44759764
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444029"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Créer des jeux de données et y accéder (préversion) dans Azure Machine Learning

Dans cet article, vous apprendrez ç créer des jeux de données Azure Machine Learning (préversion) et à accéder aux données d'expériences locales et à distance.

Grâce aux jeux de données managés, vous pouvez : 
* **Accéder facilement aux données pendant l’apprentissage du modèle** sans vous reconnecter à des magasins sous-jacents

* **Garantir la cohérence et la reproductibilité des données** en utilisant un même pointeur entre différentes expériences : blocs-notes, Machine Learning automatisé, pipelines, interface visuelle

* **Partager des données et collaborer** avec les autres utilisateurs

* **Explorer les données** et gérer le cycle de vie des instantanés et des versions de données

* **Comparer les données** en formation et en production


## <a name="prerequisites"></a>Prérequis

Pour créer et utiliser des jeux de données, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez dès aujourd'hui la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree).

* Un [espace de travail Azure Machine Learning service](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* Le [SDK Azure Machine Learning pour Python installé](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), qui inclut le paquet azureml-datasets.

> [!Note]
> Certaines classes Dataset (préversion) comportent des dépendances sur le paquet [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) (GA). Pour les utilisateurs Linux, ces classes sont uniquement prises en charge dans les distributions suivantes :  Red Hat Enterprise Linux, Ubuntu, Fedora et CentOS.

## <a name="data-formats"></a>Formats de données

Vous pouvez créer un jeu de données Azure Machine Learning à partir des données suivantes :
+ [delimited](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)
+ [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-)
+ [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)
+ [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)
+ [Azure SQL Database](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [Azure Data Lake gen. 1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)

## <a name="create-datasets"></a>Créez les jeux de données 

Vous pouvez interagir avec vos jeux de données à l’aide du paquet azureml-datasets dans le [SDK Python Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) et spécifiquement [la classe `Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py).

### <a name="create-from-local-files"></a>Créer à partir de fichiers locaux

Chargez des fichiers à partir de votre ordinateur local en spécifiant le chemin d’accès du fichier ou dossier à l’aide de la méthode [`auto_read_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-), à partir de la classe `Dataset`.  Cette méthode effectue les étapes suivantes, sans que vous ayez à spécifier le type de fichier ou des arguments d’analyse :

* Déduire et définir le délimiteur.
* Ignore les enregistrements vides en haut du fichier.
* Déduire et définir la ligne d’en-tête.
* Déduire et convertir des types de données de colonnes.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Vous pouvez également utiliser des fonctions spécifiques au fichier pour contrôler explicitement l’analyse de votre fichier. 


### <a name="create-from-azure-datastores"></a>Créer à partir d’une banque de données Azure

Pour créer des jeux de données à partir d’une banque de données Azure :

* Vérifiez que vous disposez d’un accès `contributor` ou `owner` à la banque de données Azure inscrite.

* Importez les paquets [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition) et `Dataset` à partir du SDK.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 La méthode `get()` récupère une banque de données existante dans l’espace de travail.

```
dstore = Datastore.get(workspace, datastore_name)
```

Utilisez la méthode `from_delimited_files()` pour lire les fichiers délimités et créer un jeu de données non inscrit.

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>Inscrire des jeux de données

Pour terminer le processus de création, inscrivez vos jeux de données dans l’espace de travail :

Utilisez la méthode [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) pour inscrire des jeux de données dans votre espace de travail afin de pouvoir les partager avec d’autres personnes et les réutiliser dans différentes expériences.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Si `exist_ok = False` (valeur par défaut), et que vous tentez d’inscrire un jeu de données portant le même nom qu’un autre jeu, une erreur se produit. Choisissez la valeur `True` pour remplacer les jeux existants.

## <a name="access-data-in-datasets"></a>Accéder aux informations des jeux de données

Les jeux de données inscrits sont accessibles et utilisables localement, à distance et sur des clusters de calcul comme Azure Machine Learning. Pour réutiliser votre jeu de données inscrit dans plusieurs expériences et environnements de calcul, utilisez le code suivant pour récupérer votre espace de travail et jeu de données inscrit par nom.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
Dataset.list(workspace)

# Get dataset by name
dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>Étapes suivantes

* [Explorer et préparer des jeux de données](how-to-explore-prepare-data.md).
* Pour obtenir un exemple d’utilisation de jeux de données, consultez les [exemples de blocs-notes](https://aka.ms/dataset-tutorial).
