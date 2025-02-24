---
title: Comprendre la remise Azure Reserved VM Instances | Microsoft Docs
description: Découvrez comment la remise d’instance de machine virtuelle réservée Azure est appliquée aux machines virtuelles en cours d’exécution.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: b112dd881d4b2e87e617111d00bc82c6151d7750
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370070"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Comment la remise de réservation Azure est-elle appliquée aux machines virtuelles ?

Quand vous achetez une instance de machine virtuelle réservée Azure, la remise de réservation est automatiquement appliquée aux machines virtuelles qui correspondent aux attributs et à la quantité de la réservation. Une réservation couvre les coûts de calcul de vos machines virtuelles.

Une remise de réservation s’applique aux machines virtuelles de base que vous achetez sur la Place de marché Azure.

Pour la capacité réservée SQL Database, consultez [Comprendre la remise des instances réservées Azure](billing-understand-reservation-charges.md).

Le tableau suivant montre les coûts de votre machine virtuelle après l’achat d’une instance de machine virtuelle réservée. Dans tous les cas, les frais de stockage et de mise en réseau vous sont facturés aux tarifs normaux.

| Type de machine virtuelle  | Frais avec instance de machine virtuelle réservée |
|-----------------------|--------------------------------------------|
|Machines virtuelles Linux sans logiciel supplémentaire | La réservation couvre les coûts d’infrastructure des machines virtuelles.|
|Machines virtuelles Linux avec frais de logiciel (par exemple, Red Hat) | La réservation couvre les coûts d’infrastructure. Les logiciels supplémentaires vous sont facturés.|
|Machines virtuelles Windows sans logiciel supplémentaire |La réservation couvre les coûts d’infrastructure. Les logiciels Windows vous sont facturés.|
|Machines virtuelles Windows avec logiciels supplémentaires (par exemple, SQL server) | La réservation couvre les coûts d’infrastructure. Les logiciels Windows et les autres logiciels vous sont facturés.|
|Machines virtuelles Windows avec [Azure Hybrid Benefit](../virtual-machines/windows/hybrid-use-benefit-licensing.md) | La réservation couvre les coûts d’infrastructure. Les coûts des logiciels Windows sont couverts par Azure Hybrid Benefit. Tout logiciel supplémentaire est facturé séparément.|

## <a name="how-reservation-discount-is-applied"></a>Comment la remise de réservation est-elle appliquée ?

Une remise de réservation repose sur le principe de « *capacité utilisée ou perdue* ». Ainsi, si vous ne disposez pas des ressources correspondantes pour une heure donnée, vous perdez une quantité de réservation pour cette heure. Vous ne pouvez pas reporter les heures réservées inutilisées.

Lorsque vous arrêtez une ressource, la remise de réservation s'applique automatiquement à une autre ressource correspondante dans l'étendue spécifiée. Si aucune ressource correspondante n'est trouvée dans l'étendue spécifiée, les heures réservées sont *perdues*.

## <a name="reservation-discount-for-non-windows-vms"></a>Remise de réservation pour les machines virtuelles non Windows

 La remise de réservation Azure est appliquée aux instances de machine virtuelle en cours d’exécution sur une base horaire. Les réservations que vous avez achetées sont mises en correspondance avec l’utilisation émise par les machines virtuelles en cours d’exécution pour appliquer la remise de réservation. Pour les machines virtuelles qui ne peuvent pas s’exécuter pendant une heure entière, la réservation est remplie à partir d’autres machines virtuelles n’utilisant pas de réservation, incluant des machines virtuelles s’exécutant simultanément. À la fin de l’heure, l’application de réservation pour les machines virtuelles dans l’heure est verrouillée. Quand une machine virtuelle ne s’exécute pas pendant une heure ou quand des machines virtuelles s’exécutant simultanément dans l’heure ne remplissent pas l’heure de réservation, la réservation est sous-utilisée pour cette heure. Le graphique suivant illustre l’application d’une réservation à l’utilisation de machines virtuelles facturables. La représentation est basée sur l’achat d’une réservation et sur deux instances de machine virtuelle correspondantes.

