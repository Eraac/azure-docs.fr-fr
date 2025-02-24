---
title: Explorer des travaux Azure Stream Analytics avec Visual Studio Code (préversion)
description: Cet article vous montre comment exporter un travail Azure Stream Analytics vers un projet local, répertorier des travaux et afficher des entités de travail.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 8674d478646c8f9be6b32521c6624752ac6df052
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827799"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Explorer Azure Stream Analytics avec Visual Studio Code (préversion)

L’extension Azure Stream Analytics pour Visual Studio Code permet aux développeurs de gérer facilement leurs travaux Stream Analytics. Elle peut être utilisée sur Windows, Mac et Linux. Cette extension Azure Stream Analytics permet de réaliser les actions suivantes :

- [Créer](quick-create-vs-code.md), lancer et arrêter des travaux
- Exporter des travaux existants vers un projet local
- Répertorier les travaux et afficher les entités de travail

## <a name="export-a-job-to-a-local-project"></a>Exporter un travail vers un projet local

Pour exporter un travail vers un projet local, recherchez le travail que vous souhaitez exporter dans l’**Explorateur Stream Analytics** de Visual Studio Code. Puis, sélectionnez un dossier pour votre projet. Le projet est exporté vers le dossier sélectionné et vous pouvez continuer à gérer le travail à partir de Visual Studio Code. Pour plus d’informations sur l’utilisation de Visual Studio Code pour gérer les travaux Stream Analytics, consultez le [démarrage rapide](quick-create-vs-code.md) de Visual Studio Code.

![Exporter le travail ASA dans Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Répertorier les travaux et afficher les entités de travail

Vous pouvez utiliser la vue des tâches pour interagir avec les tâches Azure Stream Analytics à partir de Visual Studio.


1. Cliquez sur l’icône **Azure** situé sur la barre d’activités Visual Studio Code, puis développez le **nœud Stream Analytics**. Vos travaux doivent apparaître sous vos abonnements.

   ![Ouvrir l’Explorateur Stream Analytics](./media/vscode-explore-jobs/open-explorer.png)

2. Développez le nœud de votre travail. Vous pouvez ouvrir et afficher la requête, la configuration, les entrées, les sorties et les fonctions du travail. 

3. Cliquez avec le bouton droit sur le nœud de votre travail, puis choisissez le nœud **Ouvrir la vue des travaux dans le portail** pour ouvrir la vue des travaux dans le portail Azure.

   ![Ouvrir la vue des travaux dans le portail](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Étapes suivantes

* [Créer un travail cloud Azure Stream Analytics dans Visual Studio Code (préversion)](quick-create-vs-code.md)
