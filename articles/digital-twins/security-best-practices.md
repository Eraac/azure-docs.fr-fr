---
title: Comprendre les meilleures pratiques de sécurité Azure Digital Twins | Microsoft Docs
description: Meilleures pratiques de sécurité Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: v-adgera
ms.openlocfilehash: 64a980ba8ed581b3cad369951220d47baf9e17ab
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849298"
---
# <a name="security-best-practices"></a>Meilleures pratiques en matière de sécurité

La sécurité Azure Digital Twins permet un accès précis aux ressources et actions de votre graphe IoT. Cette gestion granulaire des rôles et des autorisations est appelée [contrôle d'accès en fonction du rôle](./security-role-based-access-control.md).

Azure Digital Twins utilise également d'autres fonctionnalités de sécurité présentes dans Azure IoT, notamment Azure Active Directory (Azure AD). Pour cette raison, la configuration et la sécurisation des applications créées sur Azure Digital Twins impliquent l'utilisation de bon nombre des [bonnes pratiques de sécurité Azure IoT](../iot-fundamentals/iot-security-best-practices.md) actuellement recommandées.

Cet article récapitule les principales bonnes pratiques à suivre.

> [!IMPORTANT]
> Pour garantir une sécurité maximale dans votre espace IoT, consultez d’autres ressources de sécurité (y compris celles de vos fournisseurs d’appareils).

## <a name="iot-security-best-practices"></a>Meilleures pratiques relatives à la sécurité IoT

Voici certaines des pratiques les plus importantes pour sécuriser vos appareils IoT :

> [!div class="checklist"]
> * Sécurisation de tous les appareils qui sont connectés à votre espace IoT de manière à empêcher toute falsification.
> * Limitation du rôle de chaque appareil, capteur et utilisateur au sein de votre espace IoT. Si l’un d’entre eux est compromis, l’effet en sera atténué.
> * Utilisation potentielle du filtrage des adresses IP d’appareil et de la restriction des ports.
> * Limitation des E/S et de la bande passante des appareils pour améliorer les performances. La limitation du débit peut améliorer la sécurité en empêchant les attaques par déni de service.
> * Maintien à jour du microprogramme des appareils.

Voici certaines des pratiques les plus importantes pour sécuriser un espace IoT :

> [!div class="checklist"]
> * Chiffrer les données enregistrées, stockées ou persistantes.
> * Changer ou actualiser régulièrement les mots de passe ou les clés.
> * Limiter soigneusement l’accès et les autorisations en fonction du rôle. Consultez la section [Bonnes pratiques relatives au contrôle d'accès en fonction du rôle](#rbac) ci-dessous.
> * Utiliser un chiffrement puissant. Cela implique d’utiliser des mots de passe longs, des protocoles sécurisés et l’authentification à deux facteurs.

[Supervisez](./how-to-configure-monitoring.md) les ressources IoT pour identifier les valeurs hors norme, les menaces ou les paramètres de ressources indiquant un fonctionnement inhabituel. Utilisez Azure Analytics pour la gestion de la supervision.

> [!NOTE]
> Pour plus d’informations sur le traitement et le monitoring des événements, voir [Acheminer des événements et des messages avec Azure Digital Twins](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Bonnes pratiques relatives à Azure Active Directory

Azure Digital Twins utilise Azure Active Directory pour authentifier les utilisateurs et protéger les applications. Azure Active Directory assure la prise en charge de l'authentification pour différentes architectures modernes. Elles sont toutes basées sur des protocoles standard comme OAuth 2.0 ou OpenID Connect. Voici certaines bonnes pratiques clés permettant de sécuriser votre espace IoT avec Azure Active Directory :

> [!div class="checklist"]
> * Stocker les clés et les secrets d'application Azure Active Directory à un emplacement sécurisé, comme [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Utiliser un certificat émis par une [autorité de certification](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) approuvée pour s’authentifier, plutôt que des secrets d’application.
> * Limiter l’étendue d’accès OAuth 2.0 pour un jeton.
> * Vérifier la durée de validité d’un jeton, ainsi que de sa capacité à rester valide.
> * Définir des durées de validité appropriées pour les jetons.
> * Actualiser des jetons expirés.

<div id="rbac"></div>

## <a name="role-based-access-control-best-practices"></a>Bonnes pratiques relatives au contrôle d’accès en fonction du rôle

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les bonnes pratiques Azure IoT, consultez [Bonnes pratiques de sécurité IoT](../iot-fundamentals/iot-security-best-practices.md).

* Pour plus d’informations sur le contrôle d’accès en fonction du rôle, voir [Contrôle d’accès en fonction du rôle](./security-role-based-access-control.md).

* Pour plus d’informations sur l’authentification, voir [S’authentifier auprès des API](./security-authenticating-apis.md).
