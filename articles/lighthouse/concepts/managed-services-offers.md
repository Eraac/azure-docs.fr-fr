---
title: Offres de services managés dans la Place de marché Azure
description: Les offres de services managés permettent aux fournisseurs de services de vendre des offres de gestion des ressources à des clients sur la Place de marché Azure.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: a6fcf5f1d0ac194d60f834fb8d26db019c538410
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810873"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Offres de services managés dans la Place de marché Azure

Cet article décrit le nouveau type d’offre **Services managés** sur la [Place de marché Azure](https://azuremarketplace.microsoft.com). Les offres de services managés vous permettent d’offrir des services de gestion des ressources à des clients avec la gestion des ressources déléguées Azure. Vous pouvez mettre ces offres à la disposition de tous les clients potentiels ou uniquement d’un ou plusieurs clients spécifiques. Dans la mesure où vous facturez directement les clients pour les coûts liés à ces services managés, Microsoft ne facture aucuns frais.

## <a name="understand-managed-services-offers"></a>Comprendre les offres de services managés

Les offres de services managés simplifient le processus d’intégration des clients pour la gestion des ressources déléguées Azure. Une fois qu’un client achète une offre sur la Place de marché Azure, il peut spécifier les abonnements ou groupes de ressources à intégrer afin que des membres spécifiés de votre organisation puissent accomplir des tâches d’administration pour le client à partir du locataire de votre organisation.

Ensuite, aucune action supplémentaire du client ou du fournisseur de services n’est requise pour l’intégration du client. En effet, lorsque vous définissez l’offre sur le [portail Cloud Partner](https://cloudpartner.azure.com/), vous créez un manifeste spécifiant les utilisateurs, groupes et principaux de service Azure AD qui auront accès aux ressources du client à l’aide de la gestion des ressources déléguées Azure, ainsi que les rôles déterminant leur niveau d’accès. En affectant des autorisations à un groupe Azure AD plutôt qu’à une série de comptes d’utilisateur ou d’application individuels, vous pouvez ajouter ou supprimer des utilisateurs lorsque vos besoins d’accès changent.

## <a name="public-and-private-offers"></a>Offres publiques et privées

Chaque offre de services managés comprend un ou plusieurs plans. Ces plans peuvent être privés ou publics.

Si vous souhaitez limiter votre offre à des clients spécifiques, vous pouvez publier un plan privé. Dans ce cas, le plan ne peut être acheté que pour les ID d’abonnement que vous spécifiez. Pour plus d’informations, voir [Offres privées](https://docs.microsoft.com/azure/marketplace/private-offers).

Les plans publics vous permettent de promouvoir vos services auprès de nouveaux clients. Ils sont généralement plus appropriés lorsque vous n’avez besoin que d’un accès limité au locataire du client. Une fois que vous avez établi une relation avec un client, si celui-ci décide d’accorder un accès supplémentaire à votre organisation, vous pouvez soit publier un nouveau plan privé exclusivement pour ce client, soit [intégrer celui-ci pour un accès supplémentaire à l’aide de modèles Resource Manager](../how-to/onboard-customer.md).

Gardez à l’esprit qu’une fois que vous avez publié un plan public, vous ne pouvez plus le changer en plan privé. De plus, vous ne pouvez pas limiter la disponibilité d’un plan public à des clients spécifiques ou à un certain nombre de clients. En revanche, vous pouvez arrêter complètement la vente du plan si vous le souhaitez.

Le cas échéant, vous pouvez inclure des plans publics et privés dans la même offre.

## <a name="publish-managed-service-offers"></a>Publier des offres de services managés

Pour savoir comment publier une offre de services managés, voir [Publier une offre de services managés sur la Place de marché Azure](../how-to/publish-managed-services-offers.md). Pour obtenir des informations générales concernant la publication sur la Place de marché Azure à l’aide du portail Cloud Partner, voir [Guide de publication de la Place de marché Azure et AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) et [Gérer les offres sur les places de marché Azure et AppSource](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez la [Gestion des ressources déléguées Azure](azure-delegated-resource-management.md) et les [Expériences de gestion inter-locataire](cross-tenant-management-experience.md).
- [Publier des offres de services managés sur la Place de marché Azure](../how-to/publish-managed-services-offers.md).
