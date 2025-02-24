---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: ae6d590cdada24638ec2d24c83609b8e6addfaf0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177238"
---
## <a name="set-up-your-project"></a>Configuration de votre projet

Dans cette section, vous allez créer un projet pour apprendre à intégrer une application de bureau Windows .NET (XAML) avec l’option *Se connecter avec Microsoft* pour que l’application puisse interroger les API web qui nécessitent un jeton.

L’application que vous créez dans ce guide affiche un bouton pour appeler un graphique, une zone pour afficher les résultats à l’écran et un bouton de déconnexion.

> [!NOTE]
> Vous préférez télécharger le projet Visual Studio de cet exemple ? [Téléchargez un projet](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip) et passez à [l’étape Configuration](#register-your-application) pour configurer l’exemple de code avant de l’exécuter.
>

Pour créer l’application, procédez comme suit :

1. Dans Visual Studio, sélectionnez **Fichier** > **Nouveau** > **Projet**.
2. Sous **Modèles**, sélectionnez **Visual C#** .
3. Sélectionnez **Application WPF (.NET Framework)** , selon la version Visual Studio que vous utilisez.

## <a name="add-msal-to-your-project"></a>Ajouter MSAL à votre projet

1. Dans Visual Studio, sélectionnez **Outils** > **Gestionnaire de package NuGet**> **Console du gestionnaire de package**.
2. Dans la fenêtre Console du gestionnaire de package, collez la commande Azure PowerShell suivante :

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Cette commande installe Microsoft Authentication Library. MSAL gère l’acquisition, la mise en cache et l’actualisation des jetons d’utilisateur permettant d’accéder aux API protégées par Azure Active Directory v2.0.
    >

## <a name="add-the-code-to-initialize-msal"></a>Ajoutez le code pour initialiser MSAL

Dans cette étape, vous allez créer une classe pour gérer l’interaction avec MSAL, telle que la gestion des jetons.

1. Ouvrez le fichier *App.xaml.cs* et ajoutez la référence de MSAL à la classe :

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Mettez à jour la classe d’application comme suit :

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>Créer l’interface utilisateur de l’application

La section suivante explique comment une application peut interroger un serveur principal protégé tel que Microsoft Graph. 

Un fichier *MainWindow.xaml* doit être automatiquement créé dans le cadre de votre modèle de projet. Ouvrez ce fichier et remplacez le nœud *\<Grid>* de votre application par le code suivant :

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```
