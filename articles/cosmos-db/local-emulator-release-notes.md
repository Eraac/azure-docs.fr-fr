---
title: Téléchargement de l’émulateur Cosmos Azure et notes de publication
description: Lisez les notes de publication de l’émulateur Azure Cosmos et téléchargez-le.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 5985d0d82341c76993ee91b8dff6927edd1ed8b4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332140"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Utiliser l’émulateur Azure Cosmos pour le développement et le test en local

Cet article présente les notes de publication de l’émulateur Azure Cosmos avec une liste de mises à jour de fonctionnalités qui ont été apportées à chaque version. Il répertorie également la dernière version de l’émulateur pour vous permettre de la télécharger et de l’utiliser.

## <a name="download"></a>Téléchargement

| | |
|---------|---------|
|**Téléchargement de MSI**|[Centre de téléchargement Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Prise en main**|[Développer localement avec l’émulateur Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Notes de publication

### <a name="243"></a>2.4.3

- Désactivation du démarrage du service de MongoDB par défaut. Seul le point de terminaison SQL est activé par défaut. L’utilisateur doit démarrer le point de terminaison manuellement à l’aide de l’option de ligne de commande /EnableMongoDbEndpoint. C’est maintenant similaire aux autres points de terminaison de service, tels que Gremlin, Cassandra et Table.
- Correction d’un bogue dans l’émulateur lors du démarrage avec « /AllowNetworkAccess », où les points de terminaison Gremlin, Cassandra et Table ne géraient pas correctement les requêtes des clients externes.
- Ajoutez des ports de connexion directe aux paramètres de règles de pare-feu.

### <a name="240"></a>2.4.0

- Correction d’un problème avec l’émulateur, qui ne parvenait pas à démarrer lorsque des applications de surveillance réseau, comme le client Pulse, sont présentes sur l’ordinateur hôte.
