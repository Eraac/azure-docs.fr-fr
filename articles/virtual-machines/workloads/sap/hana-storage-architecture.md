---
title: Architecture de stockage de SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Architecture de stockage de SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a2cfe9dc02e69f3b47c99e01bc70bffc942338fd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707255"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Architecture de stockage de SAP HANA (grandes instances)

La disposition de stockage pour SAP HANA sur Azure (grandes instances) est configurée par SAP HANA sur le modèle de déploiement classique selon les recommandations de SAP. Les instructions sont documentées dans le livre blanc [Exigences de stockage SAP HANA](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

La grande instance HANA de classe Type I est livrée avec quatre fois le volume de mémoire et le volume de stockage. Pour les unités de grande instance HANA de classe Type II, le stockage n’est pas quatre fois plus élevé. Le volume fourni dans les unités est prévu pour le stockage des sauvegardes de fichiers journaux HANA. Pour plus d’informations, consultez [Installer et configurer SAP HANA (grandes instances) sur Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consultez le tableau suivant pour connaître l’allocation de stockage. La table indique la capacité approximative des différents volumes fournis avec les diverses unités de grande instance HANA.

| Référence SKU de grande instance HANA | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1 280 Go | 512 Go | 768 Go | 512 Go |
| S72m | 3 328 Go | 768 Go |1 280 Go | 768 Go |
| S96 | 1 280 Go | 512 Go | 768 Go | 512 Go |
| S192 | 4 608 Go | 1 024 Go | 1 536 Go | 1 024 Go |
| S192m | 11 520 Go | 1 536 Go | 1 792 Go | 1 536 Go |
| S192xm |  11 520 Go |  1 536 Go |  1 792 Go |  1 536 Go |
| S384 | 11 520 Go | 1 536 Go | 1 792 Go | 1 536 Go |
| S384m | 12 000 Go | 2 050 Go | 2 050 Go | 2 040 Go |
| S384xm | 16 000 Go | 2 050 Go | 2 050 Go | 2 040 Go |
| S384xxm |  20 000 Go | 3 100 Go | 2 050 Go | 3 100 Go |
| S576m | 20 000 Go | 3 100 Go | 2 050 Go | 3 100 Go |
| S576xm | 31 744 Go | 4 096 Go | 2 048 Go | 4 096 Go |
| S768m | 28 000 Go | 3 100 Go | 2 050 Go | 3 100 Go |
| S768xm | 40 960 Go | 6 144 Go | 4 096 Go | 6 144 Go |
| S960m | 36 000 Go | 4 100 Go | 2 050 Go | 4 100 Go |


Les volumes réels déployés peuvent varier légèrement en fonction du déploiement et de l’outil utilisés pour afficher les tailles de volume.

Si vous subdivisez une référence SKU de grande instance HANA, voici quelques exemples de division possible pour les pièces :

| Partition de la mémoire en Go | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 Go | 160 Go | 304 Go | 160 Go |
| 512 | 768 Go | 384 Go | 512 Go | 384 Go |
| 768 | 1 280 Go | 512 Go | 768 Go | 512 Go |
| 1 024 | 1 792 Go | 640 Go | 1 024 Go | 640 Go |
| 1 536 | 3 328 Go | 768 Go | 1 280 Go | 768 Go |


Ces tailles sont des volumes approximatifs qui peuvent varier légèrement en fonction du déploiement et des outils utilisés pour examiner les volumes. Il existe également d’autres tailles de partition, comme 2,5 To. Ces tailles de stockage sont calculées avec une formule semblable à celle utilisée pour les partitions précédentes. Le terme de « partitions » n’indique pas que les ressources du système d’exploitation, de la mémoire ou du processeur sont partitionnées de quelque manière que ce soit. Il indique simplement les partitions de stockage pour les différentes instances HANA que vous voudrez peut-être déployer sur une seule unité de grande instance HANA. 

Vous aurez peut-être besoin de plus de stockage. Vous pouvez ajouter du stockage par l’achat de stockage supplémentaire en unités de 1 To. Ce stockage supplémentaire peut être ajouté en tant que volume supplémentaire. Il peut également être utilisé pour étendre un ou plusieurs volumes existants. Il n’est pas possible de réduire la taille des volumes tels qu’ils ont été déployés à l’origine et documentés principalement dans le ou les tables ci-dessus. Il est également impossible de modifier les noms des volumes ou les noms des montages. Les volumes de stockage, comme décrit précédemment, sont joints aux unités de grande instance HANA en tant que volumes NFS4.

Vous pouvez utiliser des captures instantanées de stockage à des fins de récupération d’urgence, de restauration et de sauvegarde. Pour plus de détails, consultez [Haute disponibilité et récupération d’urgence SAP HANA (grandes instances) sur Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consultez [Scénarios HLI pris en charge](hana-supported-scenario.md) pour connaître les détails de la disposition du stockage de votre scénario.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Exécution de plusieurs instances SAP HANA sur une unité de grande instance HANA

Il est possible d’héberger plusieurs instances SAP HANA actives sur les unités de grande instance HANA. Afin de fournir les fonctionnalités de captures instantanées du stockage et de récupération d’urgence, une telle configuration requiert un volume défini par instance. Actuellement, les unités de grande instance HANA peuvent être classées comme suit :

- **S72, S72m, S96, S144, S192** : par incréments de 256 Go, l’unité de départ la plus petite étant de 256 Go. Des incréments différents, comme 256 Go, 512 Go, etc., peuvent être combinés jusqu’à la valeur maximale de la mémoire de l’unité.
- **S144m et S192m** : par incréments de 256 Go, l’unité la plus petite étant de 512 Go. Des incréments différents, comme 512 Go et 768 Go peuvent être combinés jusqu’à la valeur maximale de la mémoire de l’unité.
- **Classe Type II** : par incréments de 512 Go, l’unité de départ la plus petite étant de 2 To. Des incréments différents, comme 512 Go, 1 Go et 1,5 To peuvent être combinés jusqu’à la valeur maximale de la mémoire de l’unité.

Voici quelques exemples de ce à quoi peut ressembler l’exécution de plusieurs instances SAP HANA.

| SKU | Taille de la mémoire | Taille de stockage | Tailles avec plusieurs bases de données |
| --- | --- | --- | --- |
| S72 | 768 Go | 3 To | 1 instance HANA de 768 Go<br /> ou 1 instance de 512 Go + 1 instance de 256 Go<br /> ou 3 instances de 256 Go | 
| S72m | 1,5 To | 6 To | 3 instances HANA de 512 Go<br />ou 1 instance de 512 Go + 1 instance de 1 To<br />ou 6 instances de 256 Go<br />ou 1 instance de 1,5 To | 
| S192m | 4 To | 16 TO | 8 instances de 512 Go<br />ou 4 instances de 1 To<br />ou 4 instances de 512 Go + 2 instances de 1 To<br />ou 4 instances de 768 Go + 2 instances de 512 Go<br />ou 1 instance de 4 To |
| S384xm | 8 To | 22 To | 4 instances de 2 To<br />ou 2 instances de 4 To<br />ou 2 instances de 3 To + 1 instance de 2 To<br />ou 2 instances de 2,5 To + 1 instance de 3 To<br />ou 1 instance de 8 To |


Il existe d’autres variantes. 

## <a name="encryption-of-data-at-rest"></a>Chiffrement des données au repos
Le stockage utilisé pour la grande instance HANA permet un chiffrement transparent des données lorsqu’elles sont stockées sur les disques. Lorsqu’une unité de grande instance HANA est déployée, vous pouvez activer ce type de chiffrement. Vous pouvez également modifier les volumes chiffrés après le déploiement. Le passage d’un volume non chiffré à un volume chiffré est transparent et ne requiert aucun temps d’arrêt. 

Avec les références SKU de classe Type I, le volume sur lequel le numéro d’unité logique de démarrage est stocké est chiffré. Dans le cas de références SKU de grande instance HANA de classe Type II, vous devez chiffrer le numéro d’unité logique de démarrage avec les méthodes du système d’exploitation. Pour plus d’informations, contactez l’équipe de gestion des services Microsoft.

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Paramètres requis pour des plus instances HANA plus grandes sur de grandes Instances HANA
Le stockage utilisé dans les grandes Instances HANA limite la taille des fichiers. La [limite de taille est de 16 To](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) par fichier. Contrairement aux limitations de taille de fichier dans les systèmes de fichiers EXT3, HANA ne reconnaît pas implicitement la limite de stockage appliquée par le stockage sur de grandes Instances HANA. Par conséquent, HANA ne crée pas automatiquement un nouveau fichier de données lorsque la limite de taille de 16 To est atteinte. Quand HANA tente d’étendre le fichier au-delà de 16 To, il signale des erreurs et le serveur d’index se bloque à la fin.

> [!IMPORTANT]
> Afin d’empêcher HANA d’essayer d’augmenter la taille des fichiers de données au-delà de la limite de 16 To imposée par le stockage sur une grande Instance HANA, vous devez définir les paramètres suivants dans le fichier de configuration global.ini de HANA.
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - Voir aussi la note SAP [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - N’oubliez pas la note SAP [#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**Étapes suivantes**
- Voir [Scénario pris en charge pour les grandes instances HANA](hana-supported-scenario.md).