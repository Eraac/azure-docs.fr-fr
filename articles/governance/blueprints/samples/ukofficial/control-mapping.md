---
title: Exemple – Blueprint UK OFFICIAL et UK NHS – Mappage des contrôles
description: Mappage des contrôles des exemples de blueprint UK OFFICIAL et UK NHS.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 01a8e104f6d590113784db28e4bfde849d78b15f
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491909"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Mappage des contrôles des exemples de blueprint UK OFFICIAL et UK NHS

L’article suivant décrit en détail comment les exemples de blueprint UK OFFICIAL et UK NHS mappent aux contrôles UK OFFICIAL et UK NHS. Pour plus d’informations sur les contrôles, voir [UK OFFICIAL](https://www.gov.uk/government/publications/government-security-classifications).

Les mappages suivants sont effectués aux contrôles **UK OFFICIAL** et **UK NHS**. Utilisez le volet de navigation de droite pour accéder directement à la correspondance d’un contrôle spécifique. De nombreux contrôles mappés sont implémentés avec une initiative [Azure Policy](../../../policy/overview.md). Pour examiner l’initiative complète, ouvrez **Stratégie** dans le portail Azure et sélectionnez la page **Définitions**. Ensuite, recherchez et sélectionnez l’initiative de stratégie intégrée **[Préversion] Auditer les contrôles UK OFFICIAL et UK NHS et déployer des extensions de machine virtuelle spécifiques pour prendre en charge les exigences d’audit**.

## <a name="1-data-in-transit-protection"></a>1 Protection des données en transit

Le blueprint vous aide à vérifier que le transfert d’informations avec les services Azure est sécurisé en affectant des définitions [Azure Policy](../../../policy/overview.md) qui auditent les connexions non sécurisées aux comptes de stockage et au cache Redis.

- Seules les connexions sécurisées à votre cache Redis doivent être activées
- La sécurisation du transfert vers des comptes de stockage doit être activée

## <a name="23-data-at-rest-protection"></a>2.3 Protection des données au repos

Ce blueprint vous aide à appliquer votre stratégie sur l’utilisation des contrôles de chiffrement en affectant des définitions [Azure Policy](../../../policy/overview.md) qui appliquent des contrôles de chiffrement spécifiques et détectent l’utilisation de paramètres de chiffrement faibles.
Le fait de savoir où vos ressources Azure peuvent avoir des configurations de chiffrement non optimales peut vous aider à prendre des mesures correctives visant à vérifier que les ressources sont configurées conformément à votre stratégie de sécurité des informations. Plus précisément, les stratégies assignées par ce blueprint exigent le chiffrement des comptes Data Lake Storage ; exigent le chiffrement transparent des données dans les bases de données SQL ; vérifient le chiffrement sur les comptes de stockage, bases de données SQL, disques de machine virtuelle et variables de compte Automation ; vérifient les connexions non sécurisées aux comptes de stockage et au Cache Redis ; vérifient si le chiffrement par mot de passe des machines virtuelles est faible ; et vérifient les communication Service Fabric non chiffrées.

- Superviser les bases de données SQL non chiffrées dans Azure Security Center
- Un chiffrement de disque doit être appliqué aux machines virtuelles
- Les variables de compte Automation doivent être chiffrées
- La sécurisation du transfert vers des comptes de stockage doit être activée
- La propriété ClusterProtectionLevel doivent être définie sur EncryptAndSign pour les clusters Service Fabric
- La technologie Transparent Data Encryption sur les bases de données SQL doit être activée
- Déployer le chiffrement transparent des données sur les bases de données SQL
- Exiger un chiffrement sur les comptes Data Lake Store
- Emplacements autorisés (« UK SOUTH » et « UK WEST » sont codés en dur)
- Emplacements autorisés pour les groupes de ressources (« UK SOUTH » et « UK WEST » sont codés en dur)

## <a name="52-vulnerability-management"></a>5.2 Gestion des vulnérabilités

Ce blueprint vous permet de gérer les vulnérabilités du système d’informations en affectant des définitions [Azure Policy](../../../policy/overview.md) qui surveillent l’absence de protection de point de terminaison, les mises à jour système manquantes, ainsi que les vulnérabilités en rapport avec le système d’exploitation, SQL et les machines virtuelles. Ces insights fournissent des informations en temps réel sur l’état de la sécurité de vos ressources déployées et peuvent vous aider à classer par ordre de priorité les actions de correction.

- Superviser les agents Endpoint Protection manquants dans Azure Security Center
- Les mises à jour système doivent être installées sur vos machines
- Les vulnérabilités de la configuration de sécurité sur vos machines doivent être corrigées
- Les vulnérabilités de vos bases de données SQL doivent être éliminées
- Les vulnérabilités doivent être corrigées à l’aide d’une solution d’évaluation des vulnérabilités

## <a name="53-protective-monitoring"></a>5.3 Surveillance à des fins de protection

Ce blueprint vous aide à protéger les ressources du système d’informations en affectant des définitions [Azure Policy](../../../policy/overview.md) qui fournissent une surveillance de protection sur l’accès illimité, l’activité de liste verte et les menaces.

- Auditer l'accès réseau non restreint aux comptes de stockage
- Des contrôles d’application adaptatifs doit être activés sur les machines virtuelles
- Déployer la détection de menaces sur les serveurs SQL
- Déployer l’extension Microsoft IaaS Anti-malware par défaut pour Windows Server

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 Gestion sécurisée des utilisateurs / 10 Identité et authentification

Azure implémente un contrôle d’accès en fonction du rôle (RBAC) pour vous aider à gérer qui a accès aux ressources dans Azure. À l’aide du portail Azure, vous pouvez passer en revue les utilisateurs ayant accès aux ressources Azure et leurs autorisations. Ce blueprint vous permet de limiter et de contrôler les droits d’accès en affectant des définitions [Azure Policy](../../../policy/overview.md) pour auditer les comptes externes avec des autorisations de propriétaire ou de lecture/écriture, et les comptes avec des autorisations de propriétaire, de lecture ou d’écriture pour lesquels l’authentification multifacteur n’est pas activée.

- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement
- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement
- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de lecture sur votre abonnement
- Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations d’écriture doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations de lecture doivent être supprimés de votre abonnement

Ce blueprint affecte des définitions Azure Policy afin de vérifier l’utilisation de l’authentification Azure Active Directory pour les serveurs SQL et Service Fabric. L’utilisation de l’authentification Azure Active Directory permet une gestion simplifiée des autorisations et une gestion centralisée des identités des utilisateurs de bases de données et d’autres services Microsoft.

- Un administrateur Azure Active Directory doit être approvisionné pour les serveurs SQL
- Les clusters Service Fabric ne doivent utiliser Azure Active Directory que pour l’authentification client

Ce blueprint affecte également des définitions Azure Policy qui permettent de déterminer les comptes devant en priorité faire l’objet d’une révision, notamment les comptes déconseillés et les comptes externes. Si nécessaire, vous pouvez empêcher des comptes de se connecter (ou les supprimer). Dans ce cas, les droits d’accès aux ressources Azure sont immédiatement supprimés. Ce blueprint affecte deux définitions Azure Policy pour auditer un compte déconseillé dont la suppression doit être envisagée.

- Les comptes déconseillés doivent être supprimés de votre abonnement
- Les comptes déconseillés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations d’écriture doivent être supprimés de votre abonnement

Ce blueprint affecte également une définition Azure Policy qui vérifie les autorisations associées aux fichiers de mot de passe des machines virtuelles Linux qui émettent une alerte si elles sont définies incorrectement. Cette conception vous d’appliquer une action corrective pour vérifier que les authentificateurs ne sont pas compromis.

- [Préversion]: Audit Linux VM /etc/passwd file permissions are set to 0644

Ce blueprint vous aide à appliquer des mots de passe forts en affectant des définitions Azure Policy qui détectent les machines virtuelles Windows ne mettant pas en œuvre une force minimale ou d’autres exigences relatives aux mots de passe. Le fait d’avoir connaissance des machines virtuelles qui enfreignent la stratégie de force des mots de passe peut vous aider à prendre des actions correctives visant à rendre les mots de passe de tous les comptes d’utilisateurs de machine virtuelle conformes à la stratégie.

- [Préversion]: Deploy requirements to audit Windows VMs that do not have the password complexity setting enabled
- [Préversion]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [Préversion]: Deploy requirements to audit Windows VMs that do not have a minimum password age of 1 day
- [Préversion]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Préversion]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords
- [Préversion]: Audit Windows VMs that do not have the password complexity setting enabled
- [Préversion]: Audit Windows VMs that do not have a maximum password age of 70 days
- [Préversion]: Audit Windows VMs that do not have a minimum password age of 1 day
- [Préversion]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Préversion]: Audit Windows VMs that allow re-use of the previous 24 passwords

