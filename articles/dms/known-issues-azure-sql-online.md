---
title: Article sur les problèmes connus/limitations de migration dans le cadre des migrations en ligne vers Azure SQL Database | Microsoft Docs
description: Apprenez-en plus sur les problèmes connus/limitations de migration dans le cadre des migrations en ligne vers Azure SQL Database.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/09/2019
ms.openlocfilehash: 00ed2f20884c3cd8f49307bd726f14f3007f884f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60534423"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-db"></a>Problèmes connus/limitations de migration dans le cadre des migrations en ligne vers Azure SQL DB

Le présent article décrit les problèmes connus et limitations associés aux migrations en ligne de SQL Server vers Azure SQL Database.

> [!IMPORTANT]
> Avec les migrations en ligne de SQL Server pour la base de données SQL Azure, la migration de types de données SQL_variant n’est pas pris en charge.

### <a name="migration-of-temporal-tables-not-supported"></a>Migration des tables temporelles non prise en charge

**Symptôme**

Si votre base de données source se compose d’une ou plusieurs tables temporelles, votre migration de base de données échoue pendant l’opération de « chargement complet des données ». Le message suivant peut alors s’afficher :

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Exemple d’erreur avec des tables temporelles](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Solution de contournement**

1. Recherchez les tables temporelles dans votre schéma source à l’aide de la requête ci-dessous.
     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```
2. Excluez ces tables à partir du panneau **Configure migration settings** (Configurer les paramètres de migration), dans lequel vous spécifiez les tables pour la migration.

3. Réexécutez l’activité de migration.

**Ressources**

Pour plus d’informations, consultez l’article [Tables temporelles](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).
 
### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>Migration de tables incluant une ou plusieurs colonnes avec le type de données hierarchyid

**Symptôme**

Vous pouvez voir une exception SQL suggérer que ntext est incompatible avec le type hierarchyid pendant l’opération de « chargement complet des données » :
     
![Exemple d’erreur avec hierarchyid](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Solution de contournement**

1. Recherchez les tables d’utilisateur incluant des colonnes avec le type de données hierarchyid à l’aide de la requête ci-dessous.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ``` 

2. Excluez ces tables à partir du panneau **Configure migration settings** (Configurer les paramètres de migration), dans lequel vous spécifiez les tables pour la migration.

3. Réexécutez l’activité de migration.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Échecs de migration avec différentes violations d’intégrité avec des déclencheurs actifs dans le schéma pendant le « chargement complet des données » ou la « synchronisation des données incrémentielles »

**Solution de contournement**

1. Recherchez les déclencheurs actuellement actifs dans la base de données source à l’aide de la requête ci-dessous :

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Désactivez les déclencheurs sur votre base de données source à l’aide de la procédure fournie dans l’article [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Réexécutez l’activité de migration.

### <a name="support-for-lob-data-types"></a>Prise en charge des types de données LOB

**Symptôme**

Si la longueur de la colonne LOB (Large Object) dépasse 32 Ko, les données peuvent être tronquées au niveau de la cible. Vous pouvez vérifier la longueur de la colonne LOB à l’aide de cette requête : 

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Solution de contournement**

Si vous disposez d’une colonne LOB de plus de 32 Ko, contactez l’équipe d’ingénierie en cliquant ici : [Demander à l’équipe de migration de base de données Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problèmes avec les colonnes timestamp

**Symptôme**

DMS ne fait pas migrer la valeur timestamp source. En fait, DMS génère une nouvelle valeur timestamp dans la table cible.

**Solution de contournement**

Si vous souhaitez que DMS fasse migrer la valeur timestamp exacte stockée dans la table source, contactez l’équipe d’ingénierie pour [Poser des questions sur les migrations de base de données Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Les erreurs de migration de données ne fournissent pas de détails supplémentaires sur le panneau d’état détaillé des bases de données.

**Symptôme**

Lorsque des échecs de migration apparaissent dans l’affichage d’état détaillé des bases de données et que vous sélectionnez le lien **Erreurs de migration des données** dans le ruban supérieur, vous n’obtenez pas de détails supplémentaires spécifiques aux échecs de migration.

![exemple d’absence de détails concernant des erreurs de migration des données](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Solution de contournement**

Pour obtenir des détails spécifiques aux échecs, suivez les étapes ci-dessous.

1. Fermez le panneau d’état détaillé des bases de données pour afficher l’écran d’activité de migration.

     ![écran d’activité de migration](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Sélectionnez **See error details** (Consulter le détail des erreurs) pour afficher les messages d’erreur spécifiques et ainsi résoudre plus facilement les erreurs de migration.
