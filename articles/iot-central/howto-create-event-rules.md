---
title: Créer et gérer des règles d’événement dans votre application Azure IoT Central | Microsoft Docs
description: Les règles d’événement d’Azure IoT Central vous permettent de surveiller vos appareils quasi en temps réel et d’appeler automatiquement des actions, comme l’envoi d’un e-mail, quand la règle se déclenche.
author: ankitscribbles
ms.author: ankitgup
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4754e6b571845d286ef22014f87b86fae2f6633d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053019"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Créer une règle d’événement et configurer des notifications dans votre application Azure IoT Central

*Cet article s’applique aux opérateurs, aux créateurs et aux administrateurs.*

Vous pouvez utiliser Azure IoT Central pour surveiller à distance vos appareils connectés. Les règles d’Azure IoT Central vous permettent de surveiller vos appareils quasiment en temps réel et d’appeler automatiquement des actions, comme l’envoi d’un e-mail ou le déclenchement de Microsoft Flow. En quelques clics seulement, vous pouvez définir la condition pour surveiller les données de vos appareils et configurer l’action correspondante. Cet article explique comment créer des règles pour surveiller les événements envoyés par l’appareil.

Les appareils peuvent utiliser les mesures d’événement pour envoyer les événements d’appareil importants ou informatifs. Une règle d’événement se déclenche quand l’événement d’appareil sélectionné est signalé par l’appareil.

## <a name="create-an-event-rule"></a>Créer une règle d’événements

Pour permettre la création d’une règle d’événement, le modèle d’appareil doit comporter au moins une mesure d’événement définie. Cet exemple utilise un appareil distributeur réfrigéré qui signale un événement d’erreur de moteur du ventilateur. La règle surveille l’événement signalé par l’appareil et envoie un e-mail chaque fois que l’événement est signalé.

1. À l’aide de la page **Modèles d’appareil**, accédez au modèle d’appareil pour lequel vous ajoutez la règle.

1. Si vous n’avez pas encore créé de règles, vous voyez l’écran suivant :

    ![Pas encore de règles](media/howto-create-event-rules/rules_landing_page1.png)

1. Sous l’onglet **Règles**, sélectionnez **+ Nouvelle règle** pour voir les types de règles que vous pouvez créer.

1. Choisissez la vignette **Événement** pour créer une règle de surveillance d’événement.

    ![Types de règles](media/howto-create-event-rules/rule_types1.png)

1. Entrez un nom facilitant l’identification de la règle dans ce modèle d’appareil.

1. Pour activer immédiatement la règle sur tous les appareils créés à partir de ce modèle, choisissez **Activer la règle pour tous les appareils de ce modèle**.

    ![Détail de la règle](media/howto-create-event-rules/rule_detail1.png)

    La règle s’applique automatiquement à tous les appareils de ce modèle.

### <a name="configure-the-rule-conditions"></a>Configurer les conditions de la règle

La condition définit les critères qui sont surveillés par la règle.

1. Sélectionnez le symbole **+** en regard de **Conditions** pour ajouter une nouvelle condition.

1. Dans la liste déroulante Mesure, choisissez l’événement que vous voulez surveiller. Dans cet exemple, l’événement **Fan Motor Error** (Erreur du moteur du ventilateur) a été sélectionné.

   ![Condition](media/howto-create-event-rules/condition_filled_out1.png)

