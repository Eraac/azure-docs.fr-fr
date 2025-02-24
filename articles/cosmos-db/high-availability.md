---
title: Haute disponibilité dans Azure Cosmos DB
description: Cet article décrit comment Azure Cosmos DB offre une haute disponibilité
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 928c943e21e7d00b87ac1e506b98d47107ac4348
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508557"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Haute disponibilité avec Azure Cosmos DB

Azure Cosmos DB réplique en toute transparence vos données vers toutes les régions Azure associées votre compte Cosmos. Cosmos DB emploie plusieurs couches de redondance pour vos données, comme le montre l’image suivante :

![Partitionnement physique](./media/high-availability/cosmosdb-data-redundancy.png)

- Les données dans les conteneurs Cosmos sont [partitionnées horizontalement](partitioning-overview.md).

- Dans chaque région, chaque partition est protégée par un jeu de réplicas avec toutes les écritures répliquées et durablement validées par une majorité de réplicas. Les réplicas sont répartis entre 10 et 20 domaines d’erreur.

- Chaque partition est répliquée dans toutes les régions. Chaque région contient toutes les partitions de données d’un conteneur Cosmos et peut accepter des écritures et traiter les lectures.  

Si votre compte Cosmos est distribué entre *N* régions Azure, il y aura au moins *N* x 4 copies de toutes vos données. En plus de fournir un accès à faible latence aux données et la mise à l’échelle du débit d’écriture/de lecture dans les régions associées à votre compte Cosmos, le fait d’avoir plus de régions (*N* supérieur) améliore la disponibilité.  

## <a name="slas-for-availability"></a>Contrats SLA pour la disponibilité

En tant que base de données mondialement distribuée, Cosmos DB fournit des contrats SLA complets qui englobent le débit, la latence au 99e centile, la cohérence et une haute disponibilité. Le tableau suivant comporte les garanties de haute disponibilité fournie par Cosmos DB pour les comptes à région unique et multirégion. Pour bénéficier de la haute disponibilité, configurez systématiquement vos comptes Cosmos afin d’avoir plusieurs régions d’écriture.

|Type d'opération  | Région unique |Multirégion (écritures dans une seule région)|Multirégion (écritures dans une plusieurs régions) |
|---------|---------|---------|-------|
|Écritures    | 99,99    |99,99   |99,999|
|Lectures     | 99,99    |99,999  |99,999|

> [!NOTE]
> Dans la pratique, la disponibilité d’écriture réelle pour l’obsolescence limitée, la session, les modèles de cohérence éventuelle et à préfixe cohérent, est beaucoup plus importante que les contrats SLA publiés. La disponibilité réelle de lecture pour tous les niveaux de cohérence est beaucoup plus importante que les contrats SLA publiés.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Haute disponibilité avec Cosmos DB en cas de pannes régionales

Les pannes régionales ne sont pas rares et Azure Cosmos DB permet de s’assurer que votre base de données est toujours hautement disponible. Les détails suivants permettent de capturer le comportement de Cosmos DB pendant une panne, en fonction de la configuration de votre compte Cosmos :

- Avant la reconnaissance d’une opération d’écriture auprès du client alors que Cosmos DB est utilisé, les données sont validées durablement par un quorum de réplicas se trouvant la région qui accepte les opérations d’écriture.

- Les comptes multirégion configurés avec plusieurs régions d’écriture sont hautement disponibles pour les écritures et les lectures. Les basculements régionaux sont instantanés et ne nécessitent aucune modification à partir de l’application.

