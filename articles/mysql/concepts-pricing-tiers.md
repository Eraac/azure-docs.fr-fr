---
title: Niveaux tarifaires pour Azure Database pour MySQL
description: Cet article présente les niveaux tarifaires pour Azure Database pour MySQL.
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: aab3aaafd410c4f85eba7a246d0922128908bf93
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445431"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Niveaux tarifaires Azure Database pour MySQL

Vous pouvez créer un serveur Azure Database pour MySQL dans un des trois différents niveaux tarifaires : De base, Usage général et À mémoire optimisée. Les niveaux tarifaires diffèrent par la quantité de calcul dans vCores qui peut être configurée, la mémoire par vCore et la technologie de stockage utilisée pour stocker les données. Toutes les ressources sont approvisionnées au niveau du serveur MySQL. Un serveur peut avoir une ou plusieurs bases de données.

|    | **De base** | **Usage général** | **Mémoire optimisée** |
|:---|:----------|:--------------------|:---------------------|
| Génération de calcul | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Mémoire par vCore | 2 Go | 5 GO | 10 Go |
| Taille de stockage | 5 Go à 1 To | 5 Go à 4 To | 5 Go à 4 To |
| Type de stockage | Stockage Azure Standard | Stockage Premium Azure | Stockage Premium Azure |
| Période de rétention de sauvegarde de bases de données | 7 à 35 jours | 7 à 35 jours | 7 à 35 jours |

Pour choisir un niveau tarifaire, utilisez le tableau suivant comme point de départ.

| Niveau tarifaire | Charges de travail cibles |
|:-------------|:-----------------|
| De base | Charges de travail qui nécessitent des performances légères en termes de calcul et d’E/S. Il s’agit, par exemple, de serveurs utilisés pour le développement ou le test, ou pour des applications à petite échelle rarement utilisées. |
| Usage général | La plupart des charges de travail professionnelles qui nécessitent une capacité de calcul et de mémoire équilibrée avec un débit d’E/S extensible. Il s’agit, par exemple, de serveurs destinés à l’hébergement d’applications web et mobiles, ainsi que d’autres applications d’entreprise.|
| Mémoire optimisée | Charges de travail de base de données haute performance qui nécessitent des performances en mémoire suffisantes pour un traitement plus rapide des transactions et une simultanéité plus élevée. Il s’agit, par exemple, de serveurs destinés au traitement de données en temps réel et à des applications transactionnelles ou analytiques haute performance.|

