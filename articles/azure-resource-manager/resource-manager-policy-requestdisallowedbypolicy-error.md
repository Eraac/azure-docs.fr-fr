---
title: Erreur RequestDisallowedByPolicy avec une stratégie de ressource Azure | Microsoft Docs
description: Décrit la cause de l’erreur RequestDisallowedByPolicy.
services: azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c160fe39b02d8adf6c12e3736307cf7f9688b0c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66128447"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Erreur RequestDisallowedByPolicy avec une stratégie de ressource Azure

Cet article décrit la cause de l’erreur RequestDisallowedByPolicy. Il fournit également des solutions pour la résoudre.

## <a name="symptom"></a>Symptôme

Au cours du déploiement, il se peut que vous receviez une erreur **RequestDisallowedByPolicy** vous empêchant de créer les ressources. L’exemple ci-après illustre l’erreur :

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Résolution de problèmes

Pour extraire des informations sur la stratégie qui a bloqué votre déploiement, utilisez l’une des méthodes suivantes :

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dans PowerShell, fournissez cet identificateur de stratégie en tant que paramètre `Id` pour extraire des informations sur la stratégie qui a bloqué votre déploiement.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

Dans Azure CLI, spécifiez le nom de la définition de stratégie :

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Solution

Pour des raisons de sécurité ou de conformité, les administrateurs de votre abonnement ont la possibilité d’affecter des stratégies qui limitent la façon dont les ressources sont déployées. Par exemple, votre abonnement peut être soumis à une stratégie qui empêche la création d’adresses IP publiques, de groupes de sécurité réseau, d’itinéraires définis par l’utilisateur ou de tables d’itinéraires. Le message d’erreur de la section **Symptômes** indique le nom de la stratégie.
Pour résoudre ce problème, passez en revue les stratégies liées aux ressources et déterminez comment déployer des ressources conformes.

Pour plus d’informations, consultez les articles suivants :

- [Présentation d’Azure Policy](../governance/policy/overview.md)
- [Créer et gérer des stratégies pour assurer la conformité](../governance/policy/tutorials/create-and-manage.md)
