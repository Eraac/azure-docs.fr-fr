---
title: Exécuter un playbook dans la préversion d’Azure Sentinel | Microsoft Docs
description: Cet article décrit comment exécuter un playbook dans Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 52346e2ff9c47e58f2bd040582bee29eaf08bb13
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621207"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel-preview"></a>Didacticiel : Configurer des réponses automatisées aux menaces dans la préversion d’Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce didacticiel vous aide à utiliser des playbooks de sécurité dans Azure Sentinel afin de définir des réponses automatisées aux menaces pour les problèmes de sécurité détectés par Azure Sentinel.


> [!div class="checklist"]
> * Comprendre les playbooks
> * Créer un playbook
> * Exécuter un playbook


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Qu’est-ce qu’un playbook de sécurité dans Azure Security ?

Un playbook de sécurité est une collection de procédures qui peut être exécutée à partir d’Azure Sentinel en réponse à une alerte. Un playbook de sécurité peut vous aider à automatiser et à orchestrer votre réponse. Vous pouvez l’exécuter manuellement ou le configurer pour qu’il s’exécute automatiquement lors du déclenchement d’alertes spécifiques. Les playbooks de sécurité dans Azure Sentinel reposent sur [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps). Cela signifie que vous bénéficiez de la puissance, des possibilités de personnalisation et des modèles intégrés de Logic Apps. Chaque playbook est créé pour l’abonnement de votre choix. Cependant, la page Playbooks affiche tous les playbooks de tous les abonnements sélectionnés.

> [!NOTE]
> Les playbooks tirant parti d’Azure Logic Apps, des frais sont applicables. Consultez la page sur la tarification [d’Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) pour en savoir plus.

Par exemple, si vous craignez que des attaquants malveillants accèdent à vos ressources réseau, vous pouvez définir une alerte qui recherche des adresses IP malveillantes accédant à votre réseau. Ensuite, vous pouvez créer un playbook qui effectue les opérations suivantes :
1. Quand l’alerte est déclenchée, ouvrez un ticket dans ServiceNow ou tout autre système informatique de création de tickets.
2. Envoyez un message à votre canal d’opérations de sécurité dans Microsoft Teams ou Slack pour vous assurer que vos analystes de sécurité sont conscients de l’incident.
3. Envoyez toutes les informations de l’alerte à vos administrateur réseau et administrateur de sécurité supérieurs. L’e-mail inclut également deux cases d’option, **Bloquer** et **Ignorer**.
4. L’exécution du playbook continue après la réception d’une réponse des administrateurs.
5. Si les administrateurs choisissent **Bloquer**, l’adresse IP est bloquée dans le pare-feu et l’utilisateur est désactivé dans Azure AD.
6. Si les administrateurs choisissent **Ignorer**, l’alerte est fermée dans Azure Sentinel et l’incident est clos dans ServiceNow.

Les playbooks de sécurité peuvent être exécutés manuellement ou automatiquement. L’exécution manuelle signifie que, lorsque vous recevez une alerte, vous pouvez choisir d’exécuter un playbook à la demande en réponse à celle-ci. L’exécution automatique signifie que, lors de la création de la règle de corrélation, vous définissez celle-ci de façon à ce qu’elle exécute automatiquement un ou plusieurs playbooks lors du déclenchement de l’alerte.


## <a name="create-a-security-playbook"></a>Créer un playbook de sécurité

Pour créer un playbook de sécurité dans Azure Security, procédez comme suit :

1. Ouvrez le tableau de bord **Azure Sentinel**.
2. Sous **Administration**, sélectionnez **Playbooks**.

   ![Application logique](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. Dans la page **Security Center – Playbooks (préversion)** , cliquez sur le bouton **Ajouter**.

   ![Créer une application logique](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. Dans la page **Créer une application logique**, saisissez les informations requises pour créer votre application logique et cliquez sur **Créer**. 

5. Dans le [**Concepteur d’application logique**](../logic-apps/logic-apps-overview.md), sélectionnez le modèle à utiliser. Si vous sélectionnez un modèle nécessitant des informations d’identification, vous devez les lui fournir. Vous pouvez également créer un playbook vide à partir de rien. Sélectionnez **Application logique vide**. 

   ![Concepteur d’application logique](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. Vous accédez au Concepteur d’application logique où vous pouvez créer ou modifier le modèle. Pour plus d’informations sur la création d’un playbook avec [Logic Apps](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Si vous créez un playbook vide, dans le champ **Rechercher dans l’ensemble des connecteurs et déclencheurs**, tapez *Azure Sentinel*, puis sélectionnez **Quand une réponse à une alerte Azure Sentinel est déclenchée**. <br>Une fois créé, le nouveau playbook s’affiche dans la liste **Playbooks**. S’il n’y figure pas, cliquez sur **Actualiser**. 

7. Maintenant, vous pouvez définir l’action effectuée lorsque vous déclenchez le playbook. Vous pouvez ajouter une action, une condition logique, des boucles ou des conditions de casse de commutateur.

   ![Concepteur d’application logique](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Pour exécuter un playbook de sécurité

Vous pouvez exécuter un playbook à la demande.

Pour exécuter un playbook à la demande :

1. Dans la page **Cas**, sélectionnez un cas, puis cliquez sur **Afficher les informations complètes**.

2. Sous l’onglet **Alertes**, cliquez sur l’alerte qui doit déclencher l’exécution du playbook, faites défiler vers la droite, cliquez sur **Afficher les playbooks**, puis sélectionnez un playbook à **exécuter** dans la liste des playbooks disponibles pour l’abonnement. 




## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à exécuter un playbook dans Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants : Dans ce didacticiel, vous avez appris à exécuter un playbook dans Azure Sentinel. Consultez l’article expliquant [comment rechercher des menaces de façon proactive à l’aide d’Azure Sentinel](hunting.md).
> [!div class="nextstepaction"]
> [Recherchez des menaces](hunting.md) pour identifier de manière proactive des menaces sur votre réseau.

