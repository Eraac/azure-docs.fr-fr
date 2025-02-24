---
title: Utilisation de l’authentification AAD multifacteur avec Azure SQL Database et Azure SQL Data Warehouse | Microsoft Docs
description: Azure SQL Database et Azure SQL Data Warehouse prennent en charge les connexions depuis SQL Server Management Studio (SSMS) à l’aide de l’authentification universelle Active Directory.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 10/08/2018
ms.openlocfilehash: ccb78e201b90dfc27f52523348e76da57087bcc8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60614173"
---
# <a name="using-multi-factor-aad-authentication-with-azure-sql-database-and-azure-sql-data-warehouse-ssms-support-for-mfa"></a>Utilisation de l’authentification AAD multifacteur avec Azure SQL Database et Azure SQL Data Warehouse (prise en charge de SSMS pour MFA)
Azure SQL Database et Azure SQL Data Warehouse prennent en charge les connexions à partir de SQL Server Management Studio (SSMS) à l’aide de *l’authentification universelle Active Directory*. Cet article décrit les différences entre les différentes options d’authentification, ainsi que les limitations associées à l’utilisation de l’authentification universelle. 

**Télécharger la dernière version de SSMS** : sur l’ordinateur client, téléchargez la dernière version de SSMS à partir de la page [Télécharger SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 


Pour toutes les fonctionnalités abordées dans cet article, utilisez au minimum la version 17.2 (juillet 2017).  La boîte de dialogue de connexion la plus récente doit ressembler à l’image suivante :
 
  ![1mfa-universal-connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Renseigne le champ Nom d'utilisateur.")  

## <a name="the-five-authentication-options"></a>Les cinq options d’authentification  

L’authentification universelle Active Directory prend en charge les deux méthodes d’authentification non interactives :
    - l’authentification `Active Directory - Password`
    - l’authentification `Active Directory - Integrated`

Il existe deux méthodes d’authentification non interactives, qui peuvent être utilisées dans de nombreuses applications (ADO.NET, JDBC, ODBC, etc.). Ces deux méthodes n’entraînent jamais l’affichage de boîtes de dialogue contextuelles : 
- `Active Directory - Password` 
- `Active Directory - Integrated` 

La méthode interactive qui prend également en charge Azure Multi-Factor Authentication (MFA) est la suivante : 
- `Active Directory - Universal with MFA` 


Azure MFA contribue à sécuriser l’accès aux données et aux applications tout en répondant à la demande des utilisateurs souhaitant un processus d’authentification simple. Il assure une authentification forte avec une gamme d’options de vérification simples (appel téléphonique, SMS, cartes à puce avec code PIN ou notification d’application mobile) pour permettre aux utilisateurs de choisir leur méthode préférée. L’authentification multifacteur (MFA) interactive avec Azure AD peut afficher une boîte de dialogue contextuelle de validation.

Pour une description de Multi-Factor Authentication, consultez la rubrique [Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md).
Pour les étapes de configuration, consultez [Configuration de l’authentification multifacteur pour les bases de données Azure SQL pour SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Nom de domaine Azure AD et paramètre d’ID de locataire   

À partir de [la version 17 de SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), les utilisateurs qui sont importés dans le répertoire Active Directory en cours à partir d’autres répertoires Azure Active Directory en tant qu’utilisateurs invités peuvent fournir le nom de domaine Azure AD ou l’ID de locataire quand ils se connectent. Les utilisateurs invités incluent les utilisateurs qui sont invités à partir d’autres annuaires Azure AD, de comptes Microsoft, tels qu’outlook.com, hotmail.com et live.com, ou d’autres comptes, comme gmail.com. Grâce à ces informations, **l’authentification universelle Active Directory avec MFA** peut identifier l’autorité d’authentification appropriée. Cette option est également requise pour prendre en charge les comptes Microsoft (MSA) tels qu’outlook.com, hotmail.com, live.com, ou les comptes non-MSA. Les utilisateurs qui souhaitent être authentifiés à l’aide de l’authentification universelle doivent entrer leur nom de domaine Azure AD ou leur ID de locataire. Ce paramètre représente le nom de domaine/ID de locataire Azure AD lié au serveur Azure. Par exemple, si le serveur Azure est associé au domaine Azure AD `contosotest.onmicrosoft.com` où l’utilisateur `joe@contosodev.onmicrosoft.com` est hébergé en tant qu’utilisateur importé du domaine Azure AD `contosodev.onmicrosoft.com`, le nom de domaine requis pour authentifier cet utilisateur est `contosotest.onmicrosoft.com`. Lorsque l’utilisateur est un utilisateur natif d’Azure AD lié au serveur Azure et qu’il n’a pas de compte MSA, aucun nom de domaine ou ID de locataire n’est requis. Pour entrer le paramètre (à compter de SSMS version 17.2), dans la boîte de dialogue **Connexion à une base de données**, sélectionnez **Active Directory - Authentification universelle avec MFA**, cliquez sur **Options**, renseignez la zone **Nom d’utilisateur**, puis cliquez sur l’onglet **Propriétés de connexion**. Cochez la case **Nom du domaine AD ou ID de locataire** et fournissez l’autorité d’authentification, telle que le nom de domaine (**contosotest.onmicrosoft.com**) ou le GUID de l’ID de locataire.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Prise en charge d’Azure AD B2B   
Les utilisateurs Azure AD pris en charge pour les scénarios d’Azure AD B2B en tant qu’utilisateurs invités (consulter [Qu’est-ce qu’Azure AD B2B Collaboration ?](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) ne peuvent se connecter à SQL Database et SQL Data Warehouse qu’en tant que membres d’un groupe créé dans Azure AD et mappé manuellement à l’aide de l’instruction Transact-SQL `CREATE USER` dans une base de données spécifique. Par exemple, si `steve@gmail.com` est invité à Azure AD `contosotest` (avec le domaine Azure AD `contosotest.onmicrosoft.com`), un groupe Azure AD, tel que `usergroup`, doit être créé dans l’annuaire Azure AD qui contient le membre `steve@gmail.com`. Ensuite, l’administrateur Azure AD SQL ou le propriétaire de la base de données Azure AD doit créer ce groupe pour une base de données spécifique (MyDatabase) en exécutant une instruction Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER`. Une fois l’utilisateur de base de données créé, l’utilisateur `steve@gmail.com` peut se connecter `MyDatabase` à l’aide de l’option d’authentification SSMS `Active Directory – Universal with MFA support`. Par défaut, le groupe d’utilisateurs bénéficie uniquement de l’autorisation de connexion et de tout accès aux données accordé de façon habituelle. Remarquez que l’utilisateur `steve@gmail.com` en tant qu’utilisateur invité doit cocher la case et ajouter le nom de domaine AD `contosotest.onmicrosoft.com` à la boîte de dialogue **Propriété de connexion** SSMS. L’option **Nom du domaine AD ou ID de locataire** est uniquement prise en charge pour les options Authentification universelle avec prise en charge de MFA ; dans le cas contraire, elle est grisée.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limites de l’authentification universelle pour la base de données SQL et SQL Data Warehouse
- SSMS et SqlPackage.exe sont les seuls outils activés pour MFA par le biais de l’authentification universelle Active Directory.
- SSMS version 17.2 prend en charge l’accès simultané de plusieurs utilisateurs à l’aide de l’authentification universelle avec MFA. Les versions 17.0 et 17.1 limitaient à un seul compte Azure Active Directory la connexion à une instance de SSMS à l’aide de l’authentification universelle. Pour vous connecter comme un autre compte Azure AD, vous devez utiliser une autre instance de SSMS. (Cette restriction est limitée à l’authentification universelle Active Directory. Vous pouvez vous connecter à différents serveurs à l’aide de l’authentification de mot de passe Active Directory, l’authentification intégrée à Active Directory ou l’authentification SQL Server).
- SSMS prend en charge l’authentification universelle Active Directory pour la visualisation de l’Explorateur d’objets, de l’Éditeur de requête et du magasin de requêtes.
- SSMS version 17.2 fournit une prise en charge de l’Assistant DacFx pour l’exportation, l’extraction et le déploiement de la base de données. Une fois qu’un utilisateur spécifique est authentifié via la boîte de dialogue d’authentification initiale à l’aide de l’authentification universelle, l’Assistant DacFx fonctionne de la même manière que pour toutes les autres méthodes d’authentification.
- Le Concepteur de tables SSMS ne prend pas en charge l’authentification universelle.
- Il n’existe aucune configuration logicielle supplémentaire nécessaire pour l’authentification universelle Active Directory, sauf que vous devez utiliser une version prise en charge de SSMS.  
- La version ADAL (Active Directory Authentication Library) pour l’authentification universelle a été mise à jour vers la dernière version publiée disponible ADAL.dll 3.13.9. Consultez [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Étapes suivantes

- Pour les étapes de configuration, consultez [Configuration de l’authentification multifacteur pour les bases de données Azure SQL pour SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Octroyer à d'autres utilisateurs l'accès à votre base de données : [Authentification et autorisation SQL Database : octroyer l'accès](sql-database-manage-logins.md)  
- Assurez-vous que les autres utilisateurs peuvent se connecter via le pare-feu : [Configurer une règle de pare-feu au niveau du serveur Azure SQL Database à l’aide du portail Azure](sql-database-configure-firewall-settings.md)  
- [Configurer et gérer l’authentification Azure Active Directory avec SQL Database ou SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Importer un fichier BACPAC dans une nouvelle base de données Azure SQL](../sql-database/sql-database-import.md)  
- [Exporter une base de données Azure SQL dans un fichier BACPAC](../sql-database/sql-database-export.md)  
- Interface C# [Interface IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Quand vous utilisez l’authentification **Active Directory - Authentification universelle avec prise en charge de MFA**, le suivi ADAL est disponible à compter de [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Le suivi ADAL étant désactivé par défaut, vous pouvez l’activer comme suit : dans le menu **Outils**, choisissez **Options**, puis sous **Services Azure**, choisissez **Cloud Azure** et **Niveau de trace dans la fenêtre Sortie ADAL**, et activez **Sortie** dans le menu **Affichage**. Les suivis sont disponibles dans la fenêtre de sortie quand vous sélectionnez l’option **Azure Active Directory**.  
