---
title: Nouveautés d’Azure Application Gateway
description: Découvrez les nouveautés d’Azure Application Gateway, notamment les dernières notes de publication, les problèmes connus, les corrections de bogues, les fonctionnalités dépréciées et les changements à venir.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 2a494b924107baeabbcf412af7e1bbdb1db0f753
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752008"
---
# <a name="whats-new-in-azure-application-gateway"></a>Nouveautés d’Azure Application Gateway

Azure Application Gateway est mis à jour en continu. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues
- Fonctionnalités dépréciées

## <a name="new-features"></a>Nouvelles fonctionnalités

|Fonctionnalité  |Description  |Date de l’ajout  |
|---------|---------|---------|
|Règles personnalisées WAF |Application Gateway WAF_v2 prend désormais en charge la création de règles personnalisées. Consultez [Règles personnalisées Application Gateway](custom-waf-rules-overview.md). |Juin 2019 |
|Mise à disposition générale des fonctions de mise à l’échelle automatique, de redondance de zone et de prise en charge des adresses IP virtuelles statiques |Mise à disposition générale de la SKU version 2, qui améliore les performances et prend en charge la mise à l’échelle automatique, la redondance de zone et la prise en charge des adresses IP virtuelles statiques, ainsi que Key Vault et la réécriture des en-tête. Consultez la [documentation relative à la mise à l’échelle automatique de Application Gateway](application-gateway-autoscaling-zone-redundant.md). |Avril 2019 |
|Intégration dans Key Vault |Application Gateway prend désormais en charge l’intégration dans Key Vault (dans la préversion publique) des certificats de serveur associés à des écouteurs activés via HTTPS. Consultez la section relative à [l’arrêt de SSL avec des certificats Key Vault](key-vault-certs.md). |Avril 2019 |
|Opérations de réécriture et CRUD sur les en-têtes     |Vous pouvez désormais réécrire les en-têtes HTTP. Consultez le [tutoriel : Créer une passerelle d’application et réécrire des en-têtes HTTP](tutorial-http-header-rewrite-powershell.md) pour plus d’informations.|Décembre 2018|
|Configuration de WAF et liste d’exclusions     |Nous avons ajouté des options pour vous aider à configurer votre WAF et à réduire les faux positifs. Consultez [Limites de la taille des demandes adressées au pare-feu d’application web et listes d’exclusions](application-gateway-waf-configuration.md) pour plus d’informations.|Décembre 2018|
|Mise à l’échelle automatique, redondance de zone et prise en charge des adresses IP virtuelles statiques      |La référence SKU v2 apporte de nombreuses améliorations, dont la mise à l’échelle automatique et l’accroissement des performances. Consultez [Nouveautés d’Azure Application Gateway](overview.md) pour plus d’informations.|Septembre 2018|
|Vidage des connexions     |Le vidage des connexions vous permet de supprimer correctement des membres de vos pools de back-ends. Pour plus d’informations, consultez [Vidage des connexions](overview.md#connection-draining).|Septembre 2018|
|Pages d’erreur personnalisées     |Avec les pages d’erreurs personnalisées, vous pouvez créer une page d’erreur dans le format du reste de vos sites web. Pour y parvenir, consultez [Créer des pages d’erreur personnalisées Application Gateway](custom-error.md).|Septembre 2018|
|Améliorations des métriques     |Vous pouvez obtenir une meilleure vue de l’état de votre passerelle Application Gateway avec des métriques améliorées. Pour activer les métriques sur votre passerelle Application Gateway, consultez [Intégrité du serveur principal, journaux de diagnostic et métriques pour la passerelle Application Gateway](application-gateway-diagnostics.md).|Juin 2018|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Application Gateway, consultez [Présentation d’Azure Application Gateway](overview.md).
