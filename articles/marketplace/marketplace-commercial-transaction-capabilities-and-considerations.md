---
title: Considérations et fonctionnalités relatives aux transactions commerciales ‎dans la Place de marché | Azure
description: Cet article expose diverses considérations ayant trait à la tarification, la facturation et le paiement pour le type d’offre Transaction.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: yijenj
manager: nuno costa
ms.service: marketplace
ms.topic: article
ms.date: 10/29/2018
ms.author: pabutler
ms.openlocfilehash: c58a36988e3aee9b122446e3ee3c4878a582b8ad
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871033"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>Considérations et fonctionnalités relatives aux transactions dans la Place de marché commerciale

Cet article couvre les rubriques suivantes sur les transactions dans la Place de marché commerciales

* Options de publication de la Place de marché
* Présentation générale des offres Transaction
* Modèles de facturation des offres Transaction
* Exigences relatives aux offres Transaction

## <a name="marketplace-publishing-options"></a>Options de publication de la Place de marché

Les éditeurs disposent des options de publication suivantes dans la Place de marché commerciale.

### <a name="list--trial-publishing-options"></a>Options de publication Liste et Essai

Les éditeurs peuvent utiliser les options de publication Liste, Essai et BYOL pour la promotion et l’achat par l’utilisateur. Avec ces options, Microsoft ne participe pas directement aux transactions de licence logicielle des éditeurs et ne facture rien sur ces transactions. Les éditeurs ont la responsabilité et la charge de tous les aspects des transactions de licence logicielle, y compris mais sans s’y limiter, la commande, le traitement, le contrôle, la tarification, la facturation, le paiement et la collecte. Avec les options de publication Liste et Essai, les éditeurs conservent 100 % du montant collecté auprès du client en paiement des frais de licence logicielle. 

### <a name="transact-publishing-option"></a>Option de publication Transaction

En plus des options de publication Liste et Essai, les éditeurs disposent de l’option de publication Transaction. Elle tire parti des fonctionnalités de transactions commerciales de Microsoft qui sont disponibles dans le monde entier, et autorise Microsoft à héberger les transactions de la Place de marché dans le cloud pour le compte de l’éditeur.

## <a name="transact-general-overview"></a>Présentation générale des offres Transaction

Lorsque vous choisissez l’option de publication Transaction, Microsoft permet la vente de logiciels tiers et le déploiement de certains types d'offres dans l’abonnement Azure du client. Pour choisir le modèle de facturation et le type d’offre, l’éditeur doit prendre en compte la facturation des frais d’infrastructure et de ses propres frais de licence logicielle.

L’option de publication Transaction est actuellement disponible pour ces types d’offres : Machines virtuelles, Applications Azure et Applications SaaS.


