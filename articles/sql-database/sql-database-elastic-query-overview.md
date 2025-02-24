---
title: Vue d’ensemble de la requête élastique Azure SQL Database | Microsoft Docs
description: Une requête élastique vous permet d’exécuter une requête Transact-SQL qui s’étend sur plusieurs bases de données.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
manager: craigg
ms.date: 07/01/2019
ms.openlocfilehash: 5188862c50895c8e3f1bdecb4e08d39409bb5f9e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491667"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Vue d’ensemble de la requête élastique Azure SQL Database (préversion)

La fonctionnalité de requête élastique, en préversion, vous permet d’exécuter une requête Transact-SQL qui s’étend sur plusieurs bases de données dans Azure SQL Database. Elle vous permet d’effectuer des requêtes de bases de données croisées pour accéder aux tables distantes et de connecter des outils Microsoft et tiers (Excel, PowerBI, Tableau, etc.) pour lancer des requêtes parmi des couches de données avec plusieurs bases de données. Cette fonctionnalité permet de mettre à l’échelle des requêtes à des couches Données de grande taille dans la base de données SQL et de visualiser les résultats dans les rapports de business intelligence (BI).

## <a name="why-use-elastic-queries"></a>Pourquoi utiliser les requêtes élastiques

### <a name="azure-sql-database"></a>Azure SQL Database

Interrogez plusieurs bases de données Azure SQL entièrement dans T-SQL. Cela permet d’interroger en lecture seule les bases de données distantes et donne aux clients SQL Server locaux actuels la possibilité de migrer des applications en utilisant des noms en trois et quatre parties ou un serveur lié à une base de données SQL.

### <a name="available-on-standard-tier"></a>Disponible au niveau standard

La fonctionnalité de requête élastique est prise en charge dans les niveaux de service Standard et Premium. Consultez la section relative aux limitations de la préversion ci-dessous pour connaître les limitations de performances pour les niveaux de service inférieurs.

### <a name="push-parameters-to-remote-databases"></a>Distribuer les paramètres Push aux bases de données distantes

Les requêtes élastiques peuvent désormais distribuer les paramètres Push SQL aux bases de données distantes pour l’exécution.

### <a name="stored-procedure-execution"></a>Exécution d’une procédure stockée

Exécutez des appels de procédures stockées ou de fonctions distantes avec [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714).

### <a name="flexibility"></a>Flexibilité

Les tables externes avec requête élastique peuvent faire référence à des tables distantes avec un schéma ou un nom de table différent.

## <a name="elastic-query-scenarios"></a>Scénarios de requête élastique

L’objectif est de simplifier les scénarios d’interrogation dans lesquels plusieurs bases de données fournissent des lignes, regroupées au sein d’un résultat global unique. Cette requête peut être composée directement par l’utilisateur ou l’application, ou indirectement via des outils connectés à la base de données élastique associée à la requête. Ceci est particulièrement utile pendant la création de rapports, à l’aide des outils commerciaux d’intégration BI ou de données, ou toute application qui ne peut pas être changée. Avec une requête élastique, vous pouvez interroger facilement plusieurs bases de données à l’aide de l’expérience de connectivité SQL Server familière d’outils tels qu’Excel, Power BI, Tableau ou Cognos.
Une requête de base de données élastique offre un accès facile à un ensemble complet de bases de données via des requêtes émises par SQL Server Management Studio ou Visual Studio, et permet l’interrogation des diverses bases de données croisées à partir d’Entity Framework ou d’autres environnements ORM. La figure 1 illustre un scénario dans lequel une application cloud existante (qui utilise la [bibliothèque cliente de base de données élastique](sql-database-elastic-database-client-library.md)) s’appuie sur une couche Données avec montée en charge et une requête élastique est utilisée pour les rapports entre plusieurs bases de données croisées.

**Figure 1** Requête élastique utilisée sur la couche de données mise à l’échelle

![Requête élastique utilisée sur la couche de données mise à l’échelle][1]

Les scénarios clients pour une requête élastique sont caractérisés par les topologies suivantes :

