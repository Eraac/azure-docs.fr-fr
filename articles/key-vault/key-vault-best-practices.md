---
title: Meilleures pratiques pour utiliser Key Vault - Azure Key Vault | Microsoft Docs
description: Ce document décrit certaines des meilleures pratiques à employer pour utiliser Key Vault.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-key-vault
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: eb7150d0b1c3a4a312b0c05ba7612960aaf640f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65227937"
---
# <a name="best-practices-to-use-key-vault"></a>Meilleures pratiques pour utiliser Key Vault

## <a name="control-access-to-your-vault"></a>Contrôler l’accès à votre coffre

Azure Key Vault est un service cloud qui protège les clés et secrets de chiffrement comme les certificats, chaînes de connexion et mots de passe. Comme il s’agit de données sensibles et critiques, vous devez sécuriser l’accès à vos coffres de clés en acceptant seulement les applications et les utilisateurs autorisés. Cet [article](key-vault-secure-your-key-vault.md) fournit une vue d’ensemble du modèle d’accès aux coffres de clés. Il décrit l’authentification et l’autorisation, puis explique comment sécuriser l’accès à vos coffres de clés.

Voici quelques suggestions concernant le contrôle de l’accès à votre coffre :
1. Verrouiller l’accès à votre abonnement, au groupe de ressources et aux coffres de clés : contrôle d’accès en fonction du rôle (RBAC)
2. Créer des stratégies d’accès pour chaque coffre
3. Utiliser le principe des privilèges d’accès minimum pour accorder l’accès
4. Activer le pare-feu et les [points de terminaison de service de réseau virtuel](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Utiliser un coffre de clés distinct

Nous recommandons d’utiliser un coffre par application et par environnement (développement, préproduction et production). Cela vous aide à éviter le partage de secrets entre environnements et réduit la menace en cas de violation.

## <a name="backup"></a>Sauvegarde

Veillez à effectuer des sauvegardes régulières de votre [coffre](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) lors de la mise à jour, de la suppression ou de la création d’objets au sein d’un coffre.

## <a name="turn-on-logging"></a>Activer la journalisation

[Activez la journalisation](key-vault-logging.md) pour votre coffre. Configurez également des alertes.

## <a name="turn-on-recovery-options"></a>Activer les options de récupération

1. Activez la [suppression réversible](key-vault-ovw-soft-delete.md).
2. Activez la protection contre le vidage si vous souhaitez bénéficier d’une protection contre la suppression forcée du secret ou du coffre, même après activation de la suppression réversible.
