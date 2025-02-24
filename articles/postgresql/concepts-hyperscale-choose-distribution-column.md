---
title: Choisir les colonnes de distribution dans Azure Database pour PostgreSQL - Hyperscale (Citus) (préversion)
description: Choix judicieux pour les colonnes de distribution dans les scénarios Hyperscale courants
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e9fba14b8979f739fd29bc277e32fb544221d08a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65078984"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Choisir les colonnes de distribution dans Azure Database pour PostgreSQL - Hyperscale (Citus) (préversion)

Le choix de la colonne de distribution de chaque table est **une des plus importantes** décisions de modélisation. Hyperscale stocke les lignes dans les partitions en fonction de la valeur de la colonne de distribution des lignes.

Le choix correct permet de regrouper les données sur les mêmes nœuds physiques, pour accélérer les requêtes et prendre en charge toutes les fonctionnalités SQL. Un choix incorrect ralentit le système et ne permet pas de prendre en charge toutes les fonctionnalités SQL sur les nœuds.

Cette section donne des conseils sur la colonne de distribution pour les deux scénarios Hyperscale les plus courants.

### <a name="multi-tenant-apps"></a>Applications mutualisées

L’architecture mutualisée utilise une forme de modélisation de base de données hiérarchique pour distribuer les requêtes sur les nœuds dans le groupe de serveurs.  Le haut de la hiérarchie de données est appelé l’*ID de locataire*et doit être stocké dans une colonne sur chaque table.

Hyperscale inspecte les requêtes pour repérer l’ID de locataire impliqué et recherche la partition de table correspondante. Il achemine la requête à un nœud Worker unique qui contient la partition. L’exécution d’une requête avec toutes les données pertinentes placées sur le même nœud est appelée « colocation ».

Le diagramme suivant illustre la colocation dans le modèle de données mutualisé. Il contient deux tables (comptes et campagnes), chacune répartie par `account_id`. Les zones ombrées représentent des partitions, dont la couleur indique le nœud Worker contenu. Les partitions vertes sont stockées ensemble sur un nœud Worker, et les partitions bleues sur un autre. Notez comment une requête de jointure entre les comptes et les campagnes pourrait regrouper toutes les données nécessaires sur un seul nœud si vous limitez les deux tables à la même valeur account\_id.

