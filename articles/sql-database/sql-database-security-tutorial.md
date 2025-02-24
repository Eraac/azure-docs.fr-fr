---
title: Sécuriser une base de données unique ou mise en pool dans Azure SQL Database | Microsoft Docs
description: Un tutoriel qui vous apprend les techniques et les fonctionnalités permettant de sécuriser une base de données unique ou en pool dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.custom: seoapril2019
ms.openlocfilehash: ce9ba7c197bb604b9d71e2bf501ca67d32865f38
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566881"
---
# <a name="tutorial-secure-a-single-or-pooled-database"></a>Didacticiel : Sécuriser une base de données unique ou mise en pool

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> - Créer des règles de pare-feu au niveau du serveur et de la base de données
> - Configurer un administrateur Azure Active Directory (AD)
> - Gérer l’accès utilisateur avec l’authentification SQL, l’authentification Azure AD et des chaînes de connexion sécurisées
> - Activer des fonctionnalités de sécurité, comme Advanced Data Security, l’audit, le masquage des données et le chiffrement

Azure SQL Database sécurise les données dans une base de données unique ou mise en pool en vous permettant de :

- Limiter l’accès à l’aide de règles de pare-feu
- Utiliser des mécanismes d’authentification qui nécessitent une identité
- Utiliser des autorisations et appartenances en fonction du rôle
- Activer les fonctionnalités de sécurité

> [!NOTE]
> Une base de données Azure SQL sur une instance managée se sécurise à l’aide de règles de sécurité réseau et de points de terminaison privés, comme décrit dans [Instance managée de base de données Azure SQL](sql-database-managed-instance-index.yml) et [Architecture de connectivité](sql-database-managed-instance-connectivity-architecture.md).

Pour plus d’informations, consultez les articles [Vue d’ensemble de la sécurité dans Azure SQL Database](/azure/sql-database/sql-database-security-index) et [Capacités](sql-database-security-overview.md).

## <a name="prerequisites"></a>Prérequis

Pour suivre le tutoriel, vérifiez que les prérequis ci-dessous sont remplis :

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- Un serveur et une base de données SQL Azure
  - Créez-les à l’aide du [portail Azure](sql-database-single-database-get-started.md), de [CLI](sql-database-cli-samples.md) ou de [PowerShell](sql-database-powershell-samples.md).

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Pour connaître toutes les étapes du tutoriel, connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-firewall-rules"></a>Créer des règles de pare-feu

Les bases de données SQL sont protégées par des pare-feu dans Azure. Par défaut, toutes les connexions au serveur et à la base de données sont rejetées, sauf celles provenant d’autres services Azure. Pour plus d’informations, consultez [Règles de pare-feu au niveau de la base de données et au niveau du serveur Azure SQL Database](sql-database-firewall-configure.md).

Affectez au paramètre **Autoriser l’accès aux services Azure** la valeur **DÉSACTIVER** pour définir la configuration la plus sécurisée. Ensuite, créez une [adresse IP réservée (déploiement classique)](../virtual-network/virtual-networks-reserved-public-ip.md) pour la ressource à connecter, par exemple une machine virtuelle Azure ou un service cloud, puis autorisez uniquement l’accès de cette adresse IP à travers le pare-feu. Si vous utilisez le modèle de déploiement [Resource Manager](/azure/virtual-network/virtual-network-ip-addresses-overview-arm), une adresse IP publique dédiée est nécessaire pour chaque ressource.

> [!NOTE]
> SQL Database communique par le biais du port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter au serveur Azure SQL Database, à moins que votre administrateur n’ouvre le port 1433.

### <a name="set-up-sql-database-server-firewall-rules"></a>Configurer des règles de pare-feu du serveur SQL Database

Les règles de pare-feu IP au niveau du serveur s’appliquent à toutes les bases de données au sein du même serveur SQL Database.

Pour configurer une règle de pare-feu au niveau du serveur :

