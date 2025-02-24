---
title: Capacité d’une instance du service Gestion des API Azure | Microsoft Docs
description: Cet article explique à quoi correspond la métrique de capacité et comment prendre des décisions éclairées en ce qui concerne la mise à l’échelle d’une instance du service Gestion des API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.openlocfilehash: fe77361c4c9bed9310f8443ed4ff37faf7ea53a9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60658308"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Capacité d’une instance du service Gestion des API Azure

La **capacité** est la [métrique Azure Monitor](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) la plus importante et la seule dont vous devez tenir compte pour prendre des décisions éclairées en ce qui concerne la mise à l’échelle d’une instance du service Gestion des API pour accepter une charge plus élevée. Sa construction est complexe et requiert l’adoption d’un certain comportement.

Cet article explique ce à quoi correspond la **capacité**, ainsi que son comportement. Il décrit comment accéder aux métriques de **capacité** dans le Portail Azure et vous informe quand le moment est venu de procéder à une mise à l’échelle ou à une mise à niveau de votre instance du service Gestion des API.

## <a name="prerequisites"></a>Prérequis

Pour suivre les étapes décrites dans cet article, vous devez avoir :

+ Un abonnement Azure actif.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Une instance APIM. Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Définition de la capacité

![Métrique de capacité](./media/api-management-capacity/capacity-ingredients.png)

La **capacité** est un indicateur de charge sur une instance APIM. Elle reflète l’utilisation des ressources (UC, mémoire) et les longueurs de files d’attente réseau. L’utilisation de la mémoire et de l’UC indique la consommation des ressources par :

+ les services APIM, tels que les actions de gestion ou le traitement des requêtes, qui peuvent inclure le transfert de requêtes ou l’exécution d’une stratégie
+ les processus de système d’exploitation sélectionnés, y compris les processus qui impliquent le coût des négociations SSL sur les nouvelles connexions.

La **capacité** totale est une moyenne de ses propres valeurs issues de chacune des unités d’une instance du service Gestion des API.

## <a name="capacity-metric-behavior"></a>Comportement de la métrique de capacité

En raison de sa structure, la **capacité** peut être influencée par de nombreuses variables dans la vie réelle. Par exemple :

+ des modèles de connexion (nouvelle connexion basée sur une requête/réutilisation de la connexion existante)
+ la taille d’une requête et d’une réponse
+ des stratégies configurées sur chaque API ou le nombre de clients envoyant des requêtes.

Plus les opérations sur les requêtes sont complexes, plus la consommation de la **capacité** sera élevée. Par exemple, les stratégies de transformation complexes utilisent davantage d’UC qu’un simple transfert de requête. Les réponses de service principal lentes augmentent également la consommation.

> [!IMPORTANT]
> La **capacité** n’est pas une mesure directe du nombre de requêtes en cours de traitement.

![Pics de la métrique de capacité](./media/api-management-capacity/capacity-spikes.png)

La **capacité** peut également augmenter par intermittence ou être supérieure à zéro même si aucune requête n’est en cours de traitement. Cela se produit en raison d’actions propres au système ou à la plateforme et ne doit pas être pris en compte lorsque vous envisagez de mettre à l’échelle une instance.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Utiliser le Portail Azure pour étudier la capacité
  
![Métrique de capacité](./media/api-management-capacity/capacity-metric.png)  

1. Dans le [portail Azure](https://portal.azure.com/), accédez à votre instance APIM.
2. Sélectionnez **Métriques (préversion)** .
3. Dans la section de couleur violette, sélectionnez la métrique **Capacité** parmi les métriques disponibles et conservez la valeur d’agrégation **Moy** par défaut.

    > [!TIP]
    > Afin d’éviter toute mauvaise interprétation, consultez toujours une répartition de métrique de **capacité** en fonction d’un emplacement.

4. Dans la section de couleur verte, sélectionnez **Emplacement** pour fractionner la métrique par dimension.
5. Choisissez une plage horaire dans la barre supérieure de la section.

    Vous pouvez définir une alerte Métrique vous permettant de savoir à quel moment un événement inattendu survient. Par exemple, recevez des notifications quand votre instance APIM dépasse sa capacité maximale attendue pendant plus de 20 minutes.

    >[!TIP]
    > Vous pouvez configurer des alertes pour déterminer quand votre service n’a plus assez de capacité ou utiliser la fonctionnalité de mise à l’échelle automatique d’Azure Monitor pour ajouter automatiquement une unité du service Gestion des API Azure. L’opération de mise à l’échelle peut prendre environ 30 minutes, vous devez donc prévoir vos règles en conséquence.  
    > Seule la mise à l’échelle de l’emplacement principal est autorisée.

## <a name="use-capacity-for-scaling-decisions"></a>Utiliser la capacité pour les décisions de mise à l’échelle

La **capacité** est la métrique dont vous devez tenir compte pour prendre des décisions en ce qui concerne la mise à l’échelle d’une instance du service Gestion des API pour accepter une charge plus élevée. Vous devez :

+ examiner les tendances à long terme et la moyenne
+ ignorer les pics soudains qui sont la plupart du temps liés à une augmentation de charge (consultez la section « Comportement de la métrique de capacité » pour plus d’informations)
+ mettre à niveau ou mettre à l’échelle votre instance quand la **capacité** a une valeur supérieure à 60 ou 70 % pendant une période prolongée (par exemple 30 minutes). Des valeurs différentes peuvent être plus adaptées à votre service ou scénario.

>[!TIP]  
> Si vous êtes en mesure d’estimer votre trafic au préalable, testez votre instance APIM sur les charges de travail attendues. Vous pouvez augmenter progressivement la charge de la requête sur votre locataire, afin de déterminer la valeur de la métrique de capacité qui correspond à votre charge de pointe. Suivez les étapes de la section précédente pour utiliser le Portail Azure afin de déterminer la capacité consommée à un moment donné.

## <a name="next-steps"></a>Étapes suivantes

[Mettre à niveau une instance du service Gestion des API et la mettre à l’échelle](upgrade-and-scale.md)