![Colocation mutualisée](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Pour appliquer cette conception dans votre propre schéma, identifiez ce qui constitue un locataire dans votre application. Les instances courantes incluent une société, un compte, une organisation ou un client. Le nom de colonne ressemble à `company_id` ou `customer_id`. Examinez chacune de vos requêtes et demandez-vous si elle fonctionnerait si elle contenait des clauses WHERE supplémentaires pour restreindre toutes les tables impliquées aux lignes avec le même ID de locataire ?
Les requêtes du modèle mutualisé sont limités à un locataire (par exemple, les requêtes portant sur les ventes ou l’inventaire dans un magasin donné).

#### <a name="best-practices"></a>Bonnes pratiques

-   **Partitionnez les tables distribuées selon une colonne tenant\_id commune.** Par exemple, dans une application SaaS où les locataires sont des entreprises, tenant\_id représente probablement company\_id.
-   **Convertissez les tables de petite taille partagées entre locataires en tables de référence.** Lorsque plusieurs locataires partagent une petite table d’informations, distribuez-la comme une table de référence.
-   **Filtrez toutes les requêtes d’application selon tenant\_id.** Chaque requête doit demander des informations pour un seul locataire à la fois.

Lisez le [didacticiel mutualisé](./tutorial-design-database-hyperscale-multi-tenant.md) pour obtenir un exemple de création pour ce type d’application.

### <a name="real-time-apps"></a>Applications en temps réel

L’architecture mutualisée présente une structure hiérarchique et utilise la colocation de données pour acheminer les requêtes par locataire. En revanche, les architectures en temps réel dépendent des propriétés de distribution spécifiques de leurs données à obtenir un traitement hautement parallèle.

Nous utilisons « ID d’entité » pour désigner les colonnes de distribution dans le modèle en temps réel. Les entités standard sont des utilisateurs, des hôtes ou des appareils.

Les requêtes en temps réel nécessitent en général des agrégats numériques regroupés par date ou par catégorie. Hyperscale envoie ces requêtes à chaque partition pour obtenir des résultats partiels et assemble la réponse finale sur le nœud coordinateur. Les requêtes s’exécutent plus rapidement lorsqu’un maximum de nœuds contribuent, et lorsque aucun nœud unique ne doit faire une quantité disproportionnée du travail.

#### <a name="best-practices"></a>Bonnes pratiques

-   **Choisissez une colonne présentant une cardinalité élevée comme colonne de distribution.** À titre de comparaison, un champ d’\"état\" sur une table de commandes avec les valeurs « nouvelle », « payée » et « livrée » est un choix médiocre de colonne de distribution. Il part du principe que seules ces quelques valeurs existent, ce qui limite le nombre de partitions pouvant stocker les données et le nombre de nœuds pouvant les traiter. Parmi les colonnes présentant une cardinalité élevée, il est en outre judicieux de choisir celles qui sont fréquemment utilisées dans les clauses group-by ou en tant que clés de jointure.
-   **Choisissez une colonne avec une distribution uniforme.** Si vous distribuez une table sur une colonne déviée vers certaines valeurs courantes, les données de la table ont tendance à s’accumuler dans certaines partitions. Les nœuds contenant ces partitions finissent par effectuer plus de travail que les autres nœuds.
-   **Distribuez les tables de faits et de dimension sur leurs colonnes communes.**
    Votre table de faits ne peut avoir qu’une seule clé de distribution. Les tables qui créent une jointure sur une autre clé ne se trouvent pas au même emplacement que la table de faits. Choisissez une dimension pour effectuer la colocation selon la taille des lignes de jointure et la fréquence de jointure.
-   **Modifiez des tables de dimension en tables de référence.** Si une table de dimension ne peut pas être au même emplacement que la table de faits, vous pouvez améliorer les performances des requêtes en distribuant des copies de la table de dimension à tous les nœuds sous la forme d’une table de référence.

Lisez le [didacticiel de tableau de bord mutualisé](./tutorial-design-database-hyperscale-realtime.md) pour obtenir un exemple de création pour ce type d’application.

### <a name="timeseries-data"></a>Données de série chronologique

Dans une charge de travail de série chronologique, les applications interrogent les informations récentes lors de l’archivage d’anciennes informations.

L’erreur la plus fréquente lors de la modélisation des informations de série chronologique dans Hyperscale consiste à utiliser l’horodatage comme colonne de distribution. Une distribution de hachage basée sur le temps distribue le temps de façon apparemment aléatoire dans différentes partitions plutôt que de conserver des plages de temps groupées dans des partitions. Les requêtes impliquant des données temporelles référencent généralement des plages de temps (par exemple, les données les plus récentes). Par conséquent, une telle distribution par hachage conduirait à une surcharge du réseau.

#### <a name="best-practices"></a>Bonnes pratiques

-   **Ne choisissez pas un horodatage comme colonne de distribution.** Choisissez une colonne de distribution différente. Dans une application mutualisée, utilisez l’ID de locataire ou, dans une application en temps réel, l’ID d’entité.
-   **Utilisez le partitionnement de table PostgreSQL pour les données temporelles à la place.** Utilisez le partitionnement de table pour diviser une table volumineuse de données chronologiques en plusieurs tables héritées contenant chacune différentes plages de temps.  La distribution d’une table partitionnée Postgres dans Hyperscale crée des partitions pour les tables héritées.

Lisez le [didacticiel sur les séries chronologiques](https://aka.ms/hyperscale-tutorial-timeseries) pour obtenir un exemple de création pour ce type d’application.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment la [colocation](concepts-hyperscale-colocation.md) entre les requêtes distribuées permettent d’exécuter rapidement les requêtes.