- **Comptes multirégion avec une seule région d’écriture (panne de région d'écriture) :** pendant une panne de région d’écriture, ces comptes restent hautement disponibles pour les lectures. Toutefois, pour les écritures vous devez **« activer le basculement automatique »** sur votre compte Cosmos pour le basculement de la région impactée vers une autre région. Le basculement se produit dans l’ordre de priorité des régions que vous avez spécifié. Lorsque la région impactée est de nouveau en ligne, les données non répliquées présentes dans la région d’écriture impactée pendant la panne sont accessibles via le [flux de conflits](how-to-manage-conflicts.md#read-from-conflict-feed). Les applications peuvent lire le flux de conflits, résoudre les conflits en fonction de la logique propre à l’application, et réécrire les données mises à jour dans le conteneur Cosmos comme il convient. Une fois que la région d’écriture précédemment impactée a récupéré, elle devient automatiquement disponible en tant que région de lecture. Vous pouvez appeler un basculement manuel et configurer la région impactée en tant que région d’écriture. Vous pouvez également effectuer un basculement manuel à l’aide d’[Azure CLI ou du portail Azure](how-to-manage-database-account.md#manual-failover). Il n’y a **aucune perte de données ou de disponibilité** avant, pendant ou après le basculement manuel. Votre application continue d’être hautement disponible. 

- **Comptes multirégion avec une seule région d’écriture (panne de région de lecture) :** pendant une panne de région de lecture, ces comptes restent hautement disponibles pour les lectures et les écritures. La région impactée est automatiquement déconnectée de la région d’écriture et marquée comme étant hors connexion. Les [SDK Cosmos DB](sql-api-sdk-dotnet.md) redirigent les appels de lecture vers la prochaine région disponible dans la liste des régions préférées. Si aucune des régions dans la liste des régions préférées n'est disponible, les appels sont automatiquement acheminés vers la zone d’écriture en cours. Aucune modification n’est nécessaire dans le code de votre application pour gérer la panne de la région de lecture. Au final, lorsque la région impactée est de nouveau en ligne, la région de lecture précédemment concernée se synchronise automatiquement avec la région d’écriture active et est à nouveau disponible pour le traitement des requêtes de lecture. Les lectures suivantes sont redirigées vers la région récupérée sans modification nécessaire de votre code d’application. Pendant le basculement et la réintégration d’une région ayant précédemment échoué, les garanties de cohérence de lecture continuent à être respectées par Cosmos DB.

- Les comptes dans une seule région peuvent perdre leur disponibilité en raison d’une panne régionale. Il est toujours recommandé de configurer **au moins deux régions** (de préférence, au moins deux régions d’écriture) avec votre compte Cosmos pour garantir une haute disponibilité en permanence.

- Même dans un cas rare et malheureux où la région Azure est définitivement irrécupérable, il n’y a aucune perte de données si votre compte Cosmos multirégion est configuré avec le niveau de cohérence par défaut, c’est-à-dire *fort*. Dans le cas d’une région d’écriture irrécupérable de façon permanente, pour les comptes Cosmos multirégion configurés avec une cohérence d’obsolescence limitée, la fenêtre de perte de données potentielle est limitée à la fenêtre d’obsolescence (*K* or *T*) ; pour les niveaux de session, de cohérence et de cohérence éventuelle, la fenêtre de perte de données potentielle est limitée à cinq secondes maximum. 

## <a name="availability-zone-support"></a>Prise en charge des zones de disponibilité

Azure Cosmos DB est un service de base de données multimaître globalement distribué offrant haute disponibilité et résilience en cas de pannes régionales. En plus de la résilience inter-région, vous pouvez désormais activer la **redondance de zone** lorsque vous sélectionnez une région à associer à votre base de données Azure Cosmos. 

La prise en charge des zones de disponibilité permet à Azure Cosmos DB de s'assurer que les réplicas sont placés dans plusieurs zones d'une région donnée afin d'offrir haute disponibilité et résilience en cas de défaillances de zones. Cette configuration n'implique aucune modification en termes de latence et autres contrats de niveau de service. En cas de défaillance d'une seule zone, la redondance de zone assure la durabilité des données avec RPO = 0 et disponibilité avec RTO = 0. 

La redondance de zone *complète* la fonctionnalité de [réplication multimaître](how-to-multi-master.md). Seule, la redondance de zone ne suffit pas à offrir une résilience régionale. Par exemple, en cas de pannes régionales ou d'accès à faible latence dans les régions, il est recommandé de disposer de plusieurs régions d’écriture en plus de la redondance de zone. 

Lorsque vous configurez des écritures multirégions pour votre compte Azure Cosmos, vous pouvez opter pour la redondance de zone, sans frais supplémentaires. Sinon, consultez la note ci-dessous relative à la tarification de la redondance de zone. Vous pouvez activer la redondance de zone dans une région existante de votre compte Azure Cosmos en supprimant la région, puis en l'ajoutant à nouveau avec la redondance de zone activée.

Cette fonctionnalité est disponible dans les régions Azure suivantes :

* Sud du Royaume-Uni
* Asie Sud-Est 
* USA Est
* USA Est 2 
* USA Centre

> [!NOTE] 
> L’activation des zones de disponibilité pour un compte à Azure Cosmos avec une seule région entraîne des frais équivalents à l’ajout d’une région supplémentaire à votre compte. Pour plus d’informations sur la tarification, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/cosmos-db/) et les articles relatifs au [coût multirégion dans Azure Cosmos DB](optimize-cost-regions.md). 

Le tableau suivant récapitule la fonctionnalité de haute disponibilité des différentes configurations de compte : 

