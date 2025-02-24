---
title: Se connecter à des points de terminaison REST à partir d’Azure Logic Apps
description: Surveiller les points de terminaison REST dans les tâches automatisées, les processus et les flux de travail à l’aide d’Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: f0410ed7a98e4838e41407868cf26b5254811ae3
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541718"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Appeler des points de terminaison REST à l'aide d'Azure Logic Apps

Avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md) et le connecteur intégré HTTP + Swagger, vous pouvez automatiser les flux de travail qui appellent régulièrement des points de terminaison REST par le biais d'un [fichier Swagger](https://swagger.io) en générant des applications logiques. Le déclencheur et l’action HTTP + Swagger fonctionnent comme le [déclencheur et l'action HTTP](connectors-native-http.md), mais offrent une meilleure expérience dans le concepteur d'application logique en exposant la structure d’API et les sorties décrites par le fichier Swagger. Pour implémenter un déclencheur d’interrogation, suivez le modèle d’interrogation décrit dans [Créer des API personnalisées pour appeler d’autres API, services et systèmes à partir d'applications logiques](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* URL du fichier Swagger qui décrit le point de terminaison REST cible

  En règle générale, le point de terminaison REST doit répondre aux critères suivant pour permettre le bon fonctionnement du connecteur :

  * Le fichier Swagger doit être hébergé sur une URL HTTPS accessible publiquement.

  * L'option [Partage des ressources cross-origin (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) doit être activée sur le fichier Swagger.

  Pour référencer un fichier Swagger non hébergé ou ne répondant pas aux exigences de sécurité cross-origin, vous pouvez [charger ce fichier Swagger vers un conteneur d’objets blob dans un compte de stockage Azure](#host-swagger), puis activer CORS sur ce compte pour référencer le fichier.

  Les exemples de cette rubrique utilisent l'[API Visage Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/face/overview), ce qui implique un [compte Cognitive Services et une clé d'accès](../cognitive-services/cognitive-services-apis-create-account.md).

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md). Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

* L’application logique à partir de laquelle vous souhaitez appeler le point de terminaison cible. Pour commencer à utiliser déclencheur HTTP + Swagger, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser l’action HTTP + Swagger, démarrez votre application logique avec le déclencheur de votre choix. Cet exemple utilise le déclencheur HTTP + Swagger en tant que première étape.

## <a name="add-an-http--swagger-trigger"></a>Ajouter un déclencheur HTTP + Swagger

Ce déclencheur intégré envoie une requête HTTP vers une URL de fichier Swagger décrivant une API REST et renvoie une réponse dans laquelle figure le contenu de ce fichier.

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Ouvrez votre application logique vide dans le Concepteur d’application logique.

