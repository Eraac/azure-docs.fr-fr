---
title: Connexion à des points de terminaison HTTP ou HTTPS à partir de Microsoft Azure Logic Apps
description: Surveillez les points de terminaison HTTP ou HTTPS dans les tâches automatisées, les processus et les flux de travail à l’aide d’Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: fa5fd3ef8b144826468f56ea2a14be592cef5dc1
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541322"
---
# <a name="call-http-or-https-endpoints-by-using-azure-logic-apps"></a>Appeler des points de terminaison HTTP ou HTTPS via Azure Logic Apps

Avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md) et le connecteur HTTP intégré, vous pouvez automatiser les flux de travail qui appellent régulièrement n’importe quel point de terminaison HTTP ou HTTPS en générant des applications logiques. Par exemple, vous pouvez surveiller le point de terminaison de service pour votre site web en vérifiant ce point de terminaison selon une planification définie. Lorsqu’un événement se produit au niveau de ce point de terminaison, tel qu’une panne de votre site web, l’événement déclenche le flux de travail de votre application logique et exécute les actions spécifiées.

Vous pouvez utiliser le déclencheur HTTP comme première étape de votre flux de travail pour vérifier ou *interroger* un point de terminaison selon une planification régulière. À chaque vérification, le déclencheur envoie un appel ou une *demande* au point de terminaison. La réponse du point de terminaison détermine si le flux de travail de votre application logique s’exécute. Le déclencheur transmet le contenu de la réponse aux actions de votre application logique.

Vous pouvez utiliser l’action HTTP comme toute autre étape de votre flux de travail pour appeler le point de terminaison quand vous le souhaitez. La réponse du point de terminaison détermine la façon dont s’exécutent les actions restantes de votre flux de travail.

En fonction des capacités du point de terminaison cible, le connecteur HTTP prend en charge les versions 1.0, 1.1 et 1.2 du protocole TLS (Transport Layer Security). Logic Apps négocie avec le point de terminaison en utilisant la version prise en charge la plus récente possible. Si, par exemple, le point de terminaison prend en charge la version 1.2, le connecteur commence par utiliser celle-ci. Sinon, le connecteur utilise la version prise en charge juste inférieure.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* L’URL du point de terminaison cible que vous souhaitez appeler

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md). Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

* L’application logique à partir de laquelle vous souhaitez appeler le point de terminaison cible. Pour démarrer avec un déclencheur HTTP, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser l’action HTTP, démarrez votre application logique avec le déclencheur de votre choix. Cet exemple utilise le déclencheur HTTP en tant que première étape.

## <a name="add-an-http-trigger"></a>Ajouter un déclencheur HTTP

Ce déclencheur intégré effectue un appel HTTP vers l’URL spécifiée d’un point de terminaison et renvoie une réponse.

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Ouvrez votre application logique vide dans le Concepteur d’applications logiques.

1. Dans la zone de recherche du Concepteur, saisissez le filtre « http ». Dans la liste **Déclencheurs**, sélectionnez le déclencheur **HTTP**.

   ![Sélectionner le déclencheur HTTP](./media/connectors-native-http/select-http-trigger.png)

   Cet exemple renomme le déclencheur « Déclencheur HTTP » afin que l’étape ait un nom plus descriptif. En outre, il ajoute ultérieurement une action HTTP, et les deux noms doivent être uniques.

1. Indiquez les valeurs des [paramètres du déclencheur HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) à inclure dans l’appel au point de terminaison cible. Configurez la fréquence à laquelle le déclencheur doit vérifier le point de terminaison cible.

   ![Entrer les paramètres du déclencheur HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Pour en savoir plus sur les types d’authentification disponibles pour HTTP, voir [Authentifier les actions et déclencheurs HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Pour ajouter d’autres paramètres disponibles, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez les paramètres de votre choix.

1. Continuez à générer le flux de travail de votre application logique avec des actions qui s’exécutent quand le déclencheur se déclenche.

1. Lorsque vous avez terminé, pensez à enregistrer votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

## <a name="add-an-http-action"></a>Ajouter une action HTTP

Cette action intégrée effectue un appel HTTP à l’URL spécifiée d’un point de terminaison et renvoie une réponse.

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Ouvrez votre application logique dans le Concepteur d’applications logiques.

   Cet exemple utilise le déclencheur HTTP en tant que première étape.

1. Sous l’étape à laquelle vous souhaitez ajouter l’action HTTP, choisissez **Nouvelle étape**.

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. Cliquez sur le signe ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Dans la zone de recherche du Concepteur, saisissez le filtre « http ». Dans la liste **Actions**, sélectionnez **HTTP**.

   ![Sélection de l’action HTTP](./media/connectors-native-http/select-http-action.png)

   Cet exemple renomme l’action « Action HTTP » afin qu’elle ait un nom plus descriptif.

1. Indiquez les valeurs des [paramètres d’actions HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) à inclure dans l’appel au point de terminaison cible.

   ![Entrer les paramètres de l’action HTTP](./media/connectors-native-http/http-action-parameters.png)

   Pour en savoir plus sur les types d’authentification disponibles pour HTTP, voir [Authentifier les actions et déclencheurs HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Pour ajouter d’autres paramètres disponibles, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez les paramètres de votre choix.

1. Lorsque vous avez terminé, pensez à enregistrer votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

## <a name="connector-reference"></a>Référence de connecteur

Pour en savoir plus sur les paramètres des déclencheurs et des actions, consultez les sections suivantes :

* [Paramètres de déclencheurs HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [Paramètres d’actions HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>Détails des résultats

Voici d’autres informations sur les sorties d’un déclencheur ou d’une action HTTP qui renvoient les données suivantes :

| Nom de la propriété | type | Description |
|---------------|------|-------------|
| headers | objet | En-têtes de la requête |
| body | objet | Objet JSON | Objet avec le contenu du corps de la requête |
| Code d’état | int | Code d’état de la requête |
|||

| Code d’état | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Acceptée |
| 400 | Demande incorrecte |
| 401 | Non autorisé |
| 403 | Interdit |
| 404 | Introuvable |
| 500 | Erreur interne du serveur. Une erreur inconnue s’est produite. |
|||

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
