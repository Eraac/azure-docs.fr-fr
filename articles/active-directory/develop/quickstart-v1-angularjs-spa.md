---
title: Générer une application monopage AngularJS pour la connexion à et la déconnexion d’Azure Active Directory | Microsoft Docs
description: Découvrez comment générer une application monopage AngularJS qui s’intègre à Azure AD pour la connexion et appelle des API protégées par Azure AD à l’aide d’OAuth.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a1fdbcd04504181a20f5245b6f2378be5b9d405
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001208"
---
# <a name="quickstart-build-an-angularjs-single-page-app-for-sign-in-and-sign-out-with-azure-active-directory"></a>Démarrage rapide : Générer une application monopage AngularJS pour la connexion à et la déconnexion avec Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Azure Active Directory (Azure AD) simplifie l’ajout d’appels API OAuth de connexion, de déconnexion et de sécurisation à vos applications à page unique. Il permet à votre application d’authentifier les utilisateurs avec leurs comptes Windows Server Active Directory et de consommer une API web protégée par Azure AD, telle que l’API Office 365 ou Azure.

Pour les applications JavaScript s’exécutant dans un navigateur, Azure AD fournit la bibliothèque d’authentification Active Directory (ADAL) ou adal.js. adal.js a pour seule fonction de simplifier l’obtention des jetons d’accès pour votre application.

Dans ce démarrage rapide, vous allez apprendre à générer une application de liste de tâches AngularJS qui :

* connecte l’utilisateur à l’application à l’aide d’Azure AD comme fournisseur d’identité ;
* affiche des informations sur l’utilisateur ;
* appelle en toute sécurité l’API de liste des tâches de l’application en utilisant des jetons porteurs d’Azure AD ;
* déconnecte l’utilisateur de l’application.

Pour générer l’application fonctionnelle complète, vous devez :

1. Inscrire votre application auprès d’Azure AD.
2. Installer la bibliothèque ADAL et configurer l’application à page unique.
3. Utilisez la bibliothèque ADAL pour sécuriser les pages dans l’application à page unique.

> [!NOTE]
> Si vous devez activer les connexions pour les comptes personnels en plus des comptes professionnels et scolaires, vous pouvez utiliser le *[point de la plateforme d’identités Microsoft](azure-ad-endpoint-comparison.md)* . Pour plus d’informations, consultez [ce tutoriel JavaScript SPA](tutorial-v2-javascript-spa.md) ainsi que [cet article](active-directory-v2-limitations.md), qui expliquent ce que sont les *points de terminaison de la plateforme d’identité Microsoft*. 

## <a name="prerequisites"></a>Prérequis

Pour commencer, configurez les prérequis suivants :

