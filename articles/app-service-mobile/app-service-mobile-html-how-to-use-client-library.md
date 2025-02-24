---
title: Utilisation du Kit de développement logiciel (SDK) JavaScript pour Azure Mobile Apps
description: Utilisation de v pour Azure Mobile Apps
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: d5aa2e326739a97ff3d518ec383f4cf14311ca74
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446332"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Utilisation de la bibliothèque cliente JavaScript pour Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center investit dans des services nouveaux et intégrés, essentiels au développement d’applications mobiles. Les développeurs peuvent utiliser les services **Build**, **Test** et **Distribute** pour configurer le pipeline de livraison et d’intégration continues. Une fois l’application déployée, les développeurs peuvent surveiller l’état et l’utilisation de leur application à l’aide des services **Analytics** et **Diagnostics** et interagir avec des utilisateurs à l’aide du service **Push**. Les développeurs peuvent également utiliser **Auth** pour authentifier leurs utilisateurs, ainsi que le service **Data** pour conserver et synchroniser les données d’application dans le cloud. Découvrez [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-html-how-to-use-client-library) dès aujourd’hui.
>

## <a name="overview"></a>Vue d'ensemble
Ce guide indique le déroulement de scénarios courants dans le cadre de l’utilisation du dernier [Kit de développement logiciel (SDK) JavaScript pour Azure Mobile Apps]. Si vous ne connaissez pas Azure Mobile Apps, consultez d’abord la section [Démarrage rapide d’Azure Mobile Apps] pour créer un backend et créer une table. Dans ce guide, nous nous concentrons sur l’utilisation du backend mobile dans les applications web HTML/JavaScript.

## <a name="supported-platforms"></a>Plateformes prises en charge
Nous limitons la prise en charge des navigateurs à la version actuelle et à la dernière version des principaux navigateurs :  Google Chrome, Microsoft Edge, Microsoft Internet Explorer et Mozilla Firefox.  Le Kit de développement logiciel (SDK) devrait fonctionner avec n’importe quel navigateur relativement récent.

Le package est distribué comme un module JavaScript universel et prend donc en charge les format Globals, AMD et CommonJS.

## <a name="Setup"></a>Configuration et conditions préalables
Ce guide part du principe que vous avez créé un serveur principal avec une table. Ce guide suppose que la table a le même schéma que les tables dans ces didacticiels.

Installez le Kit de développement logiciel (SDK) JavaScript Azure Mobile Apps à partir de la commande `npm` :

```
npm install azure-mobile-apps-client --save
```

La bibliothèque peut également être utilisée en tant que module ES2015, au sein d'environnements CommonJS tels que Browserify et Webpack, et en tant que bibliothèque AMD.  Par exemple :

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

Vous pouvez également utiliser une version du Kit de développement logiciel (SDK) avant génération en téléchargeant directement à partir de notre CDN :

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Procédure : Authentification des utilisateurs
Azure App Service prend en charge l’authentification et l’autorisation des utilisateurs de l'application par l'intermédiaire de différents fournisseurs d'identité externes : Facebook, Google, compte Microsoft et Twitter. Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser l’identité des utilisateurs authentifiés pour implémenter des règles d’autorisation dans les scripts serveur. Pour plus d'informations, consultez la page [Prise en main de l'authentification] .

Deux flux d’authentification sont pris en charge : un flux serveur et un flux client.  Le flux serveur fournit l'authentification la plus simple, car il repose sur l'interface d'authentification Web du fournisseur. Le flux client permet une intégration approfondie avec les fonctionnalités propres aux appareils, telles que l'authentification unique, car il repose sur des Kits de développement logiciel (SDK) propres aux fournisseurs.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Procédure : Configurer votre Mobile App Service pour les URL de redirection externes.
Plusieurs types d’applications JavaScript utilisent une fonctionnalité de bouclage pour gérer les flux d’interface utilisateur OAuth.  Ces fonctionnalités sont les suivantes :

* Exécuter votre service en local
* Utilisation de Live Reload avec l’infrastructure Ionic
* Redirection vers le App Service pour authentification.

L’exécution locale peut entraîner des problèmes car, par défaut, l’authentification d’App Service est uniquement configurée pour autoriser l’accès à partir du serveur principal de votre application mobile. Utilisez la procédure suivante pour modifier les paramètres d’App Service afin d’activer l’authentification lors de l’exécution locale du serveur :

1. Connectez-vous au [portail Azure]
2. Accédez à votre backend d’application mobile.
3. Sélectionnez **Explorateur de ressources** dans le menu **OUTILS DE DÉVELOPPEMENT**.
4. Cliquez sur **Aller** pour ouvrir l’Explorateur de ressources pour votre application mobile principale dans une fenêtre ou un nouvel onglet.
5. Développez le nœud **config** > **authsettings** pour votre application.
6. Cliquez sur le bouton **Modifier** pour activer la modification de la ressource.
7. Trouvez l’élément **allowedExternalRedirectUrls** , qui doit être null. Ajoutez vos URL dans un tableau :

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Remplacez les URL dans le tableau par les URL de votre service ; dans cet exemple, `http://localhost:3000` pour l’exemple de service Node.js local. Vous pouvez également utiliser `http://localhost:4400` pour le service Ripple ou d’autres URL, selon la configuration de votre application.
8. En haut de la page, cliquez sur **Lecture/Écriture**, puis sur **PUT** pour enregistrer vos mises à jour.

Vous devez aussi ajouter les mêmes URL de bouclage aux paramètres de la liste blanche CORS :

1. Revenez au [portail Azure].
2. Accédez à votre backend d’application mobile.
3. Cliquez sur **CORS** dans le menu **API**.
4. Saisissez chaque URL dans la zone de texte **Origines autorisées** vide.  Une zone de texte est créée.
5. Cliquez sur **ENREGISTRER**

Après que le serveur principal sera mis à jour, vous serez en mesure d’utiliser les nouvelles URL de bouclage dans votre application.

<!-- URLs. -->
[Démarrage rapide d’Azure Mobile Apps]: app-service-mobile-cordova-get-started.md
[Prise en main de l'authentification]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Portail Azure]: https://portal.azure.com/
[Kit de développement logiciel (SDK) JavaScript pour Azure Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
