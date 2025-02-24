---
title: Service de mappage des compteurs pour compte gratuit Azure
description: Comprendre le service de mappage des compteurs pour les services inclus dans un compte gratuit.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 8022c065d73aafc53d3dcb77e79c3e6320e0ce39
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490398"
---
# <a name="understand-free-service-to-meter-mapping"></a>Comprendre le service gratuit de mappage des compteurs

Chaque service Azure émet des informations d’utilisation basées sur des compteurs, que le système de facturation Azure utilise pour facturer les services fournis aux utilisateurs. Pour mieux comprendre l’utilisation des services gratuits, examinons le service de mappage des compteurs pour ces services. Pour savoir comment créer des services gratuits, voir [Créer des services gratuits avec un compte gratuit Azure](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-eligible-services"></a>Service de mappage des compteurs pour les services éligibles

|    de diffusion en continu   | Nom du compteur sur le portail Azure | Nom du compteur dans le fichier ou l’API d’utilisation | ID du compteur |
| ------------ | -------------------------- | -------------------------| -------- |
| Machine virtuelle Linux B1S | Heures de calcul - Machine virtuelle Standard_B1 | Heures de calcul - Gratuit | 8260cba2-4437-47d1-a31e-2561cd370f50
| Machine virtuelle Windows B1S | Heures de calcul - Machine virtuelle Standard_B1 (Windows) | Heures de calcul - Gratuit | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| Machine virtuelle B1S - Adresses IP publiques  | Heure pour l’adresse IP - Adresses IP publiques | Heure pour l'adresse IP - Gratuit | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Stockage (Go) - Cosmos DB | Stockage (Go) - Gratuit | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 unités de requête (heures) - Cosmos DB | 100 unités de requête (heures) - Gratuit | 5d638a6f-e221-41cf-ae3f-0f81d368cef6
| Stockage Fichier | E/S standard - Fichiers (Go) - Localement redondant | E/S standard - Fichiers (Go) - Gratuit | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| Stockage Fichier | E/S standard - Unités opération Lecture de fichier (par 10 000) | E/S standard - Unités opération Lecture de fichier (par 10 000) - Gratuit | 6207404d-3389-4d20-9087-cc078ddc3fd9
| Stockage Fichier | E/S standard - Unités opération Écriture de fichier (par 10 000) | E/S standard - Unités opération Écriture de fichier (par 10 000) - Gratuit | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| Stockage Fichier | E/S standard - Unités opération Protocole de fichier (par 10 000) | E/S standard - Unités opération Protocole de fichier (par 10 000) - Gratuit | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| Stockage Fichier | E/S standard - Unités opération Liste fichier (par 10 000) | E/S standard - Unités opération Liste fichier (par 10 000) - Gratuit | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Stockage d'objets blob de blocs chaud | E/S standard - Opérations Lecture d’objet blob de blocs chaud (par 10 000) | E/S standard - Opérations Lecture d’objet blob de blocs chaud (par 10 000) - Gratuit |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Stockage d'objets blob de blocs chaud | E/S standard - Objet blob de blocs chaud (Go) - Localement redondant | E/S standard - Objet blob de blocs chaud (Go) - Gratuit | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Stockage d'objets blob de blocs chaud | E/S standard - Opérations Écriture d’objet blob de blocs chaud (par 10 000) | E/S standard - Opérations Écriture d’objet blob de blocs chaud (par 10 000) - Gratuit | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Stockage d'objets blob de blocs chaud  | E/S standard - Opérations Écriture/Liste d'objet blob de blocs chaud (par 10 000) | E/S standard - Opérations Écriture/Liste d'objet blob de blocs chaud (par 10 000) - Gratuit | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Disque managé <sup>1</sup>  | Disque géré standard/Instantanés (Go) - Localement redondant | Disque géré standard/Instantanés (Go) - Gratuit | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Disque managé <sup>1</sup>  | Opérations disque managées Standard (par 10 000) | Opérations disque managées Standard (par 10 000) - Gratuit | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Disque managé <sup>1</sup>  | Stockage Premium - Objet blob de page/P6 (unités) - Localement redondant | Stockage Premium - Objet blob de page/P6 (unités) - Gratuit | 2b98c168-27ca-4cc1-b509-e887dec87657
| Base de données SQL | Jours de base de données S0 standard - SQL Database | Jours de base de données S0 standard - Gratuit | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Partagé - Bande passante <sup>2</sup> | Transfert de données sortant (Go) | Transfert de données sortant (Go) - Gratuit | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

<sup>1</sup> Si vous créez une machine virtuelle Windows et choisissez un disque managé, vous utiliserez le compteur de disques managés en tant que partie de la machine virtuelle.

<sup>2</sup> Les compteurs partagés peuvent être utilisés via plusieurs services. Par exemple, tant les machines virtuelles que le stockage émettent l’utilisation basée sur le compteur Transfert de données sortant (Go).

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Contactez-nous.

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Étapes suivantes
- [Mettre à niveau votre abonnement](billing-upgrade-azure-subscription.md)
