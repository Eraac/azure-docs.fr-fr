---
title: Comment résoudre les problèmes du runtime d’Azure Functions est inaccessible.
description: Découvrez comment résoudre les problèmes relatifs à un compte de stockage non valide.
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.service: azure-functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 6057fa52cd2f1e9b9fd525723f96ab66983fb5d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61020296"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Comment résoudre les messages « functions runtime is unreachable »


## <a name="error-text"></a>Texte d’erreur
Ce document est destiné à résoudre les problèmes liés à l’affichage de l’erreur suivante dans le portail Functions.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>Résumé
Ce problème se produit lorsque le Runtime d’Azure Functions ne peut pas démarrer. Cette erreur se produit le plus souvent lorsque l’application de fonction perd l’accès à son compte de stockage. [En savoir plus sur les exigences concernant les comptes de stockage ici](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Résolution de problèmes
Nous allons étudier les quatre cas d’erreur les plus courants, et découvrir comment les identifier et les résoudre.

1. Compte de stockage supprimé
1. Paramètres d’application du compte de stockage supprimés
1. Informations d’identification du compte de stockage invalides
1. Compte de stockage inaccessible
1. Limite d’exécution quotidienne atteinte

## <a name="storage-account-deleted"></a>Compte de stockage supprimé

Chaque application de fonction nécessite un compte de stockage afin de fonctionner. Si ce compte est supprimé, votre fonction ne s’exécutera pas.

### <a name="how-to-find-your-storage-account"></a>Comment trouver votre compte de stockage

Démarrez en recherchant le nom de votre compte de stockage dans les paramètres d’application. `AzureWebJobsStorage` ou `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` contiendra le nom de votre compte de stockage encapsulé dans une chaîne de connexion. Lire des informations plus précises aux [références de paramètres d’application ici](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)

Recherchez votre compte de stockage dans le portail Azure pour voir s’il existe toujours. S’il a été supprimé, vous devez recréer un compte de stockage et remplacer vos chaînes de connexion de stockage. Votre code de fonction est perdu et vous devrez à nouveau le redéployer.

## <a name="storage-account-application-settings-deleted"></a>Paramètres d’application du compte de stockage supprimés

Dans l’étape précédente, si vous ne disposiez pas d’une chaîne de connexion à un compte de stockage, il est probable qu’elle ait été supprimée ou écrasée. La suppression des paramètres de l’application s’effectue le plus souvent en utilisant des emplacements de déploiement ou des scripts Azure Resource Manager pour définir les paramètres de l’application.

### <a name="required-application-settings"></a>Paramètres de l’application obligatoires

* Obligatoire
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Obligatoire pour les fonctions du Plan de consommation
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[En savoir plus sur ces paramètres d’application ici](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Assistance

* N’activez pas le « paramètre d’emplacement » pour ces paramètres. Lorsque vous échangez les emplacements de déploiement, la fonction s’interrompt.
* Ne modifiez pas ces paramètres lors de déploiements automatisés.
* Ces paramètres doivent être fournis et valides au moment de la création. Un déploiement automatisé sans ces paramètres rend l’application non fonctionnelle et ce, même si les paramètres sont ajoutés après coup.

## <a name="storage-account-credentials-invalid"></a>Informations d’identification du compte de stockage invalides

Les chaînes de connexion de compte de stockage ci-dessus doivent être mises à jour si vous régénérez les clés de stockage. [En savoir plus sur la gestion des clés de stockage ici](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>Compte de stockage inaccessible

Votre Function App doit être en mesure d’accéder au compte de stockage. Les problèmes courants qui bloquent l’accès d’une Function à un compte de stockage sont :

* Des Function Apps déployées sur les environnements App Service sans les règles de réseau appropriées pour autoriser le trafic vers et depuis le compte de stockage
* Le pare-feu de compte de stockage est activé et pas configuré pour autoriser le trafic vers et à partir des Functions. [En savoir plus sur la configuration du pare-feu de compte de stockage ici](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Quota d’exécution quotidienne atteint

Si vous avez configuré un quota d’exécution quotidien, votre Function App sera temporairement désactivée et la plupart des contrôles du portail seront indisponibles. 

* Pour vérifier, accédez à Ouvrir les fonctionnalités de la plateforme > Paramètres de Function App dans le portail. Le message suivant s’affiche si vous avez dépassé votre quota
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Supprimer le quota et redémarrez votre application pour résoudre le problème.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que votre Function App est de nouveau opérationnelle, examinons les démarrages rapides et références développeur pour être à nouveau opérationnel !

* [Créer votre première fonction Azure](functions-create-first-azure-function.md)  
  vous permet de créer votre première fonction à l’aide du démarrage rapide d’Azure Functions. 
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)  
  fournit des informations techniques supplémentaires sur l’exécution d’Azure Functions, ainsi qu’une référence pour le codage de fonctions et la définition des déclencheurs et des liaisons.
* [Test d’Azure Functions](functions-test-a-function.md)  
  décrit plusieurs outils et techniques permettant de tester vos fonctions.
* [Comment mettre à l’échelle Azure Functions](functions-scale.md)  
  Présente les plans de service disponibles pour Azure Functions, dont le plan d’hébergement de consommation, et explique comment choisir le plan adapté à vos besoins. 
* [En savoir plus sur Azure App Service](../app-service/overview.md)  
  Azure Functions s’appuie sur Azure App Service pour les fonctionnalités essentielles comme les déploiements, les variables d’environnement et les diagnostics. 
