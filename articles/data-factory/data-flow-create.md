---
title: Créer un mappage de flux de données Azure Data Factory
description: Comment créer un mappage de flux de données Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 366ed60534544ebbf889e2f72fe703f9b821f871
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65235651"
---
# <a name="create-azure-data-factory-data-flow"></a>Créer un flux de données Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Le mappage de flux de données ADF permet de transformer des données à grande échelle sans aucun codage. Vous pouvez concevoir une tâche de transformation de données dans le concepteur de flux de données en créant une série de transformations. Commencez par un certain nombre de transformations sources, suivies par des étapes de transformation de données. Ensuite, réalisez votre flux de données avec un récepteur qui servira de destination à vos résultats.

Commencez par créer une fabrique de données v2 sur le portail Azure. Ensuite, cliquez sur la vignette « Créer et surveiller » pour lancer l’interface utilisateur de Data Factory.

![Options de flux de données](media/data-flow/v2portal.png "Créer un flux de données")

Une fois dans l’interface utilisateur de Data Factory, vous pouvez utiliser des exemples de flux de données, disponibles dans la galerie de modèles ADF. Dans ADF, créez un « Pipeline à partir d’un modèle » et sélectionnez la catégorie de flux de données dans la galerie de modèles.

![Options de flux de données](media/data-flow/template.png "Créer un flux de données")

Il vous est demandé d’entrer les informations d’identification de votre compte de Stockage Blob Azure.

![Options de flux de données](media/data-flow/template2.png "Créer un flux de données 2")

[Les données utilisées dans ces exemples se trouvent ici](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Téléchargez les exemples de données et stockez les fichiers dans vos comptes de Stockage Blob Azure pour pouvoir exécuter les exemples.

## <a name="create-new-data-flow"></a>Créer un flux de données

Utilisez le bouton « signe plus » Créer une ressource de l’interface utilisateur ADF pour créer des flux de données.

![Options de flux de données](media/data-flow/newresource.png "Nouvelle ressource")

## <a name="next-steps"></a>Étapes suivantes

Créez votre transformation des données avec une [transformation source](data-flow-source.md).
