---
title: Concevoir votre première base de données relationnelle - C# -Azure SQL Database | Microsoft Docs
description: Apprenez à concevoir votre première base de données relationnelle dans une base de données unique dans Azure SQL Database en C# avec ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: f6f0160e06ac10a6f1e4e51c93bfa133818117c8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568971"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-cx23-and-adonet"></a>Didacticiel : Concevoir une base de données relationnelle dans une base de données unique au sein d’Azure SQL Database en C&#x23; avec ADO.NET

Azure SQL Database est une solution DBaaS relationnelle dans Microsoft Cloud (Azure). Ce didacticiel vous montre comment utiliser le portail Azure et ADO.NET avec Visual Studio pour :

> [!div class="checklist"]
> * Créer une base de données unique à l’aide du portail Azure*
> * Configurer une règle de pare-feu IP au niveau du serveur à l’aide du portail Azure
> * Se connecter à la base de données avec ADO.NET et Visual Studio
> * Créer des tables avec ADO.NET
> * Insérer, mettre à jour et supprimer des données avec ADO.NET
> * Interroger les données avec ADO.NET

*Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Une installation de [Visual Studio 2019](https://www.visualstudio.com/downloads/) ou ultérieur.

## <a name="create-a-blank-single-database"></a>créer une base de données unique vide.

Une base de données unique dans Azure SQL Database est créée avec un ensemble défini de ressources de calcul et de stockage. Cette base de données est créée dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) et elle est gérée à l’aide d’un [serveur de base de données](sql-database-servers.md).

Suivez ces étapes pour créer une base de données unique vide.

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.
2. Dans la page **Nouveau**, sélectionnez **Bases de données** dans la section Place de marché Azure, puis cliquez sur **SQL Database** dans la section **Sélection**.

   ![créer une base de données vide](./media/sql-database-design-first-database/create-empty-database.png)

