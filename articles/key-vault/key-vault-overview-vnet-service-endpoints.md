---
title: Points de terminaison de service de réseau virtuel pour Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Vue d’ensemble des points de terminaison de service de réseau virtuel pour Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: barbkess
ms.date: 01/02/2019
ms.service: key-vault
ms.topic: conceptual
ms.openlocfilehash: 45499dac3cc50e2b6e79f9ebcb1bc3e7b4330beb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165848"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Points de terminaison de service de réseau virtuel pour Azure Key Vault

Les points de terminaison de service de réseau virtuel pour Azure Key Vault permettent de restreindre l’accès à un réseau virtuel spécifié. Les points de terminaison vous permettent également de restreindre l’accès à une liste de plages d’adresses IPv4 (Internet Protocol version 4). L’accès est refusé à tout utilisateur se connectant à votre coffre de clés en dehors de ces sources.

Il existe une exception importante à cette restriction. Si un utilisateur a choisi d’autoriser les services Microsoft approuvés, les connexions à partir de ces services se font à travers le pare-feu. Par exemple, ces services incluent Office 365 Exchange Online, Office 365 SharePoint Online, Calcul Azure, Azure Resource Manager et Sauvegarde Azure. Ces utilisateurs doivent néanmoins toujours présenter un jeton Azure Active Directory valide et disposer d’autorisations (configurées en tant que stratégies d’accès) pour effectuer l’opération demandée. Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Scénarios d’utilisation

Vous pouvez configurer des [pare-feux et réseaux virtuels Key Vault](key-vault-network-security.md) pour refuser par défaut l’accès au trafic en provenance de tous les réseaux (y compris le trafic Internet). Vous pouvez accorder l’accès au trafic en provenance de réseaux virtuels Azure ou de plages d’adresses IP Internet publiques spécifiques, ce qui vous permet de créer une limite réseau sécurisée pour vos applications.

> [!NOTE]
> Les règles de pare-feu et de réseau virtuel Key Vault s’appliquent seulement au [plan de données](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) de Key Vault. Les opérations du plan de contrôle Key Vault (comme les opérations de création, de suppression et de modification, la définition de stratégies d’accès, la définition de pare-feux et de règles de réseau virtuel) ne sont pas affectées par les règles de pare-feux et de réseau virtuel.

Voici quelques exemples d’utilisation de points de terminaison de service :

* Vous utilisez Key Vault pour stocker des clés de chiffrement, des secrets d’application et des certificats, et vous voulez bloquer l’accès à votre coffre de clés depuis l’Internet public.
* Vous voulez verrouiller l’accès à votre coffre de clés, afin que seule votre application ou une liste restreinte d’hôtes désignés puissent se connecter à votre coffre de clés.
* Vous avez une application s’exécutant dans votre réseau virtuel Azure, qui est verrouillé pour tout trafic entrant et sortant. Votre application doit néanmoins toujours se connecter au coffre de clés pour extraire des secrets ou certificats, ou pour utiliser des clés de chiffrement.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Configurer les pare-feux et réseaux virtuels d’Azure Key Vault

Voici les étapes requises pour configurer les pare-feux et réseaux virtuels. Ces étapes s’appliquent si vous utilisez PowerShell, Azure CLI ou le portail Azure.

1. Activez la [journalisation Key Vault](key-vault-logging.md) pour voir des journaux d’activité d’accès détaillés. Ceci vous aide à poser des diagnostics quand des règles de pare-feux et de réseau virtuel empêchent l’accès à un coffre de clés. (Cette étape est facultative mais fortement recommandée.)
2. Activez des **points de terminaison de service pour Key Vault** pour les réseaux et sous-réseaux virtuels cibles.
3. Définissez des règles de pare-feux et de réseau virtuel pour un coffre de clés, afin de restreindre l’accès à ce coffre de clés à partir de réseaux virtuels, sous-réseaux et plages d’adresses IPv4 spécifiques.
4. Si ce coffre de clés doit être accessible à tous les services Microsoft approuvés, vous devez activer l’option autorisant des **services Microsoft approuvés** à se connecter à Key Vault.

Pour plus d’informations, consultez [Configurer les pare-feux et réseaux virtuels d’Azure Key Vault](key-vault-network-security.md).