![[Transactions commerciales dans la Place de marché Azure]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Facturation des coûts d’infrastructure

**Pour les offres Machines virtuelles et Applications Azure**

Pour les offres Machines virtuelles et Applications Azure, les frais d’utilisation de l’infrastructure Azure sont facturés sur l’abonnement Azure du client.  Les frais d’utilisation de l’infrastructure sont détaillés et présentés séparément des frais de licence logicielle du fournisseur sur la facture du client.

**Pour les offres Applications SaaS**

Pour les offres Applications SaaS, l’éditeur doit regrouper les frais d’utilisation de l’infrastructure Azure et les frais de licence logicielle dans le même élément de coût.  Ils font facturés au client selon un tarif fixe. L’utilisation de l’infrastructure Azure est présentée et facturée directement au partenaire.  Les frais réels d’utilisation de l’infrastructure ne sont pas visibles par le client.  Les éditeurs choisissent généralement d’inclure les frais d’utilisation de l’infrastructure Azure dans leurs tarifs de licence logicielle.  Les frais de licence logicielle ne sont pas comptabilisés ni basés sur la consommation.

## <a name="transact-billing-models"></a>Modèles de facturation des offres Transaction

Selon l’option Transaction choisie, les frais de licence logicielle de l’éditeur peuvent se présenter comme suit :  

* Gratuit : aucuns frais ne sont facturés pour les licences logicielles. 

* BYOL (apportez votre propre licence) : les frais facturables pour les licences logicielles sont gérés directement entre l’éditeur et le client. Microsoft transmet uniquement les frais d’utilisation de l’infrastructure Azure. (Pour les offres Machines virtuelles et Applications Azure uniquement.)

* Paiement à l’utilisation : les frais de licence logicielle sont facturés à un taux tarifaire par heure et par cœur (processeur virtuel) basé sur l’utilisation de l’infrastructure Azure. Cela s’applique aux offres Machines virtuelles et Applications Azure uniquement.

* • Tarification des abonnements : les frais de licence logicielle sont facturés mensuellement ou annuellement selon un tarif fixe ou par poste. Cela s’applique uniquement aux offres Applications SaaS et Applications Azure – Applications managées.

* Essai logiciel gratuit : aucuns frais ne sont facturés pour les licences logicielles pendant une période de 30 ou 90 jours.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Options tarifaires Gratuit et BYOL (apportez votre propre licence)

Quand vous publiez une offre Transaction avec l’option Gratuit ou BYOL (apportez votre propre licence), Microsoft n’intervient pas pour faciliter la transaction des ventes pour vos frais de licence logicielle. Comme avec les options de publication Liste et Essai, l’éditeur conserve 100 % du montant des frais de licence logicielle. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Options de tarification Paiement à l'utilisation et Abonnement (en fonction du site)

Lorsque vous publiez une offre Transaction avec l’option de tarification Paiement à l’utilisation ou Abonnement, Microsoft fournit la technologie et les services nécessaires pour traiter les achats, retours et rétrofacturations des licences logicielles. Dans ce scénario, l’éditeur autorise Microsoft à agir en tant qu’agent. L’éditeur autorise Microsoft à faciliter la transaction de licence logicielle, tout en conservant sa qualité de vendeur, fournisseur, distributeur et concédant.

Microsoft permet aux clients de commander, acquérir une licence et utiliser le logiciel de l’éditeur selon les conditions générales de la Place de marché commerciale de Microsoft et du contrat de licence utilisateur final de l’éditeur. Les éditeurs sont tenus de fournir leur contrat de licence utilisateur final ou de sélectionnez le [contrat Standard](https://docs.microsoft.com/azure/marketplace/standard-contract) lors de la création de l'offre.


### <a name="free-software-trials"></a>Essai logiciel gratuit

Dans les scénarios de publication d’offres Transaction, l’éditeur peut proposer gratuitement une licence logicielle pendant une période de 30 ou 90 jours. Cette remise n’inclut pas le coût de l’utilisation de l’infrastructure Azure occasionnée par l’utilisation de la solution du partenaire.

### <a name="private-offers"></a>Offres privées

Outre la sélection de types d’offres et de modèles de facturation pour monétiser une offre, les éditeurs peuvent proposer une offre privée, l’associer à des tarifs par transaction négociés ou personnaliser les configurations. Les offres privées sont disponibles pour les trois options de publication Transaction.

Cette option tarifaire peut être supérieure ou inférieure à l'offre disponible publiquement. Les offres privées permettent d’appliquer une remise ou d’ajouter un supplément à une offre. Elles peuvent être proposées à un ou plusieurs clients en autorisant leur abonnement Azure au niveau de l’offre.


### <a name="examples"></a>Exemples

**Pay-As-You-Go** 

* Si vous activez l’option Paiement à l’utilisation, vous obtenez la structure de coûts suivante.

|Coût de votre licence  | 1 $/heure  |
|---------|---------|
|Coût d’utilisation Azure (D1/1 cœur)    |   0,14 $/heure     |
|*Microsoft facture au client le montant suivant :*    |  *1,14 $/heure*       |

* Dans ce scénario, Microsoft facture 1,14 $ l’heure d’utilisation de votre image de machine virtuelle publiée.

|Microsoft facture  | 1,14 $/heure  |
|---------|---------|
|Microsoft vous verse 80 % des revenus générés par les licences|   0,80 $/heure     |
|Microsoft conserve 20 % des revenus générés par les licences  |  0,20 $/heure       |
|Microsoft conserve 100 % des revenus générés par l’utilisation d’Azure | 0,14 $/heure |

**BYOL (apportez votre propre licence)**

* Si vous activez l’option BYOL, vous obtenez la structure de coûts suivante.

|Coût de votre licence  | Frais de licence négociés et facturés par vos soins  |
|---------|---------|
|Coût d’utilisation Azure (D1/1 cœur)    |   0,14 $/heure     |
|*Microsoft facture au client le montant suivant :*    |  *0,14 $/heure*       |

* Dans ce scénario, Microsoft facture 0,14 $ l’heure d’utilisation de votre image de machine virtuelle publiée.

|Microsoft facture  | 0,14 $/heure  |
|---------|---------|
|Microsoft conserve les revenus générés par l’utilisation Azure    |   0,14 $/heure     |
|Microsoft conserve 0 % des revenus générés par les licences   |  0 $/heure       |

**Abonnement Application SaaS**

Cette option doit être configurée pour la vente via Microsoft et peut être facturée mensuellement ou annuellement à un tarif fixe ou par utilisateur.
•   Si vous activez l’option Vendre via Microsoft pour une offre SaaS, vous obtenez la structure de coûts suivante.

|Coût de votre licence       | 100 $/mois  |
|--------------|---------|
|Coût d’utilisation Azure (D1/1 cœur)    | Facturé directement à l’éditeur au lieu du client |
|*Microsoft facture au client le montant suivant :*    |  *100 $/mois (remarque : l’éditeur doit inclure les coûts d’infrastructure encourus ou transmis dans les frais de licence)*  |

* Dans ce scénario, Microsoft facture 100 $ pour votre licence logicielle et verse 80 $ à l’éditeur.
* Les partenaires ayant été qualifiés pour les Frais liés au service Place de marché réduits verront des frais de transaction réduits sur les offres SaaS de mai 2019 à juin 2020. Dans ce scénario, Microsoft facture 100 $ pour votre licence logicielle et verse 90 $ à l’éditeur.

|Microsoft facture  | 100 $/mois  |
|---------|---------|
|Microsoft vous verse 80 % des revenus générés par les licences <br> \* Microsoft paie 90 % de vos coûts de licence pour les applications SaaS qualifiées   |   80 $/mois <br> \* 90 $/mois    |
|Microsoft conserve 20 % des revenus générés par les licences <br> \* Microsoft conserve 10 % de vos coûts de licence pour les applications SaaS qualifiées  |  20 $/mois <br> \* 10 $     |

* **Frais liés au service Place de marché réduits** : Pour certains produits SaaS que vous publiez sur notre Place de marché commerciale, Microsoft réduit ses frais liés au service Place de marché de 20 % (comme décrit dans le Contrat d'éditeur Microsoft) à 10 %.  Afin que votre produit soit qualifié, au moins un de vos produits doit avoir été désigné par Microsoft comme Prêt à la co-vente IP ou Priorité à la co-vente IP. Pour recevoir ces frais liés au service Place de marché réduits pour le mois, l’éligibilité doit être respectée au moins cinq (5) jours ouvrés avant la fin de ce mois. Les frais liés au service Place de marché réduits ne s’appliquent pas aux machines virtuelles, aux applications managées ou à tout autre produit, disponible via notre Place de marché commerciale.  Les frais liés au service Place de marché réduits sont disponibles pour les offres qualifiées, avec frais de licence collectés par Microsoft entre le 1er mai 2019 et le 30 juin 2020.  Au-delà de cette date, les frais liés au service Place de marché réduits retrouveront leur montant initial.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Tarification, paiement, facturation et collecte côté client

**Facturation et paiement**

L’éditeur peut choisir la méthode de facturation par défaut du client pour présenter les frais de licence logicielle avec abonnement ou paiement à l’utilisation.

**Contrat Entreprise** 

Si la méthode de facturation par défaut du client est un Contrat Entreprise Microsoft, vos frais de licence logicielle seront facturés selon cette méthode et présentés de manière détaillée et séparée des coûts propres à l’utilisation d’Azure.

**Cartes de crédit et facture mensuelle** 

Les clients peuvent également payer à l’aide d’une carte de crédit et d’une facture mensuelle. Dans ce cas, vos frais de licence logicielle sont facturés comme dans le scénario du Contrat Entreprise, c’est-à-dire de manière détaillée et séparée des coûts de l’utilisation d’Azure.

Exemple d’un client qui fait un achat avec une carte de crédit :

|Description    |    Date  |
|----------|----------|
|Période de commande   | 15 août 2018 au 30 août 2018 |
|Échéance (mois)   | 30 août 2018 |
|Date de facturation | 1er septembre 2018 |
|Date de paiement du client | 1er septembre 2018 |
|Période de dépôt (cartes de crédit uniquement, 30 jours) | 1er septembre 2018 au 30 septembre 2018 |
|Début de la période de collecte | 1er septembre 2018 |
|Fin de la période de collecte (30 jours au maximum) | 30 septembre 2018 |
|Date de calcul du paiement (tous les mois le 15) | 1er octobre 2018 |
|Date de paiement | 15 octobre 2018 |

Exemple d’un client qui fait un achat avec un Contrat Entreprise :

| Description |    Date  |
|----------|----------|
|Période de commande | 15 août 2018 au 30 août 2018 |
|Échéance (trimestre) | 30 septembre 2018 |
|Date de facturation | 15 octobre 2018 |
|Période de dépôt (cartes de crédit uniquement, 30 jours) | n/a |
|Début de la période de collecte | 15 octobre 2018 |
|Fin de la période de collecte (90 jours au maximum) | 15 janvier 2019 |
|Date de paiement du client | 30 décembre 2018 |
|Date de calcul du paiement (tous les mois le 15) | 15 janvier 2019 |
|Date de paiement | 15 février 2019 |

**Crédits gratuits et engagement financier** 

Certains clients choisissent de prépayer l’utilisation d’Azure avec un engagement monétaire dans le Contrat Entreprise, ou ont obtenu des crédits gratuits à utiliser avec Azure. Ces crédits peuvent servir à payer l’utilisation d’Azure, mais pas les frais de licence logicielle de l’éditeur.

**Facturation et collecte** 

Les licences logicielles des éditeurs sont facturées avec la méthode de facturation choisie par le client et selon le calendrier de facturation établi. Les clients sans Contrat Entreprise sont facturés au mois pour les licences logicielles de la Place de marché. Les clients ayant un Contrat Entreprise sont facturés au mois, mais reçoivent une seule facture par trimestre.

Quand les modèles tarifaires Abonnement ou Paiement à l'utilisation sont sélectionnés, Microsoft agit en tant qu’agent de l’éditeur et est responsable de tous les aspects de la facturation, du paiement et de la collecte.

### <a name="publisher-payout-and-reporting"></a>Paiement de l’éditeur et rapports

* Les frais de licence logicielle collectés par Microsoft en tant qu’agent sont soumis à des frais de transaction de 20 %, sauf indication contraire, et sont déduits du moment payé à l’éditeur.

* Les clients achètent généralement avec un Contrat Entreprise, ou par carte de crédit dans le cadre d’un contrat de paiement à l’utilisation. Le type de contrat détermine la tarification, la facturation, la collecte et le calendrier de paiement.

>[!NOTE] 
>Tous les rapports et insights associés à l’option de publication Transaction sont disponibles dans la section Insights du portail Cloud Partner ou dans la section Analytics de l'Espace partenaires.

#### <a name="billing-questions-and-support"></a>Questions et support sur la facturation

Pour obtenir plus d’informations et connaître les règles juridiques, consultez le [Contrat d’éditeur](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (disponible dans le portail Cloud Partner).

Pour obtenir de l'aide sur les questions de facturation, contactez le [support de l'éditeur de la place de marché commerciale](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Exigences relatives aux offres Transaction

Cette section expose les exigences relatives aux différents types d’offres Transaction.

### <a name="requirements-for-all-offer-types"></a>Exigences applicables à tous les types d’offres

- Un compte Microsoft et des informations financières sont requis pour l’offre de publication Transaction, quel que soit le modèle de tarification de l’offre.
- Les informations financières obligatoires comprennent un compte de paiement et un profil fiscal.

Pour plus d’informations sur la configuration de ces comptes, consultez [Gérer votre compte Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account#financial-details).


### <a name="requirements-for-specific-offer-types"></a>Exigences applicables à certains types d’offres

L’option de publication Transaction est uniquement disponible pour les types d’offres de la Place de marché suivants : 

**Machine virtuelle** 

Choisissez entre le modèle tarifaire Gratuit, BYOL (apportez votre propre licence) et Paiement à l’utilisation, et présentez les frais comme des références SKU définies au niveau de l’offre. Sur la facture Azure du client, Microsoft présente les frais de licence logicielle de l’éditeur séparément des frais attribuables à l’utilisation de l’infrastructure Azure. Les frais d’utilisation de l’infrastructure Azure sont occasionnés par l’utilisation du logiciel de l’éditeur.

**Applications Azure : modèle de solution ou application gérée** 

Vous devez provisionner une ou plusieurs machines virtuelles et transmettre le total à payer pour chaque machine virtuelle. Pour les applications managées dans un plan unique, il est possible de sélectionner un abonnement mensuel fixe comme modèle tarifaire à la place de la tarification par machine virtuelle. Dans certains cas, les frais d’utilisation de l’infrastructure Azure sont dissociés des frais de licence logicielle, mais ils sont présentés au client sur la même facture. Toutefois, si vous configurez une offre d'application managée pour les frais d'infrastructure ISV, les ressources Azure sont facturées à l'éditeur et le client se voit présenter un tarif fixe incluant le coût de l'infrastructure, des licences logicielles et des services de gestion.

## <a name="next-steps"></a>Étapes suivantes

* Passez en revue les conditions d’éligibilité dans la section relative aux options de publication par type d’offre pour finaliser la sélection et la configuration de votre offre.
* Passez en revue les modèles de publication par vitrine pour obtenir des exemples sur la manière dont votre solution correspond à un type d’offre et à une configuration.