Après avoir créé un serveur, le nombre de vCores, la génération du matériel et le niveau tarifaire peuvent être augmentés ou diminués (excepté à vers et à partir de la version De base) en quelques secondes. Vous pouvez également augmenter ou diminuer de manière indépendante la quantité de stockage et la période de rétention des sauvegardes sans interruption de l’application. Vous ne pouvez pas modifier le type de stockage de sauvegarde après la création d’un serveur. Pour plus d’informations, consultez la section [Ressources de mise à l’échelle](#scale-resources).

## <a name="compute-generations-and-vcores"></a>Générations de calcul et vCores

Les ressources de calcul sont fournies en tant que vCores, représentant le processeur logique du matériel sous-jacent. Les régions Chine Est 1, Chine Nord 1, US DoD Centre et US DoD Est utilisent des processeurs logiques Gen 4 basés sur des processeurs Intel E5-2673 v3 (Haswell) à 2,4 GHz. Toutes les autres régions utilisent des processeurs logiques Gen 5 basés sur des processeurs Intel E5-2673 v4 (Broadwell) à 2,3 GHz.

## <a name="storage"></a>Stockage

Le stockage que vous approvisionnez est la quantité de stockage disponible pour votre serveur Azure Database pour MySQL. Le stockage est utilisé pour les fichiers de base de données, les fichiers temporaires, les journaux d’activité de transaction, et les journaux d’activité du serveur MySQL. La quantité totale de stockage que vous approvisionnez définit également la capacité d’E/S disponible sur votre serveur.

|    | **De base** | **Usage général** | **Mémoire optimisée** |
|:---|:----------|:--------------------|:---------------------|
| Type de stockage | Stockage Azure Standard | Stockage Premium Azure | Stockage Premium Azure |
| Taille de stockage | 5 Go à 1 To | 5 Go à 4 To | 5 Go à 4 To |
| Taille d’incrément de stockage | 1 Go | 1 Go | 1 Go |
| E/S par seconde | Variable |3 E/S par seconde/Go<br/>Min 100 E/S par seconde<br/>Max 6000 E/S par seconde | 3 E/S par seconde/Go<br/>Min 100 E/S par seconde<br/>Max 6000 E/S par seconde |

Vous pouvez ajouter de la capacité de stockage supplémentaire pendant et après la création du serveur et autoriser le système à faire évoluer le stockage automatiquement en fonction de la consommation de votre charge de travail. Le niveau De base n’offre pas de garantie d’E/S par seconde. Dans les niveaux tarifaires Usage général et À mémoire optimisée, les IOPS augmentent avec la taille de stockage approvisionnée selon un ratio de 3:1.

Vous pouvez surveiller votre consommation d’E/S dans le Portail Azure ou à l’aide des commandes Azure CLI. Les métriques pertinentes à surveiller sont [la limite de stockage, le pourcentage de stockage, le stockage utilisé et le pourcentage d’E/S](concepts-monitoring.md).

### <a name="large-storage-preview"></a>Stockage volumineux (préversion)

Nous avons augmenté les limites de stockage dans nos niveaux de service Usage général et Mémoire optimisée. Les serveurs nouvellement créés qui ont opté pour la préversion peuvent configurer jusqu’à 16 To de stockage. Les IOPS évoluent à un taux de 3:1 jusqu’à 20 000. Comme pour le stockage à disponibilité générale actuel, vous pouvez ajouter de la capacité de stockage supplémentaire après la création du serveur et autoriser le système à faire évoluer le stockage automatiquement en fonction de la consommation de votre charge de travail.

|              | **Usage général** | **Mémoire optimisée** |
|:-------------|:--------------------|:---------------------|
| Type de stockage | Stockage Premium Azure | Stockage Premium Azure |
| Taille de stockage | 32 Go à 16 To| 32 Go à 16 To |
| Taille d’incrément de stockage | 1 Go | 1 Go |
| E/S par seconde | 3 E/S par seconde/Go<br/>Min 100 E/S par seconde<br/>Max 20 000 IOPS| 3 E/S par seconde/Go<br/>Min 100 E/S par seconde<br/>Max 20 000 IOPS |

> [!IMPORTANT]
> Le stockage volumineux est actuellement en préversion publique dans les régions suivantes : USA Est, USA Est 2, USA Centre, USA Ouest, Europe Nord, Europe Ouest, Royaume-Uni Sud, Royaume-Uni Ouest, Asie Sud-Est, Asie Est, Asie-Pacifique, Japon Est, Japon Ouest, Corée Centre, Corée Sud, Australie Est, Australie Sud-Est.
>
> La préversion du stockage volumineux ne prend actuellement pas en charge les éléments suivants :
>
> * Sauvegardes géographiquement redondantes
> * Réplication entre des régions

### <a name="reaching-the-storage-limit"></a>Atteindre la limite de stockage

Les serveurs avec moins de 100 Go de stockage configurés sont marqués en lecture seule si l’espace de stockage libre est inférieur à 512 Mon ou à 5 % de la taille configurée. Les serveurs avec plus de 100 Go de stockage configurés sont marqués en lecture seule lorsque l’espace de stockage libre est inférieur à 5 Go.

Par exemple, si vous avez provisionné 110 Go de stockage et que l’utilisation réelle dépasse 105 Go, le serveur est marqué en lecture seule. Sinon, si vous avez provisionné 5 Go de stockage, le serveur est marqué en lecture seule lorsque le stockage disponible est inférieur à 512 Mo.

Pendant que le service tente de marquer le serveur en lecture seule, toutes les nouvelles demandes de transactions d’écriture sont bloquées et les transactions actives existantes continuent de s’exécuter. Une fois que le serveur est marqué en lecture seule, toutes les opérations d’écriture et validations de transaction ultérieures échouent. Les requêtes de lecture continueront de fonctionner sans interruption. Après avoir augmenté le stockage provisionné, le serveur sera prêt à accepter de nouvelles transactions d’écriture.

Nous vous recommandons d’activer la croissance automatique du stockage ou de configurer une alerte pour vous avertir lorsque votre serveur de stockage est proche du seuil, afin d’éviter la mise en lecture seule. Pour plus d’informations, consultez la documentation sur [comment configurer une alerte](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Croissance automatique du stockage

Si la croissance automatique du stockage est activée, le stockage évolue automatiquement sans affecter la charge de travail. Pour les serveurs avec moins de 100 Go de stockage configurés, la taille de stockage configurée augmente de 5 Go dès que l’espace de stockage libre est inférieur à 1 Go ou 10 % (selon la valeur la plus élevée) du stockage configuré. Pour les serveurs avec plus de 100 Go de stockage configuré, la taille de stockage configurée augmente de 5 % lorsque l’espace de stockage libre est inférieur à 5 % de la taille de stockage configurée. Les limites de stockage maximales indiquées ci-dessus s’appliquent.

Par exemple, si vous avez provisionné 1 000 Go de stockage et que l’utilisation réelle dépasse 950 Go, la taille de stockage du serveur passe à 1 050 Go. Sinon, si vous avez configuré 10 Go de stockage, la taille de stockage passe à 15 Go lorsque moins de 1 Go de stockage est libre.

## <a name="backup"></a>Sauvegarde

Le service effectue automatiquement des sauvegardes de votre serveur. La période de rétention minimale pour les sauvegardes est de sept jours. Vous pouvez définir une période de rétention allant jusqu’à 35 jours. La rétention peut être ajustée à tout moment pendant la durée de vie du serveur. Vous avez le choix entre les sauvegardes géoredondantes ou localement redondantes. Les sauvegardes géoredondantes sont également stockées dans la [région associée géographiquement](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) de la région dans laquelle votre serveur a été créé. Cette redondance fournit un niveau de protection en cas de sinistre. Vous obtenez également la possibilité de restaurer votre serveur vers n’importe quelle autre région Azure dans laquelle le service est disponible avec des sauvegardes géoredondantes. Il n’est pas possible de changer entre les deux options de stockage de sauvegarde après la création du serveur.

## <a name="scale-resources"></a>Mettre les ressources à l’échelle

Après avoir créé votre serveur, vous pouvez modifier de manière indépendante les vCores, la génération du matériel, le niveau tarifaire (excepté à partir de la version De base), la quantité de stockage et la période de rétention de sauvegarde. Vous ne pouvez pas modifier le type de stockage de sauvegarde après la création d’un serveur. Le nombre de vCores peut être augmenté ou diminué. La période de rétention de sauvegarde peut être augmentée ou diminuée et va de 7 à 35 jours. La taille de stockage ne peut être qu’augmentée. La mise à l’échelle des ressources peut être effectuée par le biais du portail ou d’Azure CLI. Pour obtenir un exemple de mise à l’échelle à l’aide d’Azure CLI, consultez [Surveiller et mettre à l’échelle un serveur Azure Database pour MySQL à l’aide d’Azure CLI](scripts/sample-scale-server.md).

Lorsque vous modifiez le nombre de vCores, la génération du matériel ou le niveau tarifaire, une copie du serveur d’origine est créée avec la nouvelle allocation du calcul. Une fois que le nouveau serveur est opérationnel, les connexions sont basculées vers le nouveau serveur. Pendant le moment durant lequel le système bascule vers le nouveau serveur, aucune nouvelle connexion ne peut être établie, et toutes les transactions non validées sont restaurées. Cette fenêtre varie, mais dans la plupart des cas elle dure moins d’une minute.

La mise à l’échelle du stockage et la modification de la période de rétention de sauvegarde sont des opérations en ligne. Aucune interruption de service n’a lieu et votre application n’est pas affectée. Comme les E/S par seconde augmentent avec la taille du stockage approvisionné, vous pouvez augmenter le nombre de E/S par seconde disponibles pour votre serveur en mettant à l’échelle l’espace de stockage.

## <a name="pricing"></a>Tarifs

Pour obtenir les dernières informations sur la tarification, veuillez consulter le service [Page de tarification](https://azure.microsoft.com/pricing/details/mysql/). Pour voir le coût de la configuration souhaitée, le [Portail Azure](https://portal.azure.com/#create/Microsoft.MySQLServer) affiche le coût mensuel dans l’onglet **Niveau tarifaire** selon les options que vous avez sélectionnées. Si vous n’avez pas d’abonnement Azure, vous pouvez utiliser la calculatrice de prix Azure pour obtenir une estimation. Pour personnaliser les options, sur le site web [Calculatrice de prix d’Azure](https://azure.microsoft.com/pricing/calculator/), sélectionnez **Ajouter des éléments**, développez la catégorie **Bases de données**, puis choisissez **Azure Database pour MySQL**.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Créer un serveur MySQL dans le portail](howto-create-manage-server-portal.md).
- Apprenez-en davantage sur les [limites de service](concepts-limits.md).
- Apprendre à [monter en charge avec des réplicas en lecture](howto-read-replicas-portal.md).
