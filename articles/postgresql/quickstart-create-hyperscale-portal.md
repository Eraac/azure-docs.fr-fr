---
title: Azure Database pour PostgreSQL – Démarrage rapide d’Hyperscale (Citus) (préversion)
description: Guide de démarrage rapide pour créer et interroger des tables distribuées sur Azure Database pour PostgreSQL Hyperscale (Citus) (préversion).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: efc3801ab03f739761a41bec754f975fe43dcd8e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792022"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Démarrage rapide : Créer une base de données Azure Database pour PostgreSQL - Hyperscale (Citus) dans le portail Azure

Azure Database pour PostgreSQL est un service administré que vous utilisez pour exécuter, gérer et mettre à l’échelle des bases de données PostgreSQL hautement disponibles dans le cloud. Ce guide de démarrage rapide vous montre comment créer un groupe de serveurs Azure Database pour PostgreSQL - Hyperscale (Citus) (préversion) avec le portail Azure. Vous allez explorer les données distribuées : partitionnement de tables entre les nœuds, ingestion d’exemples de données et exécution de requêtes qui s’exécutent sur plusieurs nœuds.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Créer et distribuer des tables

Une fois connecté au nœud coordinateur Hyperscale avec psql, vous pouvez effectuer certaines tâches de base.

Il existe trois types de tables dans les serveurs Hyperscale :

- Tables distribuées ou partitionnées (réparties dans le but de faciliter la scalabilité pour les performances et la parallélisation)
- Tables de référence (plusieurs copies gérées)
- Tables locales (souvent utilisées pour les tables d’administration interne)

Dans ce guide de démarrage rapide, nous abordons principalement les tables distribuées.

Le modèle de données avec lequel nous allons travailler est simple : des données utilisateur et des données d’événements provenant de GitHub. Les événements incluent la création de duplication (fork), des commits git liés à une organisation, etc.

Une fois que vous vous êtes connecté via psql, nous créons nos tables. Dans la console psql, exécutez :

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

Le champ `payload` de `github_events` a le type de données JSONB. JSONB est le type de données JSON sous forme binaire dans Postgres. Ce type de données facilite le stockage d’un schéma plus flexible dans une seule colonne.

Postgres peut créer un index `GIN` sur ce type, qui indexe chaque clé et chaque valeur qui s’y trouve. Avec un index, il devient rapide et facile d’interroger la charge utile avec différentes conditions. Poursuivons et créons deux index avant de charger nos données. Dans psql :

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Ensuite, nous allons placer ces tables Postgres sur le nœud coordinateur et indiquer à Hyperscale de les partitionner sur les workers. Pour cela, nous allons exécuter une requête pour chaque table, en spécifiant la clé sur laquelle la partitionner. Dans notre exemple, nous allons partitionner la table des événements et des utilisateurs sur `user_id` :

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Nous sommes prêts à charger des données. Toujours dans psql, dépensez pour télécharger les fichiers :

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Ensuite, chargez les données à partir des fichiers dans les tables distribuées :

```sql
\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Exécuter des requêtes

Il est maintenant temps d’exécuter quelques requêtes. Commençons par une simple instruction `count (*)` pour voir la quantité de données que nous avons chargées :

```sql
SELECT count(*) from github_events;
```

Ceci a parfaitement fonctionné. Nous reviendrons sous peu à ce genre d’agrégation, mais pour l’instant, nous allons examiner quelques autres requêtes. Dans la colonne `payload` JSONB se trouvent différentes données, mais elles varient selon le type d’événement. Les événements `PushEvent` contiennent une taille qui inclut le nombre de commits distincts pour l’envoi (push). Nous pouvons l’utiliser pour trouver le nombre total de commits par heure :

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Jusqu’à présent, les requêtes impliquaient exclusivement github\_events, mais nous pouvons combiner ces informations avec github\_users. Comme nous avons partitionné les utilisateurs et les événements sur le même identificateur (`user_id`), les lignes des deux tables avec des ID d’utilisateur correspondants sont [colocalisées](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) sur les mêmes nœuds de base de données et peuvent facilement faire l’objet d’une jointure.

Si nous faisons une jointure sur `user_id`, Hyperscale peut procéder à l’exécution de la jointure au niveau des partitions pour permettre une exécution en parallèle sur les nœuds worker. Par exemple, recherchons les utilisateurs qui ont créé le plus grand nombre de dépôts :

```sql
SELECT login, count(*)
FROM github_events ge
JOIN github_users gu
ON ge.user_id = gu.user_id
WHERE event_type = 'CreateEvent' AND
      payload @> '{"ref_type": "repository"}'
GROUP BY login
ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Au cours des étapes précédentes, vous avez créé des ressources Azure dans un groupe de serveurs. Si vous pensez ne pas avoir besoin de ces ressources à l’avenir, supprimez le groupe de serveurs. Appuyez sur le bouton **Supprimer** dans la page **Vue d’ensemble** de votre groupe de serveurs. Quand vous y êtes invité dans une page contextuelle, confirmez le nom du groupe de serveurs, puis cliquez sur le bouton **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez découvert comment configurer un groupe de serveurs Hyperscale (Citus). Vous vous êtes connecté avec psql, vous avez créé un schéma et vous avez distribué des données.

Suivez maintenant un tutoriel pour créer des applications multilocataires scalables.
> [!div class="nextstepaction"]
> [Concevoir une base de données multilocataire](https://aka.ms/hyperscale-tutorial-multi-tenant)
