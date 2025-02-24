---
title: Écritures accélérées pour Apache HBase dans Azure HDInsight
description: Offre une vue d’ensemble de la fonctionnalité Écritures accélérées dans Azure HDInsight, qui utilise des disques managés Premium pour améliorer les performances du journal WAL (write-ahead log) Apache HBase.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 219899c2e336f544ff6572589cc79f84f555490d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233840"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Écritures accélérées pour Apache HBase dans Azure HDInsight

Cet article fournit des informations sur la fonctionnalité **Écritures accélérées** pour Apache HBase dans Azure HDInsight et explique comment elle peut être utilisée efficacement pour améliorer les performances en écriture. **Écritures accélérées** utilise les [disques managés SSD Premium Azure](../../virtual-machines/linux/disks-types.md#premium-ssd) pour améliorer les performances du journal WAL (write-ahead log) Apache HBase. Pour en savoir plus sur Apache HBase, consultez [Nouveautés d’Apache HBase dans Azure HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Vue d’ensemble de l’architecture HBase

Dans HBase, une **ligne** se compose d’une ou plusieurs **colonnes** et est identifiée par une **clé de ligne**. Plusieurs lignes constituent une **table**. Les colonnes contiennent des **cellules**, qui sont des versions horodatées de la valeur de cette colonne. Les colonnes sont regroupées en **familles de colonnes**, et toutes les colonnes d’une même famille sont stockées dans des fichiers de stockage appelés **HFiles**.

Dans HBase, les **régions** servent à équilibrer la charge de traitement des données. HBase commence par stocker les lignes d’une table dans une seule région. Les lignes sont réparties sur plusieurs régions à mesure que le volume de données de la table augmente. Les **serveurs de région** peuvent gérer les demandes de plusieurs régions.

## <a name="write-ahead-log-for-apache-hbase"></a>Journal WAL (write-ahead log) pour Apache HBase

HBase commence par écrire des mises à jour de données sur un type de journal de validation appelé journal WAL (write-ahead log). Une fois la mise à jour stockée dans le journal WAL (write-ahead log), celle-ci est écrite dans **MemStore** en mémoire. Lorsque la capacité maximale des données en mémoire est atteinte, celles-ci sont écrites sur le disque sous forme de fichier **HFile**.

Si un **RegionServer** se bloque ou devient indisponible avant que MemStore ne soit vidé, le journal WAL (write-ahead log) peut être utilisé pour réexécuter les mises à jour. En l’absence de journal WAL (write-ahead log), si un **RegionServer** tombe en panne avant d’avoir vidé les mises à jour vers un fichier **HFile**, toutes ces mises à jour sont perdues.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Fonctionnalité Écritures accélérées pour Apache HBase dans Azure HDInsight

La fonctionnalité Écritures accélérées résout le problème des latences en écriture plus élevées en raison de l’utilisation des journaux WAL (write-ahead log) stockés dans le cloud.  La fonctionnalité Écritures accélérées pour les clusters HDInsight Apache HBase associe des disques SSD managés Premium à chaque serveur de région (nœud Worker). Les journaux WAL (write-ahead log) sont ensuite écrits sur le système de fichiers DFS hadoop montés sur ces disques SSD managés Premium et non pas dans le stockage cloud.  Les disques managés Premium utilisent des disques SSD et offrent d’excellentes performances d’E/S, ainsi qu’une tolérance de panne.  Contrairement aux disques non managés, si une unité de stockage tombe en panne, elle n’affecte pas les autres unités de stockage du même groupe à haute disponibilité.  Par conséquent, les disques managés offrent une faible latence en écriture et une meilleure résilience pour vos applications. Pour en savoir plus sur les disques managés Azure, consultez [Présentation des disques managés Azure](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Comment activer la fonctionnalité Écritures accélérées pour HBase dans HDInsight

Pour créer un nouveau cluster HBase avec la fonctionnalité Écritures accélérées, suivez les étapes de [Configurer des clusters dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) jusqu’à l’**étape 3 relative au stockage**. Sous **Paramètres de Metastore**, cochez la case en regard de **Activer les écritures accélérées HBase (préversion)** . Puis, passez aux étapes restantes pour la création du cluster.

![Activez l’option Écritures accélérées pour HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/accelerated-writes-cluster-creation.png)

## <a name="other-considerations"></a>Autres points à considérer

Pour préserver la durabilité des données, créez un cluster comportant au moins trois nœuds Worker. Une fois créé, vous ne pourrez plus réduire ne nombre de nœuds Worker du cluster.

Videz ou désactivez vos tables HBase avant de supprimer le cluster, afin de ne pas perdre les données du journal WAL (write-ahead log).

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>Étapes suivantes

* Documentation officielle relative à Apache HBase sur la [fonctionnalité Journal WAL (write ahead log)](https://hbase.apache.org/book.html#wal)
* Pour mettre à niveau votre cluster HDInsight Apache HBase de manière à utiliser la fonctionnalité Écritures accélérées, consultez [Effectuer la migration d’un cluster Apache HBase vers une nouvelle version](apache-hbase-migrate-new-version.md).
