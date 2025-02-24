---
title: Utilisation de transactions dans Azure SQL Data Warehouse | Microsoft Docs
description: Conseils relatifs à l’implémentation de transactions dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/22/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 7f00f8a25d0abf3af6d76b372b44145546a79879
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479607"
---
# <a name="using-transactions-in-sql-data-warehouse"></a>Utilisation de transactions dans SQL Data Warehouse
Conseils relatifs à l’implémentation de transactions dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions.

## <a name="what-to-expect"></a>À quoi s’attendre
Comme vous le savez, SQL Data Warehouse prend en charge les transactions dans le cadre de la charge de travail de l’entrepôt de données. Toutefois, pour garantir que les performances de SQL Data Warehouse sont maintenues à l’échelle, certaines fonctionnalités sont limitées, par rapport à SQL Server. Cet article identifie les différences et répertorie les autres éléments disponibles. 

## <a name="transaction-isolation-levels"></a>Niveaux d’isolation des transactions
SQL Data Warehouse implémente les transactions ACID. Toutefois, le niveau d’isolation de la prise en charge transactionnelle est limité à READ UNCOMMITTED ; ce niveau ne peut pas être changé. Si READ UNCOMMITTED pose un problème, vous pouvez implémenter plusieurs méthodes de codage pour éviter les lectures erronées des données. Les méthodes les plus répandues utilisent la commande CTAS et le basculement des partitions de table (souvent appelé « modèle de fenêtre glissante ») afin d’empêcher les utilisateurs d’interroger les données en cours de préparation. Les vues qui appliquent un préfiltre aux données sont une autre approche répandue.  

## <a name="transaction-size"></a>Taille de la transaction
Une transaction de modification de données unique est limitée en taille. La limite est appliquée par distribution. Par conséquent, l’allocation totale peut être calculée en multipliant la limite par le nombre de distributions. Pour évaluer approximativement le nombre maximal de lignes dans la transaction, divisez la limite de la distribution par la taille totale de chaque ligne. Pour les colonnes de longueur variable, pensez à prendre une longueur de colonne moyenne au lieu d’utiliser la taille maximale.

Dans le tableau ci-dessous, les hypothèses suivantes ont été formulées :

* Une distribution égale des données s’est produite 
* La longueur de ligne moyenne est de 250 octets

## <a name="gen2"></a>Deuxième génération

| [DWU](sql-data-warehouse-overview-what-is.md) | Limite par distribution (Go) | Nombre de distributions | Taille de transaction MAX (Go) | Nombre de lignes par distribution | Nombre de lignes max par transaction |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4 000 000 |240 000 000 |
| DW200c |1.5 |60 |90 |6 000 000 |360 000 000 |
| DW300c |2.25 |60 |135 |9 000 000 |540 000 000 |
| DW400c |3 |60 |180 |12 000 000 |720 000 000 |
| DW500c |3,75 |60 |225 |15 000 000 |900 000 000 |
| DW1000c |7.5 |60 |450 |30 000 000 |1 800 000 000 |
| DW1500c |11,25 |60 |675 |45 000 000 |2 700 000 000 |
| DW2000c |15 |60 |900 |60 000 000 |3 600 000 000 |
| DW2500c |18,75 |60 |1 125 |75 000 000 |4 500 000 000 |
| DW3000c |22,5 |60 |1 350 |90 000 000 |5 400 000 000 |
| DW5000c |37,5 |60 |2 250 |150 000 000 |9 000 000 000 |
| DW6000c |45 |60 |2 700 |180 000 000 |10 800 000 000 |
| DW7500c |56,25 |60 |3 375 |225 000 000 |13 500 000 000 |
| DW10000c |75 |60 |4 500 |300 000 000 |18 000 000 000 |
| DW15000c |112,5 |60 |6 750 |450 000 000 |27 000 000 000 |
| DW30000c |225 |60 |13 500 |900 000 000 |54 000 000 000 |

## <a name="gen1"></a>Première génération

| [DWU](sql-data-warehouse-overview-what-is.md) | Limite par distribution (Go) | Nombre de distributions | Taille de transaction MAX (Go) | Nombre de lignes par distribution | Nombre de lignes max par transaction |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4 000 000 |240 000 000 |
| DW200 |1.5 |60 |90 |6 000 000 |360 000 000 |
| DW300 |2.25 |60 |135 |9 000 000 |540 000 000 |
| DW400 |3 |60 |180 |12 000 000 |720 000 000 |
| DW500 |3,75 |60 |225 |15 000 000 |900 000 000 |
| DW600 |4.5 |60 |270 |18 000 000 |1 080 000 000 |
| DW1000 |7.5 |60 |450 |30 000 000 |1 800 000 000 |
| DW1200 |9 |60 |540 |36 000 000 |2 160 000 000 |
| DW1500 |11,25 |60 |675 |45 000 000 |2 700 000 000 |
| DW2000 |15 |60 |900 |60 000 000 |3 600 000 000 |
| DW3000 |22,5 |60 |1 350 |90 000 000 |5 400 000 000 |
| DW6000 |45 |60 |2 700 |180 000 000 |10 800 000 000 |

