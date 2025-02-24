---
title: 'Continuité et reprise d’activité : Régions jumelées Azure | Microsoft Docs'
description: Apprenez-en plus sur les paires régionales d’Azure, afin d’assurer la résilience des applications en cas de défaillance des centres de données.
author: rayne-wiselman
manager: carmon
ms.service: multiple
ms.topic: article
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: 81ba993e6cbe55b45d34325545754bec561ce479
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514461"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Continuité et reprise d’activité : Régions jumelées Azure

## <a name="what-are-paired-regions"></a>Régions jumelées : définition

Azure fonctionne dans plusieurs zones géographiques à travers le monde. Une zone géographique Azure est une zone définie du monde contenant au moins une région Azure. Une région Azure est une zone géographique contenant un ou plusieurs centres de données.

Chaque région Azure est associée à une autre région au sein de la même région géographique, constituant ainsi des paires régionales. La seule exception est le sud du Brésil qui est associé à une zone en dehors de sa zone géographique. Pour les paires de régions, Azure sérialise les mises à jour de plateforme (maintenance planifiée) pour que les deux régions soient mises à jour l’une après l’autre, et non en même temps. En cas de panne affectant plusieurs régions, au moins l’une des régions de chaque paire est prioritaire pour la récupération.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Figure 1 – Paires régionales Azure

| Geography | Régions jumelées |  |
|:--- |:--- |:--- |
| Asie |Est de l'Asie |Asie du Sud-Est |
| Australie |Est de l’Australie |Australie Sud-Est |
| Australie |Centre de l’Australie |Centre de l’Australie 2 |
| Brésil |Brésil Sud |USA Centre Sud |
| Canada |Centre du Canada |Est du Canada |
| Chine |Chine du Nord |Chine orientale|
| Chine |Chine Nord 2 |Chine orientale 2|
| Europe |Europe Nord (Irlande) |Europe Ouest (Pays-Bas) |
| France |France Centre|France Sud|
| Allemagne |Centre de l’Allemagne |Nord-Est de l’Allemagne |
| Inde |Inde centrale |Inde du Sud |
| Inde |Inde Ouest |Inde Sud |
| Japon |Est du Japon |Ouest du Japon |
| Corée du Sud |Centre de la Corée |Corée du Sud |
| Amérique du Nord |Est des États-Unis |États-Unis de l’Ouest |
| Amérique du Nord |Est des États-Unis 2 |Centre des États-Unis |
| Amérique du Nord |Centre-Nord des États-Unis |États-Unis - partie centrale méridionale |
| Amérique du Nord |Ouest des États-Unis 2 |USA Centre-Ouest 
| Afrique du Sud | Afrique du Sud Nord | Afrique du Sud Ouest
| Royaume-Uni |Ouest du Royaume-Uni |Sud du Royaume-Uni |
| Émirats Arabes Unis | Émirats arabes unis Nord | Émirats arabes unis Centre
| Ministère de la défense des États-Unis |Est des États-Unis – US DoD |Centre des États-Unis – US DoD |
| Gouvernement américain |Gouvernement des États-Unis – Arizona |Gouvernement des États-Unis – Texas |
| Gouvernement américain |US Gov Iowa |Gouvernement américain - Virginie |
| Gouvernement américain |Gouvernement américain - Virginie |Gouvernement des États-Unis – Texas |

Tableau 1 - Mise en correspondance des paires régionales Azure

- La région Inde Ouest est jumelée dans une seule direction. La région secondaire de la région Inde de l’Ouest est Inde du Sud, mais la région secondaire de la région Inde du Sud est Centre de l’Inde.
- La région Brésil Sud est unique, car elle est jumelée avec une région située en dehors de sa propre zone géographique. La région secondaire de la région Brésil Sud est USA Centre Sud. La région secondaire de la région USA Centre Sud n’est pas Brésil Sud.
- La région secondaire de la région US Gov Iowa est US Gov Virginie.
- La région secondaire de la région US Gov Virginie est US Gov Texas.
- La région secondaire de la région US Gov Texas est US Gov Arizona.


Nous vous recommandons de configurer la continuité d’activité et reprise d’activité (BCDR) dans les paires régionales pour tirer parti des stratégies d’isolation et de disponibilité Azure. Pour les applications qui prennent en charge plusieurs régions actives, nous vous recommandons d’utiliser les deux régions d’une paire lorsque cela est possible. Cela garantit la disponibilité optimale des applications, ainsi qu’un temps de récupération réduit en cas de sinistre. 