3. Remplissez le formulaire **Base de données SQL** avec les informations suivantes, comme indiqué dans l’illustration précédente :

    | Paramètre       | Valeur suggérée | DESCRIPTION |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nom de la base de données** | *yourDatabase* | Pour connaître les noms de bases de données valides, consultez [Identificateurs de base de données](/sql/relational-databases/databases/database-identifiers). |
    | **Abonnement** | *yourSubscription*  | Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
    | **Groupe de ressources** | *yourResourceGroup* | Pour les noms de groupe de ressources valides, consultez [Naming conventions](/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
    | **Sélectionner une source** | Base de données vide | Indique qu’une base de données vide doit être créée. |

4. Cliquez sur **Serveur** pour utiliser un serveur de base de données existant ou créez et configurez-en un. Sélectionnez un serveur existant ou cliquez sur **Créer un serveur** et remplissez le formulaire **Nouveau serveur** avec les informations suivantes :

    | Paramètre       | Valeur suggérée | DESCRIPTION |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Nom du serveur** | Nom globalement unique | Pour les noms de serveur valides, consultez [Naming conventions](/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
    | **Connexion d’administrateur du serveur** | Nom valide | Pour connaître les noms de connexions valides, consultez [Identificateurs de base de données](/sql/relational-databases/databases/database-identifiers). |
    | **Mot de passe** | Mot de passe valide | Votre mot de passe doit comporter au moins huit caractères et contenir des caractères appartenant à trois des catégories suivantes : majuscules, minuscules, chiffres et caractères non alphanumériques. |
    | **Lieu** | Emplacement valide | Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/). |

    ![create database-server](./media/sql-database-design-first-database/create-database-server.png)

5. Cliquez sur **Sélectionner**.
6. Cliquez sur **Niveau tarifaire** pour spécifier le niveau de service, le nombre de DTU ou de vCore et la quantité de stockage. Vous pouvez explorer les options concernant le nombre de DTU/vCore et le stockage disponible pour chaque niveau de service.

    Après avoir sélectionné le niveau de service, le nombre de DTU ou de vCore et la quantité de stockage, cliquez sur **Appliquer**.

7. Entrez un **Classement** pour la base de données vide (pour ce tutoriel, utilisez la valeur par défaut). Pour en savoir plus sur les classements, voir [Classements](/sql/t-sql/statements/collations)

8. Maintenant que vous avez rempli le formulaire **SQL Database**, cliquez sur **Créer** pour provisionner la base de données unique. Cette étape peut prendre quelques minutes.

9. Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.

   ![notification](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Créer une règle de pare-feu IP au niveau du serveur

Le service SQL Database crée un pare-feu IP au niveau du serveur. Ce pare-feu empêche les applications et outils externes de se connecter au serveur et à toutes les bases de données sur ce serveur, sauf si une règle de pare-feu autorise leur adresse IP à traverser le pare-feu. Pour activer la connectivité externe à votre base de données unique, vous devez d’abord ajouter une règle de pare-feu IP pour votre adresse IP (ou plage d’adresses IP). Suivez ces étapes pour créer une [règle de pare-feu IP au niveau du serveur SQL Database](sql-database-firewall-configure.md).

> [!IMPORTANT]
> Le service SQL Database communique par le biais du port 1433. Si vous essayez de vous connecter à ce service à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre base de données unique, sauf si votre administrateur ouvre le port 1433.

1. Une fois le déploiement terminé, cliquez sur **Bases de données SQL** dans le menu de gauche, puis cliquez sur *yourDatabase* dans la page **Bases de données SQL**. La page de vue d’ensemble de votre base de données s’ouvre. Elle affiche le **nom complet du serveur** (par exemple, *yourserver.database.windows.net*) et fournit des options pour poursuivre la configuration.

2. Copiez le nom complet du serveur pour vous connecter à votre serveur et aux bases de données à partir de SQL Server Management Studio.

   ![nom du serveur](./media/sql-database-design-first-database/server-name.png)

3. Cliquez sur **Définir le pare-feu du serveur** dans la barre d’outils. La page **Paramètres de pare-feu** du serveur SQL Database s’ouvre.

   ![règle de pare-feu IP au niveau du serveur](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Dans la barre d’outils, cliquez sur **Ajouter une adresse IP cliente** afin d’ajouter votre adresse IP actuelle à une nouvelle règle de pare-feu IP. Une règle de pare-feu IP peut ouvrir le port 1433 pour une seule adresse IP ou une plage d’adresses IP.

5. Cliquez sur **Enregistrer**. Une règle de pare-feu IP au niveau du serveur est créée pour votre adresse IP actuelle et ouvre le port 1433 sur le serveur SQL Database.

6. Cliquez sur **OK**, puis fermez la page **Paramètres de pare-feu**.

Votre adresse IP peut désormais traverser le pare-feu IP. Vous pouvez maintenant vous connecter à votre base de données unique à l’aide de SQL Server Management Studio ou tout autre outil de votre choix. Veillez à utiliser le compte d’administrateur de serveur que vous avez créé précédemment.

> [!IMPORTANT]
> Par défaut, l’accès par le biais du pare-feu IP SQL Database est activé pour tous les services Azure. Cliquez sur **ÉTEINT** sur cette page pour le désactiver pour tous les services Azure.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer certaines tâches de base sur les bases de données, comme créer une base de données et des tables,vous connecter à la base de données, charger des données et exécuter des requêtes. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une base de données
> * Configurer une règle de pare-feu
> * Se connecter à la base de données avec [Visual Studio et C#](sql-database-connect-query-dotnet-visual-studio.md)
> * créez des tables
> * Insérer, mettre à jour, supprimer et interroger des données

Passez au tutoriel suivant pour en savoir plus sur la migration des données.

> [!div class="nextstepaction"]
> [Migrer SQL Server vers Azure SQL Database hors connexion à l’aide de DMS](../dms/tutorial-sql-server-to-azure-sql.md)
