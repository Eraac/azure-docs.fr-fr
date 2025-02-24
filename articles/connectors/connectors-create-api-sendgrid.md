---
title: Se connecter à SendGrid à partir d’Azure Logic Apps | Microsoft Docs
description: Automatiser des tâches et des flux de travail qui envoient des e-mails et gérer des listes de diffusion dans SendGrid à l’aide d’Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 7eecd3908883b195b52755d03e70872afe9180bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105732"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Envoyer des e-mails et gérer des listes de diffusion dans SendGrid à l’aide d’Azure Logic Apps

Avec Azure Logic Apps et le connecteur SendGrid, vous pouvez créer des tâches et des flux de travail automatisés qui envoient des e-mails et gèrent vos listes de destinataires, par exemple :

* Envoyer un e-mail.
* Ajouter des destinataires à des listes.
* Obtenir, ajouter et gérer la suppression globale.

Vous pouvez utiliser des actions SendGrid dans vos applications logiques pour effectuer ces tâches. Vous pouvez également faire en sorte que des actions utilisent le résultat des actions de SendGrid. 

Ce connecteur ne fournit que des actions ; ainsi, pour démarrer votre application logique, sélectionnez un déclencheur distinct, tel que le déclencheur **Périodicité**. Par exemple, si vous ajoutez régulièrement des destinataires à vos listes, vous pouvez envoyer des e-mails concernant les destinataires et les listes à l’aide du connecteur Outlook Office 365 ou du connecteur Outlook.com.
Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

* Un [compte SendGrid](https://www.sendgrid.com/) et une [clé API SendGrid](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Votre clé API autorise votre application logique à créer une connexion et à accéder à votre compte SendGrid.

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte SendGrid. Pour utiliser une action SendGrid, démarrez votre application logique avec un autre déclencheur, par exemple, le déclencheur **Périodicité**.

## <a name="connect-to-sendgrid"></a>Se connecter à SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez votre application logique dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Choisissez une procédure : 

   * Sous la dernière étape où vous souhaitez ajouter une action, choisissez **Nouvelle étape**. 

     -ou-

   * Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. 
   Cliquez sur le signe plus ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Dans la zone de recherche, entrez « sendgrid » comme filtre. Dans la liste des actions, sélectionnez l’action souhaitée.

1. Donnez un nom à votre connexion. 

1. Entrez votre clé API SendGrid, puis choisissez **Créer**.

1. Fournissez les informations nécessaires pour votre action sélectionné et continuez à générer le flux de travail de votre application logique.

## <a name="connector-reference"></a>Référence de connecteur

Pour obtenir des détails techniques sur les déclencheurs, les actions et les limites, qui sont décrits par la description OpenAPI du connecteur (anciennement Swagger), consultez la [page de référence](/connectors/sendgrid/) du connecteur.

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)