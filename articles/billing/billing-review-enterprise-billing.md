---
title: Passer en revue les données de facturation d’un Accord de Mise en Œuvre Entreprise avec l’API REST | Microsoft Docs
description: Découvrez comment utiliser les API REST Azure pour passer en revue les informations de facturation de l’Accord de Mise en Œuvre Entreprise.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: banders
ms.openlocfilehash: 25d9b48696dc2a83ea0ba77c1be2c7aad7627fff
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443150"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Passer en revue la facturation de l’Accord de Mise en Œuvre Entreprise avec les API REST

Les API de création de rapports Azure vous aident à passer en revue et à gérer vos coûts Azure.

Dans cet article, vous apprendrez à récupérer les informations de facturation associées aux comptes de facturation, au service ou aux comptes d’inscription Accord Entreprise à l’aide d’API REST Azure. 

## <a name="individual-account-billing"></a>Facturation des comptes individuels

Pour obtenir des détails d’utilisation relatifs aux comptes dans un service :

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Le paramètre `{billingAccountId}` est requis et doit contenir l’ID du compte.

Les en-têtes suivants sont requis : 

|En-tête de requête|Description|  
|--------------------|-----------------|  
|*Content-Type :*|Requis. Défini sur `application/json`.|  
|*Authorization :*|Requis. Défini sur une `Bearer` [clé d’API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) valide. |  

Cet exemple montre un appel synchrone qui retourne des informations détaillées pour le cycle de facturation actuel. Pour des raisons de performances, les appels synchrones retournent des informations pour le mois dernier.  Vous pouvez également appeler [l’API de façon asynchrone](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) pour retourner des données pour 36 mois.


## <a name="response"></a>response  

Le code d’état 200 (OK) est retourné pour indiquer une réponse correcte, qui contient une liste de coûts détaillés pour le compte.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

Cet exemple est abrégé. Consultez [Get usage detail for a billing account](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist) (Obtenir les détails d’utilisation pour un compte de facturation) pour obtenir une description complète de chaque champ de réponse et de la gestion des erreurs.

## <a name="department-billing"></a>Facturation de service 

Obtenez la synthèse des détails d’utilisation relatifs à tous les comptes d’un service. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Le paramètre `{departmentId}` est requis et doit contenir l’ID du service dans le compte d’inscription.

Les en-têtes suivants sont requis : 

|En-tête de requête|Description|  
|--------------------|-----------------|  
|*Content-Type :*|Requis. Défini sur `application/json`.|  
|*Authorization :*|Requis. Défini sur une `Bearer` [clé d’API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) valide. |  

Cet exemple montre un appel synchrone qui retourne des informations détaillées pour le cycle de facturation actuel. Pour des raisons de performances, les appels synchrones retournent des informations pour le mois dernier.  Vous pouvez également appeler [l’API de façon asynchrone](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) pour retourner des données pour 36 mois.

### <a name="response"></a>response  

Le code d’état 200 (OK) est retourné pour indiquer une réponse correcte, qui contient une liste des informations d’utilisation détaillées et ainsi que les coûts associés à une période de facturation donnée et un ID de facture pour le service.


L’exemple suivant montre la sortie de l’API REST pour le service `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

Cet exemple est abrégé. Consultez [Get usage detail for a department](/rest/api/consumption/usagedetails/list#departmentusagedetailslist) (Obtenir les détails d’utilisation pour un service) pour obtenir une description complète de chaque champ de réponse et de la gestion des erreurs.

## <a name="enrollment-account-billing"></a>Facturation du compte d’inscription

Obtenez la synthèse des détails d’utilisation pour le compte d’inscription.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Le paramètre `{enrollmentAccountId}` est requis et doit contenir l’ID du compte d’inscription.

Les en-têtes suivants sont requis : 

|En-tête de requête|Description|  
|--------------------|-----------------|  
|*Content-Type :*|Requis. Défini sur `application/json`.|  
|*Authorization :*|Requis. Défini sur une `Bearer` [clé d’API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) valide. |  

Cet exemple montre un appel synchrone qui retourne des informations détaillées pour le cycle de facturation actuel. Pour des raisons de performances, les appels synchrones retournent des informations pour le mois dernier.  Vous pouvez également appeler [l’API de façon asynchrone](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) pour retourner des données pour 36 mois.

### <a name="response"></a>response  

Le code d’état 200 (OK) est retourné pour indiquer une réponse correcte, qui contient une liste des informations d’utilisation détaillées et ainsi que les coûts associés à une période de facturation donnée et un ID de facture pour le service.

L’exemple suivant montre la sortie de l’API REST pour l’inscription d’entreprise `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
``` 

Cet exemple est abrégé. Consultez [Get usage detail for an enrollment account](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist) (Obtenir les détails d’utilisation pour un compte d’inscription) pour obtenir une description complète de chaque champ de réponse et de la gestion des erreurs.

## <a name="next-steps"></a>Étapes suivantes 
- Consulter [Présentation des rapports d’entreprise](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Examiner [l’API REST de facturation d’entreprise](https://docs.microsoft.com/rest/api/billing/)   
- [Bien démarrer avec l’API REST Azure](https://docs.microsoft.com/rest/api/azure/)   
