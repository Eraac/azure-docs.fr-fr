---
title: Afficher et télécharger les tarifs Azure de votre organisation
description: Découvrez comment afficher et télécharger les tarifs ou une estimation des coûts avec les tarifs de votre organisation.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: a7f7da197a06dbbb730a7386882e534b8381cf9e
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491350"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Afficher et télécharger les tarifs Azure de votre organisation

Les clients Azure disposant d’un Contrat Entreprise Azure (EA) ou d’un [Contrat client Microsoft](#check-your-access-to-a-microsoft-customer-agreement) peuvent voir et télécharger leurs tarifs dans le Portail Azure.

## <a name="ea-pricing"></a>Tarification EA

Selon les stratégies définies pour votre organisation par l’administrateur d’entreprise, seuls certains rôles d’administrateur permettent d’accéder aux informations tarifaires Contrat Entreprise de votre organisation. Pour plus d’informations, consultez [Comprendre les rôles d’administrateur Contrat Entreprise Azure dans Azure](billing-understand-ea-roles.md).

1. En tant qu’administrateur d’entreprise, connectez-vous au [Portail Azure](https://portal.azure.com/).
1. Recherchez *Gestion des coûts + facturation*.

   ![Capture d’écran montrant la recherche dans le portail Azure](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. Sous le compte de facturation, sélectionnez **Utilisation + frais**.

   ![Capture d’écran montrant l’utilisation et les frais sous Facturation](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. Sélectionnez ![Capture d’écran montrant la recherche sur le portail Azure](./media/billing-ea-pricing/download-icon.png) **Télécharger** pour le mois correspondant.

1. Sous **Grille tarifaire**, sélectionnez **Télécharger le fichier CSV**.

   ![Capture d’écran montrant le bouton de téléchargement de fichier csv de la grille tarifaire](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="microsoft-customer-agreement-pricing"></a>Tarification du Contrat client Microsoft

Vous devez être gestionnaire de factures, propriétaire, contributeur ou lecteur de profil de facturation pour afficher et télécharger les tarifs. Pour en savoir plus sur les rôles de facturation pour les Contrats client Microsoft, consultez [Tâches et rôles du profil de facturation](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="download-price-sheets-for-the-current-billing-period"></a>Télécharger les grilles tarifaires pour la période de facturation actuelle

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez *Gestion des coûts + facturation*.
1. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être sélectionner au préalable un compte de facturation.
1. Dans la zone **Vue d’ensemble**, recherchez les liens de téléchargement sous les frais en cumul mensuel à ce jour.
1. Sélectionnez **Grille tarifaire Azure**.
![Capture d’écran montrant le téléchargement à partir du panneau Vue d’ensemble](./media/billing-ea-pricing/open-pricing.png)

### <a name="download-price-sheets-for-billed-charges"></a>Télécharger les grilles tarifaires pour les frais facturés

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez *Gestion des coûts + facturation*.
1. Sélectionnez un profil de facturation. Selon votre accès, vous devrez peut-être sélectionner au préalable un compte de facturation.
1. Sélectionnez **Factures**.
1. Dans la grille des factures, recherchez la ligne de la facture correspondant à la grille tarifaire que vous souhaitez télécharger.
1. Cliquez sur les points de suspension (`...`) à la fin de la ligne.
![Capture d’écran montrant les points de suspension sélectionnés](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. Si vous souhaitez voir les tarifs des services dans la facture sélectionnée, sélectionnez **Grille tarifaire de facturation**.
1. Si vous souhaitez voir les tarifs de tous les services Azure pour la période de facturation donnée, sélectionnez **Grille tarifaire Azure**.

![Capture d’écran montrant le menu contextuel avec les grilles tarifaires](./media/billing-ea-pricing/contextmenu-pricesheet.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Estimer les coûts avec la calculatrice de prix Azure

Vous pouvez également utiliser les tarifs de votre organisation pour estimer les coûts à l’aide de la calculatrice de prix Azure.

1. Accédez à la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator).
1. En haut à droite, sélectionnez **Connexion**.
1. Sous **Programmes et offre** > **Programme de licence**, sélectionnez **Contrat Entreprise**.
1. Sous **Programmes et offre** > **Contrat sélectionné**, sélectionnez **Aucune sélection**.

    ![Capture d’écran montrant le bouton de téléchargement de fichier csv de la grille tarifaire](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. Choisissez l’organisation.
1. Sélectionnez **Appliquer**.
1. Recherchez et ajoutez des produits à votre devis.
1. Les prix estimés affichés sont basés sur les tarifs de l’organisation que vous avez sélectionnés.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Vérifier votre accès à un Contrat client Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes client EA, consultez :

- [Gérer l’accès aux informations de facturation EA pour Azure](billing-manage-access.md)
- [Afficher et télécharger votre facture Microsoft Azure](billing-download-azure-invoice.md)
- [Afficher et télécharger votre utilisation et vos frais Microsoft Azure](billing-download-azure-daily-usage.md)
- [Comprendre votre facture pour les clients Contrat Entreprise](billing-understand-your-bill-ea.md)

Si vous avez un Contrat client Microsoft, consultez :

- [Comprendre les termes de votre grille tarifaire pour un Contrat client Microsoft](billing-mca-understand-pricesheet.md)
- [Afficher et télécharger votre facture Microsoft Azure](billing-download-azure-invoice.md)
- [Afficher et télécharger votre utilisation et vos frais Microsoft Azure](billing-download-azure-daily-usage.md)
- [Afficher et télécharger les documents fiscaux pour votre profil de facturation](billing-mca-download-tax-document.md)
- [Comprendre les frais indiqués sur la facture de votre profil de facturation](billing-mca-understand-your-bill.md)