![Capture d’écran montrant une réservation appliquée et deux instances de machine virtuelle correspondantes](./media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1. Toute utilisation supérieure à la ligne de réservation est facturée au tarif standard de paiement à l’utilisation. Toute utilisation sous la ligne de réservation n’est pas facturée, car elle a déjà été payée dans le cadre de l’achat de la réservation.
2. Dans l’heure 1, l’instance 1 s’exécute pendant 0,75 heure, et l’instance 2 pendant 0,5 heure. L’utilisation totale de l’heure 1 est 1,25 heure. Vous payez 0,25 heure restante au tarif du paiement à l’utilisation.
3. Pour les heures 2 et 3, les deux instances se sont exécutées chacune pendant 1 heure. Une instance est couverte par la réservation, et l’autre est facturée au tarif du paiement à l’utilisation.
4. Pour l’heure 4, l’instance 1 s’exécute pendant 0,5 heure tandis que l’instance 2 s’exécute pendant 1 heure. L’instance 1 est entièrement couverte par la réservation, et la durée de 0,5 heure de l’instance 2 est couverte. Vous êtes facturé au tarif du paiement à l’utilisation pour cette durée de 0,5 heure restante.

Pour comprendre et voir l’application de vos réservations Azure dans les rapports d’utilisation pour la facturation, voir [Comprendre l’utilisation des réservations](billing-understand-reserved-instance-usage-ea.md).

## <a name="reservation-discount-for-windows-vms"></a>Remise de réservation pour les machines virtuelles Windows

Quand vous exécutez des instances de machine virtuelle Windows, la réservation est appliquée pour couvrir les coûts d’infrastructure. L’application de la réservation aux coûts d’infrastructure de machine virtuelle pour les machines virtuelles Windows est identique à celle qui est appliquée pour les machines virtuelles non Windows. Les logiciels Windows vous sont facturés séparément, par processeur virtuel. Vous [Coûts des logiciels Windows avec les réservations](billing-reserved-Instance-windows-software-costs.md). Vous pouvez supporter les coûts de licence Windows avec [Azure Hybrid Benefit pour Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

## <a name="discount-can-apply-to-different-sizes"></a>La remise peut s'appliquer à différentes tailles

Quand vous achetez une instance de machine virtuelle réservée, si vous sélectionnez **Optimisé pour** : **Flexibilité de taille d’instance**, l’étendue de la remise dépend de la taille de machine virtuelle que vous sélectionnez. La réservation peut s’appliquer aux tailles des machines virtuelles dans le même groupe de gammes de tailles. Pour plus d’informations, consultez [Flexibilité en termes de taille de machine virtuelle avec des instances de machines virtuelles réservées](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

## <a name="discount-applies-to-matching-servicetype-only"></a>La remise s’applique au type de service correspondant uniquement

Une remise de réservation s’applique uniquement à l’utilisation de la machine virtuelle où la valeur `ServiceType` dans `AdditionalInfo` correspond à la réservation achetée. L'application de la remise de réservation ne tient pas compte du compteur utilisé pour les machines virtuelles et évalue uniquement `ServiceType`. Déterminez le type de service pour lequel vous avez acheté la machine virtuelle. Vous pouvez échanger une réservation de machine virtuelle de stockage non Premium contre une réservation de stockage Premium, et inversement.

## <a name="classic-vms-and-cloud-services"></a>Machines virtuelles et services cloud classiques

Les instances réservées de machines virtuelles s’appliquent aux machines virtuelles et services cloud classiques que la flexibilité de taille d'instance est activée. Pour les services cloud, la remise de réservation s’applique uniquement au coût du calcul. Lorsque la remise de réservation est appliquée aux services cloud, les frais d’utilisation sont divisés en frais de calcul (compteur Linux) et en frais de services cloud (compteur de gestion des services cloud). Pour plus d'informations, consultez [Comment la remise de réservation s'applique-t-elle aux services cloud ?](billing-reserved-instance-windows-software-costs.md#cloud-services-software-meters-not-included-in-reservation-cost)

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

- [Qu’est-ce qu’une réservation Azure ?](billing-save-compute-costs-reservations.md)
- [Prépayer des machines virtuelles avec des instances de machines virtuelles réservées Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Prépayer des ressources de calcul SQL Database avec une capacité réservée Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Gérer les réservations Azure](billing-manage-reserved-vm-instance.md)
- [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](billing-understand-reserved-instance-usage.md)
- [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](billing-understand-reserved-instance-usage-ea.md)
- [Comprendre l’utilisation d’une réservation pour les abonnements CSP](/partner-center/azure-reservations)
- [Coûts des logiciels Windows non inclus dans les réservations](billing-reserved-instance-windows-software-costs.md)
