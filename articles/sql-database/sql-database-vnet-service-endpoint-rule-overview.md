---
title: Points de terminaison de service de réseau virtuel et règles relatives aux bases de données uniques et mises en pool dans Azure SQL| Microsoft Docs
description: Marquez un sous-réseau en tant que point de terminaison de service de réseau virtuel, puis le point de terminaison en tant que règle de réseau virtuel dans la liste ACL de votre serveur Azure SQL Database. Le serveur SQL Database accepte alors les communications provenant de toutes les machines virtuelles et d’autres nœuds sur le sous-réseau.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: vanto, genemi
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 8c33cd7fe702f46f9c88643895b96445a9aa6a78
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60331414"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Utiliser des points de terminaison de service de réseau virtuel et des règles pour les serveurs de base de données

Les *règles de réseau virtuel* constituent une fonctionnalité de sécurité du pare-feu. Elles permettent de déterminer si le serveur de vos bases de données uniques et de votre pool élastique Azure [SQL Database](sql-database-technical-overview.md), ou de vos bases de données [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md), doit accepter les communications provenant de sous-réseaux spécifiques de réseaux virtuels. Cet article explique pourquoi la fonctionnalité de règle de réseau virtuel est parfois la meilleure solution qui s’offre à vous pour autoriser en toute sécurité les communications à destination de vos instances Azure SQL Database et SQL Data Warehouse.

> [!IMPORTANT]
> Cet article s’applique à un serveur SQL Azure et aux bases de données SQL Database et SQL Data Warehouse créées sur le serveur SQL Azure. Par souci de simplicité, la base de données SQL est utilisée pour faire référence à SQL Database et SQL Data Warehouse. Cet article ne s'applique *pas* au déploiement d'une **instance managée** dans Azure SQL Database car aucun point de terminaison de service ne lui est associé.

Pour créer une règle de réseau virtuel, il doit d’abord exister un [point de terminaison de service de réseau virtuel][vm-virtual-network-service-endpoints-overview-649d] pour la règle à référencer.

## <a name="how-to-create-a-virtual-network-rule"></a>Création d’une règle de réseau virtuel

