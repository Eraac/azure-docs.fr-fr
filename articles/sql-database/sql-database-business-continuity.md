---
title: Continuité des activités cloud - récupération de base de données - SQL Database | Microsoft Docs
description: Découvrez comment Azure SQL Database prend en charge la continuité des activités cloud et la récupération de base de données et vous aide à maintenir les applications cloud critiques pour la mission.
keywords: continuité des activités, continuité des activités cloud, récupération d’urgence de base de données, récupération de base de données
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 06/25/2019
ms.openlocfilehash: 2f3723e0a1b14edd6f516f3cc080501bea80d486
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442049"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Vue d’ensemble de la continuité de l’activité avec la base de données Azure SQL

La **continuité d’activité** dans Azure SQL Database fait référence aux mécanismes, stratégies et procédures qui permettent à votre entreprise de continuer à fonctionner en cas d’interruption affectant notamment son infrastructure informatique. Dans la plupart des cas, Azure SQL Database gère les événements d’interruption qui peuvent se produire dans l’environnement de cloud et préservent l’exécution de vos applications et processus d’entreprise. Toutefois, certains de ces événements ne peuvent pas être automatiquement gérés par SQL Database, notamment dans les cas suivants :

- Un utilisateur a accidentellement supprimé ou mis à jour une ligne dans une table.
- Un attaquant a réussi à supprimer des données ou une base de données.
- Un tremblement de terre a provoqué une panne d’alimentation et désactivé temporairement le centre de données.

Cette vue d’ensemble décrit les fonctionnalités que fournit Azure SQL Database pour la continuité d’activité et la reprise d’activité après sinistre. Découvrez les options, les recommandations et les didacticiels pour la récupération à partir d’événements d’interruption qui pourraient entraîner une perte de données ou l’indisponibilité de votre base de données et de votre application. Connaissez la procédure à suivre lorsqu’un utilisateur ou qu’une erreur d’application affecte l’intégrité des données, lorsqu’une région Azure subit une panne ou que votre application nécessite une maintenance.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Fonctionnalités de la base de données SQL que vous pouvez utiliser pour garantir une continuité d’activité

Du point de vue de la base de données, il existe quatre scénarios d’interruption potentiels :

- Des défaillances matérielles ou logicielles locales telles qu’une panne de lecteur de disque affectent le nœud de base de données.
- Une suppression ou une altération des données se produit, généralement à la suite d’un bogue d’application ou d’une erreur humaine. Ces défaillances sont spécifiques à l’application et ne peuvent généralement pas être détectées par le service de base de données.
- Une panne de centre de données se produit, à la suite d’une catastrophe naturelle, par exemple. Ce scénario nécessite un certain degré de redondance géographique permettant le basculement d’application vers un autre centre de données.
- Des erreurs de mise à niveau ou de maintenance se sont produites. Ces problèmes imprévus qui se produisent au cours d’une maintenance ou de mises à niveau d’infrastructure planifiées peuvent nécessiter une restauration rapide à un état antérieur de la base de données.

Pour atténuer les défaillances matérielles et logicielles locales, SQL Database inclut une [architecture haute disponibilité](sql-database-high-availability.md) qui garantit une récupération automatique de ces défaillances jusqu’à une disponibilité SLA de 99,995 %.  

Pour protéger votre entreprise contre la perte de données, SQL Database crée automatiquement des sauvegardes de bases de données complètes (toutes les semaines), des sauvegardes de bases de données différentielles (toutes les 12 heures) et des sauvegardes de fichiers journaux (toutes les 5 à 10 minutes). Les sauvegardes sont stockées dans le stockage RA-GRS pendant au moins 7 jours pour tous les niveaux de service. Tous les niveaux de service, à l’exception du support De base, prennent en charge une période de rétention des sauvegardes configurable pour la restauration dans le temps jusqu’à 35 jours. 

SQL Database fournit également plusieurs fonctionnalités de continuité des activités que vous pouvez utiliser pour atténuer différents scénarios non planifiés. 

