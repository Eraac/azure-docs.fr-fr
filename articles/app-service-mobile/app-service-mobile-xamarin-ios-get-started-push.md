---
title: Ajouter des notifications Push à votre application Xamarin.iOS à l’aide d’Azure App Service
description: Découvrez comment utiliser Azure App Service pour envoyer des notifications Push à votre application Xamarin.iOS.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 9edd5e755b4a42fe881a0863bb284039299ec713
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446263"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Ajouter des notifications Push à votre application Xamarin.iOS

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center investit dans des services nouveaux et intégrés, essentiels au développement d’applications mobiles. Les développeurs peuvent utiliser les services **Build**, **Test** et **Distribute** pour configurer le pipeline de livraison et d’intégration continues. Une fois l’application déployée, les développeurs peuvent surveiller l’état et l’utilisation de leur application à l’aide des services **Analytics** et **Diagnostics** et interagir avec des utilisateurs à l’aide du service **Push**. Les développeurs peuvent également utiliser **Auth** pour authentifier leurs utilisateurs, ainsi que le service **Data** pour conserver et synchroniser les données d’application dans le cloud. Découvrez [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-ios-get-started-push) dès aujourd’hui.
>

## <a name="overview"></a>Vue d'ensemble

Dans ce didacticiel, vous ajoutez des notifications Push au projet [Démarrage rapide Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) afin qu'une notification Push soit envoyée chaque fois qu'un enregistrement est inséré.

Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devrez ajouter le package d’extension de notification Push. Consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) pour plus d’informations.

## <a name="prerequisites"></a>Prérequis

* Terminez le [didacticiel de démarrage rapide Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md) .
* Un appareil iOS physique. Les notifications Push ne sont pas prises en charge par le simulateur iOS.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Inscrire l'application pour les notifications push dans le portail de développeurs d'Apple

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Configuration de votre application mobile pour l'envoi de notifications push

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Mettre à jour le projet de serveur pour l'envoi de notifications Push

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Configurer votre projet Xamarin.iOS

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Ajout de notifications Push à votre application

1. Dans **QSTodoService**, ajoutez la propriété suivante pour qu’**AppDelegate** puisse acquérir le client mobile :

    ```csharp
    public MobileServiceClient GetClient {
        get
        {
            return client;
        }
        private set
        {
            client = value;
        }
    }
    ```

2. Ajoutez l’instruction `using` suivante au début du fichier **AppDelegate.cs** .

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. Dans **AppDelegate**, remplacez l'événement **FinishedLaunching** :

   ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // registers for push for iOS8
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

        return true;
    }
    ```

4. Dans le même fichier, remplacez l’événement `RegisteredForRemoteNotifications`. Dans ce code, vous inscrivez une notification de modèle simple qui sera envoyée sur toutes les plateformes prises en charge par le serveur.

    Pour plus d’informations sur les modèles avec Notification Hubs, consultez [Modèles](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        MobileServiceClient client = QSTodoService.DefaultService.GetClient;

        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyAPNS}
        };

        // Register for push with your mobile app
        var push = client.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

5. Ensuite, remplacez l’événement **DidReceivedRemoteNotification** :

   ```csharp
    public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps [new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

L'application est mise à jour et prend en charge les notifications Push.

## <a name="test"></a>Tester les notifications push dans votre application

1. Appuyez sur le bouton **Démarrer** pour générer le projet, puis démarrez l’application sur un appareil compatible iOS, et enfin cliquez sur **OK** pour accepter les notifications Push.

   > [!NOTE]
   > Vous devez accepter explicitement les notifications Push de votre application. Cette demande s’effectue uniquement lors du premier démarrage de l’application.

2. Dans l’application, tapez une tâche, puis cliquez sur l’icône plus ( **+** ).
3. Vérifiez que vous avez reçu une notification, puis cliquez sur **OK** pour fermer celle-ci.
4. Répétez l’étape 2 et fermez immédiatement l’application, puis vérifiez qu’une notification est affichée.

Vous avez terminé ce didacticiel.
