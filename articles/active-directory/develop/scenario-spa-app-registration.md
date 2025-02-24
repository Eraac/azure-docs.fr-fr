---
title: Application monopage (inscription d’application) - Plateforme d’identité Microsoft
description: Découvrez comment créer une application monopage (inscription d’application)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1faf4669dca2aaaf3f873e66f859473ccd99f10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074829"
---
# <a name="single-page-application---app-registration"></a>Application monopage - Inscription d’application

Cette page explique les spécificités de l’inscription d’application pour une application monopage (SPA).

Suivez les étapes pour [inscrire une nouvelle application sur la Plateforme d’identité Microsoft](quickstart-register-app.md), puis sélectionnez les comptes pris en charge pour votre application. Le scénario SPA peut prendre en charge l’authentification avec des comptes dans votre organisation ou des comptes Microsoft de n’importe quelle organisation et personnels.

Découvrez ensuite les aspects spécifiques de l’inscription d’application qui s’appliquent aux applications monopages.

## <a name="register-a-redirect-uri"></a>Inscrire un URI de redirection

Le flux implicite envoie les jetons dans une redirection vers l’application monopage exécutée dans un navigateur web. Il est donc important d’inscrire un URI de redirection auquel votre application peut recevoir les jetons. Vérifiez que l’URI de redirection correspond exactement à l’URI de votre application.

Dans le [Portail Azure](https://go.microsoft.com/fwlink/?linkid=2083908), accédez à votre application inscrite. Dans la page **Authentification** de l’application, sélectionnez la plateforme **Web** et entrez la valeur de l’URI de redirection pour votre application dans le champ **URI de redirection**.

## <a name="enable-the-implicit-flow"></a>Activer le flux implicite

Dans la même page **Authentification**, sous **Paramètres avancés**, vous devez également activer l’**octroi implicite**. Si votre application connecte des utilisateurs et obtient des jetons d’ID uniquement, cochez simplement la case **Jetons d’ID**.

Si votre application doit également obtenir des jetons d’accès pour appeler des API, veillez à cocher la case **Jetons d’accès** également. Pour plus d’informations, consultez [Jetons d’ID](./id-tokens.md) et [Jetons d’accès](./access-tokens.md).

## <a name="api-permissions"></a>Autorisations des API

Les applications monopages peuvent appeler des API pour le compte de l’utilisateur connecté. Elles doivent demander des autorisations déléguées. Pour plus d’informations, consultez [Ajouter des autorisations pour accéder aux API web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Configuration de code de l’application](scenario-spa-app-configuration.md)
