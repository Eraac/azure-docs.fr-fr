---
title: Identité managée pour Data Factory | Microsoft Docs
description: En savoir plus sur l’identité managée pour Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: 3c1bb38eb12ce77d172257706cd458cebda4bd8c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66153421"
---
# <a name="managed-identity-for-data-factory"></a>Identité managée pour Data Factory

Cet article vous permet de mieux comprendre ce qu’est l’identité managée pour Data Factory (anciennement Managed Service Identity) et comment elle fonctionne.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Vue d'ensemble

Lors de la création d’une fabrique de données, une identité managée est créée en même temps que la fabrique. L’identité managée est une application managée qui est inscrite auprès d’Azure Active Directory et qui représente la fabrique de données en question.

L’identité managée pour Data Factory bénéficie des fonctionnalités suivantes :

- [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md), pour laquelle l’identité managée de fabrique de données est utilisée pour l’authentification auprès d’Azure Key Vault.
- Connecteurs, notamment [Stockage Blob Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure SQL Database](connector-azure-sql-database.md) et [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Activité web](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Générer une identité managée

L’identité managée pour Data Factory est générée de la façon suivante :

- Lors de la création d’une fabrique de données via le **Portail Azure ou PowerShell**, l’identité managée est toujours créée automatiquement.
- Lors de la création d’une fabrique de données grâce au **kit de développement logiciel (SDK)** , l’identité managée n’est créée que si vous spécifiez « Identity = new FactoryIdentity() » durant la création de l’objet usine. Consultez l’exemple dans [Démarrage rapide .NET - Créer une fabrique de données](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Lors de la création d’une fabrique de données grâce à l’**API REST**, l’identité managée n’est créée que si vous le spécifiez la section « identity » dans le corps de la requête. Consultez l’exemple dans [Démarrage rapide REST - Créer une fabrique de données](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Si vous constatez que votre fabrique de données n’est pas associée à une identité managée après l’instruction [retrieve managed identity](#retrieve-managed-identity), vous pouvez en générer une explicitement par programmation, en mettant à jour la fabrique de données avec l’initiateur d’identité :

- [Générer l’identité managée à l’aide de PowerShell](#generate-managed-identity-using-powershell)
- [Générer l’identité managée à l’aide de REST API](#generate-managed-identity-using-rest-api)
- [Générer l’identité managée avec un modèle Azure Resource Manager](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Générer l’identité managée à l’aide du SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- L’identité managée ne peut pas être modifiée. La mise à jour d’une fabrique de données pour laquelle vous disposez déjà d’une identité managée n’a aucun effet ; l’identité managée reste inchangée.
>- Si vous mettez à jour une fabrique de données qui dispose déjà d’une identité managée, sans spécifier le paramètre « identity » dans l’objet fabrique, ou sans spécifier la section « identity » dans le corps de la requête REST, vous recevez un message d’erreur.
>- Lorsque vous supprimez une fabrique de données, l’identité managée associée est également supprimée.

### <a name="generate-managed-identity-using-powershell"></a>Générer l’identité managée à l’aide de PowerShell

Appelez la commande **Set-AzDataFactoryV2** à nouveau, vous verrez alors les champs « identity » qui viennent d’être générés :

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Générer l’identité managée à l’aide de REST API

Appelez ensuite l’API avec la section« identity » dans le corps de la requête :

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Corps de la requête** : add "identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Réponse** : l’identité managée est créée automatiquement, et la section « identity » est remplie en conséquence.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Générer l’identité managée avec un modèle Azure Resource Manager

**Modèle** : add "identity": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Générer l’identité managée à l’aide du SDK

Appelez la fabrique de données pour créer ou mettre à jour la fonction avec Identity=new FactoryIdentity(). Exemple de code utilisant NET :

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Récupérer une identité managée

Vous pouvez récupérer l’identité managée à partir du portail Azure ou par programmation. Les sections suivantes vous montrent quelques exemples.

>[!TIP]
> Si vous ne voyez pas l’identité managée, [générez l’identité managée](#generate-managed-identity) en mettant à jour votre fabrique.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Récupérer l’identité managée à l’aide du portail Azure

Les informations sur l’identité managée se trouvent sur le portail Azure sous : votre fabrique de données -> Propriétés :

- ID d’objet de l’identité managée
- Locataire de l’identité managée
- **ID d’application de l’identité managée** : copiez cette valeur

![Récupérer une identité managée](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-managed-identity-using-powershell"></a>Récupérer l’identité managée à l’aide de PowerShell

L’ID principal et l’ID locataire de l’identité managée seront retournés quand vous aurez obtenu une fabrique de données spécifique comme suit :

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Copiez l’ID principal, puis exécutez la commande Azure Active Directory ci-dessous avec l’ID principal comme paramètre pour obtenir **l’ID d’application**, que vous allez utiliser pour obtenir l’accès :

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Étapes suivantes
Consultez les rubriques suivantes qui expliquent quand et comment utiliser l’identité managée de fabrique de données :

- [Stocker des informations d’identification dans Azure Key Vault](store-credentials-in-key-vault.md)
- [Copier des données vers ou depuis Azure Data Lake Storage Gen1 à l’aide d’Azure Data Factory](connector-azure-data-lake-store.md)

Pour plus d’informations sur les identités managées des ressources Azure, sur lesquelles l’identité managée de fabrique de données est basée, consultez [Que sont les identités managées pour les ressources Azure ?](/azure/active-directory/managed-identities-azure-resources/overview). 
