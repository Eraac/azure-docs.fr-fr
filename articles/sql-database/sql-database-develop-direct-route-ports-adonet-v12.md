---
title: Ports au-delà de 1433 pour SQL Database | Microsoft Docs
description: Les connexions clientes entre ADO.NET et Azure SQL Database peuvent ignorer le proxy et interagir directement avec la base de données en utilisant des ports autres que 1433.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 04/03/2019
ms.openlocfilehash: d861ccb93de7aa0b84b20215afb5fddf49aa94c9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427967"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Ports au-delà de 1433 pour ADO.NET 4.5

Cette rubrique décrit le comportement de connexion d’Azure SQL Database pour les clients qui utilisent ADO.NET version 4.5 ou ultérieure.

> [!IMPORTANT]
> Pour plus d’informations sur l’architecture de connectivité, consultez [Architecture de connectivité d’Azure SQL Database](sql-database-connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>À l’extérieur et à l’intérieur

Pour les connexions à Azure SQL Database, nous devons d’abord déterminer si le programme client s’exécute *à l’extérieur* ou *à l’intérieur* de la limite du cloud Azure. Les sous-sections suivantes abordent deux scénarios courants.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*À l’extérieur :* Le client s’exécute sur votre ordinateur de bureau

Le port 1433 est le seul port qui doit être ouvert sur votre ordinateur de bureau qui héberge votre application cliente SQL Database.

### <a name="inside-client-runs-on-azure"></a>*À l’intérieur :* Le client s’exécute sur Azure

Quand votre client s’exécute à l’intérieur de la limite du cloud Azure, il utilise ce que nous pouvons appeler un *itinéraire direct* pour interagir avec le serveur SQL Database. Une fois une connexion établie, les interactions suivantes entre le client et la base de données n’impliquent aucune passerelle Azure SQL Database.

La séquence est la suivante :

1. ADO.NET 4.5 (ou version ultérieure) initie une brève interaction avec le cloud Azure et reçoit un numéro de port identifié de manière dynamique.

   * Le numéro de port identifié de manière dynamique appartient à la plage 11000-11999.
2. ADO.NET se connecte ensuite au serveur SQL Database directement, sans passer par un intergiciel.
3. Les requêtes sont envoyées directement à la base de données et les résultats sont retournés directement au client.

Vérifiez que les plages de ports 11000-11999 sur votre ordinateur client Azure restent disponibles pour les interactions du client ADO.NET 4.5 avec SQL Database.

* En particulier, les ports dans la plage doivent être libres de tout autre bloqueur sortant.
* Sur votre machine virtuelle Azure, le **Pare-feu Windows avec fonctions avancées de sécurité** contrôle les paramètres des ports.
  
  * Vous pouvez utiliser [l’interface utilisateur du pare-feu](https://msdn.microsoft.com/library/cc646023.aspx) pour ajouter une règle dans laquelle vous spécifiez le protocole **TCP** et une plage de ports avec une syntaxe semblable à **11000-11999**.

## <a name="version-clarifications"></a>Précisions concernant les versions

Cette section clarifie les monikers qui font référence aux versions du produit. Elle répertorie également certaines paires de versions entre les produits.

### <a name="adonet"></a>ADO.NET

* ADO.NET 4.0 prend en charge le protocole TDS 7.3, mais pas la version 7.4.
* Les versions d’ADO.NET 4.5 et ultérieures prennent en charge le protocole TDS 7.4.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 ou version ultérieure

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4.2 ou version ultérieure (JDBC 4.0 prend en charge TDS 7.4, mais n’implémente pas la « redirection »)

## <a name="related-links"></a>Liens connexes

* ADO.NET 4.6 a été publié le 20 juillet 2015. Une annonce dans un billet de blog de l’équipe .NET est disponible [ici](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-net-framework-4-6.aspx).
* ADO.NET 4.5 a été publié le 15 août 2012. Une annonce dans un billet de blog de l’équipe .NET est disponible [ici](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
  * Un billet de blog sur ADO.NET 4.5.1 est disponible [ici](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-net-framework-4-5-1-preview.aspx).

* Microsoft® ODBC Driver 17 for SQL Server® - Windows, Linux et macOS https://www.microsoft.com/download/details.aspx?id=56567

* Se connecter à Azure SQL Database V12 via la redirection https://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [Liste des versions du protocole TDS](https://www.freetds.org/userguide/tdshistory.htm)
* [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md)
* [Pare-feu Azure SQL Database](sql-database-firewall-configure.md)
* [Guide pratique pour Configuration des paramètres du pare-feu sur SQL Database](sql-database-configure-firewall-settings.md)


