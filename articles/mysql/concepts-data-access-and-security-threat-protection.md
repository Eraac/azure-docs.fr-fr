---
title: Protection avancée contre les menaces – Azure Database pour MySQL | Microsoft Docs
description: Azure ATP détecte les activités de base de données anormales qui indiquent la présence potentielle de menaces de sécurité pour la base de données.
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 10fa2a409437c8cc48bcd1a674cc3832f086dcf2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60525979"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Azure ATP pour Azure Database pour MySQL

La protection avancée contre les menaces d’Azure Database pour MySQL détecte des activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de vos bases de données.

> [!NOTE]
> Advanced Threat Protection est disponible en préversion publique.

La protection avancée contre les menaces fait partie de l’offre Advanced Data Security qui est un package unifié de fonctionnalités de sécurité avancées. Vous pouvez accéder à Advanced Threat Protection et le gérer via le [portail Azure](https://portal.azure.com) ou l’[API REST](/rest/api/mysql/serversecurityalertpolicies). Cette fonctionnalité est disponible pour les serveurs Usage général et Mémoire optimisée.

> [!NOTE]
> La fonctionnalité Advanced Threat Protection **n’est pas** disponible dans les régions administratives et souveraines suivantes du cloud Azure : US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginie, US DoD Est, US DoD Central, Allemagne Centre, Allemagne Nord, Chine Est, Chine Est 2. Consultez les [produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/) pour connaître la disponibilité générale des produits.


## <a name="what-is-advanced-threat-protection"></a>Présentation d’Azure ATP

Azure ATP pour Azure Database pour MySQL fournit une nouvelle couche de sécurité qui permet aux clients de détecter les menaces potentielles et d’y répondre à mesure qu’elles se présentent en générant des alertes de sécurité sur les activités anormales de bases de données. Les utilisateurs reçoivent une alerte en cas d’activités de base de données suspectes, de vulnérabilités potentielles et de modèles d’accès et de requêtes anormaux à la base de données. Azure ATP pour Azure Database pour MySQL intègre des alertes à [Azure Security Center](https://azure.microsoft.com/services/security-center/), avec des détails sur les activités suspectes et les actions recommandées à entreprendre pour analyser et prévenir la menace. Azure ATP pour Azure Database pour MySQL vous permet de réagir facilement aux menaces potentielles visant la base de données sans devenir un expert en sécurité ou gérer des systèmes avancés de surveillance de la sécurité. 

![Concept Azure ATP](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Alertes Azure ATP 
Azure ATP pour Azure Database pour MySQL détecte les activités anormales indiquant des tentatives inhabituelles ou potentiellement dangereuses visant à accéder aux bases de données ou à les exploiter et peut déclencher les alertes suivantes :
- **Accès à partir d’un emplacement inhabituel** : cette alerte est déclenchée en cas de modification du modèle d’accès à un serveur Azure Database pour MySQL, quand un utilisateur s’est connecté au serveur Azure Database pour MySQL à partir d’un emplacement géographique inhabituel. Dans certains cas, l’alerte détecte une action légitime (une nouvelle application ou opération de maintenance du développeur). Dans d’autres cas, l’alerte détecte une action malveillante (ancien employé, attaquant externe).
- **Accès à partir d’un centre de données Azure inhabituel** : cette alerte est déclenchée en cas de modification du modèle d’accès au serveur Azure Database pour MySQL, quand un utilisateur s’est connecté au serveur à partir d’un centre de données Azure inhabituel observé sur ce serveur récemment. Dans certains cas, l’alerte détecte une action légitime (votre nouvelle application dans Azure, Power BI, l’éditeur de requête Azure Database pour MySQL). Dans d’autres cas, l’alerte détecte une action malveillante provenant d’une ressource/d’un service Azure (ancien employé, attaquant externe).
- **Accès à partir d’une entité de sécurité inconnue** : cette alerte est déclenchée en cas de modification du modèle d’accès au serveur Azure Database pour MySQL, quand un utilisateur s’est connecté au serveur avec un principal inhabituel (utilisateur Azure Database pour MySQL). Dans certains cas, l’alerte détecte une action légitime (nouvelle application, opération de maintenance du développeur). Dans d’autres cas, l’alerte détecte une action malveillante (ancien employé, attaquant externe).
- **Accès à partir d’une application potentiellement dangereuse** : cette alerte est déclenchée quand une application potentiellement dangereuse est utilisée pour accéder à la base de données. Dans certains cas, l’alerte détecte le test d’intrusion en action. Dans d’autres cas, l’alerte détecte une attaque à l’aide d’outils d’attaque courants.
- **Attaque par force brute pour obtenir les informations d’identification d’Azure Database pour MySQL** : cette alerte est déclenchée quand il existe un nombre anormalement élevé d’échecs de connexion avec des informations d’identification différentes. Dans certains cas, l’alerte détecte le test d’intrusion en action. Dans d’autres cas, l’alerte détecte une attaque par force brute.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Pour plus d’informations sur les prix, consultez la [page Tarification d’Azure Database pour MySQL](https://azure.microsoft.com/pricing/details/mysql/). 
* Configurer [Azure ATP pour Azure Database pour MySQL](howto-database-threat-protection-portal.md) avec le portail Azure  
