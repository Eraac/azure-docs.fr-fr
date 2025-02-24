---
title: Pilote Azure Blob File System pour Azure Data Lake Storage Gen2
description: Pilote ABFS pour Hadoop FileSystem
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 50d39aa3c6341e61e383c5584ab2992e3fea3189
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278084"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Pilote Azure Blob FileSystem (ABFS) : un pilote Stockage Azure dédié pour Hadoop

[Hadoop FileSystem](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html) constitue l’une des principales méthodes d’accès aux données dans Azure Data Lake Storage Gen2. Data Lake Storage Gen2 permet aux utilisateurs du stockage Blob Azure d'accéder à un nouveau pilote, le pilote Azure Blob File System ou `ABFS`. ABFS fait partie d’Apache Hadoop et est inclus dans la plupart des distributions commerciales de Hadoop. Grâce à ce pilote, de nombreuses applications et infrastructures peuvent accéder aux données du stockage Blob Azure, sans nécessiter de code faisant explicitement référence à Data Lake Storage Gen2.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Fonctionnalité préalable : pilote Windows Azure Storage Blob

Au départ, c’est le [pilote WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) (Windows Azure Storage Blob) qui prenait en charge le stockage Blob Azure. Il avait pour tâche complexe de mapper la sémantique du système de fichiers (conformément à l’interface Hadoop FileSystem) à celle de l’interface de style « magasin d’objets » exposée par Stockage Blob Azure. Ce pilote continue à prendre en charge ce modèle et fournit un accès très performant aux données stockées dans les objets blob. Il est toutefois difficile de le tenir à jour, car la quantité de code nécessaire au mappage est très importante. Par ailleurs, quand certaines opérations comme [FileSystem.rename()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) et [FileSystem.delete()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) sont appliquées à des répertoires, le pilote doit effectuer un grand nombre d’opérations (les magasins d’objet ne prenant pas en charge les répertoires), ce qui aboutit souvent à une dégradation des performances. Le pilote ABFS a été conçu pour combler les lacunes inhérentes à WASB.

## <a name="the-azure-blob-file-system-driver"></a>Pilote Azure Blob File System

[L’interface REST Azure Data Lake Storage](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) est conçue pour prendre en charge la sémantique du système de fichiers sur Stockage Blob Azure. Étant donné que Hadoop FileSystem est conçu pour prendre en charge la même sémantique, aucun mappage complexe ne doit avoir lieu dans le pilote. Le pilote Azure Blob File System (ou ABFS) est donc un simple shim client pour l’API REST.

Le pilote doit toutefois effectuer certaines opérations :

### <a name="uri-scheme-to-reference-data"></a>Schéma d’URI pour référencer les données

Conformément à d’autres implémentations de FileSystem dans Hadoop, le pilote ABFS définit son propre schéma d’URI pour que les ressources (fichiers et répertoires) puissent être adressées distinctement. Le schéma d’URI est documenté dans [Utiliser l’URI Azure Data Lake Storage Gen2](./data-lake-storage-introduction-abfs-uri.md). L’URI a la structure suivante : `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Grâce au format d’URI ci-dessus, des outils et des frameworks Hadoop standard peuvent être utilisés pour référencer ces ressources :

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

En interne, le pilote ABFS traduit la ou les ressources spécifiées dans l’URI en fichiers et répertoires, puis effectue des appels à l’API REST Azure Data Lake Storage avec ces références.

### <a name="authentication"></a>Authentication

Le pilote ABFS prend en charge deux types d’authentification. L’application Hadoop peut donc accéder de manière sécurisée aux ressources contenues dans un compte compatible avec Azure Data Lake Storage Gen2. Vous trouverez des informations complètes sur les schémas d’authentification disponibles dans le [guide de sécurité de Stockage Azure](../common/storage-security-guide.md). Il s'agit de :

- **Clé partagée :** celle-ci permet aux utilisateurs d’accéder à toutes les ressources contenues dans le compte. La clé est chiffrée et stockée dans la configuration Hadoop.

- **Jeton du porteur OAuth Azure Active Directory :** les jetons de porteur Azure AD sont obtenus et mis à jour par le pilote à l’aide de l’identité de l’utilisateur final ou d’un principal de service configuré. Avec ce modèle d’authentification, l’accès est autorisé appel par appel à l’aide de l’identité associée au jeton fourni et vérifiée par rapport à la liste de contrôle d’accès POSIX attribuée.

### <a name="configuration"></a>Configuration

La configuration du pilote ABFS est entièrement stockée dans le fichier de configuration <code>core-site.xml</code>. Sur les distributions Hadoop proposant [Ambari](https://ambari.apache.org/), la configuration peut également être gérée à l’aide du portail web ou de l’API REST Ambari.

Les détails de toutes les entrées de configuration prises en charge sont spécifiés dans la [documentation Hadoop officielle](https://hadoop.apache.org/docs/r3.2.0/hadoop-azure/abfs.html).

### <a name="hadoop-documentation"></a>Documentation Hadoop

Le pilote ABFS est présenté au complet dans la [documentation Hadoop officielle](https://hadoop.apache.org/docs/r3.2.0/hadoop-azure/abfs.html).

## <a name="next-steps"></a>Étapes suivantes

- [Créer un cluster Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)
- [Utiliser l’URI Azure Data Lake Storage Gen2](./data-lake-storage-introduction-abfs-uri.md)
