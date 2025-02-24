---
title: Journaux de serveur dans Azure Database pour PostgreSQL - Serveur unique
description: Cet article explique comment Azure Database pour PostgreSQL - Serveur unique génère les journaux des requêtes et des erreurs et comment la rétention de journal est configurée.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4d1cf2c59e324cedd9b747b1ac65d6edcb9deb45
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067350"
---
# <a name="server-logs-in-azure-database-for-postgresql---single-server"></a>Journaux de serveur dans Azure Database pour PostgreSQL - Serveur unique
La base de données Azure pour PostgreSQL génère des journaux d’activité des requêtes et des erreurs. Les journaux d’activité des requêtes et des erreurs peuvent être utilisés pour identifier, résoudre et réparer les erreurs de configuration et les problèmes de performances. (L’accès aux journaux d’activité des transactions n’est pas pris en charge.) 

## <a name="configure-logging"></a>Configuration de la journalisation 
Vous pouvez configurer la journalisation sur votre serveur avec les paramètres de journalisation. Sur chaque nouveau serveur, les options **log_checkpoints** et **log_connections** sont activées par défaut. Il existe d’autres paramètres que vous pouvez définir en fonction de vos besoins de journalisation : 

![Azure Database pour PostgreSQL - Paramètres de journalisation](./media/concepts-server-logs/log-parameters.png)

Pour plus d’informations sur ces paramètres, consultez la documentation PostgreSQL [Signalement et journalisation des erreurs](https://www.postgresql.org/docs/current/static/runtime-config-logging.html). Pour savoir comment configurer les paramètres Azure Database pour PostgreSQL, consultez la [documentation du portail](howto-configure-server-parameters-using-portal.md) ou la [documentation de la CLI](howto-configure-server-parameters-using-cli.md).

## <a name="access-server-logs-through-portal-or-cli"></a>Accès aux journaux d’activité serveur via le portail ou l’interface CLI
Si vous avez activé les journaux d’activité, vous pouvez y accéder à partir d’Azure Database pour PostgreSQL avec le [portail Azure](howto-configure-server-logs-in-portal.md), [Azure CLI](howto-configure-server-logs-using-cli.md) et les API REST Azure. Les fichiers journaux tournent toutes les heures ou par tranches de 100 Mo, sachant que la limite atteinte en premier prévaut. Vous pouvez définir la période de conservation de ce stockage de journal en utilisant le paramètre **log\_période\_conservation** associé à votre serveur. La valeur par défaut est de 3 jours et la valeur maximale est de 7 jours. Votre serveur doit disposer de suffisamment de stockage pour contenir les fichiers journaux conservés. (Ce paramètre de rétention ne régit pas les journaux de diagnostic Azure.)


## <a name="diagnostic-logs"></a>Journaux de diagnostic
Azure Database pour PostgreSQL est intégré aux journaux de diagnostic Azure Monitor. Une fois que vous avez activé les journaux sur votre serveur PostgreSQL, vous pouvez choisir qu’ils soient transmis vers des [journaux Azure Monitor](../azure-monitor/log-query/log-query-overview.md), des Event Hubs ou Stockage Azure. Pour en savoir plus sur l’activation des journaux de diagnostic, consultez la section des procédures de la [documentation des journaux de diagnostic](../azure-monitor/platform/diagnostic-logs-overview.md). 

> [!IMPORTANT]
> Cette fonctionnalité de diagnostic des journaux de serveur n’est disponible que dans les [niveaux tarifaires](concepts-pricing-tiers.md) Usage général et Mémoire optimisée.

Le tableau suivant décrit ce que contient chaque journal. En fonction du point de terminaison de sortie choisi, les champs et l’ordre dans lequel ils apparaissent peuvent varier. 

|**Champ** | **Description** |
|---|---|
| TenantId | Votre ID d’abonné |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Horodatage du moment où le journal a été enregistré en UTC |
| Type | Type de journal. Toujours `AzureDiagnostics` |
| SubscriptionId | GUID de l’abonnement auquel appartient le serveur |
| ResourceGroup | Nom du groupe de ressources auquel le serveur appartient |
| ResourceProvider | Nom du fournisseur de ressources. Toujours `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | URI de ressource |
| Ressource | Nom du serveur |
| Catégorie | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Niveau de journalisation, par exemple : LOG, ERROR, NOTICE |
| Message | Message de journal principal | 
| Domaine | Version du serveur, par exemple : postgres-10 |
| Détails | Message du journal secondaire (le cas échéant) |
| ColumnName | Nom de la colonne (le cas échéant) |
| SchemaName | Nom du schéma (le cas échéant) |
| DatatypeName | Nom du type de données (le cas échéant) |
| LogicalServerName | Nom du serveur | 
| _ResourceId | URI de ressource |

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur l’accès aux journaux d’activité à partir du [portail Azure](howto-configure-server-logs-in-portal.md) ou de l’[interface de ligne de commande Azure](howto-configure-server-logs-using-cli.md).
- En savoir plus sur la [tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
