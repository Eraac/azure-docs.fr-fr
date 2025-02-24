---
title: Se connecter à Yammer à partir d’Azure Logic Apps | Microsoft Docs
description: Automatiser les tâches et les flux de travail qui supervisent, publient et gèrent les messages, les flux et bien plus encore dans Yammer à l’aide d’Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: ca2d28f3438fd166fa282488206662c95777bf3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104729"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Superviser et gérer votre compte Yammer à l’aide d’Azure Logic Apps

Avec Azure Logic Apps et le connecteur Yammer, vous pouvez créer des tâches et des flux de travail automatisés qui supervisent et gèrent les messages, les flux et bien plus encore dans votre compte Yammer, ainsi que d’autres actions, par exemple :

* Superviser l’apparition de nouveaux messages dans les flux et groupes suivis.
* Obtenir les détails des messages, groupes, réseaux, utilisateurs et bien plus encore.
* Publier et aimer des messages.

Vous pouvez utiliser des déclencheurs qui obtiennent des réponses de votre compte Yammer et mettent la sortie à la disposition d’autres actions. Vous pouvez utiliser des actions qui effectuent des tâches avec votre compte Yammer. Vous pouvez également faire en sorte que des actions utilisent la sortie d’actions Yammer. Par exemple, quand de nouveaux messages s’affichent dans des flux ou des groupes, vous pouvez partager ces messages avec le connecteur Slack. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

* Vos informations d’identification utilisateur et compte Yammer

   Vos informations d’identification autorisent votre application logique à créer une connexion et à accéder à votre compte Yammer.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte Yammer. Pour démarrer avec un déclencheur Yammer, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser une action Yammer, démarrez votre application logique avec un autre déclencheur, par exemple, le déclencheur **Périodicité**.

## <a name="connect-to-yammer"></a>Se connecter à Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Choisissez une procédure : 

   * Pour les applications logiques vides, dans la zone de recherche, entrez « yammer » comme filtre. 
   Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité. 

     -ou-

   * Pour les applications logiques existantes : 
   
     * Sous la dernière étape où vous souhaitez ajouter une action, choisissez **Nouvelle étape**. 

       -ou-

     * Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. 
     Cliquez sur le signe plus ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.
     
       Dans la zone de recherche, entrez « yammer » comme filtre. 
       Sous la liste des actions, sélectionnez l’action souhaitée.

1. Si vous êtes invité à vous connecter à Yammer, faites-le maintenant afin de pouvoir autoriser l’accès.

1. Fournissez les informations nécessaires pour le déclencheur ou l’action sélectionnés et continuez à générer le flux de travail de votre application logique.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence](/connectors/yammer/) du connecteur.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)