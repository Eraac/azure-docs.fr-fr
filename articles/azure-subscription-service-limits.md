---
title: Quotas et limites d’abonnement Azure
description: Fournit une liste des abonnements Azure et des limites, quotas et contraintes de service habituels. Cet article inclut des informations sur la façon d’augmenter les limites ainsi que les valeurs maximales.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 05/30/2019
ms.author: byvinyal
ms.openlocfilehash: d1043f254d2a2cc2804ab8a4d68770b6d57cbed4
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295937"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Abonnement Azure et limites, quotas et contraintes de service
Ce document répertorie quelques-unes des limites de Microsoft Azure les plus courantes, parfois appelées des quotas. Ce document ne couvre pas actuellement tous les services Azure. Ces limites seront étendues et mises à jour dans des prochaines versions pour couvrir plus de services.

Pour en savoir plus sur la tarification Azure, consultez [Tarification Azure](https://azure.microsoft.com/pricing/). Elle vous permet d'estimer vos coûts à l’aide de la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/). Vous pouvez également accéder à la page de détails de tarification pour un service particulier, par exemple, [Machines virtuelles Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Pour des conseils sur la gestion des coûts, consultez [Éviter les coûts inattendus avec la gestion de la facturation et des coûts dans Azure](billing/billing-getting-started.md).

> [!NOTE]
> Si vous souhaitez élever la limite ou le quota au-dessus de la limite par défaut, [ouvrez une demande de service clientèle en ligne gratuitement](azure-resource-manager/resource-manager-quota-errors.md). Les limites ne peuvent pas être supérieures à la valeur Limite maximale indiquée dans les tableaux suivants. Si aucune colonne de limite maximale n’est présente, la ressource spécifiée ne possède pas de limites ajustables.
>
> Les [abonnements d’essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p) ne permettent pas de bénéficier d’augmentations de la limite ou du quota. Si vous disposez d’un [abonnement d’essai gratuit](https://azure.microsoft.com/offers/ms-azr-0044p), vous pouvez le mettre à niveau vers un abonnement avec [paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/). Pour en savoir plus, consultez [Mettre à niveau votre abonnement d'essai gratuit Azure vers un abonnement avec paiement à l’utilisation](billing/billing-upgrade-azure-subscription.md) et [FAQ sur l’abonnement d’essai gratuit](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Limites et Azure Resource Manager
Il est désormais possible de combiner plusieurs ressources Azure en un seul groupe de ressources Azure. Lorsque vous utilisez des groupes de ressources, les limites qui étaient auparavant mondiales sont désormais gérées au niveau régional avec Azure Resource Manager. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

Dans la liste des limites ci-dessous, une nouvelle table reflète les différences des limites lorsque vous utilisez Azure Resource Manager. Par exemple, vous disposez d’une table **Limites d’abonnement** et d’une table **Limites d’abonnement – Azure Resource Manager**. Lorsqu'une limite s'applique aux deux scénarios, elle apparaît uniquement dans la première table. Sauf indication contraire, les limites sont globales dans toutes les régions.

> [!NOTE]
> Les quotas de ressources des les groupes de ressources Azure sont accessibles par région par le biais de votre abonnement et non par abonnement, comme les quotas de gestion des services. Nous allons utiliser des quotas de processeurs virtuels pour l’exemple. Pour demander une augmentation des quotas de prise en charge des processeurs virtuels, vous devez déterminer le nombre de processeurs virtuels souhaités et les régions concernées. Effectuez ensuite une demande spécifique de quotas de processeurs virtuels de groupes de ressources Azure pour la quantité et les régions de votre choix. Si vous avez besoin de 30 processeurs virtuels dans la région Europe Ouest pour y exécuter votre application, demandez spécifiquement 30 processeurs virtuels dans cette région. Votre quota de processeurs virtuels n’est pas augmenté dans les autres régions ; seule la région Europe Ouest dispose du quota de 30 processeurs virtuels.
> <!-- -->
> Par conséquent, déterminez les quotas de groupes de ressources Azure pour votre charge de travail dans une région. Puis, demandez ce volume dans chaque région où vous envisagez d’effectuer un déploiement. Pour vous aider à déterminer vos quotas actuels pour des régions spécifiques, consultez [Résoudre les problèmes de déploiement](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>Limites de service spécifique
* [Active Directory](#active-directory-limits)
* [Gestion des API](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Automation](#automation-limits)
* [Cache Azure pour Redis](#azure-cache-for-redis-limits)
* [Azure Cloud Services](#azure-cloud-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Database pour MySQL](#azure-database-for-mysql)
* [Base de données Azure pour PostgreSQL](#azure-database-for-postgresql)
* [DNS Azure](#azure-dns-limits)
* [Pare-feu Azure](#azure-firewall-limits)
* [Azure Functions](#functions-limits)
* [Azure Kubernetes Service](#azure-kubernetes-service-limits)
* [Service Azure Machine Learning](#azure-machine-learning-service-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Monitor](#azure-monitor-limits)
* [Azure Policy](#azure-policy-limits)
* [Azure Search](#azure-search-limits)
* [Azure SignalR Service](#azure-signalr-service-limits)
* [Sauvegarde](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [Container Instances](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Content Delivery Network](#content-delivery-network-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [Event Grid](#event-grid-limits)
* [Hubs d'événements](#event-hubs-limits)
* [Front Door Service](#azure-front-door-service-limits)
* [Identity Manager](#identity-manager-limits)
* [IoT Hub](#iot-hub-limits)
* [Service IoT Hub Device Provisioning](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Media Services](#media-services-limits)
* [Mobile Services](#mobile-services-limits)
* [Azure Multi-Factor Authentication](#multi-factor-authentication-limits)
* [Mise en réseau](#networking-limits)
  * [Application Gateway](#application-gateway-limits)
  * [DNS Azure](#azure-dns-limits)
  * [Azure Front Door Service](#azure-front-door-service-limits)
  * [Pare-feu Azure](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Équilibreur de charge](#load-balancer)
  * [Adresse IP publique](#publicip-address)
  * [Network Watcher](#network-watcher-limits)
  * [Traffic Manager](#traffic-manager-limits)
  * [Réseau virtuel](#networking-limits)
* [Notification Hubs](#notification-hubs-limits)
* [Groupe de ressources](#resource-group-limits)
* [Contrôle d’accès en fonction du rôle](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [Base de données SQL](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Stockage](#storage-limits)
* [Système StorSimple](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Abonnement](#subscription-limits)
* [Machines virtuelles](#virtual-machines-limits)
* [Groupes de machines virtuelles identiques](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Limites d’abonnement
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Limites d’abonnement – Gestion des services Azure (modèle de déploiement classique)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limites d’abonnement – Azure Resource Manager
Les limites suivantes s'appliquent lorsque vous utilisez Azure Resource Manager et les groupes de ressources Azure. Les limites qui restent identiques avec Azure Resource Manager ne sont pas répertoriées. Reportez-vous au tableau précédent pour consulter ces limites.

Pour plus d’informations sur l’API Resource Manager et les limites d’écriture, voir [Limitation des requêtes de Resource Manager](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limites de groupe de ressources
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limites de machines virtuelles
#### <a name="virtual-machines-limits"></a>Limites de machines virtuelles
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limites de machines virtuelles - Azure Resource Manager
Les limites suivantes s'appliquent lorsque vous utilisez Azure Resource Manager et les groupes de ressources Azure. Les limites qui restent identiques avec Azure Resource Manager ne sont pas répertoriées. Reportez-vous au tableau précédent pour consulter ces limites.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Limites de Shared Image Gallery

Certaines limites par abonnement ont été définies pour le déploiement de ressources à l’aide des galeries d’images partagées :
- 100 galeries d’images partagées par abonnement et par région
- 1 000 définitions d’images par abonnement et par région
- 10 000 versions d’image par abonnement et par région

### <a name="virtual-machine-scale-sets-limits"></a>Limites des groupes de machines virtuelles identiques
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Limites de Container Instances
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Limites du registre de conteneurs
Le tableau suivant détaille les fonctionnalités et les limites des [niveaux de service](./container-registry/container-registry-skus.md) De base, Standard et Premium.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Limites d'Azure Kubernetes Service
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-service-limits"></a>Limites du service Azure Machine Learning
Les valeurs les plus récentes des quotas de calcul Azure Machine Learning sont disponibles dans la [page des quotas Azure Machine Learning](../articles/machine-learning/service/how-to-manage-quotas.md)

### <a name="networking-limits"></a>Limites de mise en réseau
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>Limites d’ExpressRoute
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="application-gateway-limits"></a>Limites d’Application Gateway

Sauf mention contraire, le tableau suivant s’applique aux références SKU v1, v2, Standard et WAF.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Limites de Network Watcher
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Limites de Traffic Manager
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-dns-limits"></a>Limites d'Azure DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Limites du Pare-feu Azure
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Limites d’Azure Front Door Service
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Limites de stockage
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Pour plus d’informations sur les limites des comptes de stockage, consultez la page [Objectifs de performance et évolutivité de Stockage Azure](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Limites de fournisseur de ressources de stockage 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Limites de Stockage Blob Azure
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Limite d’Azure Files
Pour plus d’informations sur les limites d'Azure Files, consultez la page [Objectifs de performance et évolutivité d'Azure Files](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Limites d’Azure File Sync
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Limites de Stockage File d’attente Azure
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Limites de Stockage Table Azure
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Limites du nombre de disques de machine virtuelle
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Pour plus d’informations, consultez [Tailles de machines virtuelles](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Disques de machines virtuelles gérées

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Disques de machines virtuelles non gérées

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Limites d'Azure Cloud Services
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Limites App Service
Les limites App Service suivantes incluent celles de Web Apps, Mobile Apps et API Apps.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Limites d'Azure Functions
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Limites de planificateur
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limites Azure Batch
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Limites de BizTalk Services
Le tableau suivant indique les limites d’Azure BizTalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Limites d’Azure Cosmos DB
Pour les limites d’Azure Cosmos DB, consultez [Limites dans Azure Cosmos DB](cosmos-db/concepts-limits.md).

### <a name="azure-database-for-mysql"></a>Azure Database pour MySQL
Pour les limites d’Azure Database pour MySQL, voir [Limitations dans Azure Database pour MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Azure Database pour PostgreSQL
Pour les limites d’Azure Database pour PostgreSQL, voir [Limitations dans Azure Database pour PostgreSQL](postgresql/concepts-limits.md).

### <a name="azure-search-limits"></a>Limites de Recherche Azure
Les niveaux tarifaires déterminent la capacité et les limites de votre service de recherche. Les niveaux sont les suivants :

* **Gratuit** : service mutualisé, partagé avec d’autres abonnés Azure, destiné à des projets d’évaluation et de développement de petite taille.
* **De base** : fournit des ressources de calcul dédiées pour des charges de travail de production à plus petite échelle, avec jusqu’à trois réplicas pour les charges de travail de requête hautement disponible.
* **Standard**, qui inclut S1, S2, S3 et S3 haute densité, est approprié pour des charges de travail de production de plus grande taille. Plusieurs niveaux existent dans le niveau Standard pour vous permettre de choisir une configuration de ressources parfaitement adaptée à votre profil de charge de travail.

**Limites par abonnement**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limites par service de recherche**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Pour en savoir plus sur les limites à un niveau plus granulaire, notamment la taille du document, les requêtes par seconde, les clés, les requêtes et les réponses, consultez [Limites de service d’Azure Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limites de Media Services
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Limite du réseau de distribution de contenu
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limites Mobile Services
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="azure-monitor-limits"></a>Limites d'Azure Monitor

#### <a name="alerts"></a>Alertes

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-alerts.md)]

#### <a name="action-groups"></a>Groupes d’actions

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-action-groups.md)]

#### <a name="log-analytics-workspaces"></a>Espaces de travail Log Analytics

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-workspaces.md)]

#### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-app-insights.md)]




### <a name="notification-hubs-limits"></a>Limites de Notification Hubs
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limites de concentrateurs d’événements
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limites de Service Bus
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Limites de hub IoT (IoT Hub)
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Limites du service IoT Hub Device Provisioning
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Limites de Data Factory
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Limites Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Limite Data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Limites Database Migration Service
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Limites Stream Analytics
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Limites d'Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Limites d’Event Grid
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Limites d’Azure Maps
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Limites d’Azure Policy
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>Limites du système StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="backup-limits"></a>Limites Azure Backup
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Limites d'Azure SignalR Service
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Limites Azure Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="api-management-limits"></a>Limites Gestion des API
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Limites du Cache Azure pour Redis
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limites du coffre de clés
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Limites de Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limites du service Automation
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Limites d'Identity Manager
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Limites de contrôle d’accès en fonction du rôle
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Limites de base de données SQL
Pour connaître les limites SQL Database, consultez [Limites de ressources SQL Database pour les bases de données uniques](sql-database/sql-database-vcore-resource-limits-single-databases.md), [Limites de ressources SQL Database pour les pools élastiques et les bases de données mises en pool](sql-database/sql-database-vcore-resource-limits-elastic-pools.md) et [Limites de ressources de SQL Database pour les instances gérées](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Limites SQL Data Warehouse
Pour connaître les limites SQL Data Warehouse, consultez [Limites des ressources SQL Data Warehouse](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Voir aussi
- [Comprendre les limites et les augmentations Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Tailles de machine virtuelle et de service cloud pour Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Tailles pour Services cloud Azure](cloud-services/cloud-services-sizes-specs.md)