1. Dans le portail Azure, sélectionnez **Bases de données SQL** dans le menu de gauche, puis sélectionnez votre base de données dans la page **Bases de données SQL**.

    ![règle de pare-feu de serveur](./media/sql-database-security-tutorial/server-name.png)

    > [!NOTE]
    > Veillez à copier le nom complet de votre serveur (comme *votreserveur.basededonnées.windows.net*) pour pouvoir l’utiliser ultérieurement au cours du tutoriel.

1. Dans la page **Vue d’ensemble**, sélectionnez **Définir le pare-feu du serveur**. La page **Paramètres de pare-feu** du serveur de base de données s’ouvre.

   1. Dans la barre d’outils, sélectionnez **Ajouter une adresse IP de client** pour ajouter votre adresse IP actuelle à une nouvelle règle de pare-feu. Une règle peut ouvrir le port 1433 pour une seule adresse IP ou une plage d’adresses IP. Sélectionnez **Enregistrer**.

      ![définir la règle de pare-feu de serveur](./media/sql-database-security-tutorial/server-firewall-rule2.png)

   1. Sélectionnez **OK** pour fermer la page **Paramètres de pare-feu**.

Vous pouvez maintenant vous connecter à n’importe quelle base de données du serveur avec l’adresse IP ou la plage d’adresses IP spécifiée.

> [!IMPORTANT]
> Par défaut, l’accès à travers le pare-feu Azure SQL Database est activé pour tous les services Azure, sous **Autoriser l’accès aux services Azure**. Choisissez **DÉSACTIVER** pour désactiver l’accès pour tous les services Azure.

### <a name="setup-database-firewall-rules"></a>Configurer des règles de pare-feu de base de données

Les règles de pare-feu au niveau de la base de données s’appliquent uniquement à des bases de données individuelles. La base de données conservent ces règles lors d’un basculement de serveur. Vous pouvez uniquement configurer des règles de pare-feu au niveau de la base de données à l’aide d’instructions Transact-SQL et uniquement après avoir configuré une règle de pare-feu au niveau du serveur.

Pour configurer une règle de pare-feu au niveau de la base de données :