1. Dans la zone de recherche du Concepteur, entrez le filtre « swagger ». Dans la liste **Déclencheurs**, sélectionnez le déclencheur **HTTP + Swagger**.

   ![Sélectionner un déclencheur HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Dans la zone **URL DU POINT DE TERMINAISON SWAGGER**, entrez l’URL du fichier Swagger, puis sélectionnez **Suivant**.

   Cet exemple utilise l’URL Swagger qui se trouve dans la région USA Ouest pour l'[API Visage Cognitive Services](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) :

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Entrer l’URL du point de terminaison Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Lorsque le concepteur affiche les opérations décrites par le fichier Swagger, sélectionnez l’opération que vous souhaitez utiliser.

   ![Opérations dans le fichier Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Indiquez les valeurs correspondant aux paramètres du déclencheur (qui varient en fonction de l’opération sélectionnée) que vous souhaitez inclure dans l’appel du point de terminaison. Configurez la fréquence à laquelle le déclencheur doit appeler le point de terminaison.

   Cet exemple renomme le déclencheur « Déclencheur HTTP + Swagger : Visage - Détecter » afin que l’étape ait un nom plus descriptif.

   ![Détails de l’opération](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Pour ajouter d’autres paramètres disponibles, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez les paramètres de votre choix.

   Pour en savoir plus sur les types d’authentification disponibles pour HTTP + Swagger, consultez [Authentifier les actions et déclencheurs HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Continuez à générer le flux de travail de votre application logique avec des actions qui s’exécutent quand le déclencheur se déclenche.

1. Lorsque vous avez terminé, pensez à enregistrer votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

## <a name="add-an-http--swagger-action"></a>Ajouter une action HTTP + Swagger

Cette action intégrée effectue une requête HTTP vers l'URL du fichier Swagger décrivant une API REST et renvoie une réponse dans laquelle figure le contenu de ce fichier.

1. Connectez-vous au [Portail Azure](https://portal.azure.com). Ouvrez votre application logique dans le Concepteur d’applications logiques.

1. Sous l’étape à laquelle vous souhaitez ajouter l’action HTTP + Swagger, sélectionnez **Nouvelle étape**.

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. Cliquez sur le signe ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Dans la zone de recherche du Concepteur, entrez le filtre « swagger ». Dans la liste **Actions**, sélectionnez **HTTP + Swagger**.

    ![Sélectionnez une action HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Dans la zone **URL DU POINT DE TERMINAISON SWAGGER**, entrez l’URL du fichier Swagger, puis sélectionnez **Suivant**.

   Cet exemple utilise l’URL Swagger qui se trouve dans la région USA Ouest pour l'[API Visage Cognitive Services](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) :

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Entrer l’URL du point de terminaison Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Lorsque le concepteur affiche les opérations décrites par le fichier Swagger, sélectionnez l’opération que vous souhaitez utiliser.

   ![Opérations dans le fichier Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Indiquez les valeurs correspondant aux paramètres de l'action (qui varient en fonction de l’opération sélectionnée) que vous souhaitez inclure dans l’appel du point de terminaison.

   Cet exemple ne présente aucun paramètre, mais renomme l’action par « Action HTTP + Swagger action : Visage - Identifier » afin que l’étape ait un nom plus descriptif.

   ![Détails de l’opération](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Pour ajouter d’autres paramètres disponibles, ouvrez la liste **Ajouter un nouveau paramètre**, puis sélectionnez les paramètres de votre choix.

   Pour en savoir plus sur les types d’authentification disponibles pour HTTP + Swagger, consultez [Authentifier les actions et déclencheurs HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Lorsque vous avez terminé, pensez à enregistrer votre application logique. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Héberger un document Swagger dans Stockage Azure

Pour référencer un fichier Swagger non hébergé ou ne répondant pas aux exigences de sécurité cross-origin, vous pouvez charger ce fichier vers un conteneur d’objets blob dans un compte de stockage Azure, puis activer CORS sur ce compte de stockage. Pour créer, configurer et stocker des fichiers Swagger dans Stockage Azure, procédez comme suit :

1. [Création d’un compte de stockage Azure](../storage/common/storage-create-storage-account.md).

1. À présent, activez CORS pour l’objet blob. Dans le menu de votre compte de stockage, sélectionnez **CORS**. Dans l'onglet **Service BLOB**, spécifiez ces valeurs, puis sélectionnez **Enregistrer**.

   | Propriété | Valeur |
   |----------|-------|
   | **Origines autorisées** | `*` |
   | **Méthodes autorisées** | `GET`, `HEAD`, `PUT` |
   | **En-têtes autorisés** | `*` |
   | **En-têtes exposés** | `*` |
   | **Âge maximal** (en secondes) | `200` |
   |||

   Bien que cet exemple utilise le [portail Azure](https://portal.azure.com), vous pouvez utiliser un outil comme [Explorateur Stockage Azure](https://storageexplorer.com/), ou configurer automatiquement ce paramètre à l’aide de cet exemple de [script PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Créez un conteneur d’objets blob](../storage/blobs/storage-quickstart-blobs-portal.md). Dans le volet **Vue d’ensemble** du conteneur, sélectionnez **Modifier le niveau accès**. Dans la liste **Niveau d’accès public**, sélectionnez **Blob (accès en lecture anonyme pour les objets blob uniquement)** , puis **OK**.

1. [Téléchargez le fichier Swagger dans le conteneur d’objets blob](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), via le [portail Azure](https://portal.azure.com) ou [Explorateur Stockage Azure](https://storageexplorer.com/).

1. Pour référencer le fichier dans le conteneur d’objets blob, utilisez un lien HTTPS qui suit ce format et respecte la casse :

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Référence de connecteur

Voici d’autres informations sur les sorties d’un déclencheur ou d’une action HTTP + Swagger. L'appel HTTP + Swagger renvoie les informations suivantes :

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