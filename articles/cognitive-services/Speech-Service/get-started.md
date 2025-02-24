---
title: Essayer les services Speech gratuitement
titleSuffix: Azure Cognitive Services
description: La prise en main des services Speech est simple et économique. Un essai gratuit de 30 jours vous permet de découvrir les fonctionnalités du service et de décider s’il est adapté aux besoins de votre application.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: aaae84fcb7df2abfe7e78adb64c22fb19fe45f4e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606732"
---
# <a name="try-speech-services-for-free"></a>Essayer les services Speech gratuitement

La prise en main des services Speech est simple et économique. Un essai gratuit de 30 jours vous permet de découvrir les fonctionnalités du service et de décider s’il est adapté aux besoins de votre application.

Si vous avez besoin de plus de temps, ouvrez un compte Microsoft Azure. Celui-ci est fourni avec 200 $ de crédit de service que vous pouvez appliquer à un abonnement payant aux services Speech pour une période maximale de 30 jours.

Enfin, les services Speech offrent un niveau gratuit de faible volume permettant de développer des applications. Vous pouvez conserver cet abonnement gratuit même après l’expiration de votre crédit de service.

## <a name="free-trial"></a>Essai gratuit

L’essai gratuit de 30 jours vous donne accès au niveau tarifaire standard pour une durée limitée.

Pour vous inscrire pour un essai gratuit de 30 jours :

