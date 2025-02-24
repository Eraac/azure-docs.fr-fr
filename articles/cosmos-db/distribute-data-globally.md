---
title: Distribuer des données mondialement avec Azure Cosmos DB
description: Découvrez plus d’informations sur la géoréplication à l’échelle de la planète, le multimaître, le basculement et la récupération des données à l’aide de bases de données mondiales à partir d’Azure Cosmos DB, service de base de données multimodèle distribué mondialement.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.openlocfilehash: e58a8cd286e4d416dd5f4e6d3fddedf1897fed1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65954155"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Distribution de données mondiale avec Azure Cosmos DB - Vue d’ensemble

Les applications actuelles doivent être hautement réactives et toujours en ligne. Pour obtenir une faible latence et une haute disponibilité, les instances de ces applications doivent être déployées dans des centres de données qui sont proches des utilisateurs. Ces applications sont généralement déployées dans plusieurs centres de données et sont dénommées comme étant globalement distribuées. Les applications globalement distribuées ont besoin d’une base de données globalement distribuée qui peut répliquer en toute transparence les données n’importe où dans le monde pour permettre aux applications de fonctionner sur une copie des données qui est proche de ses utilisateurs. 

Azure Cosmos DB est un service de base de données distribué au niveau mondial conçu pour offrir une faible latence, une évolutivité élastique du débit, une sémantique bien définie pour la cohérence des données et une haute disponibilité. En résumé, si votre application a besoin d’un temps de réponse rapide garanti partout dans le monde, si elle a besoin d’être toujours en ligne et a besoin d’une évolutivité illimitée et élastique du débit et du stockage, nous vous conseillons de développer votre application sur Azure Cosmos DB.

Vous pouvez configurer vos bases de données afin qu’elles soient disponibles au niveau mondial et accessibles dans n’importe laquelle des régions Azure concernées. Pour réduire la latence, placez les données près de l’endroit où se trouvent vos utilisateurs. Le choix des régions requises dépend de la portée globale de votre application et de l'emplacement de vos utilisateurs. Cosmos DB réplique de manière transparente les données vers l’ensemble des régions associées à votre compte Cosmos. Il fournit une image unique des conteneurs et de la base de données Cosmos distribuée à l’échelle mondiale afin que votre application puisse lire et écrire les données au niveau local. 

Avec Azure Cosmos DB, vous pouvez à tout moment ajouter ou supprimer des régions associées à votre compte. Il n’est pas nécessaire de suspendre ou de redéployer votre application pour ajouter ou supprimer une région. L’application reste en permanence hautement disponible en raison des fonctionnalités d’hébergement multiple offertes nativement par le service.

![Topologie de déploiement à haute disponibilité](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Principaux avantages de la distribution mondiale

**Créez des applications globales actif/actif.** Avec son nouveau protocole de réplication multimaître, chaque région prend à la fois en charge l’écriture et la lecture. La fonctionnalité multimaître permet également de :

- Bénéficier d’une évolutivité élastique illimitée en écriture et en lecture. 
- Disponibilité en lecture et en écriture de 99,999 % dans le monde entier.
- Lectures et écritures traitées en moins de 10 millisecondes au 99e centile.

Grâce aux API multihébergement d’Azure Cosmos DB, votre application identifie la région la plus proche et peut envoyer des requêtes à celle-ci. La région la plus proche est identifiée sans aucune modification de configuration. Lorsque vous ajoutez et supprimez des régions vers/depuis votre compte Azure Cosmos, votre application n’a pas besoin d’être redéployée ou interrompue. Elle est continuellement hautement disponible.

**Créez des applications hautement réactives.** Votre application peut lire et écrire en temps quasi réel sur toutes les régions que vous avez choisies pour votre base de données. Azure Cosmos DB gère en interne la réplication des données entre les régions avec des garanties de niveau de cohérence du niveau que vous avez sélectionné.

**Créez des applications hautement disponibles.** L’exploitation d’une base de données dans plusieurs régions du monde augmente la disponibilité d’une base de données. Si une région n’est pas disponible, les autres régions gèrent automatiquement les requêtes des applications. Pour les bases de données dans plusieurs régions, Azure Cosmos DB offre la disponibilité des lectures et écritures, à 99,999 %.

**Maintenez la continuité des activités pendant les pannes régionales.** Azure Cosmos DB prend en charge le [basculement automatique](how-to-manage-database-account.md#automatic-failover) en cas de panne régionale. Durant les pannes, Azure Cosmos DB continue à assurer ses SLA en matière de débit, de disponibilité, de cohérence et de latence. Pour vous aider à garantir la haute disponibilité de votre application dans son ensemble, Cosmos DB propose des API de basculement manuel permettant de simuler une panne régionale. Cette API vous permet de tester régulièrement la continuité des activités.

**Étendez le débit des lectures et des écritures dans le monde entier.** Vous pouvez permettre à chaque région d’être inscriptible et d’effectuer des mises à l’échelle élastique en lecture et écriture dans le monde entier. Le débit que votre application configure sur une base de données ou un conteneur Azure Cosmos est garanti dans toutes les régions associées à votre compte Azure Cosmos. Le débit provisionné est garanti par des [Contrats de niveau de service soutenus financièrement](https://aka.ms/acdbsla).

**Faites votre choix entre plusieurs modèles de cohérence bien définis.** Le protocole de réplication d’Azure Cosmos DB offre cinq modèles de cohérence bien définis, pratiques et intuitifs. Chaque modèle offre un équilibre pertinent entre cohérence et performances. Utilisez ces modèles de cohérence pour créer très simplement des applications mondialement distribuées.

## <a id="Next Steps"></a>Étapes suivantes

Découvrez plus d’informations la diffusion mondiale dans les articles suivants :

* [Article relatif au principe de la distribution mondiale d’Azure Cosmos DB](global-dist-under-the-hood.md)
* [Configurer la fonction multimaître dans vos applications](how-to-multi-master.md)
* [Configurer les clients pour la multirésidence](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Ajouter ou supprimer des régions de votre compte Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Créer une stratégie de résolution de conflits personnalisée pour les comptes d’API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)