|KPI  |Région unique sans zones de disponibilité (Non-AZ)  |Région unique avec zones de disponibilité (AZ)  |Écritures multirégions avec zones de disponibilité (AZ, 2 régions) - Paramètre recommandé |
|---------|---------|---------|---------|
|Contrat SLA de disponibilité en écriture     |   99,99 %      |    99,99 %     |  99, 999 %  |
|Contrat SLA de disponibilité en lecture   |   99,99 %      |   99,99 %      |  99, 999 %       |
|Prix  |  Taux de facturation d’une région unique |  Taux de facturation de zone de disponibilité d'une région unique |  Taux de facturation de plusieurs régions       |
|Défaillances de zone - Perte de données   |  Perte de données  |   Aucune perte de données |   Aucune perte de données  |
|Défaillances de zone – Disponibilité |  Perte de disponibilité  | Aucune perte de disponibilité  |  Aucune perte de disponibilité  |
|Latence de lecture    |  Inter-région    |   Inter-région   |    Faible  |
|Latence d’écriture    |   Inter-région   |  Inter-région    |   Faible   |
|Panne régionale - Perte de données    |   Perte de données      |  Perte de données       |   Perte de données <br/><br/> Lorsque vous utilisez la cohérence en fonction de l'obsolescence limitée avec multimaître et plusieurs régions, la perte de données est limitée à l’obsolescence configurée sur votre compte. <br/><br/> Pour éviter une perte de données en cas de panne régionale, configurez une cohérence forte avec plusieurs régions. Cette option n'est pas sans incidence en termes de disponibilité et de niveau de performance.      |
|Panne régionale – Disponibilité  |  Perte de disponibilité       |  Perte de disponibilité       |  Aucune perte de disponibilité  |
|Débit    |  X RU/s de débit approvisionné      |  X RU/s de débit approvisionné       |  2X RU/s de débit approvisionné <br/><br/> Ce mode de configuration requiert deux fois plus de débit par rapport à une région unique avec zones de disponibilité car il existe deux régions.   |

> [!NOTE] 
> Pour activer la prise en charge des zones de disponibilité, les écritures multimaîtres/multirégions doivent être activées sur le compte Azure Cosmos DB. 

Vous pouvez activer la redondance de zone lorsque vous ajoutez une région à des comptes Azure Cosmos nouveaux ou existants. Actuellement, seuls le portail Azure, PowerShell et les modèles Azure Resource Manager permettent d'activer la redondance de zone. Pour activer la redondance de zone sur votre compte Azure Cosmos, vous devez définir l'indicateur `isZoneRedundant` sur `true` pour un emplacement spécifique. Vous pouvez définir cet indicateur dans la propriété des emplacements. Par exemple, l’extrait de code PowerShell suivant permet la redondance de zone pour la région « Asie Sud-Est » :

```powershell
$locations = @( 
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" }, 
    @{ "locationName"="East US"; "failoverPriority"=1 } 
) 
```

Vous pouvez activer des zones de disponibilité à l'aide du portail Azure lorsque vous créez un compte Azure Cosmos. Lorsque vous créez un compte, activez la **géo-redondance**, les **écritures multirégions**, puis choisissez une région où les zones de disponibilité sont prises en charge : 

![Activer les zones de disponibilité à l’aide du portail Azure](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Génération d’applications hautement disponibles

- Pour garantir une disponibilité élevée en écriture et en lecture, configurez votre compte Cosmos de façon à ce qu’il s’étendre sur au moins deux régions, avec plusieurs régions d’écriture. Cette configuration offre la plus haute disponibilité, la plus faible latence et la meilleure évolutivité pour les lectures et les écritures soutenues par les contrats SLA. Pour en savoir plus, découvrez comment [configurer votre compte Cosmos avec plusieurs régions d’écriture](tutorial-global-distribution-sql-api.md).

- Pour les comptes Cosmos multirégion qui sont configurés avec une seule région d’écriture, [activez le « basculement automatique » à l’aide d’Azure CLI ou du portail Azure](how-to-manage-database-account.md#automatic-failover). Une fois le basculement automatique activé, Cosmos DB bascule automatiquement votre compte en cas de sinistre régional.  

- Même si votre compte Cosmos est hautement disponible, votre application peut ne pas être pas correctement conçue pour rester hautement disponible. Pour tester la haute disponibilité de bout en bout de votre application, appelez régulièrement le [basculement manuel à l’aide d’Azure CLI ou du portail Azure](how-to-manage-database-account.md#manual-failover), dans le cadre de procédures de récupération d’urgence ou de test de votre application.

- Dans un environnement de base de données globalement distribuée, il existe une relation directe entre le niveau de cohérence et la durabilité des données en situation de panne à l'échelle d'une région. Au moment de l'élaboration de votre plan de continuité d'activité, vous devez identifier le délai maximal acceptable nécessaire à la récupération complète de l'application après un événement perturbateur. Ce délai s’appelle l’objectif de délai de récupération (RTO, recovery time objective). Vous devez également déterminer sur quelle période maximale l'application peut accepter de perdre les mises à jour de données récentes lors de la récupération après l'événement perturbateur. Il s’agit de l’objectif de point de récupération (RPO, recovery point objective). Pour obtenir le RPO et le RTO pour Azure Cosmos DB, consultez [Niveaux de cohérence et durabilité des données](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ensuite lire les articles suivants :

* [Compromis entre disponibilité et performance pour différents niveaux de cohérence](consistency-levels-tradeoffs.md)
* [Mise à l’échelle du débit provisionné au niveau global](scaling-throughput.md)
* [Article relatif au principe de la distribution mondiale d’Azure Cosmos DB](global-dist-under-the-hood.md)
* [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md)
* [Configurer votre compte Cosmos avec plusieurs régions d’écriture](how-to-multi-master.md)
