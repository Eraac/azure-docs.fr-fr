---
title: Accès conditionnel - Azure SQL Database et Data Warehouse | Microsoft Docs
description: Découvrez comment configurer l’accès conditionnel pour Azure SQL Database et Data Warehouse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 03/29/2019
ms.openlocfilehash: 2b2a4a8f7de7e23997b2d8ba0c1c35dfd97f2541
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118765"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Accès conditionnel (MFA) avec Azure SQL Database et Data Warehouse  

[Azure SQL Database](sql-database-technical-overview.md), [Managed Instance](sql-database-managed-instance.md) et [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) prennent tous deux en charge l’accès conditionnel Microsoft. 

> [!NOTE]
> Cette rubrique s’applique à un serveur SQL Azure et aux bases de données SQL Database et SQL Data Warehouse créées sur le serveur SQL Azure. Par souci de simplicité, la base de données SQL est utilisée pour faire référence à SQL Database et SQL Data Warehouse.

Les étapes suivantes montrent comment configurer SQL Database pour appliquer une stratégie d’accès conditionnel.  

## <a name="prerequisites"></a>Prérequis  
- Vous devez configurer SQL Database ou SQL Data Warehouse pour prendre en charge l’authentification Azure Active Directory. Pour connaître la procédure spécifique, consultez [Configurer et gérer l’authentification Azure Active Directory avec SQL Database ou SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- Quand Multi-Factor Authentication est activé, vous devez vous connecter avec un outil pris en charge, tel que la dernière version de SSMS. Pour plus d’informations, consultez [Configurer l’authentification multifacteur Azure SQL Database pour SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Configurer l’accès conditionnel pour Azure SQL DB/DW  
1. Connectez-vous au portail, sélectionnez **Azure Active Directory**, puis **Accès conditionnel**. Pour plus d’informations, consultez [Référence technique de l’accès conditionnel Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
   ![Panneau d’accès conditionnel](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. Dans le panneau des **stratégies d’accès conditionnel**, cliquez sur **Nouvelle stratégie**, fournissez un nom, puis cliquez sur **Configure rules** (Configurer les règles).  
3. Sous **Affectations**, sélectionnez **Utilisateurs et groupes**, cochez **Sélectionner des utilisateurs et des groupes**, puis sélectionnez l’utilisateur ou le groupe pour l’accès conditionnel. Cliquez sur **Sélectionner**, puis sur **Terminé** pour valider la sélection.  
   ![sélectionner des utilisateurs et des groupes](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. Sélectionnez **Applications cloud**, puis cliquez sur **Sélectionner des applications**. Vous voyez toutes les applications disponibles pour l’accès conditionnel. Sélectionnez **Azure SQL Database**, cliquez en bas sur **Sélectionner**, puis sur **Terminé**.  
   ![sélectionner SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
   Si vous ne trouvez pas **Azure SQL Database** répertorié dans la troisième capture d’écran ci-dessous, effectuez les étapes suivantes :   
   - Connectez-vous à votre instance Azure SQL DB/DW à l’aide de SSMS avec un compte d’administrateur AAD.  
   - Exécutez `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
   - Connectez-vous à AAD et vérifiez qu’Azure SQL Database et Data Warehouse sont répertoriés dans AAD.  

5. Sélectionnez **Contrôles d’accès**, **Accorder**, puis cochez la stratégie que vous souhaitez appliquer. Pour cet exemple, nous sélectionnons **Imposer l’authentification multifacteur**.  
   ![sélectionner Accorder l’accès](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Résumé  
L’application sélectionnée (Azure SQL Database), qui permet de se connecter à Azure SQL DB/DW à l’aide d’Azure AD Premium, applique maintenant la stratégie d’accès conditionnel sélectionnée **Imposer l’authentification multifacteur**.  
Pour toute question sur Azure SQL Database et Data Warehouse concernant l’authentification multifacteur, contactez MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Étapes suivantes  

Pour un tutoriel, consultez [Sécuriser votre base de données Azure SQL](sql-database-security-tutorial.md).
