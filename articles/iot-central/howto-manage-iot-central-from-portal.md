---
title: Gérer IoT Central à l’aide du Portail Azure | Microsoft Docs
description: Gérez IoT Central à l’aide du Portail Azure.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 7893c8e8b8d67b4b63bd9d6bb5a71552e95c9125
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850248"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Gérer IoT Central à l’aide du Portail Azure

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Au lieu de créer et de gérer des applications IoT Central à partir de la page [Application Manager](https://aka.ms/iotcentral) d’IoT Central, vous pouvez utiliser le [portail Azure](https://portal.azure.com) pour gérer vos applications.

## <a name="create-iot-central-applications"></a>Créer des applications IoT Central

Pour créer une application, accédez au [portail Azure](https://ms.portal.azure.com), puis sélectionnez **Créer une ressource** dans le menu de navigation principal situé sur la gauche.

![Portail de gestion : menu de navigation](media/howto-manage-iot-central-from-portal/image0.png)

Dans la barre de recherche, tapez **IoT Central**.

![Portail de gestion : recherche](media/howto-manage-iot-central-from-portal/image0a1.png)

Sélectionnez la ligne **Application IoT Central** dans les résultats de la recherche.

![Portail de gestion : résultats de la recherche](media/howto-manage-iot-central-from-portal/image0b1.png)

À présent, sélectionnez **Créer**.

![Portail de gestion : Ressource IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Renseignez tous les champs dans le formulaire. Ce formulaire ressemble à celui que vous devez remplir pour créer des applications dans la page [Application Manager](https://aka.ms/iotcentral) d’IoT Central. Pour plus d’informations, consultez le guide de démarrage rapide [Créer une application IoT Central](quick-deploy-iot-central.md).

![Portail de gestion : créer une ressource IoT Central](media/howto-manage-iot-central-from-portal/image1a.png)  

Après avoir renseigné tous les champs, sélectionnez **Créer**.

## <a name="manage-existing-iot-central-applications"></a>Gérer des applications IoT Central existantes

Si vous disposez déjà d’une application Azure IoT Central, vous pouvez la supprimer ou la déplacer vers un abonnement ou un groupe de ressources différent dans le portail Azure.

> [!NOTE]
> Vous ne voyez pas les applications disponibles pour un essai gratuit dans le portail Azure, car celles-ci ne sont pas associées à votre abonnement.

Pour commencer, sélectionnez **Toutes les ressources** dans le menu de navigation principal sur la gauche. Tapez le nom de votre application dans la zone de recherche pour la trouver dans la liste de vos ressources. Sélectionnez ensuite sur l’application IoT Central que vous souhaitez gérer.

![Portail de gestion : gestion des ressources](media/howto-manage-iot-central-from-portal/image2a.png)

Pour accéder à l’application, sélectionnez l’URL de l’application IoT Central.

![Portail de gestion : gestion des ressources](media/howto-manage-iot-central-from-portal/image3.png)

Pour déplacer l’application vers un autre groupe de ressources, sélectionnez le lien **Modifier** à côté du groupe de ressources. Dans la page **Déplacer des ressources**, sélectionnez le groupe de ressources vers lequel vous souhaitez effectuer la migration de cette application.

![Portail de gestion : gestion des ressources](media/howto-manage-iot-central-from-portal/image4a.png)

Pour déplacer l’application vers un autre abonnement, sélectionnez lien **Modifier** à côté de l’abonnement. Dans la boîte de dialogue qui s’affiche, sélectionnez l’abonnement vers lequel vous souhaitez effectuer la migration de l’application.

![Portail de gestion : gestion des ressources](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à gérer des applications Azure IoT Central dans le portail Azure, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Administrer votre application](howto-administer.md)