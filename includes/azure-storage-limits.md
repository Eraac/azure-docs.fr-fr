---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e3cff54f49d40fb7da99dcafa9535349ccb70e09
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150477"
---
Le tableau suivant présente les limites par défaut pour les comptes de stockage V1, V2 et d’objets blob universels. La limite d’*entrée* désigne toutes les données issues de requêtes envoyées à un compte de stockage. La limite de *sortie* désigne toutes les données issues des réponses reçues depuis un compte de stockage.

| Ressource | Limite par défaut |
| --- | --- |
| Nombre de comptes de stockage par région et par abonnement, y compris les comptes standard et premium | 250 |
| Capacité maximale du compte de stockage | 2 Po pour les États-Unis et l’Europe, 500 To pour toutes les autres régions, y compris le Royaume-Uni |
| Nombre maximal de conteneurs d’objets blob, de partages de fichiers, de tables, de files d’attente, d’entités ou de messages par compte de stockage | Aucune limite |
| Taux de demande maximal<sup>1</sup> par compte de stockage | 20 000 demandes par seconde |
| Entrée max.<sup>1</sup> par compte de stockage (régions des États-Unis) | 10 Gbit/s si RA-GRS/GRS est activé, 20 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Entrée max.<sup>1</sup> par compte de stockage (régions hors États-Unis) | 5 Gbit/s si RA-GRS/GRS est activé, 10 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Nombre maximal de sorties pour les comptes de stockage à usage général v2 et Blob (toutes les régions) | 50 Gbit/s |
| Nombre maximal de sorties pour les comptes de stockage à usage général v1 (régions des États-Unis) | 20 Gbit/s si RA-GRS/GRS est activé, 30 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Nombre maximal de sorties pour les comptes de stockage à usage général v1 (régions hors États-Unis) | 10 Gbit/s si RA-GRS/GRS est activé, 15 Gbit/s pour LRS/ZRS<sup>2</sup> |

<sup>1</sup> Les comptes de stockage standard Azure prennent en charge la demande de limites supérieures pour les entrées. Pour demander une augmentation des limites de compte pour la sortie, contactez le [support Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> [Les options de réplication du Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) sont notamment :
- **RA-GRS** : Stockage géoredondant avec accès en lecture. Si RA-GRS est activé, les cibles de sortie pour l’emplacement secondaire sont identiques à celles de l’emplacement principal.
- **GRS** : Stockage géoredondant.
- **ZRS** : Stockage redondant interzone.
- **LRS** : Stockage localement redondant.

> [!NOTE]
> Nous vous recommandons d’utiliser des comptes de stockage universels v2 pour la plupart des scénarios. Vous pouvez facilement mettre à niveau un compte de stockage universel v1 ou un compte de stockage d’objets blob Azure vers un compte universel v2, sans que cela nécessite un temps d’arrêt ou la copie de données.
>
> Pour plus d’informations sur les comptes de stockage Azure, consultez [Vue d’ensemble des comptes de stockage Azure](../articles/storage/common/storage-account-overview.md).

Si les besoins de votre application dépassent les objectifs d’extensibilité d’un compte de stockage unique, vous pouvez concevoir votre application afin qu’elle utilise plusieurs comptes de stockage. Ensuite, vous pouvez partitionner vos objets de données sur ces comptes de stockage. Pour plus d’informations sur la tarification en volume, consultez la page [Tarification Stockage Azure](https://azure.microsoft.com/pricing/details/storage/).

Tous les comptes de stockage s’exécutent sur la topologie de réseau plat et prennent en charge les objectifs d'extensibilité et de performances décrits dans cet article, quel que soit le moment où ils ont été créés. Pour plus d'informations sur l'architecture de réseau plat Stockage Azure et sur son extensibilité, consultez [Stockage Microsoft Azure : un service de stockage cloud hautement disponible à cohérence forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

