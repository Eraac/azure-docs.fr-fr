---
title: Choisir une solution Azure pour le transfert de données | Microsoft Docs
description: Découvrez comment choisir une solution Azure pour transférer des données en fonction de la taille des données et de la bande passante réseau disponible dans votre environnement.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 56470c08f0ac940dae42821ae61846f1c86d52eb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66479514"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Choisir une solution Azure pour le transfert de données

Cet article offre une vue d’ensemble des solutions de transfert de données Azure les plus courantes. Vous y trouverez également des liens vers les options recommandées en fonction de la bande passante réseau de votre environnement et de la taille des données que vous souhaitez transférer.

## <a name="types-of-data-movement"></a>Types de déplacement des données

Le transfert de données peut se faire hors connexion ou via la connexion réseau. Choisissez votre solution en fonction des éléments suivants :

- **Taille des données** : taille des données que vous souhaitez transférer
- **Fréquence du transfert** : ingestion de données ponctuelle ou périodique
- **Réseau** : bande passante disponible pour le transfert de données dans votre environnement

Il existe plusieurs types de déplacement des données :

- **Transfert hors connexion à l’aide de l’envoi de périphériques** : utilisez des appareils physiques lorsque vous souhaitez transférer ponctuellement des données en bloc en mode hors connexion. Microsoft vous envoie un disque ou un dispositif de sécurité spécialisé. Vous pouvez également acheter et expédier vos propres disques. Vous copiez les données sur le périphérique, puis vous l’expédiez à Azure où les données sont chargées.  Dans ce cas, les options disponibles sont les suivantes : Data Box Disk, Data Box, Data Box Heavy et Import/Export (avec vos propres disques).

- **Transfert par réseau** : vous transférez vos données vers Azure via votre connexion réseau. Il existe plusieurs manières de procéder :

    - **Interface graphique** : si vous transférez occasionnellement quelques fichiers et que vous n’avez pas besoin d’automatiser le transfert de données, vous pouvez choisir un outil d’interface graphique tel que l’Explorateur Stockage Azure ou un outil web d’exploration dans le portail Azure.
    - **Transfert scripté ou programmatique** : vous pouvez utiliser les outils logiciels optimisés que nous fournissons ou appeler directement nos SDK et API REST. Les outils scriptables disponibles sont les suivants : AzCopy, Azure PowerShell et Azure CLI. Pour l’interface de programmation, utilisez l’un des SDK pour .NET, Java, Python, Node/JS, C++, Go, PHP ou Ruby.
    - **Périphériques locaux** : nous vous fournissons un périphérique physique ou virtuel qui se trouve dans votre centre de données et qui optimise le transfert de données sur le réseau. Ces périphériques offrent également un cache local contenant les fichiers fréquemment utilisés. L’appareil physique correspond à Data Box Edge et l’appareil virtuel correspond à Data Box Gateway. Les deux appareils sont exécutés sur site en permanence et sont connectés à Azure grâce au réseau.
    - **Pipeline de données managées** : vous pouvez configurer un pipeline cloud qui transfère régulièrement des fichiers entre plusieurs services Azure, en local ou les deux. Utilisez Azure Data Factory pour configurer et gérer les pipelines de données et déplacer et transformer des données pour les analyser.

L’image suivante illustre les recommandations permettant de choisir parmi les différents outils de transfert de données Azure en fonction de la bande passante réseau disponible pour le transfert, de la taille des données à transférer et de la fréquence du transfert.

![Outils de transfert de données Azure](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**Les limites supérieures des périphériques de transfert hors connexion (Data Box Disk, Data Box et Data Box Heavy) peuvent être étendues en passant des commandes multiples pour un même type d’appareil.*

## <a name="selecting-a-data-transfer-solution"></a>Sélection d’une solution de transfert de données

Répondez aux questions suivantes pour sélectionner une solution de transfert de données :

- Votre bande passante réseau disponible est-elle limitée ou inexistante et voulez-vous transférer des jeux de données volumineux ?
  
    Si oui, reportez-vous au [Scénario 1 : Transférer des jeux de données volumineux avec peu ou pas de bande passante réseau](storage-solution-large-dataset-low-network.md).
- Souhaitez-vous transférer des jeux de données volumineux via le réseau et disposez-vous d’une bande passante réseau modérée ou élevée ?

    Si oui, reportez-vous au [Scénario 2 : Transférer des jeux de données volumineux avec une bande passante réseau modérée ou élevée](storage-solution-large-dataset-moderate-high-network.md).
- Souhaitez-vous transférer occasionnellement quelques fichiers sur le réseau ?

    Si oui, reportez-vous au [Scénario 3 : Transférer de petits jeux de données avec une bande passante réseau limitée ou modérée](storage-solution-small-dataset-low-moderate-network.md).
- Souhaitez-vous transférer des données à un moment spécifique et à intervalle régulier ?

    Si oui, utilisez les options scriptées et programmatiques qui sont décrites dans le [Scénario 4 : Transfert régulier de données](storage-solution-periodic-data-transfer.md).
- Souhaitez-vous transférer des données en continu ?

    Si oui, utilisez les options qui sont décrites dans le [Scénario 4 : Transfert régulier de données](storage-solution-periodic-data-transfer.md).
 

## <a name="data-transfer-feature-in-azure-portal"></a>Fonctionnalité de transfert de données dans le Portail Microsoft Azure

Vous pouvez également vous rendre sur votre compte Stockage Azure dans le Portail Microsoft Azure et sélectionner la fonctionnalité **Transfert de données**. Indiquez la bande passante réseau dans votre environnement, la taille des données que vous souhaitez transférer et la fréquence du transfert de données. Vous verrez les solutions optimales de transfert de données correspondant aux informations que vous avez fournies. 

## <a name="next-steps"></a>Étapes suivantes

- [Présentation de l’Explorateur Stockage Azure](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Vue d’ensemble d’AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Utiliser Azure PowerShell avec le Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-powershell-guide-full)
- [Utiliser Azure CLI avec le Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- Vous en saurez plus sur :

    - [Azure Data Box, Azure Data Box Disk et Azure Data Box Heavy pour les transferts hors connexion](https://docs.microsoft.com/azure/databox/).
    - [Azure Data Box Gateway et Azure Data Box Edge pour les transferts en ligne](https://docs.microsoft.com/azure/databox-online/).
- [Qu'est-ce qu'Azure Data Factory ?](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)
- Utiliser les API REST pour transférer des données

    - [Dans .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [Dans Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)
