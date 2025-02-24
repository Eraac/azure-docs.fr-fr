---
title: Création d’un compte Azure Media Services avec le portail Azure | Microsoft Docs
description: Ce tutoriel vous guide à travers les étapes de création d’un compte Azure Media Services à l’aide du portail Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: ddc1c7f2dd207cba18a8c080c8b14cc53c149a39
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463318"
---
# <a name="create-a-media-services-account-using-the-azure-portal"></a>Créer un compte Media Services avec le portail Azure

> [!NOTE]
> Aucune nouvelle fonctionnalité ni fonction n’est ajoutée à Media Services v2. <br/>Découvrez la dernière version, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consultez aussi [Conseils de migration de v2 vers v3](../latest/migrate-from-v2-to-v3.md)

Le portail Azure permet de créer rapidement un compte Azure Media Services (AMS). Vous pouvez utiliser ce dernier pour accéder à Media Services afin de stocker, de chiffrer, d’encoder, de gérer et de diffuser du contenu multimédia dans Azure. En créant un compte Media Services, vous créez aussi un compte de stockage associé (ou vous utilisez un compte existant). Si vous supprimez un compte Media Services, les objets blob de votre compte de stockage associé ne seront pas supprimés.

Vous pouvez avoir Usage général v1 ou Usage général v2 comme compte de stockage principal. Actuellement, le portail Azure permet seulement de sélectionner la v1, mais vous pouvez ajouter la v2 lors de la création de votre compte avec l’API ou Powershell. Pour plus d’informations sur les types de stockage, consultez [À propos des comptes de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

Le compte Media Services et tous les comptes de stockage associés doivent être faire partie du même abonnement Azure. Il est fortement recommandé d’utiliser des comptes de stockage situés au même emplacement que le compte Media Services afin d’éviter des frais supplémentaires de sortie des données et une importante latence.

Cet article montre comment créer un compte Media Services à l’aide du portail Azure.

> [!NOTE]
> Pour plus d’informations sur la disponibilité des fonctionnalités Azure Media Services dans des régions différentes, consultez la [disponibilité des fonctionnalités AMS entre les centres de données](scenarios-and-availability.md#availability).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous avez besoin d’un compte Azure. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="create-an-ams-account"></a>Création d’un compte AMS

Cette section montre comment créer un compte AMS.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **+Nouveau** > **Web + Mobile** > **Media Services**.
   
    ![Media Services Créer](./media/media-services-create-account/media-services-new1.png)
3. Dans **CREATE MEDIA SERVICES ACCOUNT** (CRÉER UN COMPTE MEDIA SERVICES), entrez les valeurs requises.
   
    ![Media Services Créer](./media/media-services-create-account/media-services-new3.png)
   
   1. Dans **Nom du compte**, entrez le nom du nouveau compte AMS. Un nom de compte Media Services se compose de lettres en minuscules ou de chiffres, sans espaces. Sa longueur est comprise entre 3 et 24 caractères.
   2. Dans Abonnement, sélectionnez l’un des abonnements Azure auxquels vous avez accès.
   3. Dans **Groupe de ressources**, sélectionnez la ressource (nouvelle ou existante).  Un groupe de ressources désigne une collection de ressources qui partagent un cycle de vie, des autorisations et des stratégies. En savoir plus [ici](../../azure-resource-manager/resource-group-overview.md#resource-groups).
   4. Dans **Emplacement**, sélectionnez la région géographique qui sera utilisée pour stocker les enregistrements multimédias et les métadonnées de votre compte Media Services. Cette région servira à traiter et diffuser vos médias. Seules les régions Media Services disponibles s’affichent dans la liste déroulante. 
   5. Dans **Compte de stockage**, sélectionnez le compte de stockage qui fournira le stockage d’objets blob du contenu multimédia provenant de votre compte Media Services. Vous pouvez sélectionner un compte de stockage dans la même région géographique que votre compte Media Services ou en créer un. Ce dernier sera créé dans la même région. Les règles des noms de compte de stockage sont identiques à celles des comptes Media Services.
      
       Pour en savoir plus sur le stockage, cliquez [ici](../../storage/common/storage-introduction.md).
   6. Sélectionnez **Épingler au tableau de bord** pour voir la progression du déploiement du compte.
4. Cliquez sur **Créer** en bas du formulaire.
   
    Une fois le compte créé, passez en revue les chargements de page. Dans la table de point de terminaison de diffusion en continu, le compte aura un point de terminaison de diffusion en continu par défaut dans l’état **Arrêté**. 

    >[!NOTE]
    >Une fois votre compte AMS créé, un point de terminaison de diffusion continue **par défaut** est ajouté à l’état **Arrêté**. Pour démarrer la diffusion en continu de votre contenu et tirer parti de l’empaquetage et du chiffrement dynamiques, le point de terminaison de streaming à partir duquel vous souhaitez diffuser du contenu doit se trouver à l’état **En cours d’exécution**. 
   
## <a name="to-manage-your-ams-account"></a>Pour gérer votre compte AMS

Pour gérer votre compte AMS (par exemple, vous connecter à l’API AMS par programme, charger des vidéos, encoder des ressources, configurer la protection du contenu, surveiller la progression du travail) sélectionnez **Paramètres** sur le côté gauche du portail. À partir de **Paramètres**, accédez à l’un des panneaux disponibles (par exemple : **Accès API**, **Ressources**, **Travaux**, **Protection du contenu**).


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant télécharger des fichiers dans votre compte AMS. Pour plus d’informations, consultez la section [Téléchargement de fichiers dans un compte Media Services à l’aide du portail Azure](media-services-portal-upload-files.md).

Si vous envisagez d’accéder à l’API AMS par programme, consultez [Accéder à l’API Azure Media Services avec l’authentification Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