Si vous créez uniquement une règle de réseau virtuel, vous pouvez passer directement à la procédure et à l’explication donnée [plus loin dans cet article](#anchor-how-to-by-using-firewall-portal-59j).

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologie et description

**Réseau virtuel :** Vous pouvez avoir des réseaux virtuels associés à votre abonnement Azure.

**Sous-réseau :** Un réseau virtuel contient des **sous-réseaux**. Toutes les machines virtuelles Azure que vous avez sont assignées à des sous-réseaux. Un sous-réseau peut contenir plusieurs machines virtuelles ou d’autres nœuds de calcul. Les nœuds de calcul qui se trouvent en dehors de votre réseau virtuel ne peuvent pas accéder à ce dernier, sauf si vous configurez votre sécurité pour leur en donner l’accès.

**Point de terminaison de service de réseau virtuel :** Un [point de terminaison de service de réseau virtuel][vm-virtual-network-service-endpoints-overview-649d] est un sous-réseau dont les valeurs de propriétés incluent un ou plusieurs noms de type de service Azure formels. Dans cet article, nous nous intéressons au nom de type de **Microsoft.Sql**, qui fait référence au service Azure nommé SQL Database.

**Règle de réseau virtuel :** une règle de réseau virtuel pour votre serveur SQL Database est un sous-réseau qui figure dans la liste de contrôle d’accès (ACL) de votre serveur SQL Database. Pour figurer dans l’ACL pour le serveur SQL Database, le sous-réseau doit contenir le nom de type **Microsoft.Sql**.

Une règle de réseau virtuel donne l’instruction au serveur SQL Database d’accepter les communications provenant de tout nœud se trouvant sur le sous-réseau.

<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Avantages d’une règle de réseau virtuel

Les machines virtuelles sur vos sous-réseaux ne peuvent pas communiquer avec votre serveur SQL Database sans une intervention de votre part. La création d’une règle de réseau virtuel permet d’établir la communication. Le choix de l’approche des règles de réseau virtuel peut être justifié par contraste et comparaison avec les options de sécurité concurrentes offertes par le pare-feu.

### <a name="a-allow-access-to-azure-services"></a>R. Autoriser l’accès aux services Azure

Le volet du pare-feu dispose d’un bouton **ACTIVÉ/DÉSACTIVÉ** étiqueté **Autoriser l'accès aux services Azure**. Le paramètre **ACTIVÉ** autorise les communications provenant de toutes les adresses IP Azure et de tous les sous-réseaux Azure. Ces adresses IP ou sous-réseaux Azure ne vous appartiennent peut-être pas. Ce paramètre **ACTIVÉ** est sans doute plus ouvert que vous souhaitez que le soit votre serveur SQL Database. La fonctionnalité de règle de réseau virtuel offre un contrôle beaucoup plus précis.

### <a name="b-ip-rules"></a>B. Règles IP

Le pare-feu du serveur SQL Database permet de spécifier des plages d’adresses IP à partir desquelles les communications sont acceptées sur le serveur SQL Database. Cette approche est indiquée pour les adresses IP stables qui se trouvent en dehors du réseau privé Azure. Mais un grand nombre de nœuds à l’intérieur du réseau privé Azure sont configurés avec des adresses IP *dynamiques*. Les adresses IP dynamiques peuvent changer, par exemple quand la machine virtuelle est redémarrée. Spécifier une adresse IP dynamique dans une règle de pare-feu au sein d’un environnement de production serait inimaginable.

Vous pouvez récupérer l’option IP en obtenant une adresse IP *statique* pour votre machine virtuelle. Pour plus d’informations, consultez [Configurer des adresses IP privées pour une machine virtuelle à l’aide du portail Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

L’approche des IP statiques peut toutefois devenir difficile à gérer, et elle est coûteuse quand elle est appliquée à grande échelle. Les règles de réseau virtuel sont plus faciles à établir et à gérer.

> [!NOTE]
> Vous ne pouvez pas encore avoir SQL Database dans un sous-réseau. Si votre serveur Azure SQL Database était un nœud sur un sous-réseau de votre réseau virtuel, tous les nœuds situés dans le réseau virtuel pourraient communiquer avec le serveur SQL Database. Dans ce cas, vos machines virtuelles pourraient communiquer avec le serveur SQL Database sans avoir à utiliser de règles de réseau virtuel ni de règles IP.

Mais depuis septembre 2017, le service Azure SQL Database ne figure plus parmi les services pouvant être assignés à un sous-réseau.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Informations sur les règles de réseau virtuel

Cette section fournit des informations sur les règles de réseau virtuel.

### <a name="only-one-geographic-region"></a>Une seule région géographique

Chaque point de terminaison de service de réseau virtuel s’applique à une seule région Azure. Le point de terminaison ne permet pas à d’autres régions d’accepter les communications provenant du sous-réseau.

Une règle de réseau virtuel est limitée à la région à laquelle s’applique son point de terminaison sous-jacent.

### <a name="server-level-not-database-level"></a>Niveau serveur, et non niveau base de données

Chaque règle de réseau virtuel s’applique à tout le serveur Azure SQL Database, et pas seulement à une base de données particulière sur le serveur. En d’autres termes, la règle de réseau virtuel s’applique au niveau du serveur, et non au niveau de la base de données.

- En revanche, les règles IP peuvent être appliquées à tout niveau.

### <a name="security-administration-roles"></a>Rôles d’administration de la sécurité

Il existe une séparation des rôles de sécurité dans l’administration des points de terminaison de service de réseau virtuel. Chacun des rôles suivants doit réaliser une action :

- **Administrateur réseau :** &nbsp; Activez le point de terminaison.
- **Administrateur de base de données :** &nbsp; mettre à jour la liste de contrôle d’accès (ACL) pour ajouter le sous-réseau donné au serveur SQL Database.

*Alternative RBAC :*

Les rôles d’administrateur de réseau et d’administrateur de base de données disposent de plus de fonctionnalités que nécessaires pour gérer les règles de réseau virtuel. Seule une partie de ces fonctionnalités est réellement nécessaire.

Vous avez la possibilité d’utiliser le [contrôle d’accès en fonction du rôle (RBAC)][rbac-what-is-813s] dans Azure pour créer un rôle personnalisé unique disposant uniquement des fonctionnalités nécessaires. Le rôle personnalisé peut être utilisé au lieu d’impliquer l’administrateur de réseau ou l’administrateur de base de données. Votre surface d’exposition de sécurité est inférieure si vous assignez un rôle personnalisé à un utilisateur au lieu de lui assigner les deux principaux rôles d’administrateur.

> [!NOTE]
> Il peut arriver que la base de données Azure SQL et le sous-réseau de réseau virtuel se trouvent dans des abonnements différents. Dans ce cas, vous devez vérifier les configurations suivantes :
> - Les deux abonnements doivent se trouver dans le même locataire Azure Active Directory.
> - L’utilisateur dispose des autorisations requises pour lancer des opérations, telles que l’activation des points de terminaison de service et l’ajout d’un sous-réseau de réseau virtuel sur le serveur donné.
> - Le fournisseur Microsoft.Sql doit être inscrit pour les deux abonnements.

## <a name="limitations"></a>Limites

Pour Azure SQL Database, la fonctionnalité de règle de réseau virtuel présente les limitations suivantes :

- Une application web peut être mappée à une adresse IP privée dans un sous-réseau/réseau virtuel. Même si les points de terminaison de service sont activés à partir du réseau virtuel/sous-réseau donné, les connexions entre l’application web et le serveur présenteront une source IP publique Azure, et non une source de sous-réseau/réseau virtuel. Pour activer la connectivité à partir d’une application web vers un serveur disposant de règles de pare-feu de réseau virtuel, vous devez activer le paramètre **Autoriser l’accès des services Azure au serveur**.

- Dans le pare-feu pour votre base de données SQL Database, chaque règle de réseau virtuel fait référence à un sous-réseau. Tous ces sous-réseaux référencés doivent être hébergés dans la même région géographique qui héberge la base de données SQL Database.

- Chaque serveur Azure SQL Database peut avoir jusqu’à 128 entrées ACL pour un réseau virtuel donné.

- Les règles de réseau virtuel s’appliquent uniquement à des réseaux virtuels Azure Resource Manager, et non à des réseaux avec un [modèle de déploiement Classic][arm-deployment-model-568f].

- L’activation des points de terminaison de service de réseau virtuel pour Azure SQL Database active également les points de terminaison des services Azure MySQL et PostgreSQL. Toutefois, avec les points de terminaison activés, les tentatives de connexion à partir des points de terminaison pour vos instances de MySQL ou PostgreSQL peuvent échouer.
  - Il est fort probable qu'une règle de réseau virtuel n'ait pas été configurée pour MySQL et PostgreSQL. Vous devez configurer une règle de réseau virtuel pour Azure Database pour MySQL et PostgreSQL afin de permettre la connexion.

- Sur le pare-feu, les plages d’adresses IP s’appliquent aux éléments de mise en réseau suivants, contrairement aux règles de réseau virtuel :
  - [Réseau privé virtuel (VPN) site à site (S2S)][vpn-gateway-indexmd-608y]
  - En local via [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Considérations en cas d’utilisation des points de terminaison de service

Lorsque vous utilisez des points de terminaison de service pour Azure SQL Database, passez en revue les considérations suivantes :

- **Une sortie à destination d’adresses IP publiques Azure SQL Database est nécessaire** : Des groupes de sécurité réseau (NSG) doivent être ouverts aux adresses IP Azure SQL Database pour autoriser la connectivité. Vous pouvez pour ce faire utiliser des [balises de service](../virtual-network/security-overview.md#service-tags) NSG pour Azure SQL Database.

### <a name="expressroute"></a>ExpressRoute

Si vous utilisez [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) localement, pour le Peering public ou Microsoft, vous devez identifier les adresses IP NAT (traduction d’adresses réseau) utilisées. Pour l’homologation publique, chaque circuit ExpressRoute utilise par défaut deux adresses IP NAT qui sont appliquées au trafic de service Azure lorsque le trafic entre dans le réseau principal de Microsoft Azure. Pour l’homologation Microsoft, les adresses IP NAT qui sont utilisées sont fournies par le client ou par le fournisseur de services. Pour autoriser l’accès à vos ressources de votre service, vous devez autoriser ces adresses IP publiques dans le paramètre de pare-feu IP de ressource. Pour trouver les adresses IP de votre circuit ExpressRoute d’homologation publique, [ouvrez un ticket de support avec ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via le portail Azure. Découvrez d’autres informations sur [l’homologation publique et Microsoft NAT pour ExpressRoute.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Pour permettre la communication entre votre circuit et Azure SQL Database, vous devez créer des règles de réseau IP pour les adresses IP publiques de votre processus NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-azure-services-to-access-server"></a>Impact de la désactivation du paramètre option Autoriser l’accès des services Azure au serveur.

De nombreux utilisateurs souhaitent supprimer la règle **Autoriser l’accès des services Azure au serveur** de leurs serveurs SQL Azure et la remplacer par une règle de pare-feu de réseau virtuel.
Or, la suppression de cette règle affecte les fonctionnalités suivantes :

### <a name="import-export-service"></a>Service d’importation/exportation

Le service d’importation/exportation Azure SQL Database s’exécute sur des machines virtuelles dans Azure. Ces machines virtuelles, qui ne se trouvent pas dans votre réseau virtuel, obtiennent une adresse IP Azure lors de la connexion à votre base de données. Si vous supprimez la règle **Autoriser l’accès des services Azure au serveur**, ces machines virtuelles ne seront plus en mesure d’accéder à vos bases de données.
Vous pouvez contourner le problème. Exécutez le service d’importation/exportation BACPAC directement dans votre code à l’aide de l’API DACFx. Assurez-vous que le déploiement s’effectue dans une machine virtuelle qui se trouve dans le sous-réseau de réseau virtuel pour lequel vous avez défini la règle de pare-feu.

### <a name="sql-database-query-editor"></a>Éditeur de requêtes SQL Database

L’éditeur de requêtes Azure SQL Database est déployé sur des machines virtuelles dans Azure. Ces machines virtuelles ne se trouvent pas dans votre réseau virtuel. Elles obtiennent donc une adresse IP Azure lors de la connexion à votre base de données. Si vous supprimez la règle **Autoriser l’accès des services Azure au serveur**, ces machines virtuelles ne seront plus en mesure d’accéder à vos bases de données.

### <a name="table-auditing"></a>Audit de table

Il existe actuellement deux façons d’activer l’audit sur votre instance SQL Database. L’audit de table échoue une fois que vous avez activé des points de terminaison de service sur votre instance Azure SQL Server. Pour contourner le problème, vous pouvez utiliser un audit d’objets blob.

### <a name="impact-on-data-sync"></a>Impact sur la synchronisation de données

Azure SQL Database dispose de la fonctionnalité de synchronisation de données qui se connecte à vos bases de données à l’aide d’adresses IP Azure. Lorsque vous utilisez des points de terminaison de service, il est probable que vous comptiez désactiver le paramètre **Autoriser l’accès des services Azure au serveur** pour votre serveur SQL Database. Cela arrêtera la fonctionnalité de synchronisation de données.

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Impact de l’utilisation des points de terminaison de service de réseau virtuel avec le stockage Azure

Stockage Azure a implémenté la même fonctionnalité qui vous permet de limiter la connectivité à votre compte Stockage Azure. Si vous choisissez d’utiliser cette fonctionnalité avec un compte Stockage Azure qui est utilisé par Azure SQL Server, vous risquez de rencontrer des problèmes. Vous trouverez ci-dessous une liste et une présentation des fonctionnalités Azure SQL Database et Azure SQL Data Warehouse qui sont concernées par ce problème.

### <a name="azure-sql-data-warehouse-polybase"></a>Azure SQL Data Warehouse PolyBase

La technologie PolyBase est couramment utilisée pour charger des données dans Azure SQL Data Warehouse à partir de comptes Stockage Azure. Si le compte Stockage Azure à partir duquel vous chargez des données limite l’accès à un ensemble de sous-réseaux de réseau virtuel, la connectivité entre PolyBase et le compte sera interrompue. Pour autoriser les scénarios d’importation et d’exportation PolyBase dans lesquels Azure SQL Data Warehouse se connecte de manière sécurisée à Stockage Azure au réseau virtuel, suivez les étapes indiquées ci-dessous :

#### <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Le module PowerShell Azure Resource Manager est toujours pris en charge par Azure SQL Database, mais tous les développements futurs sont destinés au module Az.Sql. Pour ces cmdlets, consultez [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Les arguments des commandes dans le module Az et dans les modules AzureRm sont sensiblement identiques.

1.  Installez Azure PowerShell en vous aidant de ce [guide](https://docs.microsoft.com/powershell/azure/install-az-ps).
2.  Si vous disposez d’un compte de stockage d’objets blob ou v1 universel, vous devez commencer par le mettre à niveau avec un compte v2 universel en vous aidant de ce [guide](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
3.  Vous devez avoir activé **Autoriser les services Microsoft approuvés à accéder à ce compte de stockage** sous le menu de paramètres **Pare-feux et réseaux virtuels** du compte Stockage Azure. Pour plus d’informations, consultez ce [guide](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
 
#### <a name="steps"></a>Étapes
1. Dans PowerShell, **inscrivez votre serveur SQL Database** auprès d’Azure Active Directory (AAD) :

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```
    
   1. Créez un **compte de stockage v2 universel** en vous aidant de ce [guide](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

   > [!NOTE]
   > - Si vous disposez d’un compte de stockage d’objets blob ou v1 universel, vous devez **d’abord le mettre à niveau avec v2** en vous aidant de ce [guide](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
   > - Pour examiner les problèmes connus liés à Azure Data Lake Storage Gen2, consultez ce [guide](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues).
    
1. Sous votre compte de stockage, accédez à **Contrôle d’accès (IAM)** , puis cliquez sur **Ajouter une attribution de rôle**. Attribuez le rôle RBAC **Contributeur aux données Blob de stockage** à votre serveur SQL Database.

   > [!NOTE] 
   > Seuls les membres dotés du privilège Propriétaire peuvent effectuer cette étape. Pour découvrir les divers rôles intégrés pour les ressources Azure, consultez ce [guide](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1. **Connectivité PolyBase au compte Stockage Azure :**

   1. Créez une **[clé principale](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** de base de données si vous n’en avez pas déjà créée une :
       ```SQL
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```
    
   1. Créez des informations d’identification incluses dans l’étendue de la base de données avec **IDENTITY = 'Managed Service Identity'**  :

       ```SQL
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```
       > [!NOTE] 
       > - Il est inutile de spécifier SECRET avec la clé d’accès Stockage Azure, car ce mécanisme utilise l’[identité managée](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) en arrière-plan.
       > - Le nom d’IDENTITY doit être **'Managed Service Identity'** pour que la connectivité PolyBase entre le compte Stockage Azure et le réseau virtuel fonctionne de manière sécurisée.    
    
   1. Créez la source de données externe avec le schéma abfss:// pour vous connecter à votre compte de stockage v2 universel en utilisant PolyBase :

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```
       > [!NOTE] 
       > - Si des tables externes sont déjà associées au compte de stockage d’objets blob ou v1 universel, vous devez dans un premier temps supprimer ces tables externes et dans un deuxième temps supprimer la source de données externe correspondante. Créez ensuite la source de données externe en faisant en sorte que le schéma abfss:// se connecte au compte de stockage v2 universel comme indiqué ci-dessus, puis recréez toutes les tables externes en utilisant cette nouvelle source de données externe. Vous pouvez utiliser l’[Assistant Générer et publier des scripts](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) pour générer des scripts de création pour toutes les tables externes.
       > - Pour plus d’informations sur le schéma abfss://, consultez ce [guide](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
       > - Pour plus d’informations sur CREATE EXTERNAL DATA SOURCE, consultez ce [guide](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).
        
   1. Exécutez des requêtes comme vous le faites habituellement en utilisant des [tables externes](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Audit d’objets blob Azure SQL Database

L’audit d’objets blob transfère des journaux d’audit à votre propre compte de stockage. Si ce compte de stockage utilise la fonctionnalité de points de terminaison de service de réseau virtuel, la connectivité entre Azure SQL Database et le compte de stockage est arrêtée.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Ajout d’une règle de pare-feu de réseau virtuel à votre serveur sans activer les points de terminaison de service de réseau virtuel

Il y a longtemps, avant que cette fonctionnalité ne soit améliorée, vous deviez activer les points de terminaison de service VNet avant de pouvoir implémenter une règle de réseau virtuel dynamique dans le pare-feu. Les points de terminaison associaient un sous-réseau/réseau virtuel donné à une base de données Azure SQL Database. Depuis janvier 2018, vous pouvez éviter cette opération en définissant l’indicateur **IgnoreMissingVNetServiceEndpoint**.

La simple définition d’une règle de pare-feu ne permet pas de sécuriser le serveur. Vous devez également activer les points de terminaison de service Vnet pour que la sécurité entre en vigueur. Quand vous activez les points de terminaison de service, votre sous-réseau/réseau virtuel est arrêté pendant qu’il passe de l’état désactivé à l’état activé. Cela est particulièrement vrai dans le contexte de grands réseaux virtuels. Vous pouvez utiliser l’indicateur **IgnoreMissingVNetServiceEndpoint** pour réduire ou éliminer le temps d’arrêt pendant la transition.

Vous pouvez définir l’indicateur **IgnoreMissingVNetServiceEndpoint** en utilisant PowerShell. Pour plus d’informations, consultez [Utiliser PowerShell pour créer un point de terminaison de service de réseau virtuel et une règle pour Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Erreurs 40914 et 40615

L’erreur de connexion 40914 est liée aux *règles de réseau virtuel*, comme spécifié dans le volet Pare-feu du portail Azure. L’erreur 40615 est similaire, à ceci près qu’elle est liée aux *règles des adresses IP* sur le pare-feu.

### <a name="error-40914"></a>Erreur 40914

*Texte du message :* Impossible d’ouvrir le serveur « *[nom-serveur]* » demandé par la connexion. Le client n’est pas autorisé à accéder au serveur.

*Description de l’erreur :* le client se trouve dans un sous-réseau qui dispose de points de terminaison de serveur de réseau virtuel. Mais le serveur Azure SQL Database n’a pas de règle de réseau virtuel qui accorde au sous-réseau le droit de communiquer avec la base de données SQL Database.

*Résolution de l’erreur :* dans le volet Pare-feu du portail Azure, utilisez le contrôle des règles de réseau virtuel pour [ajouter une règle de réseau virtuel](#anchor-how-to-by-using-firewall-portal-59j) pour le sous-réseau.

### <a name="error-40615"></a>Erreur 40615

*Texte du message :* Impossible d'ouvrir le serveur '{0}' demandé par la connexion. Le client avec l'adresse IP '{1}' n'est pas autorisé à accéder au serveur.

*Description de l’erreur :* Le client tente de se connecter à partir d’une adresse IP qui n’est pas autorisée à se connecter au serveur Azure SQL Database. Le pare-feu du serveur ne dispose d’aucune règle d’adresse IP qui autorise un client à communiquer à partir de l’adresse IP donnée vers la base de données SQL Database.

*Résolution de l’erreur :* entrez l’adresse IP du client en tant que règle IP. Effectuez cette opération via le volet Pare-feu du portail Azure.

Une liste de plusieurs messages d’erreur de base de données SQL Database est documentée [ici][sql-database-develop-error-messages-419g].

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Le portail peut créer une règle de réseau virtuel

Cette section montre comment vous pouvez utiliser le [portail Azure][http-azure-portal-link-ref-477t] pour créer une *règle de réseau virtuel* sur votre serveur Azure SQL Database. La règle donne l’instruction à votre serveur SQL Database d’accepter les communications provenant d’un sous-réseau spécifique qui a été marqué comme étant un *point de terminaison de service de réseau virtuel*.

> [!NOTE]
> Si vous envisagez d’ajouter un point de terminaison de service aux règles de pare-feu de réseau virtuel de votre serveur Azure SQL Database, commencez par vérifier que les points de terminaison de service sont activés pour le sous-réseau.
>
> Si les points de terminaison de service ne sont pas activés pour le sous-réseau, le portail vous invite à les activer. Cliquez sur le bouton **Activer** sur le même panneau que celui où vous ajoutez la règle.

## <a name="powershell-alternative"></a>Alternative PowerShell

Un script PowerShell peut également créer des règles de réseau virtuel. Avec l’applet de commande essentielle **New-AzSqlServerVirtualNetworkRule**. Si cette option vous intéresse, consultez [Utiliser PowerShell pour créer un point de terminaison de service de réseau virtuel et une règle pour Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>API REST de remplacement

En interne, les applets de commande PowerShell pour les actions de réseau virtuel SQL appellent des API REST. Vous pouvez appeler les API REST directement.

- [Règles de réseau virtuel : opérations][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Prérequis

Vous devez déjà disposer d’un sous-réseau étiqueté avec le *nom de type* de point de terminaison de service de réseau virtuel particulier approprié pour Azure SQL Database.

- Le nom de type de point de terminaison approprié est **Microsoft.Sql**.
- Si votre sous-réseau n’est pas étiqueté avec le nom de type, consultez [Vérifier que votre sous-réseau est un point de terminaison][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Étapes dans le portail Azure

1. Connectez-vous au [portail Azure][http-azure-portal-link-ref-477t].

2. Accédez ensuite à **Serveurs SQL** &gt; **Pare-feu / Réseaux virtuels**.

3. Définissez le contrôle **Autoriser l’accès aux services Azure** sur DÉSACTIVÉ.

    > [!IMPORTANT]
    > Si vous maintenez le contrôle ACTIVÉ, votre serveur Azure SQL Database acceptera les communications à partir de n’importe quel sous-réseau. En termes de sécurité, le fait de laisser le contrôle ACTIVÉ peut avoir pour effet de multiplier excessivement les accès. La fonctionnalité de points de terminaison de service de réseau virtuel Microsoft Azure, associée à la fonctionnalité de règle de réseau virtuel de SQL Database, peuvent ensemble réduire votre surface d’exposition de sécurité.

4. Cliquez sur le contrôle **+ Ajouter existant** dans la section **Réseaux virtuels**.

    ![Cliquez sur Ajouter existant (point de terminaison de sous-réseau, en tant que règle SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. Dans le nouveau volet **Créer/mettre à jour**, renseignez les contrôles avec les noms de vos ressources Azure.

    > [!TIP]
    > Vous devez inclure le **préfixe d’adresse** correct pour votre sous-réseau. Vous pouvez trouver la valeur correspondante dans le portail.
    > Accédez à **Toutes les ressources** &gt; **Tous les types** &gt; **Réseaux virtuels**. Le filtre affiche vos réseaux virtuels. Cliquez sur votre réseau virtuel, puis sur **Sous-réseaux**. La colonne **PLAGE D’ADRESSES** contient le préfixe d’adresse nécessaire.

    ![Renseignez les champs de la nouvelle règle.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Cliquez sur le bouton **OK** en bas du volet.

7. Le volet du pare-feu affiche la règle de réseau virtuel obtenue.

    ![Le volet du pare-feu affiche la nouvelle règle.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Les états suivants s’appliquent aux règles :
> - **Prêt :** indique que l’opération que vous avez lancée a abouti.
> - **Échec :** indique que l’opération que vous avez lancée a échoué.
> - **Supprimé :** s’applique uniquement à l’opération de suppression et indique que la règle a été supprimée et qu’elle ne s’applique plus.
> - **En cours :** indique que l’opération est en cours d’exécution. L’ancienne règle s’applique lorsque l’opération est à cet état.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Articles connexes

- [Points de terminaison de service de réseau virtuel Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Règles de pare-feu au niveau du serveur et de la base de données Azure SQL Database][sql-db-firewall-rules-config-715d]

La fonctionnalité de règle de réseau virtuel pour Azure SQL Database est disponible depuis fin septembre 2017.

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser PowerShell pour créer un point de terminaison de service de réseau virtuel, puis une règle de réseau virtuel pour Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Règles de réseau virtuel : opérations][rest-api-virtual-network-rules-operations-862r] avec les API REST

<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.yml

[rbac-what-is-813s]:../role-based-access-control/overview.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
