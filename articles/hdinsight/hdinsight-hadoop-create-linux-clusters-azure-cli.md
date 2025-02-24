---
title: Créer des clusters Apache Hadoop avec Azure CLI - Azure HDInsight
description: Apprenez à créer des clusters HDInsight à l’aide de l’interface multiplateforme Azure CLI.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: hrasheed
ms.openlocfilehash: 0a278cd98b0dd6c6d8f0fe9bfee81e5bafd4f543
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65597682"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Créer des clusters HDInsight à l’aide de l’interface de ligne de commande Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Les étapes de cette procédure présentent la création d’un cluster HDInsight 3.6 à l’aide d’Azure CLI.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Azure CLI. Si vous n'avez pas installé Azure CLI, consultez [Installer l'interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) pour connaître les étapes à suivre.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Créer un cluster

1. Connectez-vous à votre abonnement Azure. Si vous prévoyez d'utiliser Azure Cloud Shell, sélectionnez simplement **Essayer** dans le coin supérieur droit du bloc de code. Sinon, entrez la commande suivante :

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Définir des variables d’environnement L’utilisation des variables de cet article repose sur Bash. De petites modifications seront nécessaires si vous utilisez d'autres environnements. Consultez [az-hdinsight-create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) pour accéder à une liste complète des paramètres possibles pour la création du cluster.

    |Paramètre | Description |
    |---|---|
    |`--size`| Nombre de nœuds Worker figurant dans le cluster. Cet article utilise la variable `clusterSizeInNodes` en tant que valeur passée à `--size`. |
    |`--version`| Version du cluster HDInsight Cet article utilise la variable `clusterVersion` en tant que valeur passée à `--version`. Voir aussi : [Version de HDInsight prises en charge](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Type de cluster HDInsight, par exemple : hadoop, interactivehive, hbase, kafka, storm, spark, rserver, mlservices.  Cet article utilise la variable `clusterType` en tant que valeur passée à `--type`. Voir aussi : [Types de cluster et configuration](./hdinsight-hadoop-provision-linux-clusters.md#cluster-types).|
    |`--component-version`|Versions des différents composants Hadoop, séparées par des espaces, au format « composant=version ». Cet article utilise la variable `componentVersion` en tant que valeur passée à `--component-version`. Voir aussi : [Composants Hadoop](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    Remplacez `RESOURCEGROUPNAME`, `LOCATION`, `CLUSTERNAME`, `STORAGEACCOUNTNAME`, et `PASSWORD` par les valeurs souhaitées. Modifiez les valeurs des autres variables comme vous le souhaitez. Entrez ensuite les commandes CLI.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'
    
    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [Créez le groupe de ressources](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) en entrant la commande ci-dessous :

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Pour obtenir la liste des emplacements valides, utilisez la commande `az account list-locations`, puis l’un des emplacements de la valeur `name`.

4. [Créez un compte de stockage Azure](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) en saisissant la commande ci-dessous :

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Extrayez la clé primaire du compte de stockage Azure](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) et stockez-la dans une variable en entrant la commande ci-dessous :

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Créez un conteneur de stockage Azure](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) en saisissant la commande suivante :

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Créez le cluster HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) en saisissant la commande suivante :

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > Il existe différents types de clusters HDInsight correspondant à la charge de travail ou à la technologie pour laquelle ils sont utilisés. Il n’existe aucune méthode prise en charge pour créer un cluster combinant plusieurs types, tels que Storm et HBase sur un seul cluster.

    Le processus de création de cluster peut prendre plusieurs minutes. En règle générale, il dure environ 15 minutes.

## <a name="clean-up-resources"></a>Supprimer des ressources

Après avoir terminé ce tutoriel, vous souhaiterez peut-être supprimer le cluster. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés.

Entrez tout ou partie des commandes suivantes pour supprimer des ressources :

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, reportez-vous aux [exigences de contrôle d’accès](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé un cluster HDInsight à l’aide de l’interface de ligne de commande Azure. Pour apprendre à l’utiliser, consultez les rubriques ci-dessous :

### <a name="apache-hadoop-clusters"></a>Clusters Apache Hadoop

* [Utilisation d’Apache Hive avec HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilisation d’Apache Pig avec HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters Apache HBase

* [Bien démarrer avec Apache HBase sur HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Développer des applications Java pour Apache HBase sur HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clusters Apache Storm

* [Développer des topologies Java pour Apache Storm sur HDInsight](storm/apache-storm-develop-java-topology.md)
* [Utiliser des composants Python dans Apache Storm sur HDInsight](storm/apache-storm-develop-python-topology.md)
* [Déploiement et analyse des topologies avec Apache Storm sur HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
