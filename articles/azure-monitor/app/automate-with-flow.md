---
title: Automatiser les processus Azure Application Insights avec Microsoft Flow
description: Découvrez comment utiliser Microsoft Flow pour automatiser rapidement des processus reproductibles en utilisant le connecteur Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mbullwin
ms.openlocfilehash: 15299be83758c157bf3bc7d9fb27b50763b9148e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60903549"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatiser les processus Azure Application Insights avec le connecteur pour Microsoft Flow

Vous devez exécuter à plusieurs reprises les mêmes requêtes sur vos données de télémétrie pour vérifier le bon fonctionnement de votre service ? Cherchez-vous à automatiser ces requêtes pour rechercher les tendances et les anomalies, puis générer vos propres flux de travail autour d’elles ? Le connecteur Azure Application Insights pour Microsoft Flow est l’outil qu’il vous faut pour atteindre ces objectifs.

Avec cette intégration, vous pouvez désormais automatiser de nombreux processus sans écrire la moindre ligne de code. Après avoir créé un flux à l’aide d’une action Application Insights, le flux exécute automatiquement votre requête Application Insights Analytics. 

Vous pouvez également ajouter des actions. Microsoft Flow met à votre disposition des centaines d’actions. Par exemple, vous pouvez utiliser Microsoft Flow pour envoyer automatiquement une notification par e-mail ou créer un bogue dans Azure DevOps. Vous pouvez également utiliser l’un des nombreux [modèles](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) disponibles pour le connecteur pour Microsoft Flow. Ces modèles accélèrent le processus de création d’un flux. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Créer un flux pour Application Insights

Dans ce didacticiel, vous allez apprendre à créer un flux qui utilise l’algorithme de clusters automatiques Analytics aux attributs de groupe dans les données pour une application web. Ce flux envoie automatiquement les résultats par courrier électronique. Il s’agit d’un exemple de la façon dont vous pouvez utiliser Microsoft Flow et Application Insights Analytics ensemble. 

### <a name="step-1-create-a-flow"></a>Étape 1 : Créer un flux
1. Connectez-vous à [Microsoft Flow](https://flow.microsoft.com), puis sélectionnez **Mes flux**.
2. Cliquez sur **Nouveau**, puis sur **Créer entièrement**.

    ![Créer entièrement un flux](./media/automate-with-flow/1createflow.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Étape 2 : Créer un déclencheur pour votre flux
1. Sous l’onglet Intégré, sélectionnez **Planifier**, puis **Planification - Récurrence**.

    ![Sélectionner une planification sous Intégré](./media/automate-with-flow/2schedule.png)

1. Dans la zone **Intervalle**, entrez **1**, puis dans la zone **Fréquence**, sélectionnez **Jour**.
2. Cliquez sur **Nouvelle étape**.

    ![Configurer la périodicité de la planification en entrant une fréquence et un intervalle](./media/automate-with-flow/3schedulerecurrence.png)


### <a name="step-3-add-an-application-insights-action"></a>Étape 3 : Ajouter une action Application Insights
1. Recherchez **Azure Application Insights**.
2. Cliquez sur **Azure Application Insights - Visualiser la requête Analytics**.
 
    ![Choisir une action : Azure Application Insights - Visualiser la requête Analytics](./media/automate-with-flow/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Étape 4 : Se connecter à une ressource Application Insights

Pour cette étape, vous avez besoin d’un ID d’application et d’une clé d’API pour votre ressource. Vous pouvez les récupérer sur le portail Azure, comme illustré dans le schéma suivant :

![ID d’application dans le portail Azure](./media/automate-with-flow/5apiaccess.png)

![Clé API dans le portail Azure](./media/automate-with-flow/6apikey.png)

- Renseignez le nom de votre connexion, l’ID d’application et la clé d’API.

    ![Fenêtre de connexion Microsoft Flow](./media/automate-with-flow/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Étape 5 : Spécifier le type de requête et de graphique Analytics
Cet exemple de requête sélectionne les requêtes ayant échoué au cours du dernier jour et les met en corrélation avec les exceptions qui se sont produites dans le cadre de l’opération. Analytics les met en corrélation en fonction de l’identificateur operation_Id. La requête segmente ensuite les résultats à l’aide de l’algorithme de cluster automatique. 

Lorsque vous créez vos propres requêtes, vérifiez qu’elles fonctionnent correctement dans Analytics avant de les ajouter à votre flux.

- Ajoutez la requête Analytics suivante, puis sélectionnez le type de graphique de tableau HTML. Sélectionnez ensuite **Nouvelle étape**.

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```
    
    ![Fenêtre de configuration de requête Analytics](./media/automate-with-flow/8query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Étape 6 : Configurer le flux pour envoyer un e-mail

1. Recherchez **Office 365 Outlook**.
2. Cliquez sur **Office 365 Outlook - Envoyer un message électronique**.

    ![Fenêtre de sélection d’Office 365 Outlook](./media/automate-with-flow/9outlookaction.png)

1. Dans la fenêtre **Envoyer un message électronique**, effectuez les étapes suivantes :

   a. Tapez l’adresse e-mail du destinataire.

   b. Tapez l’objet de l’e-mail.

   c. Cliquez n’importe où dans la zone **Corps**, puis, dans le menu de contenu dynamique qui s’ouvre sur la droite, sélectionnez **Corps**.

   d. Cliquez sur **Afficher les options avancées**.

    ![Configuration d’Office 365 Outlook](./media/automate-with-flow/10sendemailbody.png)

1. Dans le menu de contenu dynamique, effectuez les étapes suivantes :

    a. Sélectionnez **Nom de la pièce jointe**.

    b. Sélectionnez **Attachment Content** (Contenu de la pièce jointe).
    
    c. Dans la zone **Is HTML** (Est HTML), sélectionnez **Oui**.

    ![Fenêtre de configuration d’e-mail Office 365](./media/automate-with-flow/11emailattachment.png)

### <a name="step-7-save-and-test-your-flow"></a>Étape 7 : Enregistrer et tester votre flux
- Dans la zone **Nom du flux**, ajoutez un nom pour votre flux, puis cliquez sur **Enregistrer**.

    ![Nommer le flux et enregistrer](./media/automate-with-flow/12nameflow.png)

Vous pouvez attendre que le déclencheur exécute cette action ou vous pouvez exécuter immédiatement le flux en [exécutant le déclencheur à la demande](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Lorsque le flux s’exécute, les destinataires que vous avez spécifiés dans la liste des e-mails reçoivent un e-mail similaire à :

![Exemple de message électronique](./media/automate-with-flow/flow9.png)


## <a name="next-steps"></a>Étapes suivantes

- Découvrez la création de [requêtes Analytics](../../azure-monitor/log-query/get-started-queries.md).
- Découvrez [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





