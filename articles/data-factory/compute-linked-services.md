---
title: Environnements de calcul pris en charge par Azure Data Factory | Microsoft Docs
description: Découvrez les environnements de calcul que vous pouvez utiliser dans les pipelines Azure Data Factory (tels qu’Azure HDInsight) pour transformer ou traiter les données.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 0e7405e48307091ff5df12096d49a00c011e2de3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480434"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Environnements de calcul pris en charge par Azure Data Factory
Cet article décrit les différents environnements de calcul que vous pouvez utiliser pour traiter ou transformer des données. Il fournit également des détails sur les différentes configurations (à la demande ou de type « apporter votre propre configuration ») prises en charge par Data Factory lors de la configuration des services liés qui relient ces environnements de calcul à Azure Data Factory.

Le tableau suivant fournit une liste d’environnements de calcul pris en charge par Data Factory et les activités qui peuvent s’exécuter sur ces derniers. 

| Environnement de calcul                                          | activités                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Cluster HDInsight à la demande](#azure-hdinsight-on-demand-linked-service) ou [votre propre cluster HDInsight](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Streaming Hadoop](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Personnalisée](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Activités Machine Learning : exécution par lot et ressource de mise à jour](transform-data-using-machine-learning.md) |
| [Service Analytique Azure Data Lake](#azure-data-lake-analytics-linked-service) | [Langage U-SQL du service Analytique Data Lake](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Procédure stockée](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Notebook](transform-data-databricks-notebook.md), [Jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |

>  

## <a name="on-demand-hdinsight-compute-environment"></a>Environnement de calcul HDInsight à la demande
Dans ce type de configuration, l'environnement de calcul est entièrement géré par le service Azure Data Factory. Il est automatiquement créé par le service Azure Data Factory avant qu'une tâche de traitement des données ne soit soumise et il est supprimé lorsque la tâche est terminée. Vous pouvez créer un service lié pour un environnement de calcul à la demande, le configurer et contrôler les paramètres granulaires pour l'exécution de la tâche, la gestion du cluster et les actions d'amorçage.

> [!NOTE]
> La configuration à la demande est actuellement prise en charge uniquement pour les clusters Azure HDInsight. Azure Databricks prend également en charge les travaux à la demande à l'aide de clusters de travaux. Pour plus d'informations, reportez-vous à [Service Azure Databricks lié](#azure-databricks-linked-service).

## <a name="azure-hdinsight-on-demand-linked-service"></a>Service lié à la demande Azure HDInsight
Le service Azure Data Factory peut créer automatiquement un cluster HDInsight à la demande pour traiter des données. Le cluster est créé dans la même région que celle du compte de stockage (propriété linkedServiceName dans JSON) associé au cluster. Le compte de stockage doit être un compte de stockage Azure standard général. 

Notez les points **importants** suivants sur le service lié HDInsight à la demande :

* Le cluster HDInsight à la demande est créé sous votre abonnement Azure. Vous pouvez voir le cluster dans votre portail Azure lorsque le cluster est opérationnel. 
* Les journaux d’activité des tâches exécutées sur un cluster HDInsight à la demande sont copiés dans le compte de stockage associé au cluster HDInsight. Les valeurs clusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword spécifiées dans votre définition de service lié sont utilisées pour la connexion au cluster à des fins de dépannage approfondi pendant le cycle de vie du cluster. 
* Vous êtes facturé uniquement lorsque le cluster HDInsight est actif et exécute des tâches.
* Vous pouvez utiliser une **action de script** avec le service lié à la demande Azure HDInsight.  

> [!IMPORTANT]
> Il faut généralement au moins **20 minutes** pour mettre en service un cluster Azure HDInsight à la demande.

### <a name="example"></a>Exemples
Le JSON suivant définit un service lié HDInsight à la demande sous Linux. Le service Data Factory crée automatiquement un cluster HDInsight **Linux** pour traiter l’activité requise. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> Le cluster HDInsight crée un **conteneur par défaut** dans le stockage d’objets blob que vous avez spécifié dans le JSON (**linkedServiceName**). HDInsight ne supprime pas ce conteneur lorsque le cluster est supprimé. Ce comportement est normal. Avec le service lié HDInsight à la demande, un cluster HDInsight est créé à chaque fois qu’une tranche doit être traitée, à moins qu’il n’existe un cluster activé (**timeToLive**), et est supprimé une fois le traitement activé. 
>
> Pendant la poursuite de l’activité, vous voyez de nombreux conteneurs dans votre Stockage Blob Azure. Si vous n’en avez pas besoin pour dépanner les travaux, il se peut que vous deviez les supprimer pour réduire les frais de stockage. Les noms de ces conteneurs sont conformes au modèle suivant : `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Utilisez des outils tels que [Microsoft Storage Explorer](https://storageexplorer.com/) pour supprimer des conteneurs dans votre stockage d’objets blob Azure.
>
> 

### <a name="properties"></a>properties
| Propriété                     | Description                              | Obligatoire |
| ---------------------------- | ---------------------------------------- | -------- |
| Type                         | La propriété de type doit être définie sur **HDInsightOnDemand**. | OUI      |
| clusterSize                  | Nombre de nœuds worker/données dans le cluster. Le cluster HDInsight est créé avec 2 nœuds principaux et le nombre de nœuds worker que vous spécifiez pour cette propriété. Les nœuds étant de taille Standard_D3 à 4 cœurs, un cluster à 4 nœuds de travail prend 24 cœurs (4\*4 = 16 nœuds pour les nœuds de travail + 2\*4 = 8 cœurs pour les nœuds principaux). Pour plus de détails, voir [Configurer des clusters dans HDInsight avec Hadoop, Spark, Kafka et bien plus encore](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | OUI      |
| linkedServiceName            | Le service lié Azure Storage utilisé par le cluster à la demande pour le stockage et le traitement des données. Le cluster HDInsight est créé dans la même région que ce compte de stockage Azure. Azure HDInsight présente une limite relative au nombre total de cœurs que vous pouvez utiliser dans chaque région Azure prise en charge. Assurez-vous que vous disposez de quotas de cœurs suffisants dans cette région Azure pour offrir la taille de cluster requise. Pour plus de détails, voir, [Configurer des clusters dans HDInsight avec Hadoop, Spark, Kafka et bien plus encore](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>Actuellement, vous ne pouvez pas créer un cluster HDInsight à la demande qui utilise un Azure Data Lake Store en guise de stockage. Si vous souhaitez stocker les données de résultat à partir du traitement HDInsight dans un Azure Data Lake Store, utilisez une activité de copie pour copier les données du stockage Blob Azure dans Azure Data Lake Store. </p> | OUI      |
| clusterResourceGroup         | Le cluster HDInsight est créé dans ce groupe de ressources. | OUI      |
| timetolive                   | La durée d’inactivité autorisée pour le cluster HDInsight à la demande. Spécifie la durée pendant laquelle le cluster HDInsight à la demande reste actif après l’achèvement d’une exécution d’activité s’il n’existe aucun autre travail actif dans le cluster. La valeur minimale autorisée est 5 minutes (00:05:00).<br/><br/>Par exemple, si une exécution d’activité prend 6 minutes et si la propriété TimeToLive est définie sur 5 minutes, le cluster reste actif pendant 5 minutes après les 6 minutes du traitement de l’exécution d’activité. Si une autre exécution d’activité intervient dans la fenêtre de 6 minutes, elle est traitée par le même cluster.<br/><br/>La création d’un cluster HDInsight à la demande étant une opération coûteuse (elle peut prendre du temps), utilisez ce paramètre selon le besoin pour améliorer les performances d’une fabrique de données en réutilisant un cluster HDInsight à la demande.<br/><br/>Si vous définissez la valeur de la propriété TimeToLive sur 0, le cluster est supprimé dès que l’exécution d’activité est terminée. Alors que, si vous définissez une valeur élevée, le cluster peut rester inactif pour vous permettre de vous connecter à des fins de dépannage, mais cela peut entraîner des coûts importants. Par conséquent, il est important de définir la valeur appropriée en fonction de vos besoins.<br/><br/>Plusieurs pipelines peuvent partager l’instance du cluster HDInsight à la demande si la valeur de la propriété timetolive est correctement définie. | OUI      |
| clusterType                  | Type du cluster HDInsight à créer. Valeurs autorisées : « hadoop » et « spark ». Si aucune valeur n’est spécifiée, la valeur par défaut est hadoop. Un cluster activé avec le Pack Sécurité Entreprise ne peut pas être créé à la demande. Utilisez plutôt un [cluster existant / apportez votre propre environnement de calcul](#azure-hdinsight-linked-service). | Non       |
| version                      | Version du cluster HDInsight. À défaut de spécification, la version actuelle par défaut de HDInsight est utilisée. | Non       |
| hostSubscriptionId           | ID d’abonnement Azure utilisé pour créer le cluster HDInsight. Si non spécifié, l’ID d’abonnement de votre contexte de connexion Azure est utilisé. | Non       |
| clusterNamePrefix           | Préfixe du nom de cluster HDI. Un horodatage est ajouté automatiquement à la fin du nom du cluster.| Non       |
| sparkVersion                 | Version de spark si le type de cluster est « Spark » | Non       |
| additionalLinkedServiceNames | Spécifie les comptes de stockage supplémentaires pour le service lié HDInsight afin que le service Data Factory puisse les enregistrer en votre nom. Ces comptes de stockage doivent être dans la même région que le cluster HDInsight, qui est créé dans la même région que le compte de stockage spécifié par linkedServiceName. | Non       |
| osType                       | Type de système d'exploitation. Les valeurs autorisées sont les suivantes : Linux et Windows (pour HDInsight 3.3 uniquement). Par défaut, c’est Linux. | Non       |
| hcatalogLinkedServiceName    | Le nom du service lié à SQL Azure pointant vers la base de données HCatalog. Le cluster HDInsight à la demande est créé en utilisant la base de données Azure SQL en tant que metastore. | Non       |
| connectVia                   | Runtime d’intégration à utiliser pour répartir les activités à ce service lié HDInsight. Pour le service lié HDInsight à la demande, il prend uniquement en charge un runtime d’intégration Azure. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non       |
| clusterUserName                   | Nom d’utilisateur pour accéder au cluster. | Non       |
| clusterPassword                   | Mot de passe sous forme de chaîne sécurisée pour accéder au cluster. | Non       |
| clusterSshUserName         | Nom d’utilisateur SSH pour se connecter à distance au nœud du cluster (pour Linux). | Non       |
| clusterSshPassword         | Mot de passe sous forme de chaîne sécurisée pour établir une connexion SSH à distance au nœud du cluster (pour Linux). | Non       |
| scriptActions | Spécifiez un script pour les [personnalisations de cluster HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) lors de la création d'un cluster à la demande. <br />Actuellement, l'outil de création d'interface utilisateur d'Azure Data Factory prend en charge la spécification d'une seule action de script, mais vous pouvez contourner cette limitation dans le JSON (spécifiez plusieurs actions de script dans le JSON). | Non |


> [!IMPORTANT]
> HDInsight prend en charge plusieurs versions de cluster Hadoop qui peuvent être déployées. Le choix d'une version crée une version spécifique de la distribution de la plateforme de données Hortonworks (HDP) et un ensemble de composants qui sont contenus dans cette distribution. La liste des versions de HDInsight prises en charge continue à être actualisée afin de fournir les correctifs et composants les plus récents de l’écosystème Hadoop. Assurez-vous de toujours faire référence aux informations les plus récentes sur la [version de HDInsight et le type de système d’exploitation pris en charge](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) pour être certain d’utiliser la version prise en charge de HDInsight. 
>
> [!IMPORTANT]
> Les services HDInsight liés ne prennent actuellement pas en charge les clusters HBase, Interactive Query (Hive LLAP) et Storm. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>Exemple JSON additionalLinkedServiceNames

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>Authentification d’un principal du service

Le service lié de HDInsight à la demande nécessite une authentification du principal du service pour créer des clusters HDInsight à votre place. Pour utiliser une authentification du principal du service, inscrivez une entité d’application dans Azure Active Directory (Azure AD), et octroyez-lui le rôle de **contributeur** de l’abonnement ou du groupe de ressources dans lequel le cluster HDInsight est créé. Pour une procédure détaillée, voir [Utiliser le portail pour créer une application et un principal du service Azure Active Directory pouvant accéder aux ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Prenez note des valeurs suivantes, qui vous permettent de définir le service lié :

- ID de l'application
- Clé de l'application 
- ID client

Utilisez l’authentification par principal de service en spécifiant les propriétés suivantes :

| Propriété                | Description                              | Obligatoire |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Spécifiez l’ID client de l’application.     | OUI      |
| **servicePrincipalKey** | Spécifiez la clé de l’application.           | OUI      |
| **client**              | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Vous pouvez le récupérer en pointant la souris dans le coin supérieur droit du portail Azure. | OUI      |

### <a name="advanced-properties"></a>Propriétés avancées

Vous pouvez également spécifier les propriétés suivantes pour la configuration granulaire du cluster HDInsight à la demande.

| Propriété               | Description                              | Obligatoire |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Spécifie les paramètres de configuration de base (par exemple, core-site.xml) pour le cluster HDInsight à créer. | Non       |
| hBaseConfiguration     | Spécifie les paramètres de configuration HBase (hbase-site.xml) pour le cluster HDInsight. | Non       |
| hdfsConfiguration      | Spécifie les paramètres de configuration HDFS (hdfs-site.xml) pour le cluster HDInsight. | Non       |
| hiveConfiguration      | Spécifie les paramètres de configuration Hive (hive-site.xml) pour le cluster HDInsight. | Non       |
| mapReduceConfiguration | Spécifie les paramètres de configuration MapReduce (mapred-site.xml) pour le cluster HDInsight. | Non       |
| oozieConfiguration     | Spécifie les paramètres de configuration Oozie (oozie-site.xml) pour le cluster HDInsight. | Non       |
| stormConfiguration     | Spécifie les paramètres de configuration Storm (storm-site.xml) pour le cluster HDInsight. | Non       |
| yarnConfiguration      | Spécifie les paramètres de configuration Yarn (yarn-site.xml) pour le cluster HDInsight. | Non       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exemple : configuration de cluster HDInsight à la demande avec les propriétés avancées

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
            "coreConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "hiveConfiguration": {
                "templeton.mapper.memory.mb": "5000"
            },
            "mapReduceConfiguration": {
                "mapreduce.reduce.java.opts": "-Xmx4000m",
                "mapreduce.map.java.opts": "-Xmx4000m",
                "mapreduce.map.memory.mb": "5000",
                "mapreduce.reduce.memory.mb": "5000",
                "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
            },
            "yarnConfiguration": {
                "yarn.app.mapreduce.am.resource.mb": "5000",
                "mapreduce.map.memory.mb": "5000"
            },
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

### <a name="node-sizes"></a>Tailles de nœuds
Vous pouvez spécifier les tailles du nœud principal, du nœud de données et du nœud zookeeper en utilisant les propriétés suivantes : 

| Propriété          | Description                              | Obligatoire |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Spécifie la taille du nœud principal. La valeur par défaut est : Standard_D3. Pour plus d’informations, consultez la section **Spécification des tailles de nœud**. | Non       |
| dataNodeSize      | Spécifie la taille du nœud de données. La valeur par défaut est : Standard_D3. | Non       |
| zookeeperNodeSize | Spécifie la taille du nœud ZooKeeper. La valeur par défaut est : Standard_D3. | Non       |

#### <a name="specifying-node-sizes"></a>Spécification des tailles de nœud
Pour connaître les valeurs des chaînes à spécifier pour les propriétés mentionnées dans la section précédente, consultez [Tailles des machines virtuelles](../virtual-machines/linux/sizes.md). Les valeurs doivent être conformes aux **applets de commande et API** référencées dans l’article. Comme vous pouvez le voir dans l’article, le nœud de données de grande taille (par défaut) a 7 Go de mémoire, ce qui risque de s’avérer insuffisant pour votre scénario. 

Si vous voulez créer des nœuds principaux et des nœuds worker de taille D4, spécifiez la valeur **Standard_D4** pour les propriétés headNodeSize et dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Si vous spécifiez une valeur incorrecte pour ces propriétés, vous risquez de rencontrer l’**erreur** suivante : Failed to create cluster. (Impossible de créer le cluster.) Exception : Impossible de terminer l’opération de création du cluster. Operation failed with code ’400’. Cluster left behind state (État du cluster abandonné) : 'Error' (« Error »). Message : 'PreClusterCreationValidationFailure'. Quand vous recevez ce message d’erreur, vérifiez que vous utilisez les noms d’**applet de commande et d’API** figurant dans l’article [Tailles des machines virtuelles](../virtual-machines/linux/sizes.md).        

## <a name="bring-your-own-compute-environment"></a>Apportez votre propre environnement de calcul
Dans ce type de configuration, les utilisateurs peuvent inscrire un environnement de calcul existant en tant que service lié dans Data Factory. L'environnement de calcul est géré par l'utilisateur et le service Data Factory l'utilise pour exécuter les activités.

Ce type de configuration est pris en charge pour les environnements de calcul suivants :

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Service Analytique Azure Data Lake
* Azure SQL DB, Azure SQL DW, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Service lié Azure HDInsight
Vous pouvez créer un service lié Azure HDInsight pour inscrire votre propre cluster HDInsight avec Data Factory.

### <a name="example"></a>Exemples

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
      "type": "HDInsight",
      "typeProperties": {
        "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>properties
| Propriété          | Description                                                  | Obligatoire |
| ----------------- | ------------------------------------------------------------ | -------- |
| Type              | La propriété de type doit être définie sur **HDInsight**.            | OUI      |
| clusterUri        | L'URI du cluster HDInsight.                            | OUI      |
| username          | Spécifiez le nom de l'utilisateur à utiliser pour se connecter à un cluster HDInsight existant. | OUI      |
| password          | Spécifiez le mot de passe du compte d'utilisateur.                       | OUI      |
| linkedServiceName | Nom du service lié de stockage Azure faisant référence au stockage Blob Azure utilisé par le cluster HDInsight. <p>Actuellement, vous ne pouvez pas spécifier un service lié Azure Data Lake Store pour cette propriété. Vous pouvez accéder aux données d’Azure Data Lake Store à partir de scripts Hive/Pig si le cluster HDInsight a accès à Data Lake Store. </p> | OUI      |
| isEspEnabled      | Spécifiez « *true* » si le cluster HDInsight est activé avec le [Pack Sécurité Entreprise](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction). La valeur par défaut est « *false* ». | Non       |
| connectVia        | Runtime d’intégration à utiliser pour répartir les activités à ce service lié. Vous pouvez utiliser un runtime d’intégration Azure ou un runtime d’intégration auto-hébergé. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. <br />Pour un cluster HDInsight activé avec le Pack Sécurité Entreprise, utilisez un runtime d'intégration auto-hébergé qui dispose d'une visibilité directe sur le cluster ou qui doit être déployé dans le même réseau virtuel que le cluster HDInsight activé avec le Pack Sécurité Entreprise. | Non       |

> [!IMPORTANT]
> HDInsight prend en charge plusieurs versions de cluster Hadoop qui peuvent être déployées. Le choix d'une version crée une version spécifique de la distribution de la plateforme de données Hortonworks (HDP) et un ensemble de composants qui sont contenus dans cette distribution. La liste des versions de HDInsight prises en charge continue à être actualisée afin de fournir les correctifs et composants les plus récents de l’écosystème Hadoop. Assurez-vous de toujours faire référence aux informations les plus récentes sur la [version de HDInsight et le type de système d’exploitation pris en charge](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) pour être certain d’utiliser la version prise en charge de HDInsight. 
>
> [!IMPORTANT]
> Les services HDInsight liés ne prennent actuellement pas en charge les clusters HBase, Interactive Query (Hive LLAP) et Storm. 
>
> 

## <a name="azure-batch-linked-service"></a>Service lié Azure Batch

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vous pouvez créer un service lié Azure Batch pour inscrire un pool de machines virtuelles (VM) Batch à une fabrique de données. Vous pouvez exécuter l’activité personnalisée à l’aide d’Azure Batch.

Consultez les rubriques suivantes si vous ne connaissez pas le service Azure Batch :

* [Présentation de base d’Azure Batch](../batch/batch-technical-overview.md) pour une vue d’ensemble du service Azure Batch.
* Applet de commande [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) pour créer un compte Azure Batch (ou) [Portail Azure](../batch/batch-account-create-portal.md) pour créer le compte Azure Batch à l’aide du portail Azure. Pour obtenir des instructions détaillées sur l'utilisation de l'applet de commande, consultez la rubrique [Utilisation de PowerShell pour gérer un compte Azure Batch](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) .
* Applet de commande [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) pour créer un pool Azure Batch.

### <a name="example"></a>Exemples

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>properties
| Propriété          | Description                              | Obligatoire |
| ----------------- | ---------------------------------------- | -------- |
| Type              | La propriété de type doit être définie sur **AzureBatch**. | OUI      |
| accountName       | Nom du compte Azure Batch.         | OUI      |
| accessKey         | Clé d'accès du compte Azure Batch.  | OUI      |
| batchUri          | URL de votre compte Azure Batch, au format https://*batchaccountname.region*.batch.azure.com. | OUI      |
| poolName          | Nom du pool de machines virtuelles.    | OUI      |
| linkedServiceName | Nom du service lié Azure Storage associé à ce service lié Azure Batch. Ce service lié est utilisé pour les fichiers intermédiaires requis pour exécuter l’activité. | OUI      |
| connectVia        | Runtime d’intégration à utiliser pour répartir les activités à ce service lié. Vous pouvez utiliser un runtime d’intégration Azure ou un runtime d’intégration auto-hébergé. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non       |

## <a name="azure-machine-learning-linked-service"></a>Service lié Microsoft Azure Machine Learning
Vous créez un service lié Azure Machine Learning pour inscrire un point de terminaison de notation par lot Machine Learning pour une fabrique de données.

### <a name="example"></a>Exemples

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>properties
| Propriété               | Description                              | Obligatoire                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Type                   | La propriété de type doit être définie sur : **AzureML**. | OUI                                      |
| mlEndpoint             | L'URL de la notation par lot.                   | OUI                                      |
| apiKey                 | L'API du modèle d'espace de travail publié.     | OUI                                      |
| updateResourceEndpoint | URL des ressources de mise à jour pour un point de terminaison du service web Azure Machine Learning utilisé pour mettre à jour le service web prédictif avec le fichier de modèle formé | Non                                       |
| servicePrincipalId     | Spécifiez l’ID client de l’application.     | Obligatoire si updateResourceEndpoint est spécifié |
| servicePrincipalKey    | Spécifiez la clé de l’application.           | Obligatoire si updateResourceEndpoint est spécifié |
| locataire                 | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Vous pouvez le récupérer en pointant la souris dans le coin supérieur droit du portail Azure. | Obligatoire si updateResourceEndpoint est spécifié |
| connectVia             | Runtime d’intégration à utiliser pour répartir les activités à ce service lié. Vous pouvez utiliser un runtime d’intégration Azure ou un runtime d’intégration auto-hébergé. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non                                       |

## <a name="azure-data-lake-analytics-linked-service"></a>Service lié Azure Data Lake Analytics
Vous créez un service lié **Analytique Azure Data Lake** pour lier un service de calcul Analytique Azure Data Lake Analytics à une fabrique de données Azure. L’activité U-SQL Analytique Data Lake dans le pipeline fait référence à ce service lié. 

### <a name="example"></a>Exemples

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>properties

| Propriété             | Description                              | Obligatoire                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| Type                 | La propriété de type doit être définie sur : **AzureDataLakeAnalytics**. | OUI                                      |
| accountName          | Nom du compte du service Analytique Azure Data Lake.  | OUI                                      |
| dataLakeAnalyticsUri | URI du service Analytique Azure Data Lake.           | Non                                       |
| subscriptionId       | ID d’abonnement Azure                    | Non                                       |
| nom_groupe_ressources    | Nom du groupe de ressources Azure                | Non                                       |
| servicePrincipalId   | Spécifiez l’ID client de l’application.     | OUI                                      |
| servicePrincipalKey  | Spécifiez la clé de l’application.           | OUI                                      |
| locataire               | Spécifiez les informations de locataire (nom de domaine ou ID de locataire) dans lesquels se trouve votre application. Vous pouvez le récupérer en pointant la souris dans le coin supérieur droit du portail Azure. | OUI                                      |
| connectVia           | Runtime d’intégration à utiliser pour répartir les activités à ce service lié. Vous pouvez utiliser un runtime d’intégration Azure ou un runtime d’intégration auto-hébergé. À défaut de spécification, le runtime d’intégration Azure par défaut est utilisé. | Non                                       |



## <a name="azure-databricks-linked-service"></a>Service Azure Databricks lié
Vous pouvez créer un **service Azure Databricks lié** pour inscrire l’espace de travail Databricks que vous utiliserez pour exécuter les charges de travail Databricks (notebooks).

### <a name="example---using-new-job-cluster-in-databricks"></a>Exemple : utilisation d’un nouveau cluster de travail dans Databricks

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "https://eastus.azuredatabricks.net",
            "newClusterNodeType": "Standard_D3_v2",
            "newClusterNumOfWorker": "1:10",
            "newClusterVersion": "4.0.x-scala2.11",
            "accessToken": {
                "type": "SecureString",
                "value": "dapif33c9c721144c3a790b35000b57f7124f"
            }
        }
    }
}

```

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Exemple : utilisation d’un cluster interactif existant dans Databricks

```json
{
    "name": " AzureDataBricksLinedService",
    "properties": {
      "type": " AzureDatabricks",
      "typeProperties": {
        "domain": "https://westeurope.azuredatabricks.net",
        "accessToken": {
            "type": "SecureString", 
            "value": "dapif33c9c72344c3a790b35000b57f7124f"
          },
        "existingClusterId": "{clusterId}"
        }
}

```

### <a name="properties"></a>properties

| Propriété             | Description                              | Obligatoire                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| Nom                 | Nom du service lié               | OUI   |
| Type                 | La propriété de type doit être définie sur : **AzureDatabricks**. | OUI                                      |
| domaine               | Spécifiez la région Azure en fonction de la région de l’espace de travail Databricks. Exemple : https://eastus.azuredatabricks.net | OUI                                 |
| accessToken          | Un jeton d’accès est requis pour que la fabrique de données s’authentifie auprès d’Azure Databricks. Un jeton d’accès doit être généré à partir de l’espace de travail Databricks. Des étapes plus détaillées pour rechercher le jeton d’accès sont disponibles [ici](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)  | OUI                                       |
| existingClusterId    | ID de cluster d’un cluster existant pour exécuter tous les travaux dessus. Il doit s’agit d’un cluster interactif déjà créé. Vous devrez peut-être redémarrer manuellement le cluster s’il ne répond pas. Databricks suggère d’exécuter des travaux sur les nouveaux clusters pour une plus grande fiabilité. Vous pouvez trouver l’ID de cluster d’un cluster interactif sur l’espace de travail Databricks -> Clusters -> Nom du cluster interactif -> Configuration -> Balises. [En savoir plus](https://docs.databricks.com/user-guide/clusters/tags.html) | Non 
| newClusterVersion    | La version Spark du cluster. Cela créera un cluster de travail dans Databricks. | Non  |
| newClusterNumOfWorker| Nombre de nœuds de travail que ce cluster doit avoir. Un cluster dispose d’un pilote de Spark et num_workers exécuteurs pour un total de num_workers + 1 nœuds Spark. Une chaîne au format Int32, telle que « 1 » signifie que numOfWorker est égal à 1 ou « 1:10 » signifie que la mise à l’échelle automatique à partir de 1 comme minimum et 10 comme maximum.  | Non                |
| newClusterNodeType   | Ce champ code, via une seule valeur, les ressources disponibles pour chacun des nœuds Spark de ce cluster. Par exemple, les nœuds Spark peuvent être configurés et optimisés pour des charges de travail gourmandes en mémoire ou en calcul. Ce champ est obligatoire pour les nouveaux clusters                | Non               |
| newClusterSparkConf  | un ensemble de paires clé-valeur de configuration Spark spécifiées par l’utilisateur et facultatives. Les utilisateurs peuvent également transmettre une chaîne d’options JVM supplémentaires au pilote et aux exécuteurs, respectivement via spark.driver.extraJavaOptions et spark.executor.extraJavaOptions. | Non  |
| newClusterInitScripts| un ensemble de scripts d’initialisation facultatifs définis par l’utilisateur pour le nouveau cluster. Spécification du chemin d'accès DBFS aux scripts init. | Non  |


## <a name="azure-sql-database-linked-service"></a>Service lié Azure SQL Database
Créez un service lié Azure SQL et utilisez-le avec l’ [activité de procédure stockée](transform-data-using-stored-procedure.md) pour appeler une procédure stockée à partir d’un pipeline Data Factory. Pour plus d’informations sur ce service lié, consultez la page [Connecteur SQL Azure](connector-azure-sql-database.md#linked-service-properties) .

## <a name="azure-sql-data-warehouse-linked-service"></a>Service lié Azure SQL Data Warehouse
Créez un service lié Azure SQL Data Warehouse et utilisez-le avec l’ [activité de procédure stockée](transform-data-using-stored-procedure.md) pour appeler une procédure stockée à partir d’un pipeline Data Factory. Pour plus d’informations sur ce service lié, consultez la page [Connecteur Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#linked-service-properties) .

## <a name="sql-server-linked-service"></a>Service lié SQL Server
Créez un service lié à SQL Server et utilisez-le avec l’ [activité de procédure stockée](transform-data-using-stored-procedure.md) pour appeler une procédure stockée à partir d’un pipeline Data Factory. Pour plus d’informations sur ce service lié, consultez la page [Connecteur SQL Server](connector-sql-server.md#linked-service-properties) .

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des activités de transformation prises en charge par Azure Data Factory, voir [Transformer des données](transform-data.md).
