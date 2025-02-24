---
title: Fichier Include
description: Fichier Include pour les pages d’accueil du scénario de client confidentiel (démon, application Web, API Web)
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
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 9ee7422b372993d60c629524eb036b9678e5776c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177245"
---
## <a name="registration-of-secrets-or-certificates"></a>Enregistrement de secrets ou de certificats

Comme pour toute application de client confidentiel, vous devez inscrire un certificat ou un secret. Vous pouvez inscrire les secrets de votre application via une expérience interactive dans le [portail Microsoft Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview), ou à l’aide des outils de ligne de commande (par exemple, PowerShell).

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>Inscrire des secrets client avec le portail d’inscription d’applications

La gestion des informations d’identification du client est effectuée dans la page des **certificats et secrets** d’une application :

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- Le secret d’une application (également appelé secret client) est généré par Azure AD, lors de l’inscription de l’application de client confidentiel. Cette génération est effectuée via l’option **Nouveau secret client**. À ce stade, vous devez copier la chaîne du secret dans le Presse-papiers pour pouvoir l’utiliser dans votre application, avant de cliquer sur **Enregistrer**. Cette chaîne ne s’affichera plus.
- Le certificat est chargé lors de l’inscription d’application, via le bouton **Charger un certificat**

Pour en savoir plus, voir [Démarrage rapide : Configurer une application cliente pour accéder aux API web | Ajouter des informations d’identification à votre application web](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)

### <a name="registering-client-secrets-using-powershell"></a>Inscription de secrets client à l’aide de PowerShell

Vous pouvez également inscrire votre application auprès d’Azure AD à l’aide des outils de ligne de commande. L’exemple de commande [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) montre comment inscrire un secret d’application ou un certificat avec une application Azure AD :

- Pour en savoir plus sur l’inscription d’un secret d’application, voir [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- Pour en savoir plus sur la méthode à suivre pour inscrire un certificat avec l’application, voir [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)