La limite de taille de transaction est appliquée par transaction ou opération. Elle n’est pas appliquée à toutes les transactions simultanées. Par conséquent, chaque transaction est autorisée à écrire cette quantité de données dans le journal. 

Pour optimiser et réduire la quantité de données écrites dans le journal, consultez l’article [Bonnes pratiques relatives aux transactions](sql-data-warehouse-develop-best-practices-transactions.md).

> [!WARNING]
> La taille de transaction maximale ne peut être obtenue que pour les tables distribuées HASH ou ROUND_ROBIN où la répartition des données est égale. Si la transaction écrit les données de manière asymétrique dans les distributions, alors la limite est susceptible d’être atteinte avant la taille de transaction maximale.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>État des transactions
SQL Data Warehouse utilise la fonction XACT_STATE() pour signaler l’échec d’une transaction, en utilisant la valeur -2. Cette valeur signifie que la transaction a échoué et est marquée pour une restauration uniquement.

> [!NOTE]
> L’association de la valeur -2 à la fonction XACT_STATE afin de signaler l’échec d’une transaction constitue un comportement différent par rapport à SQL Server. SQL Server utilise la valeur -1 pour représenter une transaction non validable. De plus, il peut tolérer la présence de certaines erreurs au sein d’une transaction sans pour autant signaler que cette dernière ne peut pas être validée. Par exemple, la valeur `SELECT 1/0` entraîne une erreur, mais ne fait pas passer une transaction à l’état non validable. Par ailleurs, SQL Server autorise également les lectures dans une transaction non validable. Mais SQL Data Warehouse ne vous le permet pas. Si une erreur se produit dans une transaction SQL Data Warehouse, celle-ci passe automatiquement à l’état -2 et vous ne serez pas en mesure d’utiliser d’autres instructions select tant que l’instruction n’a pas été restaurée. Vous devez donc impérativement vérifier le code de votre application afin de vous assurer qu’il utilise la fonction XACT_STATE(), car des modifications du code peuvent être nécessaires.
> 
> 

Dans SQL Server par exemple, vous pouvez voir une transaction ressemblant à celle-ci :

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Le code précédent génère le message d’erreur suivant :

Msg 111233, Level 16, State 1, Line 1 111233; La transaction active a été abandonnée. Les modifications en attente ont été restaurées. Cause : une transaction à un état de restauration uniquement n’a pas été explicitement restaurée avant une instruction DDL, DML ou SELECT.

Vous n’obtiendrez pas la sortie des fonctions ERROR_* non plus.

Dans SQL Data Warehouse, le code doit être également légèrement modifié :

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Le comportement attendu est maintenant examiné. L’erreur dans la transaction est gérée, et les fonctions ERROR_* fournissent les valeurs attendues.

La seule chose qui a changé est que l’opération ROLLBACK de la transaction doit se produire avant la lecture des informations sur l’erreur, dans le bloc CATCH.

## <a name="errorline-function"></a>Fonction Error_Line()
Il est également important de signaler que SQL Data Warehouse n’implémente pas et ne prend pas en charge la fonction ERROR_LINE(). Si cette fonction est dans votre code, vous devez la supprimer pour respecter les exigences de SQL Data Warehouse. Placez plutôt des libellés de requête dans votre code pour implémenter les fonctionnalités équivalentes. Pour plus d’informations, consultez l’article [LABEL](sql-data-warehouse-develop-label.md).

## <a name="using-throw-and-raiserror"></a>Utilisation des paramètres THROW et RAISERROR
Le paramètre THROW est l’implémentation la plus moderne du déclenchement d’exceptions dans SQL Data Warehouse. Toutefois, le paramètre RAISERROR est également pris en charge. Il existe cependant quelques différences, qu’il est préférable de prendre en compte.

* Les numéros associés aux messages d’erreur définis par l’utilisateur ne peuvent pas se trouver dans la plage de valeurs allant de 100 000 à 150 000 dans le cas du paramètre THROW.
* Les messages d’erreurs associés au paramètre RAISERROR sont définis sur la valeur fixe de 50 000.
* L’utilisation de l’élément sys.messages n’est pas prise en charge.

## <a name="limitations"></a>Limites
En ce qui concerne les transactions, SQL Data Warehouse présente quelques restrictions supplémentaires.

Les voici :

* Les transactions distribuées ne sont pas acceptées.
* Les transactions imbriquées ne sont pas autorisées.
* Les points de sauvegarde ne sont pas acceptés.
* Transactions sans nom
* Transactions sans marquage
* Aucune prise en charge de DDL comme CREATE TABLE dans une transaction définie par l’utilisateur

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’optimisation des transactions, consultez les [bonnes pratiques relatives aux transactions](sql-data-warehouse-develop-best-practices-transactions.md). Pour plus d’informations sur les bonnes pratiques relatives à SQL Data Warehouse, consultez [Bonnes pratiques relatives à SQL Data Warehouse](sql-data-warehouse-best-practices.md).