1. Accédez à [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

1. Sélectionnez l’onglet **API Reconnaissance vocale**.

   ![Onglet Services vocaux](media/index/try-speech-api-free-trial1.png)

1. Sous **Services Speech**, sélectionnez le bouton **Obtenir la clé API**.

   ![Clé API](media/index/try-speech-api-free-trial2.png)

1. Acceptez les conditions et sélectionnez vos paramètres régionaux dans le menu déroulant.

   ![J’accepte les conditions](media/index/try-speech-api-free-trial3.png)

1. Connectez-vous à l’aide de votre compte Microsoft, Facebook, LinkedIn ou GitHub.

    Vous pouvez vous inscrire pour obtenir un compte Microsoft gratuit sur le [portail Compte Microsoft](https://account.microsoft.com/account). Pour commencer, cliquez sur **Se connecter avec Microsoft** puis, à l’invite de connexion, cliquez sur **Créez-en un**. Suivez les étapes pour créer et vérifier votre nouveau compte Microsoft.

Une fois connecté à Essayez Cognitive Services, votre essai gratuit commence. La page web affichée répertorie tous les services Azure Cognitive Services pour lesquels vous disposez actuellement d’un abonnement d’essai. Deux clés d'abonnement sont répertoriées en regard de **Services Speech**. Vous pouvez utiliser n’importe laquelle de ces clés dans vos applications.

> [!NOTE]
> Tous les abonnements d’essai gratuit se trouvent dans la région USA Ouest. Quand vous effectuez des requêtes, veillez à utiliser le point de terminaison `westus`.

## <a name="new-azure-account"></a>Nouveau compte Azure

Les nouveaux comptes Azure reçoivent un crédit de service de 200 $ disponible jusqu’à 30 jours. Vous pouvez utiliser ce crédit pour explorer plus en détail les services Speech ou pour commencer à développer des applications.

Pour obtenir un nouveau compte Azure, accédez à la [page d’inscription Azure](https://azure.microsoft.com/free/ai/), cliquez sur **Démarrer gratuitement** et créez un compte Azure à l’aide de votre compte Microsoft.

Vous pouvez vous inscrire pour obtenir un compte Microsoft gratuit sur le [portail Compte Microsoft](https://account.microsoft.com/account). Pour commencer, cliquez sur **Se connecter avec Microsoft** puis, à l’invite de connexion, cliquez sur **Créez-en un**. Suivez les étapes pour créer et vérifier votre nouveau compte Microsoft.

Une fois que vous avez crée votre compte Azure, suivez les étapes de la section suivante pour démarrer un abonnement aux services Speech.

## <a name="create-a-speech-resource-in-azure"></a>Créer une ressource Speech dans Azure

Pour ajouter une ressource des services Speech (niveau gratuit ou payant) à votre compte Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en utilisant votre compte Microsoft.

1. Sélectionnez **Créer une ressource** en haut à gauche du portail.

    ![Créer une ressource](media/index/try-speech-api-create-speech1.png)

1. Dans la fenêtre **Nouveau**, recherchez **Speech**.

1. Dans les résultats de la recherche, sélectionnez **Speech**.

    ![Sélectionnez Speech](media/index/try-speech-api-create-speech2.png)

1. Sous **Speech**, sélectionnez le bouton **Créer**.

    ![Sélectionnez le bouton Créer](media/index/try-speech-api-create-speech3.png)

1. Sous **Créer**, entrez :

   * Un nom pour la nouvelle ressource. Ce nom vous permet de faire la distinction entre plusieurs abonnements au même service.
   * Choisissez l’abonnement Azure associé à la nouvelle ressource pour déterminer la façon dont les frais sont facturés.
   * Choisissez la région où la ressource sera utilisée. Actuellement, les services Speech sont disponibles dans les régions Asie Est, Europe Nord et USA Ouest.
   * Choisissez soit un niveau tarifaire payant ou gratuit. Cliquez sur **Afficher tous les détails de la tarification** pour plus d’informations sur la tarification et les quotas d’utilisation pour chaque niveau.
   * Créez un groupe de ressources pour cet abonnement Speech ou affectez l’abonnement à un groupe de ressources existant. Les groupes de ressources vous permettent d’organiser vos différents abonnements Azure.
   * Pour accéder facilement à votre abonnement à l’avenir, cochez la case **Épingler au tableau de bord**.
   * Sélectionnez **Créer**.

     ![Sélectionnez le bouton Créer](media/index/try-speech-api-create-speech4.png)

     La création et le déploiement de votre nouvelle ressource Speech prennent quelques instants. Sélectionnez **Démarrage rapide** pour afficher des informations sur votre nouvelle ressource.

     ![Panneau Démarrage rapide](media/index/try-speech-api-create-speech5.png)

1. Sous **Démarrage rapide**, cliquez sur le lien **Clés** sous l’étape 1 pour afficher vos clés d’abonnement. Chaque abonnement dispose de deux clés ; vous pouvez utiliser l’une ou l’autre dans votre application. Sélectionnez le bouton en regard de chaque clé pour la copier dans le Presse-papiers et la coller dans votre code.

> [!NOTE]
> Vous pouvez créer autant d’abonnements de niveau standard que vous le souhaitez dans une ou plusieurs régions. Toutefois, vous ne pouvez créer qu’un seul abonnement de niveau gratuit. Les déploiements de modèle au niveau gratuit qui restent inutilisés durant sept jours sont supprimés automatiquement.

## <a name="switch-to-a-new-subscription"></a>Basculer vers un nouvel abonnement

Pour basculer d’un abonnement à un autre, par exemple quand votre essai gratuit expire ou quand vous publiez votre application, remplacez la région et la clé d’abonnement dans votre code par celles de la nouvelle ressource Azure.

> [!NOTE]
> Les clés d’essai gratuit sont créées dans la région USA Ouest (`westus`). Un abonnement créé par le biais du tableau de bord Azure peut être dans une autre région, si vous le souhaitez.

* Si votre application utilise un [SDK Speech](speech-sdk.md), vous fournissez le code de région, tel que `westus`, quand vous créez une configuration de reconnaissance vocale.
* Si votre application utilise l'une des [API REST](rest-apis.md) des services Speech, la région fait partie de l'URI de point de terminaison que vous utilisez pour effectuer des requêtes.

Les clés créées pour une région sont valides uniquement dans cette région. Si vous essayez de les utiliser avec d’autres régions, des erreurs d’authentification se produisent.

## <a name="next-steps"></a>Étapes suivantes

Effectuez l’un de nos Démarrages rapides de 10 minutes ou consultez nos exemples de SDK :

> [!div class="nextstepaction"]
> [Démarrage rapide : Reconnaissance vocale en C#](quickstart-csharp-dotnet-windows.md)
> [Exemples du SDK Speech](speech-sdk.md#get-the-samples)