* **Partitionnement vertical - Requêtes sur plusieurs bases de données** (topologie 1) : les données sont partitionnées verticalement entre plusieurs bases de données d’une couche Données. En règle générale, les différents ensembles de tables résident sur des bases de données différentes. Cela signifie que le schéma est différent sur des bases de données différentes. Par exemple, toutes les tables d’inventaire se trouvent sur une base de données alors que toutes les tables liées à la comptabilité se trouvent dans une seconde base de données. Les scénarios d’utilisation courants avec cette topologie requièrent une interrogation ou la compilation de rapports englobant des tables de plusieurs bases de données.
* **Partitionnement horizontal - Partitionnement** (topologie 2) : les données sont partitionnées horizontalement pour répartir les lignes dans un niveau Données ayant fait l’objet d’un scale-out. Avec cette approche, le schéma est identique sur toutes les bases de données participantes. Cette approche est également appelée « partitionnement ». Ce partitionnement est effectué et géré à l’aide de (1) la bibliothèque d’outils de base de données élastique ou (2) la fonction d’auto-partitionnement. Une requête élastique est utilisée pour interroger ou compiler des rapports sur plusieurs partitions.

> [!NOTE]
> Une requête élastique est mieux adaptée aux scénarios de création de rapports où la plus grande partie du traitement (filtrage, agrégation) peut s’effectuer du côté de la source externe. Elle n’est pas adaptée aux opérations ETL où une grande quantité de données est transférée à partir de bases de données distantes. Pour les charges de travail de création de rapports intensive ou les scénarios d’entreposage de données avec des requêtes plus complexes, pensez à utiliser [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Le partitionnement vertical - requêtes de bases de données croisées

Pour commencer le codage, voir [Prise en main des requêtes de bases de données croisées (partitionnement vertical)](sql-database-elastic-query-getting-started-vertical.md).

Une requête élastique peut être utilisée pour mettre les données situées dans une base de données SQL à la disposition d’autres bases de données SQL. Ainsi, les requêtes issues d’une base de données peuvent faire référence à des tables dans n’importe quelle autre base de données SQL distante. La première étape consiste à définir une source de données externe pour chaque base de données distante. La source de données externe est définie dans la base de données locale à partir de laquelle vous souhaitez accéder aux tables situées sur la base de données distante. Aucune modification de la base de données distante n’est nécessaire. Pour les scénarios verticaux dans lesquels les différentes bases de données comportent des schémas différents, les requêtes élastiques peuvent être utilisées pour implémenter des scénarios d’utilisation courants tels que l’accès aux données de référence et l’interrogation de bases de données croisées.

> [!IMPORTANT]
> Vous devez posséder l’autorisation ALTER ANY EXTERNAL DATA SOURCE. Cette autorisation est incluse dans l’autorisation ALTER DATABASE. Les autorisations ALTER ANY EXTERNAL DATA SOURCE sont nécessaires pour faire référence à la source de données sous-jacente.
>

**Données de référence** : la topologie est utilisée pour la gestion des données de référence. Dans la figure ci-dessous, deux tables (T1 et T2) avec des données de référence sont conservées dans une base de données dédiée. Avec une requête élastique, vous pouvez désormais accéder aux tables T1 et T2 à distance depuis d’autres bases de données, comme indiqué dans la figure. Utilisez Topologie 1 si les tables de référence sont des requêtes de petite taille ou distantes se trouvant dans la table de référence et ayant des prédicats sélectifs.

**Figure 2** Partitionnement vertical -Utilisation d’une requête élastique pour interroger des données de référence

![Partitionnement vertical -Utilisation d’une requête élastique pour interroger des données de référence][3]

**Interrogation de plusieurs bases de données** : les requêtes élastiques autorisent les cas d’usage qui nécessitent l’interrogation de plusieurs bases de données SQL. La figure 3 présente quatre bases de données différentes : CRM, Inventaire, Ressources humaines et Produits. Les requêtes exécutées dans une des bases de données doivent également accéder à une ou à toutes les autres bases de données. Avec une requête élastique, vous pouvez configurer votre base de données pour ce cas en exécutant plusieurs instructions DDL simples sur chacune des quatre bases de données. Après cette configuration à usage unique, l’accès à une table distante se fait simplement en faisant référence à une table locale à partir de vos requêtes T-SQL ou de vos outils d’analyse décisionnelle. Cette approche est recommandée si les requêtes distantes ne renvoient pas de résultats volumineux.

**Figure 3** Partitionnement vertical - Utilisation de requête élastique pour l’interrogation de plusieurs bases de données

![Partitionnement vertical - Utilisation de requête élastique pour l’interrogation de plusieurs bases de données][4]

Les étapes suivantes servent à configurer des requêtes de base de données élastique pour des scénarios de partitionnement verticaux qui requièrent l’accès à une table située sur des bases de données SQL distantes avec le même schéma :

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource de **RDBMS**
* [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Après avoir exécuté les instructions DDL, vous pouvez accéder à la table distante « mytable » comme s’il s’agissait d’une table locale. Azure SQL Database ouvre automatiquement une connexion à la base de données distante, traite votre demande sur la base de données distante et retourne les résultats.

## <a name="horizontal-partitioning---sharding"></a>Partitionnement horizontal - partitionnement

L’utilisation d’une requête élastique destinée à effectuer des tâches de création de rapports sur une couche de données partitionnée exige un [mappage de partitionnement de base de données élastique](sql-database-elastic-scale-shard-map-management.md) pour représenter les bases de données de la couche de données. En règle générale, un seul mappage de partition est utilisé dans ce scénario, et une base de données partitionnée dédiée avec des fonctions d’interrogation élastique (nœud principal) sert de point d’entrée pour les requêtes de création de rapport. Seule cette base de données dédiée doit avoir accès à la table de partition. La figure 4 illustre cette topologie et sa configuration avec la base de données de requête élastique et du mappage de partition. Les bases de données de la couche de données peuvent appartenir à n’importe quelle version ou édition Azure SQL Database. Pour plus d’informations sur la bibliothèque cliente de base de données élastique, consultez [gestion de mappage de partition](sql-database-elastic-scale-shard-map-management.md).

**Figure 4** partitionnement horizontal : utilisation d’une requête élastique pour les rapports sur les couches de données partitionnées

![partitionnement horizontal : utilisation d’une requête élastique pour les rapports sur les couches de données partitionnées][5]

> [!NOTE]
> Une base de données élastique (nœud principal) peut être une base de données distincte, ou la même base de données qui héberge la carte de partitions.
> Quelle que soit la configuration choisie, assurez-vous que le niveau de service et la taille de calcul de cette base de données sont suffisamment élevés pour traiter la quantité attendue de demandes de connexion/requête.

Les étapes suivantes configurent des requêtes de base de données flexibles pour les scénarios de partitionnement horizontal qui requièrent l’accès à un ensemble de tables généralement situées sur plusieurs bases de données SQL distantes :

* [CREATE MASTER KEY](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) mycredential
* Créer un [mappage de partition](sql-database-elastic-scale-shard-map-management.md) représentant votre couche de données à l’aide d’une bibliothèque de base de données élastique cliente.
* [CREATE/DROP EXTERNAL DATA SOURCE](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource de type **SHARD_MAP_MANAGER**
* [CREATE/DROP EXTERNAL TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) mytable

Une fois que vous avez effectué ces opérations, vous pouvez accéder à la table partitionnée horizontalement « mytable » comme s’il s’agissait d’une table locale. Azure SQL Database ouvre automatiquement plusieurs connexions parallèles aux bases de données distantes dans laquelle dans lesquelles les tables sont stockées physiquement, traite les requêtes sur les bases de données distantes et retourne les résultats.
Vous trouverez d’autres informations sur les opérations requises pour le scénario de partitionnement horizontal dans [Requête élastique pour le partitionnement horizontal](sql-database-elastic-query-horizontal-partitioning.md).

Pour commencer le codage, voir [Prise en main d’une requête élastique pour le partitionnement horizontal (partitionnement)](sql-database-elastic-query-getting-started.md).

## <a name="t-sql-querying"></a>Requêtes T-SQL

Après avoir défini votre source de données externe et vos tables externes, vous pouvez utiliser les chaînes de connexion SQL Server pour vous connecter aux bases de données dans lesquelles vous avez défini vos tables externes. Vous pouvez ensuite exécuter des instructions T-SQL sur vos tables externes sur cette connexion avec les limitations décrites ci-dessous. Vous trouverez plus d’informations et des exemples de requêtes T-SQL dans les rubriques de la documentation sur le [partitionnement horizontal](sql-database-elastic-query-horizontal-partitioning.md) et le [partitionnement vertical](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Connectivité des outils

Vous pouvez utiliser des chaînes de connexion SQL Server standard pour connecter vos applications et les outils d’intégration BI ou données aux bases de données qui ont des tables externes. Assurez-vous que SQL Server est pris en charge comme source de données pour votre outil. Une fois connecté, vous pouvez traiter la base de données de requête élastique et les tables externes de cette base de données comme n’importe quelle autre base de données SQL Server à laquelle vous vous connectez avec votre outil.

> [!IMPORTANT]
> L’authentification à l’aide d’Azure Active Directory avec des requêtes élastiques n’est pas prise en charge actuellement.

## <a name="cost"></a>Coût

La requête élastique est incluse dans le coût des bases de données Azure SQL Database. Notez que les topologies dont les bases de données distantes se trouvent dans un centre de données autre que le point de terminaison de requête élastique sont prises en charge. Cependant, la sortie de données à partir de bases de données distantes est facturée régulièrement selon les [tarifs Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Limitations de la version préliminaire

* L’exécution de votre première requête élastique peut prendre quelques minutes sur le niveau de service Standard. Ce délai est nécessaire au chargement de la fonctionnalité de requête flexible ; les performances de chargement s’améliorent avec les niveaux de service et les tailles de calcul supérieurs.
* La rédaction de script de sources de données externes ou de tables externes à partir de SSMS ou SSDT n’est pas encore prise en charge.
* L’importation/exportation de base de données de SQL ne prend pas en charge les tables et sources de données externes. Si vous devez utiliser importer/exporter, supprimez ces objets avant l’exportation, puis recréez-les après l’importation.
* Une requête élastique prend actuellement en charge uniquement les accès en lecture seule à des tables externes. Vous pouvez toutefois utiliser des fonctionnalités T-SQL complètes sur la base de données dans laquelle la table externe est définie. Cela peut être utile, par exemple pour conserver les résultats temporaires à l’aide de SELECT <liste_colonnes> INTO <table_locale>, ou pour définir des procédures stockées dans la base de données de requêtes élastiques qui font référence à des tables externes.
* À l’exception de nvarchar (max), les types métier (y compris les types spatiaux) ne sont pas pris en charge dans les définitions de table externes. Pour résoudre ce problème, vous pouvez créer une vue sur la base de données distante qui convertit le type LOB en nvarchar (max), définir votre table externe sur la vue au lieu de la table de base, et puis effectuer un cast vers le type LOB d’origine dans vos requêtes.
* Les colonnes de type de données nvarchar(max) dans le jeu de résultats désactivent la technique avancée de traitement par lot utilisée dans l’implémentation de la requête élastique et peuvent affecter les performances d’une requête pour un ordre de grandeur, ou même deux ordres de grandeur dans des cas d’usage non canoniques où une grande quantité de données non agrégées est transférée à la suite d’une requête.
* Les statistiques des colonnes via les tables externes ne sont pas prises en charge actuellement. Les statistiques des tables sont prises en charge, mais doivent être créées manuellement.
* Une requête élastique fonctionne uniquement avec Azure SQL Database. Vous ne pouvez pas l’utiliser pour interroger l’instance Microsoft SQL Server locale, ou SQL Server dans une machine virtuelle.

## <a name="feedback"></a>Commentaires

Partagez vos commentaires sur votre expérience des requêtes élastiques avec nous ci-dessous, sur les forums MSDN ou sur Stack Overflow. Nous souhaitons connaître votre avis concernant le service (défauts, améliorations possibles, lacunes).

## <a name="next-steps"></a>Étapes suivantes

* Pour le didacticiel sur le partitionnement vertical, consultez [Prise en main des requêtes de bases de données croisées (partitionnement vertical)](sql-database-elastic-query-getting-started-vertical.md).
* Pour la syntaxe et des exemples de requêtes pour des données partitionnées verticalement, consultez [Requêtes sur des données partitionnées verticalement](sql-database-elastic-query-vertical-partitioning.md)
* Pour commencer le didacticiel sur le partitionnement horizontal (sharding), consultez [Prise en main des requêtes élastiques pour le partitionnement horizontal (sharding)](sql-database-elastic-query-getting-started.md).
* Pour la syntaxe et des exemples de requêtes pour des données partitionnées horizontalement, consultez [Requêtes sur des données partitionnées horizontalement](sql-database-elastic-query-horizontal-partitioning.md)
* Consultez [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714) pour une procédure stockée qui exécute une instruction Transact-SQL sur une seule base de données Azure SQL distante ou un ensemble de bases de données servant de partitions dans un schéma de partitionnement horizontal.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
