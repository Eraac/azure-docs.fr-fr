---
title: Visualisation des dépendances dans Azure Migrate | Microsoft Docs
description: Offre une vue d’ensemble des calculs d’évaluation dans le service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: a0ceffbf4666ebc6bb5f95a6f3e2501f86095232
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305659"
---
# <a name="dependency-visualization"></a>Visualisation de dépendance

Le service [Azure Migrate](migrate-overview.md) évalue les groupes de machines locales pour la migration vers Azure. Vous pouvez utiliser la fonctionnalité de visualisation des dépendances dans Azure Migrate pour créer des groupes. Cet article fournit des informations sur cette fonctionnalité.

> [!NOTE]
> La fonctionnalité de visualisation de dépendance n’est pas disponible dans Azure Government.

## <a name="overview"></a>Vue d'ensemble

La visualisation des dépendances dans Azure Migrate permet de créer des groupes hautement fiables pour les évaluations de la migration. À l’aide de la visualisation des dépendances, vous pouvez voir les dépendances de réseau des machines et identifier les machines connexes qui doivent être migrées ensemble vers Azure. Cette fonctionnalité est utile dans les scénarios où vous ne savez pas exactement quelles sont les machines qui constituent votre application et doivent être migrées ensemble vers Azure.

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Azure Migrate utilise la solution [Service Map](../operations-management-suite/operations-management-suite-service-map.md) dans [Journaux d’activité Azure Monitor](../log-analytics/log-analytics-overview.md) pour la visualisation des dépendances.
- Pour tirer parti de la visualisation des dépendances, vous pouvez associer un espace de travail Log Analytics, nouveau ou existant, à un projet Azure Migrate.
- Vous ne pouvez créer ou attacher un espace de travail que dans l’abonnement où le projet de migration est créé.
- Pour attacher un espace de travail Log Analytics à un projet, accédez à la section **Bases** de la page **Vue d’ensemble**, puis cliquez sur **Requiert une configuration**

    ![Associer un espace de travail Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Lors de l’association d’un espace de travail, vous aurez la possibilité de créer un nouvel espace de travail ou de joindre un espace existant :
  - Quand vous créez un espace de travail, vous devez spécifier un nom pour celui-ci. L’espace de travail est ensuite créé dans une région appartenant à la même [zone géographique Azure](https://azure.microsoft.com/global-infrastructure/geographies/) que le projet de migration.
  - Quand vous attachez un espace de travail existant, vous pouvez choisir parmi tous les espaces de travail disponibles dans le même abonnement que le projet de migration. Notez que seuls les espaces de travail qui ont été créés dans une région où [Service Map est pris en charge](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions) sont listés. Afin de joindre un espace de travail, assurez-vous d’avoir accès en lecture à l’espace de travail.

  > [!NOTE]
  > Une fois que vous avez joint un espace de travail à un projet, vous ne pouvez plus le modifier.

- L’espace de travail associé est étiqueté avec la clé **Projet de migration** et la valeur **Nom du projet**, que vous pouvez utiliser pour effectuer une recherche dans le portail Azure.
- Pour accéder à l’espace de travail associé au projet, vous pouvez accéder à la section **Bases** de la page **Vue d’ensemble** du projet.

    ![Accéder à l’espace de travail Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Pour utiliser la visualisation des dépendances, vous devez télécharger et installer des agents sur chaque machine locale que vous souhaitez analyser.  

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) doit être installé sur chaque machine.
- Le programme [Dependency Agent](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) doit être installé sur chaque machine.
- En outre, si certaines de vos machines sont dépourvues de connexion Internet, vous devez télécharger et installer la passerelle Log Analytics sur ces machines.

Vous n’avez pas besoin ces agents sur les machines que vous souhaitez évaluer, sauf si vous utilisez la visualisation des dépendances.

## <a name="do-i-need-to-pay-for-it"></a>Est-ce une fonctionnalité payante ?

Azure Migrate est disponible sans coût supplémentaire. L’utilisation de la fonctionnalité de visualisation des dépendances dans Azure Migrate nécessite Service Map et suppose que vous associiez un espace de travail Log Analytics, nouveau ou existant, au projet Azure Migrate. La fonctionnalité de visualisation des dépendances dans Azure Migrate est gratuite pendant 180 jours dans Azure Migrate.

1. L’utilisation de solutions autres que Service Map dans l’espace de travail Log Analytics entraîne des frais [Log Analytics standard](https://azure.microsoft.com/pricing/details/log-analytics/).
2. Pour prendre en charge des scénarios de migration sans frais supplémentaires, la solution Service Map n’entraîne pas de frais pendant 180 jours à compter du jour où l’espace de travail Log Analytics est associé au projet Azure Migrate. Au bout de 180 jours, des frais Log Analytics standard s’appliquent.

Lorsque vous inscrivez des agents auprès de l’espace de travail, utilisez l’ID et la clé fournis par le projet sur la page des étapes de l’agent d’installation.

Lorsque le projet Azure Migrate est supprimé, l’espace de travail ne l’est pas. Après la suppression du projet, l’utilisation de Service Map n’est pas gratuite et chaque nœud est facturé en fonction du niveau payant de l’espace de travail Log Analytics.

> [!NOTE]
> La fonctionnalité de visualisation des dépendances utilise Service Map via un espace de travail Log Analytics. Depuis le 28 février 2018, avec l’annonce de la disponibilité générale d’Azure Migrate, la fonctionnalité est désormais disponible sans frais supplémentaires. Vous devez créer un nouveau projet pour utiliser l’espace de travail gratuit. Les espaces de travail existants avant la disponibilité générale sont toujours facturés, nous vous recommandons donc d’utiliser un nouveau projet.

En savoir plus sur la tarification Azure Migrate [ici](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Comment gérer l’espace de travail ?

Vous pouvez utiliser l’espace de travail Log Analytics en dehors d’Azure Migrate. Il n’est pas supprimé si vous supprimez le projet de migration dans lequel il a été créé. Si vous n’avez plus besoin de l’espace de travail, [supprimez-le](../azure-monitor/platform/manage-access.md) manuellement.

Ne supprimez pas l’espace de travail créé par Azure Migrate, sauf si vous supprimez le projet de migration. La suppression de l’espace de travail entraînerait un dysfonctionnement de la fonctionnalité de visualisation des dépendances.

## <a name="next-steps"></a>Étapes suivantes
- [Regrouper des machines à l’aide de dépendances de machines](how-to-create-group-machine-dependencies.md)
- [Découvrez plus en détail](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization) les questions fréquemment posées au sujet de la visualisation des dépendances.