Ce blueprint vous permet également de contrôler l’accès aux ressources Azure en affectant des définitions Azure Policy. Ces stratégies vérifient l’utilisation des types de ressources et des configurations susceptibles d’accorder un accès plus permissif aux ressources. Le fait de savoir quelles ressources enfreignent ces stratégies peut vous aider à prendre des actions correctives visant à limiter l’accès aux ressources Azure aux utilisateurs autorisés.

- [Préversion]: Deploy requirements to audit Linux VMs that have accounts without passwords
- [Préversion]: Deploy requirements to audit Linux VMs that allow remote connections from accounts without passwords
- [Préversion]: Audit Linux VMs that have accounts without passwords
- [Préversion]: Audit Linux VMs that allow remote connections from accounts without passwords
- Les comptes de stockage doivent être migrés vers de nouvelles ressources Azure Resource Manager
- Les machines virtuelles doivent être migrées vers de nouvelles ressources Azure Resource Manager
- Faire l’audit des machines virtuelles n’utilisant aucun disque managé

## <a name="11-external-interface-protection"></a>11 Protection des interfaces externes

Outre l’utilisation de plus de 25 stratégies pour une gestion sécurisée appropriée des utilisateurs, ce blueprint vous aide à protéger les interfaces de service contre les accès non autorisés en attribuant une définition [ Stratégie Azure](../../../policy/overview.md) qui surveille les comptes de stockage non restreints. Des comptes de stockage avec un accès illimité, peuvent permettre un accès involontaire à des informations contenues dans le système d’information. Cet blueprint attribue également une stratégie qui active des contrôles d’application adaptatifs sur des machines virtuelles.

