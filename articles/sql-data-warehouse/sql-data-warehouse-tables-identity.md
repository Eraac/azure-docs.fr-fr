---
title: Utilisation de la propriété IDENTITY pour créer des clés de substitution - Azure SQL Data Warehouse | Microsoft Docs
description: Suggestions et exemples d’utilisation de la propriété IDENTITY pour créer des clés de substitution dans des tables Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/30/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 19a06d0fdff324dc3bee246ef7a5a7011c089872
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65851595"
---
# <a name="using-identity-to-create-surrogate-keys-in-azure-sql-data-warehouse"></a>Utilisation de la propriété IDENTITY pour créer des clés de substitution dans Azure SQL Data Warehouse

Suggestions et exemples d’utilisation de la propriété IDENTITY pour créer des clés de substitution dans des tables Azure SQL Data Warehouse.

## <a name="what-is-a-surrogate-key"></a>Qu’est-ce qu’une clé de substitution ?

Une clé de substitution dans une table est une colonne avec un identificateur unique pour chaque ligne. La clé n’est pas générée à partir des données de la table. Les modélisateurs de données aiment créer des clés de substitution sur leurs tables lorsqu’ils conçoivent des modèles d’entrepôt de données. Vous pouvez utiliser la propriété IDENTITY pour atteindre cet objectif de manière simple et efficace, sans affecter les performances de chargement.  

## <a name="creating-a-table-with-an-identity-column"></a>Création d’une table avec une colonne IDENTITY

La propriété IDENTITY est conçue pour augmenter la taille des instances sur toutes les distributions de l’entrepôt de données sans affecter les performances de chargement. Par conséquent, l’implémentation d’IDENTITY est adaptée pour atteindre ces objectifs.

Vous pouvez définir une table ayant la propriété IDENTITY lorsque vous créez la table à l’aide d’une syntaxe similaire à l’instruction suivante :

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Vous pouvez ensuite utiliser `INSERT..SELECT` pour remplir la table.

Le reste de cette section met en évidence les nuances de l’implémentation pour vous aider à mieux les comprendre.  

### <a name="allocation-of-values"></a>Allocation de valeurs

La propriété IDENTITY ne garantit pas l’ordre dans lequel les valeurs de substitution sont alloués, ce qui reflète le comportement de SQL Server et d’Azure SQL Database. Toutefois, dans Azure SQL Data Warehouse, l’absence de garantie est plus marquée.

L’exemple suivant en est une illustration :

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

Dans l’exemple précédent, les deux lignes sont arrivées dans la distribution 1. La première ligne a la valeur de substitution 1 dans la colonne `C1`, et la deuxième la valeur de substitution 61. Ces deux valeurs ont été générées par la propriété IDENTITY. Toutefois, l’allocation des valeurs n’est pas contiguë. Ce comportement est normal.

### <a name="skewed-data"></a>Données décalées

La plage de valeurs pour le type de données est répartie uniformément entre les distributions. Si une table distribuée subit un décalage de données, la plage de valeurs disponibles pour le type de données peut être épuisée prématurément. Par exemple, si toutes les données se terminent dans une seule distribution, la table a accès à seulement un sixième des valeurs du type de données. Pour cette raison, la propriété IDENTITY se limite aux types de données `INT` et `BIGINT` uniquement.

### <a name="selectinto"></a>SELECT .. INTO

Lorsqu’une colonne IDENTITY existante est sélectionnée dans une nouvelle table, la nouvelle colonne hérite de la propriété IDENTITY, sauf si une des conditions suivantes est remplie :

- L’instruction SELECT contient une jointure.
- Plusieurs instructions SELECT sont jointes à l’aide d’UNION.
- La colonne IDENTITY est répertoriée plusieurs fois dans la liste SELECT.
- La colonne SELECT fait partie d’une expression.

Si l’une de ces conditions est vraie, la colonne est créée comme étant NOT NULL au lieu d’hériter de la propriété IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

CREATE TABLE AS SELECT (CTAS) suit le même comportement SQL Server que celui documenté pour SELECT..INTO. Toutefois, vous ne pouvez pas spécifier de propriété IDENTITY dans la définition de la colonne de la partie `CREATE TABLE` de l’instruction. Vous ne pouvez pas non plus utiliser la fonction IDENTITY dans la partie `SELECT` de l’instruction CTAS. Pour remplir une table, vous devez utiliser `CREATE TABLE` pour définir la table suivie de l’instruction `INSERT..SELECT`.

## <a name="explicitly-inserting-values-into-an-identity-column"></a>Insérer explicitement des valeurs dans une colonne IDENTITY

SQL Data Warehouse prend en charge la syntaxe `SET IDENTITY_INSERT <your table> ON|OFF`. Vous pouvez utiliser cette syntaxe pour insérer explicitement des valeurs dans la colonne IDENTITY.

Nombreux sont les modélisateurs de données à aimer utiliser des valeurs négatives prédéfinies pour certaines lignes dans leurs dimensions. Un exemple est la ligne -1 ou « membre inconnu ».

Le script suivant montre comment ajouter explicitement cette ligne à l’aide de SET IDENTITY_INSERT :

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>Chargement des données

La présence de la propriété IDENTITY a des conséquences sur votre code de chargement des données. Cette section met en évidence certains modèles de base pour charger des données dans les tables à l’aide d’IDENTITY.

Pour charger des données dans une table et générer une clé de substitution à l’aide d’IDENTITY, créez la table et utilisez l’instruction INSERT... SELECT ou INSERT... VALEURS pour effectuer le chargement.

L’exemple suivant met en évidence le modèle de base :

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> Il n’est pas possible d’utiliser `CREATE TABLE AS SELECT` actuellement lors du chargement des données dans une table comportant une colonne IDENTITY.
>

Pour plus d’informations sur le chargement de données, consultez [Conception de processus ELT pour Azure SQL Data Warehouse](design-elt-data-loading.md) et [Meilleures pratiques de chargement](guidance-for-loading-data.md).

## <a name="system-views"></a>Vues système

Vous pouvez utiliser la vue de catalogue [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql) pour identifier une colonne qui comporte la propriété IDENTITY.

Pour vous aider à mieux comprendre la structure de la base de données, cet exemple montre comment intégrer sys.identity_column à d’autres vues du catalogue système :

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Limites

La propriété IDENTITY ne peut pas être utilisée :

- Lorsque le type de données de colonne n’est pas INT ou BIGINT
- Lorsque la colonne est également la clé de distribution
- Lorsque la table est une table externe

Les fonctions associées suivantes ne sont pas prises en charge dans SQL Data Warehouse :

- [IDENTITY()](/sql/t-sql/functions/identity-function-transact-sql)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql)

## <a name="common-tasks"></a>Tâches courantes

Cette section fournit un exemple de code que vous pouvez utiliser pour effectuer des tâches courantes, lorsque vous travaillez avec des colonnes IDENTITY.

La colonne C1 est la colonne IDENTITY dans toutes les tâches suivantes.

### <a name="find-the-highest-allocated-value-for-a-table"></a>Rechercher la valeur allouée la plus élevée d’une table

Utilisez la fonction `MAX()` pour déterminer la valeur maximale allouée d’une table distribuée :

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Rechercher la valeur initiale et l’incrément de la propriété IDENTITY

Vous pouvez utiliser les affichages catalogue pour découvrir les valeurs de configuration de la valeur initiale et de l’incrément d’identité d’une table à l’aide de la requête suivante :

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des tables](/azure/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [CREATE TABLE (Transact-SQL) IDENTITY (Propriété)](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest)
- [DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql)
