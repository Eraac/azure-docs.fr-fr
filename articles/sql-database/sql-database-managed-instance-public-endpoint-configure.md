---
title: Configurer un point de terminaison public - Azure SQL Database Managed Instance | Microsoft Docs
description: Découvrez comment configurer un point de terminaison public pour les instances gérées
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: d3e68a5287e59c576f85491e6e5eba33fac080ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65465159"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Configurer un point de terminaison public dans Azure SQL Database Managed Instance

Un point de terminaison public pour une [instance gérée](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) permet l’accès aux données de votre instance gérée depuis un emplacement extérieur au [réseau virtuel](../virtual-network/virtual-networks-overview.md). Vous pouvez accéder à votre instance gérée à partir de services Azure multi-locataire tels que Power BI, Azure App Service ou un réseau local. Comme vous utilisez le point de terminaison public sur une instance gérée, vous n’avez pas besoin de recourir à un VPN, ce qui peut aider à éviter les problèmes de débit VPN.

Cet article porte sur les points suivants :

> [!div class="checklist"]
> - Activer un point de terminaison public pour votre instance gérée dans le Portail Microsoft Azure
> - Activer un point de terminaison public pour votre instance gérée à l’aide de PowerShell
> - Configurer votre groupe de sécurité réseau d’instance gérée pour autoriser le trafic vers le point de terminaison public de l’instance gérée
> - Obtenir la chaîne de connexion du point de terminaison public de l’instance gérée

## <a name="permissions"></a>Autorisations

En raison de la sensibilité des données qui se trouvent sur une instance gérée, la configuration permettant d’activer le point de terminaison public de ce type d’instance repose sur un processus en deux étapes. Cette mesure de sécurité est conforme à la séparation des tâches :

- L’activation d’un point de terminaison public sur une instance gérée doit être effectuée par l’administrateur de cette instance. Cet administrateur est indiqué sur la page **Vue d’ensemble** de votre ressource d’instance gérée SQL.
- L’autorisation du trafic à l’aide d’un groupe de sécurité réseau doit être effectuée par un administrateur réseau. Pour en savoir plus, consultez la liste des [permissions du groupe de sécurité réseau](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Activation d’un point de terminaison public pour une instance gérée dans le Portail Microsoft Azure

1. Démarrez le Portail Microsoft Azure sur <https://portal.azure.com/.>
1. Ouvrez le groupe de ressources avec l’instance gérée, puis sélectionnez **l’instance gérée SQL** sur laquelle vous souhaitez configurer le point de terminaison.
1. Dans la page de paramètres **Sécurité**, sélectionnez l’onglet **Réseau virtuel**.
1. Dans la page de configuration du réseau virtuel, sélectionnez **Activer**, puis cliquez sur l’icône **Enregistrer** afin de mettre à jour la configuration.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Activation d’un point de terminaison public pour une instance gérée à l’aide de PowerShell

### <a name="enable-public-endpoint"></a>Activer un point de terminaison public

Exécutez les commandes PowerShell suivantes. Remplacez la valeur **subscription-id** par l’identifiant de votre abonnement. Remplacez également la valeur **rg-name** par le nom du groupe de ressources de votre instance gérée, puis remplacez **mi-name** par le nom de cette dernière.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Désactiver un point de terminaison public

Pour désactiver un point de terminaison public à l’aide de PowerShell, vous devez exécuter la commande suivante (sans oublier de fermer le groupe de sécurité réseau du port entrant 3342, s’il est configuré) :

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Autoriser le trafic du point de terminaison public sur le groupe de sécurité réseau

1. Si la page de configuration de l’instance gérée est toujours ouverte, accédez à l’onglet **Vue d’ensemble**. Dans le cas contraire, revenez à votre ressource **d’instance gérée SQL**. Sélectionnez le lien **Réseau/sous-réseau virtuel**, qui vous permet d’accéder à la page de configuration du réseau virtuel.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Sélectionnez l’onglet **Sous-réseaux** figurant dans le volet de configuration du réseau virtuel, sur la gauche, et notez le **GROUPE DE SÉCURITÉ** de votre instance gérée.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Revenez au groupe de ressources contenant votre instance gérée. Vous devez voir le nom du **groupe de sécurité réseau** indiqué ci-dessus, que vous avez noté. Sélectionnez ce nom pour accéder à la page de configuration du groupe de sécurité réseau.

1. Sélectionnez l’onglet **Règles de sécurité de trafic entrant**, et **ajoutez** une règle ayant une priorité plus élevée que la règle **deny_all_inbound** avec les paramètres suivants : </br> </br>

    |Paramètre  |Valeur suggérée  |Description  |
    |---------|---------|---------|
    |**Source**     |N’importe quelle adresse IP ou balise de service         |<ul><li>Pour les services Azure tels que Power BI, sélectionnez la balise de service Azure Cloud</li> <li>Pour votre ordinateur ou une machine virtuelle Azure, utilisez l’adresse IP NAT.</li></ul> |
    |**Plages de ports source**     |*         |Laissez la valeur * (tout) pour cette option, car les ports sources sont généralement alloués de manière dynamique et, de ce fait, imprévisibles |
    |**Destination**     |Quelconque         |En conservant la valeur Tout pour le paramètre Destination, vous autorisez le trafic au sein du sous-réseau de l’instance gérée |
    |**Plages de ports de destination**     |3342         |Définissez la portée du port de destination sur 3342, qui est le point de terminaison TDS public de l’instance gérée |
    |**Protocole**     |TCP         |L’instance gérée utilise le protocole TCP pour TDS |
    |**Action**     |AUTORISER         |Autorisez le trafic entrant vers une instance gérée par le biais du point de terminaison public |
    |**Priorité**     |1300         |Assurez-vous que cette règle présente une priorité plus élevée que la règle **deny_all_inbound** |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Le port 3342 est utilisé pour les connexions du point de terminaison public à l’instance gérée ; il ne peut pas être changé à ce stade.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Obtention de la chaîne de connexion du point de terminaison public de l’instance gérée

1. Accédez à la page de configuration de l’instance gérée SQL qui a été activée pour le point de terminaison public. Sélectionnez l’onglet **Chaînes de connexion** sous la configuration **Paramètres**.
1. Notez que le nom d’hôte du point de terminaison public présente le format <nom_mi>. **public**. <zone_dns>. database.windows.net et que le port utilisé pour la connexion est 3342.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrir comment [utiliser une instance gérée Azure SQL Database en toute sécurité avec un point de terminaison public](sql-database-managed-instance-public-endpoint-securely.md).