- Auditer l'accès réseau non restreint aux comptes de stockage
- Des contrôles d’application adaptatifs doit être activés sur les machines virtuelles

## <a name="12-secure-service-administration"></a>12 Administration sécurisée des services

Azure implémente un contrôle d’accès en fonction du rôle (RBAC) pour vous aider à gérer qui a accès aux ressources dans Azure. À l’aide du portail Azure, vous pouvez passer en revue les utilisateurs ayant accès aux ressources Azure et leurs autorisations. Ce blueprint vous permet de limiter et de contrôler les droits d’accès privilégié en affectant des définitions [Azure Policy](../../../policy/overview.md) pour auditer les comptes externes avec des autorisations de propriétaire ou d’écriture, ainsi que les comptes avec des autorisations de propriétaire ou d’écriture pour lesquels l’authentification multifacteur n’est pas activée.

Les systèmes utilisés pour l’administration d’un service cloud bénéficieront d’un accès hautement privilégié à ce service. Toute compromission de ces systèmes aurait des impacts significatifs : possibilité de contourner les contrôles de sécurité, de voler ou manipuler de grands volumes de données, etc. Les méthodes utilisées par les administrateurs du fournisseur de services pour gérer le service opérationnel doivent être conçues pour atténuer les risques d’exploitation qui pourraient compromettre la sécurité du service. Si ce principe n’est pas implémenté, une personne malveillante pourrait trouver les moyens de contourner les contrôles de sécurité afin de voler ou de manipuler d’importants volumes de données.

- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement
- L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations d’écriture sur votre abonnement
- Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations d’écriture doivent être supprimés de votre abonnement

Ce blueprint affecte des définitions Azure Policy afin de vérifier l’utilisation de l’authentification Azure Active Directory pour les serveurs SQL et Service Fabric. L’utilisation de l’authentification Azure Active Directory permet une gestion simplifiée des autorisations et une gestion centralisée des identités des utilisateurs de bases de données et d’autres services Microsoft.

- Un administrateur Azure Active Directory doit être approvisionné pour les serveurs SQL
- Les clusters Service Fabric ne doivent utiliser Azure Active Directory que pour l’authentification client

Ce blueprint affecte également des définitions Azure Policy qui permettent de déterminer les comptes devant en priorité faire l’objet d’une révision, notamment des comptes déconseillés et des comptes externes avec des autorisations élevées. Si nécessaire, vous pouvez empêcher des comptes de se connecter (ou les supprimer). Dans ce cas, les droits d’accès aux ressources Azure sont immédiatement supprimés. Ce blueprint affecte deux définitions Azure Policy pour auditer un compte déconseillé dont la suppression doit être envisagée.

- Les comptes déconseillés doivent être supprimés de votre abonnement
- Les comptes déconseillés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement
- Les comptes externes disposant d’autorisations d’écriture doivent être supprimés de votre abonnement

Ce blueprint affecte également une définition Azure Policy qui vérifie les autorisations associées aux fichiers de mot de passe des machines virtuelles Linux qui émettent une alerte si elles sont définies incorrectement. Cette conception vous d’appliquer une action corrective pour vérifier que les authentificateurs ne sont pas compromis.

- [Préversion]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="13-audit-information-for-users"></a>13 Informations d’audit pour les utilisateurs

Ce blueprint vous permet de garantir que les événements système sont journalisés en affectant des définitions [Azure Policy](../../../policy/overview.md) qui vérifient les paramètres de journalisation sur les ressources Azure. Une stratégie affectée vérifie également si les machines virtuelles n’envoient pas de journaux à un espace de travail Log Analytics spécifié.

- Superviser les serveurs SQL non audités dans Azure Security Center
- Auditer le paramètre de diagnostic
- Auditer les paramètres d'audit au niveau du serveur SQL
- [Préversion]: Deploy Log Analytics Agent for Linux VMs
- [Préversion]: Deploy Log Analytics Agent for Windows VMs
- Déployer Network Watcher lors de la création de réseaux virtuels

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez passé en revue le mappage des contrôles des blueprints UK OFFICIAL et UK NHS, consultez les articles suivants pour découvrir une vue d’ensemble et comment déployer cet exemple :

> [!div class="nextstepaction"]
> [Blueprints UK OFFICIAL et UK NHS – Vue d’ensemble](./index.md)
> [Blueprints UK OFFICIAL et UK NHS – Étapes de déploiement](./deploy.md)

Autres articles sur les blueprints et la manière de les utiliser :

- Découvrir le [cycle de vie des blueprints](../../concepts/lifecycle.md).
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).
