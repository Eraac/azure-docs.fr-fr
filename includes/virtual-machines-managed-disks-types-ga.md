---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2bc5602011ed64b11b1b8c96b7e69a8d5ee9bf32
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133055"
---
## <a name="premium-ssd"></a>SSD Premium

Les disques SSD Premium Azure offrent une prise en charge très performante et une faible latence pour les machines virtuelles avec des charges de travail qui utilisent beaucoup d’entrée/sortie (E/S). Pour tirer parti de la vitesse et des performances des disques de stockage Premium, vous pouvez migrer les disques de machines virtuelles existantes vers des disques SSD Premium. Les disques SSD Premium sont adaptés aux applications de production critiques. Ces disques sont utilisables uniquement avec des séries de machines virtuelles compatibles avec le stockage Premium.

Pour en savoir plus sur les types et les tailles de machines virtuelles individuelles dans Azure pour Windows, notamment les tailles compatibles avec le stockage Premium, consultez [Tailles des machines virtuelles Windows](../articles/virtual-machines/windows/sizes.md). Pour en savoir plus sur les types et les tailles de machines virtuelles individuelles dans Azure pour Linux, notamment les tailles compatibles avec le stockage Premium, consultez [Tailles des machines virtuelles Linux](../articles/virtual-machines/linux/sizes.md).

### <a name="disk-size"></a>Taille du disque
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Lorsque vous configurez un disque de stockage Premium, contrairement au stockage standard, la capacité, les E/S par seconde et le débit de ce disque sont assurés. Par exemple, si vous créez un disque P50, Azure configure une capacité de stockage de 4 095 Go, 7 500 E/S par seconde et un débit de 250 Mo/s pour ce disque. Votre application peut utiliser tout ou partie de la capacité et des performances. Les disques SSD Premium sont conçus pour fournir des latences faibles de quelques millisecondes ainsi que l’IOPS et le débit cibles décrits dans le précédent tableau 99,9 % du temps.

### <a name="transactions"></a>Transactions

Pour les disques SSD Premium, chaque opération d’E/S inférieure ou égale à 256 Kio de débit est considérée comme une seule opération d’E/S. Les opérations d’E/S supérieures à 256 Kio de débit sont considérées comme plusieurs opérations d’E/S de 256 Kio chacune.

## <a name="standard-ssd"></a>SSD Standard

Les disques SSD Azure Standard constituent une option de stockage économique optimisée pour les charges de travail nécessitant des performances cohérentes à des niveaux d’IOPS bas. Les disques SSD Standard offrent une bonne expérience de niveau d’entrée pour ceux qui souhaitent migrer vers le cloud, en particulier si vous rencontrez des problèmes de variation avec les charges de travail exécutées sur vos solutions HDD locales. Par rapport aux disques HDD Standard, les disques SSD Standard offrent une meilleure disponibilité, cohérence, fiabilité et latence. Les disques SSD Standard sont adaptés aux serveurs web, serveurs d’applications avec peu d’IOPS, applications d’entreprise peu utilisées et charges de travail Dev/Test. Comme les disques HDD Standard, les disques SSD Standard sont disponibles sur toutes les machines virtuelles Azure.

### <a name="disk-size"></a>Taille du disque
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Les disques SSD Standard sont conçus pour fournir des latences de quelques millisecondes ainsi qu’un IOPS et un débit dans les limites décrites dans le tableau précédent 99 % du temps. L’IOPS et le débit réels peuvent varier selon les modèles de trafic. Les disques SSD Standard offrent des performances plus cohérentes que les disques HDD avec une latence plus faible.

### <a name="transactions"></a>Transactions

Pour les disques SSD Standard, chaque opération d’E/S inférieure ou égale à 256 Kio de débit est considérée comme une seule opération d’E/S. Les opérations d’E/S supérieures à 256 Kio de débit sont considérées comme plusieurs opérations d’E/S de 256 Kio chacune. Ces transactions ont un impact sur la facturation.

## <a name="standard-hdd"></a>HDD Standard

Les disques HDD standard Azure offrent une prise en charge fiable et économique des disques des machines virtuelles exécutant des charges de travail non sensibles aux latences. Dans le cadre d’un stockage Standard, les données sont stockées sur des disques durs. La latence, l’IOPS et le débit sur les disques HDD Standard peuvent varier beaucoup plus que sur les disques SSD. Les disques HDD Standard sont conçus pour offrir une latence inférieure à 10 ms en écriture et à 20 ms en lecture pour la plupart des opérations d’E/S. Toutefois, les performances réelles peuvent varier en fonction de la taille d’E/S et du modèle de charge de travail. Avec des machines virtuelles, vous pouvez utiliser des disques HDD Standard pour les scénarios Dev/Test et les charges de travail moins critiques. Les disques HDD Standard sont disponibles dans toutes les régions Azure et peuvent être utilisés avec toutes les machines virtuelles Azure.

### <a name="disk-size"></a>Taille du disque
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transactions

Pour les disques SSD Standard, chaque opération d’E/S est considérée comme une seule transaction, indépendamment de la taille des E/S. Ces transactions ont un impact sur la facturation.

## <a name="billing"></a>Facturation

Lorsque vous utilisez des disques managés, les considérations de facturation suivantes s’appliquent :

- Type de disque
- Taille des disques managés
- Instantanés
- Transferts de données sortantes
- Nombre de transactions

**Taille des disques managés** : les disques managés sont facturés selon la taille provisionnée. Azure fait correspondre la taille provisionnée (arrondie à la valeur supérieure) à la taille de disque offerte la plus proche. Pour plus d’informations sur les tailles de disque proposées, consultez les tableaux précédents. Chaque disque correspond à une offre de taille de disque configurée prise en charge, et est facturé en conséquence. Par exemple, si vous avez provisionné un disque SSD Standard de 200 Gio, celui-ci correspond à l’offre de taille de disque E15 (256 Gio). La facturation de n’importe quel disque configuré est calculée au prorata horaire sur la base du tarif mensuel de l’offre de stockage Premium. Par exemple, si vous provisionnez un disque E10 et le supprimez au bout de 20 heures, l’offre E10 vous est facturée au prorata de 20 heures. Le montant facturé est indépendant de la quantité de données écrites sur le disque.

**Instantanés** : Les instantanés facturés en fonction de la taille utilisée. Par exemple, si vous créez une capture instantanée d’un disque managé avec une capacité approvisionnée de 64 Gio et une taille des données utilisées réelle de 10 Gio, l'instantané est facturé uniquement pour la taille des données utilisées de 10 Gio.
