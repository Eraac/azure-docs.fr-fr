---
title: Limites de la sécurité d’Azure Data Box Gateway | Microsoft Docs
description: Décrit les fonctionnalités de sécurité et de confidentialité qui protègent votre service, votre appareil et vos données Azure Data Box Gateway en local et dans le cloud.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 230d1a28ba15a8736e46c02cb08217a28fc18599
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64695183"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Protection des données et sécurité Azure Data Box Gateway

La sécurité est une préoccupation majeure pour toute nouvelle technologie, surtout si cette technologie est utilisée avec des données confidentielles ou propriétaires. Azure Data Box Gateway vous assure que seuls les entités autorisées peuvent consulter, modifier ou supprimer vos données.

Cet article décrit les fonctionnalités de sécurité Azure Data Box Gateway qui permettent de protéger chacun des composants de la solution et les données stockées sur ces derniers.

La solution Data Box Gateway repose sur l’interaction de quatre composants principaux :

- **Service Data Box Gateway, hébergé dans Azure**. la ressource de gestion qui vous permet de créer la commande de l’appareil, de configurer l’appareil et de suivre la commande jusqu’à son achèvement.
- **Appareil Data Box Gateway**. L’appareil virtuel que vous approvisionnez dans l’hyperviseur du système que vous fournissez. Cet appareil virtuel est utilisé pour importer vos données locales dans Azure.
- **Clients/hôtes connectés à l’appareil**. Les clients de votre infrastructure se connectant à l’appareil Data Box Gateway et contenant des données devant être protégées.
- **Stockage cloud**. Emplacement dans la plateforme cloud Azure où les données sont stockées. Il s’agit généralement du compte de stockage lié à la ressource Data Box Gateway que vous avez créée.


## <a name="data-box-gateway-service-protection"></a>Protection de service Data Box Gateway

Le service Data Box Gateway est un service de gestion hébergé dans Azure. Ce service est utilisé pour configurer et gérer l’appareil.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Protection d’appareil Data Box Gateway

L’appareil Data Box Gateway est un appareil virtuel qui est approvisionné dans l’hyperviseur d’un système local que vous fournissez. Cet appareil permet d’envoyer des données vers Azure. Votre appareil :

- A besoin d’une clé d’activation pour accéder au service Data Box Edge/Data Box Gateway.
- Est protégé à tout moment par un mot de passe d’appareil.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Protéger l’appareil via une clé d’activation

Seul les appareils Data Box Gateway autorisés sont autorisés à rejoindre le service Data Box Gateway que vous avez créé dans votre abonnement Azure. Pour autoriser un appareil, vous devez utiliser une clé d’activation pour activer l’appareil auprès du service Data Box Gateway.

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

Pour plus d’informations, consultez [Obtenir une clé d’activation](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Protéger l’appareil via un mot de passe

Les mots de passe garantissent que seuls les utilisateurs autorisés accèdent à vos données. Les appareils Data Box Gateway démarrent en étant verrouillés.

Vous pouvez :

- Vous connecter à l’interface utilisateur web locale de l’appareil via un navigateur, puis indiquer un mot de passe pour vous connecter à l’appareil.
- Vous connecter à distance à l’interface PowerShell de l’appareil via HTTP. La gestion à distance est désactivée par défaut. Vous pouvez ensuite fournir le mot de passe pour vous connecter à l’appareil. Pour plus d’informations, consultez [Connexion à distance à votre appareil Data Box Gateway](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- Utiliser l’interface utilisateur web locale pour [modifier le mot de passe](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Si vous modifiez le mot de passe, veillez à informer tous les utilisateurs à distance afin qu’ils ne connaissent pas de problème de connexion.


## <a name="protect-your-data"></a>Protéger vos données

Cette section décrit les fonctionnalités de sécurité Data Box Gateway visant à protéger les données transmises et stockées.

### <a name="protect-data-at-rest"></a>Protection des données au repos

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Protection des données à la volée

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>Protection des données par les comptes de stockage

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- Faites tourner puis [synchronisez vos clés de compte de stockage](data-box-gateway-manage-shares.md#sync-storage-keys) régulièrement pour mieux protéger votre compte de stockage des utilisateurs non autorisés.

## <a name="manage-personal-information"></a>Gestion des informations personnelles

Le service Data Box Gateway collecte des informations personnelles dans les scénarios suivants :

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

Pour afficher la liste des utilisateurs qui ont accès à un partage ou qui peuvent supprimer un partage, suivez les étapes de la page [Gérer les partages sur Data Box Gateway](data-box-gateway-manage-shares.md).

Pour plus d’informations, consultez la Politique de confidentialité Microsoft dans le [Centre de gestion de la confidentialité](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Étapes suivantes

[Déployer votre appareil Data Box Gateway](data-box-gateway-deploy-prep.md)