* Téléchargez [la structure de l’application](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) ou [l’exemple terminé](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).
* Procurez-vous un locataire Azure AD dans lequel vous pouvez créer des utilisateurs et inscrire une application. Si vous ne disposez pas encore d’un client, [découvrez comment en obtenir un](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Étape 1 : Inscrire l’application DirectorySearcher

Pour autoriser votre application à authentifier les utilisateurs et à obtenir des jetons, vous devez tout d’abord l’inscrire dans votre client Azure AD :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Si vous êtes connecté à plusieurs répertoires, vous devez peut-être vous assurer que vous consultez le répertoire approprié. Pour ce faire, dans la barre supérieure, cliquez sur votre compte. Dans la liste **Répertoire**, choisissez le locataire Azure AD auprès duquel vous voulez inscrire votre application.
1. Cliquez sur **Tous les services** dans le volet de gauche, puis sélectionnez **Azure Active Directory**.
1. Cliquez sur **Inscriptions d’applications**, puis sur **Nouvelle inscription**.
1. Lorsque la page **Inscrire une application** s’affiche, entrez le nom de votre application.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
1. Sélectionnez la plateforme **Web** dans la section **URI de redirection** et définissez la valeur sur `https://localhost:44326/` (l’emplacement vers lequel Azure AD retourne des jetons).
1. Lorsque vous avez terminé, sélectionnez **Inscrire**. Sur la page **Vue d’ensemble**, notez la valeur **ID d’application (client)** .
1. Adal.js utilise le flux implicite OAuth pour communiquer avec Azure AD. Vous devez activer ce flux pour votre application. Dans le volet de navigation de gauche de l’application inscrite, sélectionnez **Authentification**.
1. Dans **Paramètres avancés**, sous **Octroi implicite**, cochez les cases **Jetons d’ID** et **Jetons d’accès**. Les jetons d’ID et jetons d’accès sont nécessaires dans la mesure où cette application doit connecter des utilisateurs et appeler une API.
1. Sélectionnez **Enregistrer**.
1. Accordez les autorisations à votre locataire sur votre application. Accédez à **autorisations d’API**, puis sélectionnez le bouton **Accorder le consentement de l’administrateur** sous **Accorder le consentement**.
1. Sélectionnez **Oui** pour confirmer.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Étape 2 : Installer la bibliothèque ADAL et configurer l’application monopage

Maintenant que vous disposez d’une application dans Azure AD, vous pouvez installer adal.js et écrire votre code lié à l’identité.

### <a name="configure-the-javascript-client"></a>Configurer le client JavaScript

Commencez par ajouter adal.js au projet TodoSPA à l’aide de la console du gestionnaire de package :

1. Téléchargez [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) et ajoutez-le au répertoire du projet `App/Scripts/`.
2. Téléchargez [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) et ajoutez-le au répertoire du projet `App/Scripts/`.
3. Chargez chaque script avant la fin de la section `</body>` in `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Configurer le serveur principal

Pour que l’API de liste des tâches de serveur principal de l’application à page unique accepte les jetons à partir du navigateur, le serveur principal a besoin d’informations de configuration sur l’inscription de l’application. Dans le projet TodoSPA, ouvrez `web.config`. Remplacez les valeurs des éléments de la section `<appSettings>` afin qu’elles reflètent les valeurs que vous avez utilisées dans le portail Azure. Votre code se réfère à ces valeurs chaque fois qu’il utilise la bibliothèque ADAL.

   * `ida:Tenant` est le domaine de votre client Azure AD, par exemple, contoso.onmicrosoft.com.
   * `ida:Audience` est l’ID client de votre application, copié à partir du portail.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Étape 3 : Utiliser la bibliothèque ADAL pour sécuriser les pages dans l’application à monopage

Adal.js s’intègre avec l’itinéraire AngularJS t les fournisseurs HTTP afin de permettre de sécuriser les vues individuelles dans votre application à page unique.

1. Dans `App/Scripts/app.js`, ouvrez le module adal.js :

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Initialisez `adalProvider` à l’aide des valeurs de configuration de l’inscription de votre application, également dans `App/Scripts/app.js` :

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. Sécurisez la vue `TodoList` dans l’application à l’aide d’une seule ligne de code : `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Résumé

Vous disposez maintenant d’une application à page unique sécurisée qui peut connecter les utilisateurs et émettre un jeton porteur vers son API de serveur principal. Lorsqu’un utilisateur clique sur le lien **TodoList**, adal.js redirige automatiquement vers Azure AD pour la connexion, si nécessaire. En outre, adal.js joint automatiquement un jeton d’accès à toutes les demandes Ajax envoyées au serveur principal de l’application.

Les étapes ci-dessus sont le minimum requis pour générer une application à page unique à l’aide d’adal.js. Mais certaines autres fonctionnalités sont utiles dans application à page unique :

* Pour émettre explicitement des demandes de connexion et de déconnexion, vous pouvez définir des fonctions dans vos contrôleurs qui appellent adal.js. Dans `App/Scripts/homeCtrl.js` :

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* Vous pouvez présenter des informations utilisateur dans l’interface utilisateur de l’application. Le service ADAL a déjà été ajouté au contrôleur `userDataCtrl`. Vous pouvez donc accéder à l’objet `userInfo` dans la vue associée, `App/Views/UserData.html` :

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Il existe de nombreux scénarios dans lesquels vous souhaitez savoir si l’utilisateur est connecté ou non. Vous pouvez aussi utiliser l’objet `userInfo` pour rassembler ces informations. Par exemple, dans `index.html`, vous pouvez afficher le bouton **Connexion** ou **Déconnexion** en fonction de l’état d’authentification :

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Votre application à page unique intégrée à Azure AD peut authentifier les utilisateurs, appeler en toute sécurité son serveur principal à l’aide d’OAuth 2.0 et obtenir des informations de base sur l’utilisateur. Si vous ne l’avez pas encore fait, il est maintenant temps de remplir votre client avec quelques utilisateurs. Exécutez votre application à page unique de listes des tâches et connectez-vous avec un de ces utilisateurs. Ajoutez des tâches à la liste des tâches des utilisateurs, déconnectez-vous et reconnectez-vous.

Adal.js facilite l’intégration des fonctionnalités d’identité communes dans votre application. Elle effectue les tâches ingrates pour vous : gestion du cache, prise en charge du protocole OAuth, présentation d’une interface utilisateur de connexion à l’utilisateur, actualisation des jetons expirés et bien plus encore.

Pour référence, l’exemple terminé (sans vos valeurs de configuration) est disponible dans [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez à présent aborder d’autres scénarios.

> [!div class="nextstepaction"]
> [Appeler une API web CORS à partir d’une application monopage](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).