- Les [tables temporelles](sql-database-temporal-tables.md) vous permettent de restaurer des versions de ligne à partir de n’importe quel point dans le temps.
- Les [sauvegardes automatisées intégrées](sql-database-automated-backups.md) et la [limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore) vous permettent de restaurer l’ensemble de la base de données selon un moment donné au cours de la période de rétention configurée jusqu’à 35 jours.
- Vous pouvez [restaurer une base de données supprimée](sql-database-recovery-using-backups.md#deleted-database-restore) au point où sa suppression s’est produite si le **serveur SQL Database n’a pas été supprimé**.
- La [rétention des sauvegardes à long terme](sql-database-long-term-retention.md) vous permet de conserver les sauvegardes sur une période allant jusqu’à 10 ans.
- La [géoréplication active](sql-database-active-geo-replication.md) vous permet de créer des réplicas lisibles et d’effectuer un basculement manuel vers n’importe quel réplica en cas de panne de centre de données ou de mise à niveau d’application.
- Le [groupe de basculement automatique](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) permet à l’application d’effectuer automatiquement une récupération en cas de panne du centre de données.

## <a name="recover-a-database-within-the-same-azure-region"></a>Récupérer une base de données dans la même région Azure

Vous pouvez utiliser des sauvegardes de base de données automatiques pour restaurer une base de données à un point dans le temps passé. Vous pouvez ainsi récupérer suite à des altérations de données dues à des erreurs humaines. La restauration dans le temps vous permet de créer une nouvelle base de données sur le même serveur qui représente l’état des données avant la corruption. Pour la plupart des bases de données, les opérations de restauration prennent moins de 12 heures. La récupération d’une base de données très volumineuse ou très active peut prendre plus de temps. Pour plus d’informations sur le temps de récupération, voir [Temps de récupération de la base de données](sql-database-recovery-using-backups.md#recovery-time). 

Si la période maximale de rétention de base de données prise en charge associée à la limite de restauration dans le temps n’est pas suffisante pour votre application, vous pouvez la rallonger en configurant une stratégie de conservation à long terme (LTR) pour les bases de données. Pour plus d’informations, consultez [Conservation des sauvegardes à long terme](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-azure-region"></a>Récupérer une base de données sur une autre région Azure

Bien que le fait soit rare, un centre de données Azure peut subir une panne. En cas de panne, il entraîne une interruption de service qui peut durer de quelques minutes à plusieurs heures.

- Vous pouvez attendre que votre base de données redevienne disponible une fois la panne réparée au niveau du centre de données. Cette méthode fonctionne pour les applications qui peuvent se permettre d’avoir la base de données déconnectée. C’est le cas, par exemple, d’un projet de développement ou d’une version d’évaluation gratuite sur lesquels vous n’avez pas à travailler en permanence. Lorsqu’un centre de données subit une panne, vous ne savez pas combien de temps cette panne peut durer. Cette méthode fonctionne donc uniquement si vous n’avez pas à travailler sur votre base de données pendant un certain temps.
- Vous pouvez également restaurer une base de données sur n’importe quel serveur d’une région Azure quelconque à l’aide des [sauvegardes de base de données géo-redondantes](sql-database-recovery-using-backups.md#geo-restore) (ou géorestaurations). La géorestauration utilise une sauvegarde géoredondante en tant que source et peut être mise à profit pour récupérer une base de données même si la base de données ou le centre de données est inaccessible en raison d’une défaillance.
- Enfin, vous pouvez reprendre l’activité rapidement en cas de panne si vous avez configuré des réplicas géosecondaires à l’aide de la [géoréplication active](sql-database-active-geo-replication.md) ou d’un [groupe de basculement automatique](sql-database-auto-failover-group.md) pour votre ou vos bases de données. Selon la technologie choisie, vous pouvez utiliser soit le basculement manuel, soit le basculement automatique. Le basculement proprement dit ne prend que quelques secondes, mais son activation par le service prend au moins 1 heure. Cela est nécessaire pour s’assurer que le basculement est justifié par l’étendue de la panne. En outre, le basculement peut entraîner une petite perte de données en raison de la nature de la réplication asynchrone. 

Au moment d’élaborer votre plan de continuité d’activité, vous devez comprendre le délai maximal acceptable nécessaire à la récupération complète de l’application après l’événement d’interruption. Ce délai s’appelle l’objectif de délai de récupération (RTO, recovery time objective). Vous devez également comprendre sur quelle période maximale l’application peut accepter de perdre les mises à jour de données récentes (intervalle de temps) lors de la récupération suite à un événement d’interruption. La perte de données potentielle est appelée un objectif de point de récupération (RPO).

Différentes méthodes de récupération offrent différents niveaux de RPO et RTO. Vous pouvez choisir une méthode de récupération spécifique ou utiliser une combinaison de méthodes pour obtenir une récupération complète de l’application. Le tableau suivant compare les RPO et RTO de chaque option de récupération.

| Méthode de récupération | RTO | RPO |
| --- | --- | --- | 
| Géo-restauration à partir de sauvegardes répliquées géographiquement | 12 h | 1 h |
| Groupes de basculement automatique | 1 h | 5 s |
| Basculement de base de données manuel | 30 s | 5 s |

> [!NOTE]
> *Basculement de base de données manuel* fait référence au basculement d’une base de données unique vers sa base de données secondaire géorépliquée en [mode non planifié](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Consultez le tableau précédemment illustré dans cet article pour plus d’informations sur le RTO et le RPO du basculement automatique.


Utilisez des groupes de basculement automatique si votre application répond à l’un des critères suivants :

- Est essentielle.
- A un contrat de niveau de service (SLA) qui n’autorise pas plus de 12 heures d’interruption de service.
- Toute interruption de service peut engager la responsabilité financière.
- Affiche un taux élevé de données modifiées et la perte d’une heure de données n’est pas acceptable.
- Le coût supplémentaire lié à l'utilisation de la géoréplication est plus faible que la responsabilité financière potentielle et la perte d'activité associée.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Vous pouvez choisir d’utiliser une combinaison de sauvegardes de bases de données et de géoréplication active en fonction des besoins de votre application. Pour plus d’informations sur la conception pour des bases de données autonomes et des pools élastiques à l’aide de ces fonctionnalités de continuité d’activité, consultez les articles [Conception d’applications pour la récupération d’urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) et [Stratégies de récupération d’urgence de pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Les sections suivantes fournissent une vue d’ensemble des étapes de la récupération à l’aide de sauvegardes de bases de données ou de la géo-réplication active. Pour des instructions détaillées, y compris les exigences de planification, les étapes de post-récupération et des informations sur la simulation d’une panne pour effectuer un exercice de récupération d’urgence, voir la section [Récupérer une base de données SQL Azure en cas de défaillance](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Se préparer à une panne

Quelle que soit la fonctionnalité de continuité d’activité que vous utilisez, vous devez :

- Identifier et préparer le serveur cible, y compris les règles de pare-feu IP au niveau du serveur, les connexions et les autorisations au niveau de la base de données MASTER.
- Déterminer comment rediriger les clients et les applications clientes vers le nouveau serveur
- Documenter les autres dépendances, notamment les paramètres d’audit et les alertes

Si la préparation n’est pas effectuée correctement, la mise en ligne de vos applications après un basculement ou une récupération de base de données prend plus de temps et nécessite probablement de résoudre certains problèmes dans une situation de stress, ce qui constitue une combinaison très risquée.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Basculement vers une base de données secondaire géo-répliquée

Si vous utilisez la géoréplication active ou des groupes de basculement automatique comme mécanisme de récupération, vous pouvez configurer une stratégie de basculement automatique ou utiliser le [basculement non planifié manuel](sql-database-active-geo-replication-portal.md#initiate-a-failover). Une fois le basculement lancé, la base de données secondaire est promue comme nouvelle base principale et peut alors enregistrer de nouvelles transactions et répondre aux requêtes, avec des pertes de données minimes pour les données qui n’ont pas encore été répliquées. Pour plus d’informations sur la création du processus de basculement, reportez-vous à la section [Conception d’une application pour la récupération d’urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Lorsque le centre de données revient en ligne, les anciennes bases primaires se reconnectent automatiquement à la nouvelle base primaire et deviennent les bases de données secondaires. Si vous devez replacer la base de données primaire dans sa région d’origine, vous pouvez initier un basculement programmé de manière manuelle (restauration automatique).

### <a name="perform-a-geo-restore"></a>Effectuer une restauration géographique

Si vous utilisez les sauvegardes automatisées avec stockage géoredondant (activé par défaut), vous pouvez récupérer la base de données à l’aide de la [géo-restauration](sql-database-disaster-recovery.md#recover-using-geo-restore). Dans la plupart des cas, la récupération intervient sous 12 heures, avec une perte de données de 1 heure maximum, selon le moment où la dernière sauvegarde de fichier journal a été effectuée et répliquée. Tant que la récupération n’est pas terminée, la base de données ne peut pas enregistrer de transactions ou répondre à des requêtes. Remarquez que la géo-restauration permet uniquement de restaurer la base de données au dernier point disponible dans le temps.

> [!NOTE]
> Si le centre de données redevient disponible avant que vous ne transfériez votre application vers la base de données récupérée, vous pouvez annuler la récupération.

### <a name="perform-post-failover--recovery-tasks"></a>Exécution de tâches de post-basculement/récupération

Après la récupération à l’aide d’un de ces mécanismes de récupération, vous devez effectuer les tâches supplémentaires suivantes afin que les utilisateurs et les applications soient de nouveau opérationnels :

- Rediriger les clients et les applications clientes vers le nouveau serveur et la base de données restaurée
- Vérifier que les règles de pare-feu IP appropriées sont en place au niveau du serveur pour permettre aux utilisateurs de se connecter ou d’utiliser les [pare-feu au niveau de la base de données](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-the-azure-portal) afin d’activer les règles qui conviennent.
- Vérifier que les connexions et les autorisations appropriées au niveau de la base de données MASTER sont en place (ou utiliser des [utilisateurs contenus](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Configurer l’audit, selon les besoins
- Configurer les alertes, selon les besoins

> [!NOTE]
> Si vous utilisez un groupe de basculement et que vous vous connectez aux bases de données à l’aide de l’écouteur en lecture-écriture, la redirection après le basculement se produit automatiquement et en toute transparence pour l’application.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Mettre à niveau une application avec un temps d’arrêt minimal

Parfois, une application doit être déconnectée en raison d’une maintenance planifiée, par exemple une mise à niveau. [Gestion des mises à niveau des applications](sql-database-manage-application-rolling-upgrade.md) explique comment utiliser la géo-réplication active pour activer les mises à niveau propagées de votre application cloud afin de réduire le temps d’arrêt pendant les mises à niveau et de fournir un chemin de récupération en cas de problème.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la conception d’applications pour des bases de données autonomes et des pools élastiques, consultez les articles [Conception d’applications pour la récupération d’urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) et [Stratégies de récupération d’urgence de pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