1. Si vous le souhaitez, vous pouvez également définir le champ **Agrégation** sur la valeur **Nombre** et spécifier le seuil correspondant.

   - Sans agrégation, la règle se déclenche pour chaque point de données d’événement qui remplit la condition. Par exemple, si vous configurez la condition de la règle pour que cette dernière se déclenche lorsqu’un événement d’**erreur du moteur du ventilateur** survient, la règle se déclenche presque immédiatement après le signalement de cet événement par l’appareil.
   - Si la fonction Nombre est utilisée comme fonction d’agrégation, vous devez définir un **seuil** et une **période d’agrégation** pendant laquelle la condition doit être évaluée. Dans ce cas, le nombre d’événements est agrégé, et la règle se déclenche uniquement si le nombre d’événements agrégés correspond au seuil.

     Par exemple, si vous souhaitez être alerté lorsque plus de trois événements d’appareil se produisent en 5 minutes, sélectionnez l’événement et définissez la fonction d’agrégation sur « nombre », l’opérateur sur « supérieur à » et le « seuil » sur 3. Définissez la période d’agrégation sur « 5 minutes ». La règle se déclenchera si l’appareil envoie plus de trois événements en 5 minutes. La fréquence d’évaluation de la règle est identique à la **période d’agrégation**, ce qui signifie que, dans cet exemple, la règle est évaluée toutes les 5 minutes.

     ![Ajouter une condition d’événement](media/howto-create-event-rules/aggregate_condition_filled_out1.png)

     >[!NOTE]
     >Vous pouvez ajouter plusieurs mesures d’événement sous **Condition**. Quand plusieurs conditions sont spécifiées, toutes les conditions doivent être remplies pour que la règle se déclenche. Chaque condition est implicitement jointe par une clause « AND ». Lorsque vous utilisez un agrégat, chaque mesure doit être agrégée.

### <a name="configure-actions"></a>Configurer les actions

Cette section vous indique comment configurer les actions à effectuer lorsque la règle est déclenchée. Les actions sont appelées lorsque toutes les conditions spécifiées dans la règle présentent la valeur true.

1. Choisissez le signe Plus ( **+** ) en regard de **Actions**. Vous voyez ici la liste des actions disponibles.

    ![Ajouter une action](media/howto-create-event-rules/add_action1.png)

1. Choisissez l’action **Envoyer un e-mail**, entrez une adresse e-mail valide dans le champ **À** et spécifiez une note qui doit apparaître dans le corps de l’e-mail quand la règle se déclenche.

    > [!NOTE]
    > Les e-mails sont envoyés seulement aux utilisateurs qui ont été ajoutés à l’application et qui se sont connectés au moins une fois. Découvrez plus en détail la [gestion des utilisateurs](howto-administer.md) dans Azure IoT Central.

   ![Configurer une action](media/howto-create-event-rules/configure_action1.png)

1. Pour enregistrer la règle, choisissez **Enregistrer**. La règle est active au bout de quelques minutes et commence à surveiller les événements envoyés à votre application. Quand la condition spécifiée dans la règle est remplie, la règle déclenche l’action d’envoi d’e-mail configurée.

Vous pouvez ajouter d’autres actions à la règle, par exemple Microsoft Flow et des Webhooks. Vous pouvez ajouter jusqu’à 5 actions par règle.

- [Action Microsoft Flow](howto-add-microsoft-flow.md) pour lancer un workflow dans Microsoft Flow quand une règle est déclenchée 
- [Action de Webhook](howto-create-webhooks.md) pour informer d’autres services quand une règle est déclenchée

## <a name="parameterize-the-rule"></a>Paramétrer la règle

Vous pouvez également configurer des actions en utilisant **Propriété de l’appareil** comme paramètre. Si une adresse e-mail est stockée en tant que propriété d’un appareil, vous pouvez l’utiliser quand vous définissez l’adresse pour **À**.

## <a name="delete-a-rule"></a>Supprimer une règle

Si vous n’avez plus besoin d’une règle, supprimez-la en ouvrant la règle et en choisissant **Supprimer**. La suppression de la règle la supprime du modèle d’appareil et de tous les appareils associés.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Activer ou désactiver une règle pour un modèle d’appareil

Accédez à l’appareil et choisissez la règle que vous voulez activer ou désactiver. Cliquez sur le bouton bascule **Activer la règle pour tous les appareils de ce modèle** afin d’activer ou de désactiver la règle pour tous les appareils associés au modèle d’appareil.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Activer ou désactiver une règle pour un appareil

Accédez à l’appareil et choisissez la règle que vous voulez activer ou désactiver. En cliquant sur le bouton bascule **Activer la règle pour cet appareil**, vous activez ou désactivez la règle pour cet appareil.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à créer des règles dans votre application Azure IoT Central, voici les étapes suivantes :

- [Ajouter une action Microsoft Flow dans les règles](howto-add-microsoft-flow.md)
- [Ajouter une action Webhook dans les règles](howto-create-webhooks.md)
- [Regrouper plusieurs actions à exécuter à partir d’une ou plusieurs règles](howto-use-action-groups.md)
- [Gérer vos appareils](howto-manage-devices.md)