## <a name="an-example-of-paired-regions"></a>Exemple de régions jumelées
La Figure 2 ci-dessous montre une application hypothétique qui utilise les régions jumelées pour la récupération d’urgence. Les numéros verts mettent en surbrillance les activités entre les régions de trois services Azure (de stockage, de calcul et de base de données) et comment ils sont configurés pour répliquer les régions. Les avantages uniques du déploiement entre les régions jumelées sont mis en surbrillance par les nombres en orange.

![Vue d’ensemble des avantages des région jumelées](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figure 2 – Paire régionale Azure hypothétique

## <a name="cross-region-activities"></a>Activités entre régions
Conformément à la figure 2.

![IaaS](./media/best-practices-availability-paired-regions/1Green.png)**Azure Compute (IaaS)** – Vous devez approvisionner des ressources de calcul supplémentaires à l’avance pour garantir la disponibilité des ressources dans d’autres régions au cours d’un incident. Pour plus d’informations, consultez le [Guide technique de la résilience Azure](resiliency/resiliency-technical-guidance.md).

![Stockage](./media/best-practices-availability-paired-regions/2Green.png) **Stockage Azure** : si vous utilisez des disques managés, découvrez les [sauvegardes inter-régions](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) avec Sauvegarde Azure, et la [réplication de machines virtuelles](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) d’une région à l’autre avec Azure Site Recovery. Si vous utilisez des comptes de stockage, le stockage géo-redondant (GRS, Geo-Redundant Storage) est configuré par défaut quand vous créez un compte de stockage Azure. Avec GRS, vos données sont répliquées trois fois dans la région principale et trois fois dans la région jumelée. Pour plus d'informations, consultez [Options de redondance du stockage Azure](storage/common/storage-redundancy.md).

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png)**Azure SQL Database** – avec la géoréplication dans Azure SQL Database, vous pouvez configurer une réplication asynchrone des transactions vers toute région du monde. En revanche, nous vous recommandons de déployer ces ressources dans une région couplée pour la plupart des scénarios de récupération d’urgence. Pour plus d’informations, consultez [Géoréplication dans Azure SQL Database](sql-database/sql-database-geo-replication-overview.md).

![Resource Manager](./media/best-practices-availability-paired-regions/4Green.png)**Azure Resource Manager** - Resource Manager offre par nature une isolation logique des composants entre les régions. Cela signifie que des échecs logiques dans une région sont moins susceptibles d’avoir un impact sur une autre.

## <a name="benefits-of-paired-regions"></a>Avantages des régions jumelées
Conformément à la figure 2.  

![Isolation](./media/best-practices-availability-paired-regions/5Orange.png)
**Isolation physique** – Quand cela est possible, Azure préfère une séparation de 483 kilomètres (300 miles) au moins entre les centres de données d’une paire régionale, même si ce n’est pas pratique, voire impossible dans toutes les régions géographiques. La séparation physique du centre de données réduit la probabilité de catastrophes naturelles, de troubles civils, de coupures de courant ou de pannes de réseau physique affectant les deux régions en même temps. L’isolation est soumise aux contraintes géographiques (étendue, disponibilité de l’infrastructure réseau et de l’alimentation, réglementations, etc.).  

![Réplication](./media/best-practices-availability-paired-regions/6Orange.png)
**Réplication fournie par plate-forme** - Certains services de stockage géo-redondant fournissent la réplication automatique à la région jumelée.

![Récupération](./media/best-practices-availability-paired-regions/7Orange.png)
**Ordre de récupération de la région** – En cas de panne étendue, la récupération d’une région est hiérarchisée pour chaque paire. Les applications déployées dans des régions jumelées ont la garantie que l’une des régions est récupérée en priorité. Si une application est déployée dans des régions qui ne sont pas jumelées, la récupération peut être retardée. Dans le pire des cas, les régions choisies peuvent être les deux dernières à être récupérées.

![Mises à jour](./media/best-practices-availability-paired-regions/8Orange.png)
**Mises à jour séquentielles** – Les mises à jour planifiées du système Azure sont déployées vers les régions jumelées séquentiellement (pas en même temps) pour limiter les interruptions de service, l’effet des bogues et les échecs logiques dans les rares cas d’échec de mise à jour.

![Données](./media/best-practices-availability-paired-regions/9Orange.png)
**Résidence de données** – Une région se trouve dans la même zone géographique que la région avec laquelle elle est jumelée (à l’exception du Sud du Brésil) pour répondre aux exigences de la résidence de données en termes d’impôts et d’application de la loi.