1. Connectez-vous à la base de données, par exemple à l’aide de [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. Dans l’**Explorateur d’objets**, cliquez avec le bouton droit sur la base de données et sélectionnez **Nouvelle requête**.

1. Dans la fenêtre de requête, ajoutez cette instruction et remplacez l’adresse IP par votre adresse IP publique :

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Dans la barre d’outils, sélectionnez **Exécuter** pour créer la règle de pare-feu.

> [!NOTE]
> Vous pouvez également créer une règle de pare-feu au niveau du serveur dans SSMS à l’aide de la commande [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current), même si vous devez être connecté à la base de données *master*.

## <a name="create-an-azure-ad-admin"></a>Créer une application Azure AD

Vérifiez que vous utilisez le domaine géré Azure Active Directory (AD) approprié. Pour sélectionner le domaine AD, utilisez le coin supérieur droit du portail Azure. Ce processus confirme que le même abonnement est utilisé pour Azure AD et SQL Server, qui hébergent votre entrepôt de données ou base de données Azure SQL.

   ![choose-ad](./media/sql-database-security-tutorial/8choose-ad.png)

Pour définir l’administrateur Azure AD :

1. Dans le portail Azure, Dans la page **Serveur SQL**, sélectionnez **Administrateur Active Directory**. Sélectionnez ensuite **Définir l’administrateur**.

    ![Sélectionner Active Directory](./media/sql-database-security-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > Vous devez avoir le rôle « Administrateur d’entreprise » ou « Administrateur général » pour effectuer cette tâche.

1. Dans la page **Ajouter un administrateur**, recherchez et sélectionnez l’utilisateur ou le groupe AD, puis choisissez **Sélectionner**. Tous les membres et groupes de votre annuaire Active Directory sont listés. Les entrées grisées ne sont pas prises en charge en tant qu’administrateurs Azure AD. Consultez [Limitations et fonctionnalités Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations).

    ![sélectionner l’administrateur](./media/sql-database-security-tutorial/admin-select.png)

    > [!IMPORTANT]
    > Le contrôle d’accès en fonction du rôle (RBAC) s’applique uniquement au portail et n’est pas propagé à SQL Server.

1. En haut de la page **Administrateur Active Directory**, sélectionnez **Enregistrer**.

    La procédure de changement d’administrateur peut prendre plusieurs minutes. Le nouvel administrateur apparaît dans la zone **Administrateur Active Directory**.

> [!NOTE]
> Quand vous définissez un administrateur Azure AD, son nom (utilisateur ou groupe) ne peut pas exister en tant qu’utilisateur de l’authentification SQL Server dans la base de données *master*. Le cas échéant, le programme d’installation échoue et annule les modifications, ce qui indique que le nom d’administrateur existe déjà. Dans la mesure où l’utilisateur de l’authentification SQL Server ne fait pas partie d’Azure AD, tout effort pour connecter l’utilisateur à l’aide de l’authentification Azure AD échoue.

Pour plus d’informations sur la configuration d’Azure AD, consultez :

- [Intégrer des identités locales à Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)
- [Ajouter votre propre nom de domaine à Azure AD](../active-directory/active-directory-domains-add-azure-portal.md)
- [Microsoft Azure prend désormais en charge la fédération avec Windows Server AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Administration de votre annuaire Azure AD](../active-directory/fundamentals/active-directory-administer.md)
- [Gérer Azure AD avec PowerShell](/powershell/azure/overview?view=azureadps-2.0)
- [Ports et protocoles nécessaires à l’identité hybride](../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Gérer l’accès à la base de données

Gérez l’accès à la base de données en ajoutant des utilisateurs à la base de données ou en autorisant l’accès utilisateur avec des chaînes de connexion sécurisées. Les chaînes de connexion s’avèrent utiles pour les applications externes. Pour plus d’informations, consultez [Contrôle d’accès SQL Azure](sql-database-control-access.md) et [Authentification AD](sql-database-aad-authentication.md).

Pour ajouter des utilisateurs, choisissez le type d’authentification de base de données :

- **Authentification SQL** : utilisez un nom d’utilisateur et un mot de passe pour les connexions qui sont uniquement valides dans le contexte d’une base de données spécifique au sein du serveur.

- **Authentification Azure AD** : utilisez des identités gérées par Azure AD.

### <a name="sql-authentication"></a>Authentification SQL

Pour ajouter un utilisateur avec l’authentification SQL :

1. Connectez-vous à la base de données, par exemple à l’aide de [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. Dans l’**Explorateur d’objets**, cliquez avec le bouton droit sur la base de données, puis choisissez **Nouvelle requête**.

1. Dans la fenêtre de requête, entrez la commande suivante :

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Dans la barre d’outils, cliquez sur **Exécuter** pour créer l’utilisateur.

1. Par défaut, l’utilisateur peut se connecter à la base de données, mais ne peut ni lire ni écrire des données. Pour accorder ces autorisations, exécutez les commandes suivantes dans une nouvelle fenêtre de requête :

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Créez des comptes non-administrateurs au niveau de la base de données, sauf s’ils ont besoin d’exécuter des tâches administratives comme la création de nouveaux utilisateurs.

### <a name="azure-ad-authentication"></a>Authentification Azure AD

Avec l’authentification Azure Active Directory, les utilisateurs de base de données doivent être créés en tant qu’utilisateurs de base de données autonomes. Un utilisateur de base de données autonome est mappé à une identité dans l’annuaire Azure AD associé à la base de données et ne dispose d’aucune connexion dans la base de données *master*. L’identité Azure AD peut correspondre à un utilisateur individuel ou à un groupe. Pour plus d’informations, consultez [Utilisateurs de base de données autonome, rendez votre base de données portable](https://msdn.microsoft.com/library/ff929188.aspx) et suivez le [tutoriel Azure AD](./sql-database-aad-authentication-configure.md) sur la façon de s’authentifier avec Azure AD.

> [!NOTE]
> Les utilisateurs de base de données (à l’exception des administrateurs) ne peuvent pas être créés à l’aide du portail Azure. Les rôles RBAC Azure ne se propagent pas aux entrepôts de données, bases de données ou serveurs SQL. Ils servent uniquement à gérer les ressources Azure et ne s’appliquent pas aux autorisations de base de données.
>
> Par exemple, le rôle *Contributeur SQL Server* ne permet pas de se connecter à une base de données ou à un entrepôt de données. Cette autorisation doit être accordée au sein de la base de données à l’aide d’instructions T-SQL.

> [!IMPORTANT]
> Les caractères spéciaux comme le deux-points `:` ou l’esperluette `&` ne sont pas pris en charge dans les noms d’utilisateur figurant dans les instructions T-SQL `CREATE LOGIN` et `CREATE USER`.

Pour ajouter un utilisateur avec l’authentification Azure AD :

1. Connectez-vous à votre serveur SQL Azure à l’aide d’un compte Azure AD doté au minimum de l’autorisation *MODIFIER UN UTILISATEUR*.

1. Dans l’**Explorateur d’objets**, cliquez avec le bouton droit sur la base de données et sélectionnez **Nouvelle requête**.

1. Dans la fenêtre de requête, entrez la commande suivante et remplacez `<Azure_AD_principal_name>` par le nom principal de l’utilisateur Azure AD ou le nom d’affichage du groupe Azure AD :

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Les utilisateurs Azure AD sont signalés dans les métadonnées de base de données par le type `E (EXTERNAL_USER)` et le type `X (EXTERNAL_GROUPS)` pour les groupes. Pour plus d’informations, consultez [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Chaînes de connexion sécurisées

Pour garantir une connexion sécurisée et chiffrée entre l’application cliente et la base de données SQL, vous devez configurer une chaîne de connexion dans les buts suivants :

- Demander une connexion chiffrée
- Ne pas faire confiance au certificat du serveur

La connexion est établie à l’aide du protocole TLS (Transport Layer Security), ce qui réduit le risque d’attaque de l’intercepteur. Des chaînes de connexion sont disponibles par base de données. Elles sont préconfigurées pour prendre en charge des pilotes de clients comme ADO.NET, JDBC, ODBC et PHP. Pour plus d’informations sur le protocole TLS et la connectivité, consultez [Considérations relatives au protocole TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

Pour copier une chaîne de connexion sécurisée :

1. Dans le portail Azure, sélectionnez **Bases de données SQL** dans le menu de gauche, puis sélectionnez votre base de données dans la page **Bases de données SQL**.

1. Dans la page **Vue d’ensemble**, sélectionnez **Afficher les chaînes de connexion de la base de données**.

1. Sélectionnez un onglet de pilote, puis copiez la chaîne de connexion complète.

    ![Chaîne de connexion ADO.NET](./media/sql-database-security-tutorial/connection.png)

## <a name="enable-security-features"></a>Activer les fonctionnalités de sécurité

Azure SQL Database fournit des fonctionnalités de sécurité accessibles à l’aide du portail Azure. Ces fonctionnalités sont disponibles à la fois pour la base de données et le serveur, à l’exception du masquage des données, qui est uniquement disponible sur la base de données. Pour plus d’informations, consultez [Advanced Data Security](sql-database-advanced-data-security.md), [Audit](sql-database-auditing.md), [Dynamic Data Masking](sql-database-dynamic-data-masking-get-started.md) et [Transparent Data Encryption](transparent-data-encryption-azure-sql.md).

### <a name="advanced-data-security"></a>Advanced Data Security

La fonctionnalité Advanced Data Security détecte les menaces potentielles quand elles se produisent et fournit des alertes de sécurité en cas d’activités anormales. Les utilisateurs peuvent analyser ces événements suspects à l’aide de la fonctionnalité d’audit, afin d’en déterminer le but (accès, violation ou exploitation de données de la base de données). Les utilisateurs disposent également d’une vue d’ensemble de la sécurité qui inclut une évaluation des vulnérabilités et un outil de découverte et de classification des données.

> [!NOTE]
> Une injection de code SQL est un exemple de menace. Il s’agit d’un processus qui permet à des attaquants d’injecter du code SQL malveillant dans des entrées d’application. Une application peut alors exécuter à son insu le code SQL malveillant et permettre à des attaquants de violer ou modifier des données incluses dans la base de données.

Pour activer Advanced Data Security :

1. Dans le portail Azure, sélectionnez **Bases de données SQL** dans le menu de gauche, puis sélectionnez votre base de données dans la page **Bases de données SQL**.

1. Dans la page **Vue d’ensemble**, sélectionnez le lien **Nom du serveur**. La page du serveur de base de données s’ouvre.

1. Dans la page **Serveur SQL**, recherchez la section **Sécurité** et sélectionnez **Advanced Data Security**.

   1. Sélectionnez **ACTIVÉ** sous **Advanced Data Security** pour activer la fonctionnalité. Choisissez un compte de stockage pour l’enregistrement des résultats de l’évaluation de la vulnérabilité. Ensuite, sélectionnez **Enregistrer**.

      ![Volet de navigation](./media/sql-database-security-tutorial/threat-settings.png)

      Vous pouvez également configurer des e-mails pour recevoir des alertes de sécurité, des détails de stockage et des types de détection des menaces.

1. Revenez à la page **Bases de données SQL** de votre base de données, puis sélectionnez **Advanced Data Security** dans la section **Sécurité**. Vous y trouverez divers indicateurs de sécurité disponibles pour la base de données.

    ![État de la menace](./media/sql-database-security-tutorial/threat-status.png)

Si des activités anormales sont détectées, vous recevez un e-mail contenant plus d’informations sur l’événement. Ces dernières incluent la nature de l’activité, la base de données, le serveur, l’heure de l’événement, les causes possibles et les mesures recommandées pour analyser et atténuer la menace potentielle. Si vous recevez un tel e-mail, sélectionnez le lien **Journal d’audit Azure SQL** pour lancer le portail Azure et afficher les enregistrements d’audit correspondant à l’heure de l’événement.

   ![Courrier électronique de détection de menaces](./media/sql-database-security-tutorial/threat-email.png)

### <a name="auditing"></a>Audit

La fonctionnalité d’audit effectue le suivi des événements de la base de données. Elle écrit ces événements dans un journal d’audit, soit dans un stockage Azure, les journaux d'activité Azure Monitor ou un hub d’événements. L’audit vous aide à respecter une conformité réglementaire, à comprendre l’activité de la base de données et à découvrir des discordances et anomalies susceptibles d’indiquer des violations potentielles de la sécurité.

Pour activer l’audit :

1. Dans le portail Azure, sélectionnez **Bases de données SQL** dans le menu de gauche, puis sélectionnez votre base de données dans la page **Bases de données SQL**.

1. Dans la section **Sécurité**, sélectionnez **Audit**.

1. Sous les paramètres **Audit**, définissez les valeurs suivantes :

   1. Définissez **Audit** sur **ACTIVÉ**.

   1. Sélectionnez l’une des **destinations du journal d’audit** suivantes :

       - **Stockage**, un compte de stockage Azure où les journaux des événements sont enregistrés et peuvent être téléchargés sous forme de fichiers *.xel*.

          > [!TIP]
          > Utilisez le même compte de stockage pour toutes les bases de données auditées afin de profiter au mieux des modèles de rapport d’audit.

       - **Log Analytics**, qui stocke automatiquement les événements à des fins de requête ou d’analyse plus approfondie.

           > [!NOTE]
           > Un **espace de travail Log Analytics** est nécessaire pour prendre en charge des fonctionnalités avancées comme l’analytique, les règles d’alerte personnalisées et les exportations Excel ou Power BI. Sans espace de travail, seul l’éditeur de requête est disponible.

       - **Hub d’événements**, qui permet de router des événements pour une utilisation dans d’autres applications.

   1. Sélectionnez **Enregistrer**.

      ![Paramètre d’audit](./media/sql-database-security-tutorial/audit-settings.png)

1. Vous pouvez maintenant sélectionner **Afficher les journaux d’audit** pour voir les données des événements de la base de données.

    ![Enregistrements d’audit](./media/sql-database-security-tutorial/audit-records.png)

> [!IMPORTANT]
> Consultez [Audit des bases de données SQL](sql-database-auditing.md) pour plus d’informations sur la façon de personnaliser davantage les événements d’audit à l’aide de PowerShell ou de l’API REST.

### <a name="dynamic-data-masking"></a>Masquage des données dynamiques

La fonctionnalité de masquage des données permet de masquer automatiquement les données sensibles dans votre base de données.

Pour activer le masquage des données :

1. Dans le portail Azure, sélectionnez **Bases de données SQL** dans le menu de gauche, puis sélectionnez votre base de données dans la page **Bases de données SQL**.

1. Dans la section **Sécurité**, sélectionnez **Dynamic Data Masking**.

1. Sous les paramètres de **masquage dynamique des données**, sélectionnez **Ajouter un masque** pour ajouter une règle de masquage. Azure renseigne automatiquement les schémas, tables et colonnes de base de données disponibles que vous pouvez sélectionner.

    ![Paramètres du masque](./media/sql-database-security-tutorial/mask-settings.png)

1. Sélectionnez **Enregistrer**. Les informations sélectionnées sont maintenant masquées à des fins de confidentialité.

    ![Exemple de masque](./media/sql-database-security-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Chiffrement transparent des données

La fonctionnalité de chiffrement permet de chiffrer automatiquement vos données au repos. Elle ne nécessite aucune modification des applications qui accèdent à la base de données chiffrée. Pour les nouvelles bases de données, le chiffrement est activé par défaut. Vous pouvez également chiffrer les données à l’aide de SSMS et de la fonctionnalité [Always Encrypted](sql-database-always-encrypted.md).

Pour activer ou vérifier le chiffrement :

1. Dans le portail Azure, sélectionnez **Bases de données SQL** dans le menu de gauche, puis sélectionnez votre base de données dans la page **Bases de données SQL**.

1. Dans la section **Sécurité**, sélectionnez **	Transparent Data Encryption**.

1. Si nécessaire, définissez **Chiffrement des données** sur **ACTIVÉ**. Sélectionnez **Enregistrer**.

    ![Chiffrement transparent des données](./media/sql-database-security-tutorial/encryption-settings.png)

> [!NOTE]
> Pour voir l’état du chiffrement, connectez-vous à la base de données à l’aide de [SSMS](./sql-database-connect-query-ssms.md) et interrogez la colonne `encryption_state` de la vue [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql). L’état `3` indique que la base de données est chiffrée.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à renforcer la sécurité de votre base de données en quelques étapes simples. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> - Créer des règles de pare-feu au niveau du serveur et de la base de données
> - Configurer un administrateur Azure Active Directory (AD)
> - Gérer l’accès utilisateur avec l’authentification SQL, l’authentification Azure AD et des chaînes de connexion sécurisées
> - Activer des fonctionnalités de sécurité, comme Advanced Data Security, l’audit, le masquage des données et le chiffrement

Passez au tutoriel suivant pour apprendre à implémenter la géodistribution.

> [!div class="nextstepaction"]
>[Mettre en œuvre une base de données réparties géographiquement](sql-database-implement-geo-distributed-database.md)
