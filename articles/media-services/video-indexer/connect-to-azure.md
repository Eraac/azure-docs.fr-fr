---
title: Créer un compte Video Indexer dans le Portail Azure
titlesuffix: Azure Media Services
description: Cet article explique comment créer un compte Video Indexer dans le Portail Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 33493f1bdff6071737aad4bfb8c7d0e5e22896db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799841"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Créer un compte Video Indexer connecté à Azure

Lorsque vous créez un compte Video Indexer, vous pouvez choisir un compte d’essai gratuit (où vous obtenez un certain nombre de minutes d’indexation gratuites) ou une option payante (où vous n’êtes pas limités par le quota). Avec l’essai gratuit, Video Indexer fournit jusqu’à 600 heures d’indexation gratuite aux utilisateurs du site web et jusqu’à 2 400 heures d’indexation gratuite aux utilisateurs de l’API. Avec l’option payante, vous créez un compte Video Indexer connecté à votre abonnement Azure et un compte Azure Media Services. Vous payez pour les minutes indexées, ainsi que pour les frais liés au compte média. 

Cet article explique comment créer un compte Video Indexer connecté à un abonnement Azure et un compte Azure Media Services. Cette rubrique indique les étapes permettant de se connecter à Azure via le flux automatique (par défaut). Elle explique également comment se connecter à Azure manuellement (option avancée).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure.

    Si vous n’avez pas encore d’abonnement Azure, [inscrivez-vous pour bénéficier d’un essai gratuit Azure](https://azure.microsoft.com/free/).

* Un domaine Azure Active Directory (AD).

    Si vous n’avez pas de domaine Azure AD, créez-en un avec votre abonnement Azure. Pour plus d’informations, consultez la rubrique [Gestion des noms de domaine personnalisés dans Azure Active Directory](../../active-directory/users-groups-roles/domains-manage.md).

* Un utilisateur et un membre dans votre domaine Azure AD. Vous allez utiliser ce membre lors de la connexion de votre compte Video Indexer à Azure.

    Cet utilisateur doit être un utilisateur Azure AD avec un compte professionnel ou scolaire, et non un compte personnel, tel que outlook.com, live.com ou hotmail.com.

    ![Tous les utilisateurs ADD](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Prérequis supplémentaires pour le flux automatique

Un utilisateur et un membre dans votre domaine Azure AD. Vous allez utiliser ce membre lors de la connexion de votre compte Video Indexer à Azure.

Cet utilisateur doit être membre de votre abonnement Azure avec un rôle de **Propriétaire** ou les rôles de **Contributeur** et **d’Administrateur de l’accès utilisateur**. Un utilisateur peut être ajouté à deux reprises, avec deux rôles. Une fois comme contributeur et une autre fois comme administrateur des accès utilisateur.

![Contrôle d’accès](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Prérequis supplémentaires pour le flux manuel

Inscrivez le fournisseur de ressources EventGrid à l’aide du portail Azure.

Dans le [Portail Azure](https://portal.azure.com/), accédez à **Abonnements**-> [abonnement] ->**Fournisseurs de ressources**. 

Recherchez **Microsoft.Media** et **Microsoft.EventGrid**. S’il n’a pas l’état « Inscrit », cliquez sur **Inscrire**. Quelques minutes sont nécessaires pour effectuer l’inscription.

![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Connexion à Azure

1. Accédez au site web [Video Indexer](https://www.videoindexer.ai/) et connectez-vous.

2. Cliquez sur le bouton **Créer un compte** :

    ![Connexion à Azure](./media/create-account/connect-to-azure.png)

3. Lorsque la liste des abonnements s’affiche, sélectionnez l’abonnement que vous souhaitez utiliser.

    ![Connexion de Video Indexer à Azure](./media/create-account/connect-vi-to-azure-subscription.png)

4. Sélectionnez une région Azure parmi les lieux pris en charge : USA Ouest 2, Europe Nord et Asie-Pacifique.
5. Sous **Compte Azure Media Services**, choisissez une des options suivantes :

    * Pour créer un compte Media Services, sélectionnez **Créer un groupe de ressources**. Indiquez le nom de votre groupe de ressources.

        Azure crée ce compte dans votre abonnement, y compris un compte de stockage Azure. Votre nouveau compte Media Services a une configuration initiale par défaut avec un point de terminaison de streaming et 10 unités réservées S3.
    * Pour utiliser un compte Media Services existant, sélectionnez **Utiliser une ressource existante**. Dans la liste des comptes, sélectionnez votre compte.

        Votre compte Media Services doit avoir la même région que votre compte Video Indexer. 

        > [!NOTE]
        > Pour réduire la durée d’indexation et améliorer le débit, il est fortement recommandé d’ajuster le type et le nombre [d’unités réservées](../previous/media-services-scale-media-processing-overview.md ) sur **10 unités réservées S3** dans votre compte Media Services. Consultez [Modification du type d’unité réservée](../previous/media-services-portal-scale-media-processing.md).

    * Pour configurer manuellement votre connexion, cliquez sur le lien **Basculer vers la configuration manuelle**.

        Pour plus d’informations, consultez la section [Se connecter à Azure manuellement](#connect-to-azure-manually-advanced-option) (option avancée) ci-dessous.
6. Lorsque vous avez terminé, choisissez **Connecter**. Cette opération peut prendre quelques minutes. 

    Une fois que vous êtes connecté à Azure, votre nouveau compte Video Indexer apparaît dans la liste des comptes :

    ![Nouveau compte](./media/create-account/new-account.png)

7. Accéder à votre nouveau compte

## <a name="connect-to-azure-manually-advanced-option"></a>Se connecter à Azure manuellement (option avancée)

En cas d’échec de la connexion à Azure, vous pouvez tenter de résoudre le problème en vous connectant manuellement.

> [!NOTE]
> Il est vivement recommandé de disposer des trois comptes suivants dans la même région : le compte Video Indexer que vous connectez au compte Media Services, ainsi que le compte de stockage Azure connecté au même compte Media Services.

### <a name="create-and-configure-a-media-services-account"></a>Créer et configurer un compte Media Services

1. Utilisez le portail [Azure](https://portal.azure.com/) pour créer un compte Azure Media Services tel que décrit dans [Création d’un compte](../previous/media-services-portal-create-account.md).

    Lorsque vous créez un compte de stockage pour votre compte Media Services, sélectionnez **StorageV2** en tant que type de compte et **Stockage géo-redondant (GRS)** dans le champ Réplication.

    ![nouveau compte AMS](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Veillez à noter les noms de ressource et de compte Media Services. Vous en aurez besoin lors des étapes de la prochaine section.

2. Ajustez le type et le nombre [d’unités réservées](../previous/media-services-scale-media-processing-overview.md ) sur **10 unités réservées S3** dans le compte Media Services que vous avez créé. Consultez [Modification du type d’unité réservée](../previous/media-services-portal-scale-media-processing.md).
3. Avant de pouvoir lire vos vidéos dans l’application web de Video Indexer, vous devez démarrer le **point de terminaison de streaming** par défaut du nouveau compte Media Services.

    Dans le nouveau compte Media Services, cliquez sur **Points de terminaison de streaming**. Sélectionnez le point de terminaison de streaming, puis cliquez sur Démarrer.

    ![nouveau compte AMS](./media/create-account/create-ams-account2.png)

4. Pour que Video Indexer puisse s’authentifier auprès de l’API Media Services, vous devez créer une application AD. Les étapes suivantes vous guident dans le processus d’authentification Azure AD décrit dans [Prise en main de l’authentification Azure AD à l’aide du portail Azure](../previous/media-services-portal-get-started-with-aad.md) :

    1. Dans le nouveau compte Media Services, sélectionnez **Accès API**.
    2. Sélectionnez [Service principal authentication method](../previous/media-services-portal-get-started-with-aad.md#service-principal-authentication) (Méthode d’authentification de principal de service).
    3. Obtenez l’ID client et la clé secrète client, tel que décrit dans la section [Obtenir l’ID client et la clé secrète client](../previous/media-services-portal-get-started-with-aad.md#get-the-client-id-and-client-secret).

        Après avoir sélectionné **Paramètres**->**Clés**, ajoutez une **Description** et cliquez sur **Enregistrer**. La valeur de clé est alors remplie automatiquement.

        Si la clé expire, le propriétaire du compte devra contacter le support de Video Indexer pour la renouveler.

        > [!NOTE]
        > Veillez à noter la valeur de clé et l’ID d’application. Vous en aurez besoin lors des étapes de la prochaine section.

### <a name="connect-manually"></a>Se connecter manuellement

Dans la boîte de dialogue **Connecter Video Indexer à un abonnement Azure** de la page [Video Indexer](https://www.videoindexer.ai/), sélectionnez le lien **Basculer vers la configuration manuelle**.

Dans la boîte de dialogue, indiquez les informations suivantes :

|Paramètre|Description|
|---|---|
|Région du compte Video Indexer|Nom de la région du compte Video Indexer. Pour améliorer les performances et réduire les coûts, il est fortement recommandé de spécifier le nom de la région où se trouvent la ressource Azure Media Services et le compte de stockage Azure. |
|Locataire Azure Active Directory (AAD)|Nom du locataire Azure AD, par exemple contoso.onmicrosoft.com. Les informations relatives au locataire peuvent être récupérées à partir du portail Azure. Placez votre curseur sur le nom de l’utilisateur connecté, en haut à droite. Le nom est indiqué à droite du **domaine**.|
|Identifiant d’abonnement|Abonnement Azure sous lequel cette connexion doit être créée. L’ID d’abonnement peut être récupéré à partir du portail Azure. Cliquez sur **Tous les services** dans le panneau gauche, puis recherchez « abonnements ». Sélectionnez **Abonnements** et choisissez l’ID voulu dans la liste de vos abonnements.|
|Nom du groupe de ressources Azure Media Services|Nom du groupe de ressources dans lequel vous avez créé le compte Media Services.|
|Nom de la ressource de service multimédia|Nom du compte Azure Media Services que vous avez créé dans la section précédente.|
|ID de l'application|ID d’application Azure AD (avec des autorisations pour le compte Media Services spécifié) que vous avez créé dans la section précédente.|
|Clé de l’application|Clé de l’application Azure AD que vous avez créée dans la section précédente. |

## <a name="considerations"></a>Considérations

Tenez compte des considérations suivantes pour Azure Media Services :

* Si vous vous connectez automatiquement, vous voyez un nouveau groupe de ressources, un nouveau compte Media Services et un nouveau compte de stockage dans votre abonnement Azure.
* Si vous vous connectez automatiquement, Video Indexer définit les **unités réservées** multimédia sur 10 unités S3 :

    ![Unités réservées Media Services](./media/create-account/ams-reserved-units.png)

* Si vous vous connectez à un compte Media Services existant, Video Indexer ne modifie pas la configuration existante pour les **unités réservées** multimédia.

   Vous devrez peut-être ajuster le type et le nombre d’unités réservées multimédia en fonction de la charge prévue. N’oubliez pas que si votre charge est élevée et si vous n’avez pas suffisamment unités ou manquez de vitesse, le traitement de vidéos peut échouer en raison d’une expiration de délai.

* Si vous vous connectez à un nouveau compte Media Services, Video Indexer démarre automatiquement son **point de terminaison de streaming** par défaut :

    ![Point de terminaison de diffusion en continu Media Services](./media/create-account/ams-streaming-endpoint.png)

    Les points de terminaison de streaming nécessitent un temps de démarrage important. Par conséquent, il peut s’écouler plusieurs minutes entre le moment où vous vous connectez à votre compte Azure et la diffusion et le visionnage de vos vidéos dans l’application web Video Indexer.

* Si vous vous connectez à un compte Media Services existant, Video Indexer ne modifie pas la configuration du point de terminaison de streaming par défaut. Si aucun **point de terminaison de streaming** n’est en cours d’exécution, vous ne serez pas en mesure de regarder les vidéos à partir de ce compte Media Services, ni dans Video Indexer.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez interagir par programmation avec votre compte d’évaluation et/ou avec les comptes Video Indexer connectés à Azure en suivant les instructions de la section [Utiliser des API](video-indexer-use-apis.md).

Vous devez vous servir du même utilisateur Azure AD que lors de la connexion à Azure.


