---
title: Migrer votre entrepôt Azure SQL Data Warehouse existant vers Gen2 | Microsoft Docs
description: Instructions concernant la migration d’un entrepôt de données existant vers Gen2 et la planification de la migration par région.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 3141f3a1d6a9f09261dee4113276af72168e35e8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444705"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Mettre à niveau votre entrepôt de données vers Gen2

Microsoft aide à réduire le coût d’entrée de gamme de l’exécution d’un entrepôt de données.  Des niveaux de service de calcul plus bas capables de gérer des requêtes exigeantes sont désormais disponibles pour Azure SQL Data Warehouse. Lire l’intégralité de l’annonce [Prise en charge du niveau de service de calcul plus bas Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). La nouvelle offre est disponible dans les régions indiquées dans le tableau ci-dessous. Pour les régions prises en charge, les entrepôts de données Gen1 existants peuvent être mis à niveau vers Gen2 à l’aide de l’une des méthodes suivantes :

- **Processus de mise à niveau automatique :** Les mises à niveau automatiques ne commencent pas dès que le service est disponible dans une région.  Quand les mises à niveau automatiques commenceront dans une région spécifique, les mises à niveau individuelles de DW s’effectueront pendant la planification de maintenance que vous avez sélectionnée.
- [**Mise à niveau automatique vers Gen2 :** ](#self-upgrade-to-gen2) Vous pouvez contrôler quand mettre à niveau en effectuant une mise à niveau automatique vers Gen2. Si votre région n’est pas encore prise en charge, vous pouvez restaurer à partir d’un point de restauration directement vers une instance Gen2 dans une région prise en charge.

## <a name="automated-schedule-and-region-availability-table"></a>Tableau des planifications automatisées et de la disponibilité dans les régions

Le tableau suivant récapitule par région le moment où le niveau de capacité de calcul Gen2 inférieur sera disponible et où les mises à niveau automatiques commencent. Les dates sont susceptibles d’être modifiées. Reconsultez-le pour voir quand votre région sera disponible.

\* indique qu’une planification spécifique pour la région est actuellement non disponible.

| **Région** | **Gen2 inférieur disponible** | **Commencement de la mise à niveau automatique** |
|:--- |:--- |:--- |
| Australie Est |Disponible |1er juin 2019 |
| Australie Sud-Est |Disponible |1er mai 2019 |
| Brésil Sud |Disponible |1er juin 2019 |
| Centre du Canada |Disponible |1er juin 2019 |
| Est du Canada |\* |\* |
| USA Centre |Disponible |1er juin 2019 |
| Chine orientale |\* |\* |
| Chine orientale 2 |\* |Gen2 uniquement |
| Chine du Nord |\* |\* |
| Chine Nord 2 |Disponible |Gen2 uniquement |
| Asie Est |Disponible |1er juin 2019 |
| USA Est |Disponible |1er juin 2019 |
| USA Est 2 |Disponible |1er juin 2019 |
| France Centre |\* |1er juin 2019 |
| Centre de l’Allemagne |\* |\* |
| Allemagne Centre-Ouest |1er septembre 2019|2 janvier 2020 |
| Inde Centre |Disponible |1er juin 2019 |
| Sud de l’Inde |Disponible |1er juin 2019 |
| Japon Est |Disponible |1er juin 2019 |
| Japon Ouest |Disponible |1er mai 2019 |
| Centre de la Corée |Disponible |1er juin 2019 |
| Corée du Sud |Disponible |1er mai 2019 |
| USA Centre Nord |Disponible |1er mai 2019 |
| Europe Nord |Disponible |1er juin 2019 |
| USA Centre Sud |Disponible |1er juin 2019 |
| Asie Sud-Est |Disponible |1er juin 2019 |
| Sud du Royaume-Uni |Disponible en 2019 |1er juin 2019 |
| Ouest du Royaume-Uni |\*|\* |
| USA Centre-Ouest |2 septembre 2019 |2 janvier 2020|
| Europe Ouest |Disponible |1er juin 2019 |
| USA Ouest |Disponible |1er juin 2019 |
| USA Ouest 2 |Disponible |1er juin 2019 |

## <a name="automatic-upgrade-process"></a>Processus de mise à niveau automatique

Selon le graphique de disponibilité ci-dessus, nous allons planifier des mises à niveau automatisées pour vos instances Gen1. Pour éviter des interruptions inattendues sur la disponibilité de l’entrepôt de données, les mises à niveau automatiques seront planifiées pendant votre planification de maintenance. La possibilité de créer une nouvelle instance Gen1 sera désactivée dans les régions où des mise à niveau automatiques vers Gen2 sont effectuées. Gen1 sera déconseillée une fois que les mises à niveau automatiques auront été effectuées. Pour plus d’informations sur les planifications, consultez [Afficher une planification de maintenance](viewing-maintenance-schedule.md).

Le processus de mise à niveau impliquera une brève baisse de connectivité (environ 5 min) quand nous redémarrons votre entrepôt de données.  Une fois que votre entrepôt de données aura été redémarré, il sera entièrement disponible pour utilisation. Cependant, vous pouvez être confronté à une délai des performances pendant que le processus de mise à niveau continue à mettre à niveau les fichiers de données en arrière-plan. La durée totale de la dégradation des performances varie en fonction de la taille de vos fichiers de données.

Vous pouvez également accélérer le processus de mise à niveau des fichiers de données en exécutant la commande [Alter Index Rebuild](sql-data-warehouse-tables-index.md) sur toutes les tables columnstore primaires à l’aide d’une plus large classe de ressources et de SLO après le redémarrage.

> [!NOTE]
> Alter Index Rebuild est une opération hors connexion et les tables ne seront pas disponibles tant que la reconstruction ne sera pas terminée.

## <a name="self-upgrade-to-gen2"></a>Mise à niveau automatique vers Gen2

Vous pouvez choisir d’effectuer une mise à niveau automatique en suivant ces étapes sur un entrepôt de données Gen1 existant. Si vous choisissez la mise à niveau automatique, vous devez l’effectuer avant le début du processus de mise à niveau automatique dans votre région. Vous évitez ainsi tout risque de conflit dû aux mises à niveau automatique.

Deux options s’offrent à vous quand vous procédez à une mise à niveau automatique.  Vous pouvez soit mettre à niveau votre entrepôt de données actuel sur place, soit restaurer un entrepôt de données Gen1 dans une instance Gen2.

- [Effectuer une mise à niveau sur place](upgrade-to-latest-generation.md) : cette option met à niveau votre entrepôt de données Gen1 existant vers Gen2. Le processus de mise à niveau impliquera une brève baisse de connectivité (environ 5 min) quand nous redémarrons votre entrepôt de données.  Une fois que votre entrepôt de données aura été redémarré, il sera entièrement disponible pour utilisation. Si vous rencontrez des problèmes pendant la mise à niveau, ouvrez une [demande de support](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) et indiquez « Mise à niveau vers Gen2 » comme cause possible.
- [Effectuer une mise à niveau à partir du point de restauration](sql-data-warehouse-restore.md) : créez un point de restauration défini par l’utilisateur sur votre entrepôt de données Gen1 actuel, puis effectuez la restauration directement vers une instance Gen2. L’entrepôt de données Gen1 existant est conservé. Une fois la restauration terminée, votre entrepôt de données Gen2 sera entièrement disponible pour utilisation.  Une fois tous les processus de test et de validation exécutés sur l’instance Gen2 restaurée, l’instance Gen1 d’origine peut être supprimée.

   - Étape 1 : À partir du portail Azure, [créez un point de restauration défini par l’utilisateur](sql-data-warehouse-restore.md#create-a-user-defined-restore-point-using-the-azure-portal).
   - Étape 2 : Lors d’une restauration à partir d’un point de restauration défini par l’utilisateur, définissez le « niveau de performances » sur votre niveau Gen2 préféré.

Vous pouvez être confronté à un délai de dégradation des performances pendant que le processus de mise à niveau continue à mettre à niveau les fichiers de données en arrière-plan. La durée totale de la dégradation des performances varie en fonction de la taille de vos fichiers de données.

Pour accélérer le processus de migration de données en arrière-plan, vous pouvez forcer immédiatement le déplacement des données en exécutant la commande [Alter Index Rebuild](sql-data-warehouse-tables-index.md) sur toutes les tables columnstore primaires que vous interrogez sur une plus large classe de SLO et de ressources.

> [!NOTE]
> Alter Index Rebuild est une opération hors connexion et les tables ne seront pas disponibles tant que la reconstruction ne sera pas terminée.

Si vous rencontrez des problèmes avec votre entrepôt de données, créez une [demande de support](sql-data-warehouse-get-started-create-support-ticket.md) et indiquez « Mise à niveau de Gen2 » comme cause possible.

Pour plus d’informations, consultez [Mettre à niveau vers Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Questions fréquentes sur la migration

**Q : Le coût de Gen2 est-il le même que celui de Gen1 ?**

- R : Oui.

**Q : Comment les mises à niveau affecteront-elles mes scripts d’automatisation ?**

- R : Tout script d’automatisation qui fait référence à un objectif de niveau de service doit être changé pour correspondre à l’équivalent Gen2.  Consultez les informations détaillées [ici](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**Q : Combien de temps prend normalement une mise à niveau automatique ?**

- R : Vous pouvez effectuer une mise à niveau sur place ou à partir d’un point de restauration.  
   - Une mise à niveau sur place provoque une suspension momentanée puis une reprise de votre entrepôt de données.  Un processus en arrière-plan continuera pendant que l’entrepôt de données sera en ligne.  
   - L’opération prend plus de temps si vous effectuez la mise à niveau par le biais d’un point de restauration, car la mise à niveau passera par le processus de restauration complète.

**Q : Combien de temps prend la mise à niveau automatique ?**

- R : Le temps d’arrêt réel pour la mise à niveau est uniquement le temps nécessaire pour suspendre et reprendre le service, qui est compris entre 5 et 10 minutes. Après la courte interruption, un processus en arrière-plan exécutera une migration de stockage. La durée du processus en arrière-plan dépend de la taille de votre entrepôt de données.

**Q : Quand cette mise à niveau automatique aura-t-elle lieu ?**

- R : Pendant votre planification de maintenance. Exploiter la planification de maintenance que vous avez choisie limite les perturbations pour votre activité.

**Q : Que dois-je faire si mon processus de mise à niveau en arrière-plan semble être bloqué ?**

 - R : Lancez une réindexation de vos tables Columnstore. Notez que la réindexation de la table s’effectuera hors connexion.

**Q : Que se passe-t-il si Gen2 n’a pas l’objectif de niveau de service que j’ai sur Gen1 ?**
- R : Si vous exécutez DW600 ou DW1200 sur Gen1, il est conseillé d’utiliser DW500c ou DW1000c, respectivement, car Gen2 fournit plus de mémoire, plus de ressources et des performances plus élevées que Gen1.

**Q : Puis-je désactiver la géosauvegarde ?**
- R :  Non. La géosauvegarde est une fonctionnalité d’entreprise permettant de préserver la disponibilité de votre entrepôt de données dans le cas où une région n’est plus disponible. Si vous avez d’autres questions, ouvrez une [demande de support](sql-data-warehouse-get-started-create-support-ticket.md).

**Q : Existe-t-il une différence de syntaxe T-SQL entre Gen1 et Gen2 ?**

- R : Aucun changement n’affecte la syntaxe du langage T-SQL entre Gen1 et Gen2.

**Q : Gen2 prend-il en charge les fenêtres de maintenance ?**

- R : Oui.

**Q : Pourrai-je créer une instance Gen1 après la mise à niveau de ma région ?**

- R :  Non. Une fois qu’une région aura été mise à niveau, la création d’instances Gen1 sera désactivée.

## <a name="next-steps"></a>Étapes suivantes

- [Étapes de la mise à niveau](upgrade-to-latest-generation.md)
- [Fenêtres de maintenance](maintenance-scheduling.md)
- [Supervision de l’intégrité des ressources](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Revue avant de commencer une migration](upgrade-to-latest-generation.md#before-you-begin)
- [Mise à niveau sur place et mise à niveau à partir d’un point de restauration](upgrade-to-latest-generation.md)
- [Créer un point de restauration défini par l’utilisateur](sql-data-warehouse-restore.md#restore-through-the-azure-portal)
- [Découvrir comment effectuer une restauration vers Gen2](sql-data-warehouse-restore.md#restore-an-active-or-paused-database-using-the-azure-portal)
- [Ouvrir une demande de support SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)
