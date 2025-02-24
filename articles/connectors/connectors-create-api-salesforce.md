---
title: Se connecter à Salesforce à partir d’Azure Logic Apps | Microsoft Docs
description: Automatiser des tâches et des flux de travail qui surveillent, créent et gèrent des enregistrements et des travaux de Salesforce à l’aide d’Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 292d517f2c99974f4674a4c94472a0a320320ce4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106013"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Surveiller, créer et gérer des ressources Salesforce à l’aide d’Azure Logic Apps

Avec Azure Logic Apps et le connecteur Salesforce, vous pouvez créer pour vos ressources Salesforce des tâches et workflows automatisés, tels que les enregistrements, les travaux et les objets, par exemple :

* Surveiller si des enregistrements sont créés ou modifiés. 
* Créer, obtenir et gérer des travaux et des enregistrements, y compris des actions d’insertion, de mise à jour et de suppression.

Vous pouvez utiliser des déclencheurs Salesforce qui obtiennent des réponses de Salesforce et mettent le résultat à la disposition d’autres actions. Vous pouvez utiliser des actions dans vos applications logiques pour effectuer des tâches avec des ressources Salesforce. Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

* Un [compte Salesforce](https://salesforce.com/)

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte Salesforce. Pour démarrer avec un déclencheur Salesforce, [créez une application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour utiliser une action Salesforce, démarrez votre application logique avec un autre déclencheur, par exemple, le déclencheur **Périodicité**.

## <a name="connect-to-salesforce"></a>Se connecter à Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Choisissez une procédure : 

   * Pour les applications logiques vides, dans la zone de recherche, entrez « salesforce » comme filtre. 
   Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité. 

     -ou-

   * Pour les applications logiques existantes, sous l’étape où vous souhaitez ajouter une action, choisissez **Nouvelle étape**. Dans la zone de recherche, saisissez le filtre « salesforce ». Dans la liste des actions, sélectionnez l’action souhaitée.

1. Si vous êtes invité à vous connecter à Salesforce, faites-le maintenant pour pouvoir y accéder.

   Vos informations d’identification autorisent votre application logique à créer une connexion à Salesforce et à accéder à vos données.

1. Fournissez les informations nécessaires pour le déclencheur ou l’action sélectionnés et continuez à générer le flux de travail de votre application logique.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence](/connectors/salesforce/) du connecteur.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)