> [!IMPORTANT]
> Une fois que les règles de pare-feu sont effectives, les utilisateurs peuvent effectuer des opérations du [plan de données](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) de Key Vault seulement quand leurs demandes proviennent de réseaux virtuels ou de plages d’adresses IPv4 autorisés. Ceci s’applique également à l’accès au coffre de clés à partir du portail Azure. Si des utilisateurs peuvent accéder à un coffre de clés à partir du portail Azure, il ne peuvent pas lister les clés/secrets/certificats si leur ordinateur client ne figure pas dans la liste autorisée. Ceci affecte également l’accès au sélecteur de compte Key Vault par d’autres services Azure. Des utilisateurs qui peuvent voir la liste des coffres de clés ne peuvent pas lister les clés si des règles de pare-feu bloquent leur ordinateur client.


> [!NOTE]
> Notez les limitations de configuration suivantes :
> * Un maximum de 127 règles de réseau virtuel et 127 règles IPv4 sont autorisées. 
> * Les petites plages d’adresses qui utilisent les tailles de préfixe « /31 » ou « /32 » ne sont pas prises en charge. Au lieu de cela, configurez ces plages avec des règles d’adresses IP individuelles.
> * Les règles de réseau IP sont autorisées uniquement pour les adresses IP publiques. Les plages d’adresses IP réservées aux réseaux privés (comme défini dans RFC 1918) ne sont pas autorisées dans les règles IP. Les réseaux privés incluent des adresses qui commencent par **10.** , **172.16-31.** et **192.168.** . 
> * Seules les adresses IPv4 sont prises en charge pour le moment.

## <a name="trusted-services"></a>Services approuvés

Voici une liste de services approuvés qui sont autorisés à accéder à un coffre de clés si l’option **Autoriser les services approuvés** est activée.

|Service approuvé|Scénarios d’utilisation|
| --- | --- |
|Service de déploiement de machines virtuelles Azure|[Déployer des certificats sur des machines virtuelles à partir d’un coffre de clés géré par le client](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)|
|Service de déploiement modèle Azure Resource Manager|[Passage de valeurs sécurisées lors du déploiement](../azure-resource-manager/resource-manager-keyvault-parameter.md)|
|Service de chiffrement de volume Azure Disk Encryption|Autoriser l’accès à la clé BitLocker (machine virtuelle Windows) ou à la phrase secrète DM (machine virtuelle Linux), et à la clé de chiffrement lors du déploiement de machines virtuelles. Ceci active [Azure Disk Encryption](../security/azure-security-disk-encryption.md).|
|Sauvegarde Azure|Autoriser la sauvegarde et restauration des clés et des secrets pertinents lors de la sauvegarde de Machines virtuelles Azure avec [Sauvegarde Azure](../backup/backup-introduction-to-azure-backup.md).|
|Exchange Online et SharePoint Online|Autoriser l’accès à la clé du client pour Azure Storage Service Encryption avec une [clé de client](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|Autoriser l’accès à la clé de locataire pour [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Déploiement d’un certificat Azure Web App via Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Transparent Data Encryption avec prise en charge de BYOK pour Azure SQL Database et Data Warehouse](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Stockage Azure|[Chiffrement du service de stockage à l’aide de clés gérées par le client dans Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Chiffrement de données dans Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) avec une clé gérée par le client.|
|Azure Databricks|[Service d'analyse rapide, simple et collaboratif basé sur Apache Spark](../azure-databricks/what-is-azure-databricks.md)|
|Gestion des API Azure|[Déployer des certificats pour un domaine personnalisé à partir de Key Vault à l’aide de MSI](../api-management/api-management-howto-use-managed-service-identity.md#use-the-managed-service-identity-to-access-other-resources)|



> [!NOTE]
> Vous devez configurer les stratégies Key Vault appropriées pour autoriser les services correspondants à accéder à Key Vault.

## <a name="next-steps"></a>Étapes suivantes

* [Sécurisez votre coffre de clés](key-vault-secure-your-key-vault.md)
* [Configurer les pare-feux et réseaux virtuels d’Azure Key Vault](key-vault-network-security.md)
