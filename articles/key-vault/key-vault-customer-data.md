---
title: Fonctionnalités des données client d’Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Découvrez des informations sur les données client dans Key Vault
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: f044c0da1cb1ed3f3c7f118764cc0e685cb3998f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64687015"
---
# <a name="azure-key-vault-customer-data-features"></a>Fonctionnalités des données client d’Azure Key Vault

Azure Key Vault reçoit des données client lors de la création ou de la mise à jour des coffres, des clés, des secrets, des certificats et des comptes de stockage gérés. Ces données client sont directement visibles dans le portail Azure et via l’API REST. Vous pouvez modifier ou supprimer les données client via la mise à jour ou la suppression de l’objet qui les contient.

Des journaux d’activité d’accès système sont générés quand un utilisateur ou une application accède à Key Vault. Les journaux d’activité d’accès détaillés sont disponibles pour les clients avec Azure Insights.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identification des données client

Les informations suivantes identifient les données client dans Azure Key Vault :

- Les stratégies d’accès pour Azure Key Vault contiennent des ID d’objet qui représentent des utilisateurs, des groupes ou des applications
- Les sujets des certificats peuvent inclure des adresses e-mail, ou d’autres identificateurs d’utilisateurs ou d’organisations
- Les contacts des certificats peuvent contenir des adresses e-mail, des noms ou des numéros de téléphone d’utilisateurs
- Les émetteurs des certificats peuvent contenir des adresses e-mail, des noms, des numéros de téléphone, des informations d’identification de compte et des détails de l’organisation
- Des étiquettes arbitraires peuvent être appliquées à des objets dans Azure Key Vault. Ces objets incluent les coffres, les clés, les secrets, les certificats et les comptes de stockage. Les étiquettes utilisées peuvent contenir des données personnelles
- Les journaux d’activité d’accès d’Azure Key Vault contiennent les ID d’objet, les [UPN](../active-directory/hybrid/plan-connect-userprincipalname.md) et les adresses IP pour chaque appel d’API REST
- Les journaux de diagnostic d’Azure Key Vault peuvent contenir des ID d’objet et des adresses IP pour les appels d’API REST

## <a name="deleting-customer-data"></a>Suppression des données client

Les mêmes API REST, la même expérience du portail et les mêmes SDK utilisés pour créer des coffres, des clés, des secrets, des certificats et des comptes de stockage gérés sont également en mesure de mettre à jour et de supprimer ces objets.

La suppression réversible vous permet de récupérer les données supprimées pendant les 90 jours suivant la suppression. Quand vous utilisez la suppression réversible, les données peuvent néanmoins être définitivement supprimées avant l’expiration de la période de conservation de 90 jours via une opération de vidage. Si le coffre ou l’abonnement a été configuré pour bloquer les opérations de vidage, il n’est pas possible de supprimer définitivement les données avant le terme de la période de conservation planifiée.

## <a name="exporting-customer-data"></a>Exportation des données client

Les mêmes API REST, expérience du portail et Kits de développement logiciel (SDK) que ceux utilisés pour créer des coffres, des clés, des secrets, des certificats et des comptes de stockage gérés vous permettent également de visualiser et d’exporter ces objets.

La journalisation des accès d’Azure Key Vault est une fonctionnalité facultative qui peut être activée de façon à générer des journaux d’activité pour chaque appel d’API REST. Ces journaux d’activité sont transférés vers un compte de stockage de votre abonnement où vous appliquez la stratégie de conservation qui répond aux exigences de votre organisation.

Les journaux de diagnostic d’Azure Key Vault contenant des données personnelles peuvent être récupérés en effectuant une demande d’exportation dans le portail Confidentialité de l’utilisateur. Cette demande doit être effectuée par l’administrateur du locataire.

## <a name="next-steps"></a>Étapes suivantes

- [Journalisation d’Azure Key Vault](key-vault-logging.md)

- [Vue d’ensemble de la suppression réversible d’Azure Key Vault](key-vault-soft-delete-cli.md)

- [Opérations sur les clés d’Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Opérations sur les secrets d’Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Certificats et stratégies d’Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Opérations sur les comptes de stockage d’Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
