---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 268467796e67caf2d20fedb44d83fd455a09b83e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177050"
---
**Transferts de données sortants** : [transferts de données sortants](https://azure.microsoft.com/pricing/details/bandwidth/) (données sortant des centres de données Azure) sont facturés en fonction de la bande passante utilisée.

**Transactions** : vous êtes facturé pour le nombre de transactions effectuées sur un disque managé standard. Pour les disques SSD Standard, chaque opération d’E/S inférieure ou égale à 256 Kio de débit est considérée comme une seule opération d’E/S. Les opérations d’E/S supérieures à 256 Kio de débit sont considérées comme plusieurs opérations d’E/S de 256 Kio chacune. Pour les disques SSD Standard, chaque opération d’E/S est considérée comme une seule transaction, indépendamment de la taille des E/S.

Pour plus d’informations sur la tarification d’Azure Disques managés (dont les coûts de transaction), consultez la page [Tarification de la fonctionnalité Disques managés](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Frais de réservation de machine virtuelle avec un disque Ultra SSD

Les machines virtuelles Azure peuvent indiquer si elles sont compatibles avec les disques Ultra SSD. Une machine virtuelle compatible avec les disques Ultra alloue une capacité de bande passante dédiée entre l'instance de machine virtuelle de calcul et l'unité d'échelle de stockage de blocs pour optimiser les performances et réduire la latence. L'ajout de cette fonctionnalité sur la machine virtuelle entraîne des frais de réservation qui ne sont imposés que si vous avez activé la fonctionnalité de disque Ultra sur la machine virtuelle sans y joindre de disque Ultra. Lorsqu'un disque Ultra est joint à la machine virtuelle compatible avec les disques Ultra, ces frais ne s'appliquent pas. Les frais sont calculés par processeur virtuel provisionné sur la machine virtuelle.

Pour plus d'informations sur les tarifs des disques Ultra, consultez la [page Tarification des disques Azure](https://azure.microsoft.com/pricing/details/managed-disks/).