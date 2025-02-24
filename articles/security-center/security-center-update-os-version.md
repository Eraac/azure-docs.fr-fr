---
title: Mettre à jour la version du système d’exploitation dans Azure Security Center | Microsoft Docs
description: Cet article vous montre comment implémenter la suggestion d’Azure Security Center **Mettre à jour la version du système d’exploitation**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: a17bc3cf8c8f1ffbb2a91e17944bfc1c203f439d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60905838"
---
# <a name="update-os-version-in-azure-security-center"></a>Mettre à jour la version du système d’exploitation dans Azure Security Center
Pour les machines virtuelles des services cloud, Azure Security Center recommande que le système d’exploitation soit mis à jour si une version plus récente est disponible.  Seuls les rôles de travail et web des services cloud en cours d’exécution dans des emplacements de production sont surveillés.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Il ne s’agit pas d’un guide pas à pas.
> 
> 

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
1. Dans le panneau **Recommandations**, sélectionnez **Mettre à jour la version du système d’exploitation**.
   ![Mettre à jour la version du système d’exploitation][1]
2. Le panneau **Mettre à jour la version du système d’exploitation**s’ouvre. Suivez les étapes de ce panneau pour mettre à jour la version du système d’exploitation.

## <a name="see-also"></a>Voir aussi
Cet article vous a montré comment implémenter la recommandation de Security Center « Mettre à jour la version du système d’exploitation ». Pour plus d’informations sur les services cloud et la mise à jour de la version du système d’exploitation d’un service cloud, consultez :

* [Vue d’ensemble des services cloud](../cloud-services/cloud-services-choose-me.md)
* [Mettre à jour un service cloud](../cloud-services/cloud-services-update-azure-service.md)
* [Configuration des services cloud](../cloud-services/cloud-services-how-to-configure-portal.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
* [FAQ sur Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](https://blogs.msdn.com/b/azuresecurity/) : découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
