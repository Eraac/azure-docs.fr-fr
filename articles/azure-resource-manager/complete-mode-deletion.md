---
title: Suppression du mode complet Azure Resource Manager par type de ressource
description: Montre comment les types de ressources gèrent la suppression du mode complet dans les modèles Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 04/24/2019
ms.author: tomfitz
ms.openlocfilehash: 5ac442c0ae1e397fd1e8b58fdbcf61eb8712046c
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302841"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Suppression de ressources Azure pour les déploiements en mode complet
Cet article décrit la manière dont les types de ressources gèrent la suppression en dehors d'un modèle déployé en mode complet.

Les types de ressources indiqués par `Yes` sont supprimés lorsque le type n’est pas présent dans le modèle déployé en mode complet. 

Les types de ressources indiqués par `No` ne sont pas automatiquement supprimés lorsqu'ils ne sont pas présents dans le modèle, mais sont supprimés si la ressource parente est supprimée. Pour une description complète du comportement, consultez [Modes de déploiement Azure Resource Manager](deployment-modes.md).


## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | DomainServices | OUI | 
> | DomainServices/oucontainer | Non | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | diagnosticSettings | Non | 
> | diagnosticSettingsCategories | Non | 

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | supportProviders | Non | 

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | aadsupportcases | Non | 
> | addsservices | Non | 
> | agents | Non | 
> | anonymousapiusers | Non | 
> | configuration | Non | 
> | journaux d’activité | Non | 
> | reports | Non | 
> | services | Non | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | configurations | Non | 
> | generateRecommendations | Non | 
> | de films | Non | 
> | suppressions | Non | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | actionRules | Non | 
> | alertes | Non | 
> | alertsList | Non | 
> | alertsSummary | Non | 
> | alertsSummaryList | Non | 
> | smartDetectorAlertRules | Non | 
> | smartDetectorRuntimeEnvironments | Non | 
> | smartGroups | Non | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | servers | OUI | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | reportFeedback | Non | 
> | service | OUI | 
> | validateServiceName | Non | 

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | attestationProviders | Non | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | classicAdministrators | Non | 
> | denyAssignments | Non | 
> | elevateAccess | Non | 
> | locks | Non | 
> | Autorisations | Non | 
> | policyAssignments | Non | 
> | policyDefinitions | Non | 
> | policySetDefinitions | Non | 
> | providerOperations | Non | 
> | roleAssignments | Non | 
> | roleDefinitions | Non | 

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | automationAccounts | OUI | 
> | automationAccounts/configurations | OUI | 
> | automationAccounts/jobs | Non | 
> | automationAccounts/runbooks | OUI | 
> | automationAccounts/softwareUpdateConfigurations | Non | 
> | automationAccounts/webhooks | Non | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | environments | Non | 
> | environments/accounts | Non | 
> | environments/accounts/namespaces | Non | 
> | environments/accounts/namespaces/configurations | Non | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | b2cDirectories | OUI | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | registrations | OUI | 
> | registrations/customerSubscriptions | Non | 
> | registrations/products | Non | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | batchAccounts | OUI | 

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | billingAccounts | Non | 
> | billingAccounts/billingProfiles | Non | 
> | billingAccounts/billingProfiles/billingSubscriptions | Non | 
> | billingAccounts/billingProfiles/invoices | Non | 
> | billingAccounts/billingProfiles/invoices/pricesheet | Non | 
> | billingAccounts/billingProfiles/operationStatus | Non | 
> | billingAccounts/billingProfiles/paymentMethods | Non | 
> | billingAccounts/billingProfiles/policies | Non | 
> | billingAccounts/billingProfiles/pricesheet | Non | 
> | billingAccounts/billingProfiles/products | Non | 
> | billingAccounts/billingProfiles/transactions | Non | 
> | billingAccounts/billingSubscriptions | Non | 
> | billingAccounts/departments | Non | 
> | billingAccounts/eligibleOffers | Non | 
> | billingAccounts/enrollmentAccounts | Non | 
> | billingAccounts/invoices | Non | 
> | billingAccounts/invoiceSections | Non | 
> | billingAccounts/invoiceSections/billingSubscriptions | Non | 
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Non | 
> | billingAccounts/invoiceSections/importRequests | Non | 
> | billingAccounts/invoiceSections/initiateImportRequest | Non | 
> | billingAccounts/invoiceSections/initiateTransfer | Non | 
> | billingAccounts/invoiceSections/operationStatus | Non | 
> | billingAccounts/invoiceSections/products | Non | 
> | billingAccounts/invoiceSections/transfers | Non | 
> | billingAccounts/products | Non | 
> | billingAccounts/projects | Non | 
> | billingAccounts/projects/billingSubscriptions | Non | 
> | billingAccounts/projects/importRequests | Non | 
> | billingAccounts/projects/initiateImportRequest | Non | 
> | billingAccounts/projects/operationStatus | Non | 
> | billingAccounts/projects/products | Non | 
> | billingAccounts/transactions | Non | 
> | billingPeriods | Non | 
> | BillingPermissions | Non | 
> | billingProperty | Non | 
> | BillingRoleAssignments | Non | 
> | BillingRoleDefinitions | Non | 
> | CreateBillingRoleAssignment | Non | 
> | departments | Non | 
> | enrollmentAccounts | Non | 
> | importRequests | Non | 
> | importRequests/acceptImportRequest | Non | 
> | importRequests/declineImportRequest | Non | 
> | invoices | Non | 
> | transfers | Non | 
> | transfers/acceptTransfer | Non | 
> | transfers/declineTransfer | Non | 
> | transfers/operationStatus | Non | 
> | usagePlans | Non | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | mapApis | OUI | 
> | updateCommunicationPreference | Non | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | BizTalk | OUI | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | blueprintAssignments | Non | 
> | blueprintAssignments/assignmentOperations | Non | 
> | blueprintAssignments/operations | Non | 
> | blueprints | Non | 
> | blueprints/artifacts | Non | 
> | blueprints/versions | Non | 
> | blueprints/versions/artifacts | Non | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | botServices | OUI | 
> | botServices/channels | Non | 
> | botServices/connections | Non | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Redis | OUI | 
> | RedisConfigDefinition | Non | 

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | appliedReservations | Non | 
> | calculatePrice | Non | 
> | catalogs | Non | 
> | commercialReservationOrders | Non | 
> | reservationOrders | Non | 
> | reservationOrders/calculateRefund | Non | 
> | reservationOrders/merge | Non | 
> | reservationOrders/reservations | Non | 
> | reservationOrders/reservations/revisions | Non | 
> | reservationOrders/return | Non | 
> | reservationOrders/split | Non | 
> | reservationOrders/swap | Non | 
> | reservations | Non | 
> | les ressources | Non | 
> | validateReservationOrder | Non | 

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | edgenodes | Non | 
> | profiles | OUI | 
> | profiles/endpoints | OUI | 
> | profiles/endpoints/customdomains | Non | 
> | profiles/endpoints/origins | Non | 
> | validateProbe | Non | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | certificateOrders | OUI | 
> | certificateOrders/certificates | Non | 
> | validateCertificateRegistrationInformation | Non | 

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | capabilities | Non | 
> | domainNames | Non | 
> | domainNames/capabilities | Non | 
> | domainNames/internalLoadBalancers | Non | 
> | domainNames/serviceCertificates | Non | 
> | domainNames/slots | Non | 
> | domainNames/slots/roles | Non | 
> | moveSubscriptionResources | Non | 
> | operatingSystemFamilies | Non | 
> | operatingSystems | Non | 
> | quotas | Non | 
> | resourceTypes | Non | 
> | validateSubscriptionMoveAvailability | Non | 
> | virtualMachines | Non | 
> | virtualMachines/diagnosticSettings | Non | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | classicInfrastructureResources | Non | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | capabilities | Non | 
> | expressRouteCrossConnections | Non | 
> | expressRouteCrossConnections/peerings | Non | 
> | gatewaySupportedDevices | Non | 
> | networkSecurityGroups | Non | 
> | quotas | Non | 
> | reservedIps | Non | 
> | virtualNetworks | Non | 
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Non | 
> | virtualNetworks/virtualNetworkPeerings | Non | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | capabilities | Non | 
> | disks | Non | 
> | images | Non | 
> | osImages | Non | 
> | osPlatformImages | Non | 
> | publicImages | Non | 
> | quotas | Non | 
> | storageAccounts | Non | 
> | storageAccounts/services | Non | 
> | storageAccounts/services/diagnosticSettings | Non | 
> | storageAccounts/vmImages | Non | 
> | vmImages | Non | 

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI | 

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | RateCard | Non | 
> | UsageAggregates | Non | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | availabilitySets | OUI | 
> | disks | OUI | 
> | images | OUI | 
> | restorePointCollections | OUI | 
> | restorePointCollections/restorePoints | Non | 
> | sharedVMImages | OUI | 
> | sharedVMImages/versions | OUI | 
> | snapshots | OUI | 
> | virtualMachines | OUI | 
> | virtualMachines/diagnosticSettings | Non | 
> | virtualMachines/extensions | OUI | 
> | virtualMachineScaleSets | OUI | 
> | virtualMachineScaleSets/extensions | Non | 
> | virtualMachineScaleSets/networkInterfaces | Non | 
> | virtualMachineScaleSets/publicIPAddresses | Non | 
> | virtualMachineScaleSets/virtualMachines | Non | 
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Non | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | AggregatedCost | Non | 
> | Balances | Non | 
> | Budgets | Non | 
> | Charges | Non | 
> | CostTags | Non | 
> | credits | Non | 
> | événements | Non | 
> | Prévisions | Non | 
> | lots | Non | 
> | Marketplaces | Non | 
> | Pricesheets | Non | 
> | products | Non | 
> | ReservationDetails | Non | 
> | ReservationRecommendations | Non | 
> | ReservationSummaries | Non | 
> | ReservationTransactions | Non | 
> | Balises | Non | 
> | Termes | Non | 
> | UsageDetails | Non | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | containerGroups | OUI | 
> | serviceAssociationLinks | Non | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | registries | OUI | 
> | registries/builds | Non | 
> | registries/builds/cancel | Non | 
> | registries/builds/getLogLink | Non | 
> | registries/buildTasks | OUI | 
> | registries/buildTasks/steps | Non | 
> | registries/eventGridFilters | Non | 
> | registries/getBuildSourceUploadUrl | Non | 
> | registries/GetCredentials | Non | 
> | registries/importImage | Non | 
> | registries/queueBuild | Non | 
> | registries/regenerateCredential | Non | 
> | registries/regenerateCredentials | Non | 
> | registries/replications | OUI | 
> | registries/runs | Non | 
> | registries/runs/cancel | Non | 
> | registries/scheduleRun | Non | 
> | registries/tasks | OUI | 
> | registries/updatePolicies | Non | 
> | registries/webhooks | OUI | 
> | registries/webhooks/getCallbackConfig | Non | 
> | registries/webhooks/ping | Non | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | containerServices | OUI | 
> | managedclusters | OUI | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applications | OUI | 
> | updateCommunicationPreference | Non | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Alertes | Non | 
> | BillingAccounts | Non | 
> | Connecteurs | OUI | 
> | Departments | Non | 
> | Dimensions | Non | 
> | EnrollmentAccounts | Non | 
> | Requête | Non | 
> | inscription | Non | 
> | Reportconfigs | Non | 
> | Rapports | Non | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | hubs | OUI | 
> | hubs/authorizationPolicies | Non | 
> | hubs/connectors | Non | 
> | hubs/connectors/mappings | Non | 
> | hubs/interactions | Non | 
> | hubs/kpi | Non | 
> | hubs/links | Non | 
> | hubs/profiles | Non | 
> | hubs/roleAssignments | Non | 
> | hubs/roles | Non | 
> | hubs/suggestTypeSchema | Non | 
> | hubs/views | Non | 
> | hubs/widgetTypes | Non | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | jobs | OUI | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | OUI | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | workspaces | OUI | 
> | workspaces/virtualNetworkPeerings | Non | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | catalogs | OUI | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | connectionManagers | OUI | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | dataFactories | OUI | 
> | dataFactories/diagnosticSettings | Non | 
> | dataFactorySchema | Non | 
> | factories | OUI | 
> | factories/integrationRuntimes | Non | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI | 
> | accounts/dataLakeStoreAccounts | Non | 
> | accounts/storageAccounts | Non | 
> | accounts/storageAccounts/containers | Non | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI | 
> | accounts/eventGridFilters | Non | 
> | accounts/firewallRules | Non | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | services | OUI | 
> | services/projects | OUI | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | servers | OUI | 
> | servers/recoverableServers | Non | 
> | servers/virtualNetworkRules | Non | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | servers | OUI | 
> | servers/recoverableServers | Non | 
> | servers/virtualNetworkRules | Non | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | servers | OUI | 
> | servers/advisors | Non | 
> | servers/queryTexts | Non | 
> | servers/recoverableServers | Non | 
> | servers/topQueryStatistics | Non | 
> | servers/virtualNetworkRules | Non | 
> | servers/waitStatistics | Non | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | IoTHubs | OUI | 
> | IotHubs/eventGridFilters | Non | 
> | ProvisioningServices | OUI | 
> | usages | Non | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | controllers | OUI | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | labs | OUI | 
> | labs/serviceRunners | OUI | 
> | labs/virtualMachines | OUI | 
> | schedules | OUI | 

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | databaseAccountNames | Non | 
> | databaseAccounts | OUI | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | domaines | OUI | 
> | domains/domainOwnershipIdentifiers | Non | 
> | generateSsoRequest | Non | 
> | topLevelDomains | Non | 
> | validateDomainRegistrationInformation | Non | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | lcsprojects | Non | 
> | lcsprojects/clouddeployments | Non | 
> | lcsprojects/connectors | Non | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | domaines | OUI | 
> | domains/topics | Non | 
> | eventSubscriptions | Non | 
> | extensionTopics | Non | 
> | topics | OUI | 
> | topicTypes | Non | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | clusters | OUI | 
> | namespaces | OUI | 
> | namespaces/authorizationrules | Non | 
> | namespaces/disasterrecoveryconfigs | Non | 
> | namespaces/eventhubs | Non | 
> | namespaces/eventhubs/authorizationrules | Non | 
> | namespaces/eventhubs/consumergroups | Non | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | features | Non | 
> | fournisseurs | Non | 

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | enroll | Non | 
> | galleryitems | Non | 
> | generateartifactaccessuri | Non | 
> | myareas | Non | 
> | myareas/areas | Non | 
> | myareas/areas/areas | Non | 
> | myareas/areas/areas/galleryitems | Non | 
> | myareas/areas/galleryitems | Non | 
> | myareas/galleryitems | Non | 
> | inscription | Non | 
> | les ressources | Non | 
> | retrieveresourcesbyid | Non | 

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | guestConfigurationAssignments | Non | 
> | software | Non | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | hanaInstances | OUI | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | clusters | OUI | 
> | clusters/applications | Non | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | jobs | OUI | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | labelGroups | Non | 
> | labelGroups/labels | Non | 
> | labelGroups/labels/conditions | Non | 
> | labelGroups/labels/subLabels | Non | 
> | labelGroups/labels/subLabels/conditions | Non | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | actiongroups | OUI | 
> | activityLogAlerts | OUI | 
> | alertrules | OUI | 
> | automatedExportSettings | Non | 
> | autoscalesettings | OUI | 
> | baseline | Non | 
> | calculatebaseline | Non | 
> | components | OUI | 
> | components/events | Non | 
> | components/pricingPlans | Non | 
> | components/query | Non | 
> | diagnosticSettings | Non | 
> | diagnosticSettingsCategories | Non | 
> | eventCategories | Non | 
> | eventtypes | Non | 
> | extendedDiagnosticSettings | Non | 
> | logDefinitions | Non | 
> | logprofiles | Non | 
> | journaux d’activité | Non | 
> | metricAlerts | OUI |
> | migrateToNewPricingModel | Non | 
> | myWorkbooks | Non | 
> | queries | Non | 
> | rollbackToLegacyPricingModel | Non | 
> | scheduledqueryrules | OUI | 
> | vmInsightsOnboardingStatuses | Non | 
> | webtests | OUI | 
> | workbooks | OUI | 

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | diagnosticSettings | Non | 
> | diagnosticSettingsCategories | Non | 

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | IoTApps | OUI | 

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Graph | OUI | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | deletedVaults | Non | 
> | vaults | OUI | 
> | vaults/accessPolicies | Non | 
> | vaults/secrets | Non | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | clusters | OUI | 
> | clusters/databases | Non | 
> | clusters/databases/dataconnections | Non | 
> | clusters/databases/eventhubconnections | Non | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | labaccounts | OUI | 
> | users | Non | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | journaux d’activité | Non | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | integrationAccounts | OUI | 
> | workflows | OUI | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | commitmentPlans | OUI | 
> | webServices | OUI | 
> | Workspaces | OUI | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI | 
> | accounts/workspaces | OUI | 
> | accounts/workspaces/projects | OUI | 
> | teamAccounts | OUI | 
> | teamAccounts/workspaces | OUI | 
> | teamAccounts/workspaces/projects | OUI | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | workspaces | OUI | 
> | workspaces/computes | Non | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Identities | Non | 
> | userAssignedIdentities | OUI | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | getEntities | Non | 
> | managementGroups | Non | 
> | les ressources | Non | 
> | startTenantBackfill | Non | 
> | tenantBackfillStatus | Non | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI | 
> | accounts/eventGridFilters | Non | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | offers | Non | 
> | offerTypes | Non | 
> | offerTypes/publishers | Non | 
> | offerTypes/publishers/offers | Non | 
> | offerTypes/publishers/offers/plans | Non | 
> | offerTypes/publishers/offers/plans/agreements | Non | 
> | offerTypes/publishers/offers/plans/configs | Non | 
> | offerTypes/publishers/offers/plans/configs/importImage | Non | 
> | privategalleryitems | Non | 
> | products | Non | 

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | classicDevServices | OUI | 
> | updateCommunicationPreference | Non | 

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | agreements | Non | 
> | offertypes | Non | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | mediaservices | OUI | 
> | mediaservices/accountFilters | Non | 
> | mediaservices/assets | Non | 
> | mediaservices/assets/assetFilters | Non | 
> | mediaservices/contentKeyPolicies | Non | 
> | mediaservices/eventGridFilters | Non | 
> | mediaservices/liveEventOperations | Non | 
> | mediaservices/liveEvents | OUI | 
> | mediaservices/liveEvents/liveOutputs | Non | 
> | mediaservices/liveOutputOperations | Non | 
> | mediaservices/streamingEndpointOperations | Non | 
> | mediaservices/streamingEndpoints | OUI | 
> | mediaservices/streamingLocators | Non | 
> | mediaservices/streamingPolicies | Non | 
> | mediaservices/transforms | Non | 
> | mediaservices/transforms/jobs | Non | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | projects | OUI | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applicationGateways | OUI | 
> | applicationSecurityGroups | OUI | 
> | azureFirewallFqdnTags | Non | 
> | azureFirewalls | OUI | 
> | bgpServiceCommunities | Non | 
> | connections | OUI | 
> | ddosCustomPolicies | OUI | 
> | ddosProtectionPlans | OUI | 
> | dnsOperationStatuses | Non | 
> | dnszones | OUI | 
> | dnszones/A | Non | 
> | dnszones/AAAA | Non | 
> | dnszones/all | Non | 
> | dnszones/CAA | Non | 
> | dnszones/CNAME | Non | 
> | dnszones/MX | Non | 
> | dnszones/NS | Non | 
> | dnszones/PTR | Non | 
> | dnszones/recordsets | Non | 
> | dnszones/SOA | Non | 
> | dnszones/SRV | Non | 
> | dnszones/TXT | Non | 
> | expressRouteCircuits | OUI | 
> | expressRouteServiceProviders | Non | 
> | frontdoors | OUI | 
> | frontdoorWebApplicationFirewallPolicies | OUI | 
> | getDnsResourceReference | Non | 
> | interfaceEndpoints | OUI | 
> | internalNotify | Non | 
> | loadBalancers | OUI | 
> | localNetworkGateways | OUI | 
> | natGateways | OUI | 
> | networkIntentPolicies | OUI | 
> | networkInterfaces | OUI | 
> | networkProfiles | OUI | 
> | networkSecurityGroups | OUI | 
> | networkWatchers | OUI | 
> | networkWatchers/connectionMonitors | OUI | 
> | networkWatchers/lenses | OUI | 
> | networkWatchers/pingMeshes | OUI | 
> | privateLinkServices | OUI | 
> | publicIPAddresses | OUI | 
> | publicIPPrefixes | OUI | 
> | routeFilters | OUI | 
> | routeTables | OUI | 
> | serviceEndpointPolicies | OUI | 
> | trafficManagerGeographicHierarchies | Non | 
> | trafficmanagerprofiles | OUI | 
> | trafficmanagerprofiles/heatMaps | Non | 
> | virtualHubs | OUI | 
> | virtualNetworkGateways | OUI | 
> | virtualNetworks | OUI | 
> | virtualNetworkTaps | OUI | 
> | virtualWans | OUI | 
> | vpnGateways | OUI | 
> | vpnSites | OUI | 
> | webApplicationFirewallPolicies | OUI | 

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | namespaces | OUI | 
> | namespaces/notificationHubs | OUI | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | périphériques | Non | 
> | linkTargets | Non | 
> | storageInsightConfigs | Non | 
> | workspaces | OUI | 
> | workspaces/dataSources | Non | 
> | workspaces/linkedServices | Non | 
> | workspaces/query | Non | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | managementassociations | Non | 
> | managementconfigurations | OUI | 
> | solutions | OUI | 
> | views | OUI | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | policyEvents | Non | 
> | policyStates | Non | 
> | policyTrackedResources | Non | 
> | remediations | Non | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | consoles | Non | 
> | dashboards | OUI | 
> | userSettings | Non | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | workspaceCollections | OUI | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | capacities | OUI | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | OUI | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | backupProtectedItems | Non | 
> | vaults | OUI | 

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | namespaces | OUI | 
> | namespaces/authorizationrules | Non | 
> | namespaces/hybridconnections | Non | 
> | namespaces/hybridconnections/authorizationrules | Non | 
> | namespaces/wcfrelays | Non | 
> | namespaces/wcfrelays/authorizationrules | Non | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | les ressources | Non | 
> | subscriptionsStatus | Non | 

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | availabilityStatuses | Non | 
> | childAvailabilityStatuses | Non | 
> | childResources | Non | 
> | événements | Non | 
> | impactedResources | Non | 
> | Notifications | Non | 

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | deployments | Non | 
> | deployments/operations | Non | 
> | links | Non | 
> | notifyResourceJobs | Non | 
> | fournisseurs | Non | 
> | resourceGroups | Non | 
> | les ressources | Non | 
> | subscriptions | Non | 
> | subscriptions/providers | Non | 
> | subscriptions/resourceGroups | Non | 
> | subscriptions/resourcegroups/resources | Non | 
> | subscriptions/resources | Non | 
> | subscriptions/tagnames | Non | 
> | subscriptions/tagNames/tagValues | Non | 
> | tenants | Non | 

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applications | OUI | 
> | saasresources | Non | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | flows | OUI | 
> | jobcollections | OUI | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | resourceHealthMetadata | Non | 
> | searchServices | OUI | 

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | advancedThreatProtectionSettings | Non | 
> | alertes | Non | 
> | allowedConnections | Non | 
> | appliances | Non | 
> | applicationWhitelistings | Non | 
> | AutoProvisioningSettings | Non | 
> | Compliances | Non | 
> | dataCollectionAgents | Non | 
> | discoveredSecuritySolutions | Non | 
> | externalSecuritySolutions | Non | 
> | InformationProtectionPolicies | Non | 
> | jitNetworkAccessPolicies | Non | 
> | monitoring | Non | 
> | monitoring/antimalware | Non | 
> | monitoring/baseline | Non | 
> | monitoring/patch | Non | 
> | stratégies | Non | 
> | pricings | Non | 
> | securityContacts | Non | 
> | securitySolutions | Non | 
> | securitySolutionsReferenceData | Non | 
> | securityStatus | Non | 
> | securityStatus/endpoints | Non | 
> | securityStatus/subnets | Non | 
> | securityStatus/virtualMachines | Non | 
> | securityStatuses | Non | 
> | securityStatusesSummaries | Non | 
> | paramètres | Non | 
> | tâches | Non | 
> | topologies | Non | 
> | workspaceSettings | Non | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | diagnosticSettings | Non | 
> | diagnosticSettingsCategories | Non | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | namespaces | OUI | 
> | namespaces/authorizationrules | Non | 
> | namespaces/disasterrecoveryconfigs | Non | 
> | namespaces/eventgridfilters | Non | 
> | namespaces/queues | Non | 
> | namespaces/queues/authorizationrules | Non | 
> | namespaces/topics | Non | 
> | namespaces/topics/authorizationrules | Non | 
> | namespaces/topics/subscriptions | Non | 
> | namespaces/topics/subscriptions/rules | Non | 
> | premiumMessagingRegions | Non | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | clusters | OUI | 
> | clusters/applications | Non | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applications | OUI | 
> | gateways | OUI | 
> | networks | OUI | 
> | secrets | OUI | 
> | volumes | OUI | 

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | SignalR | OUI | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applianceDefinitions | OUI | 
> | appliances | OUI | 
> | applicationDefinitions | OUI | 
> | applications | OUI | 
> | jitRequests | OUI | 

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | managedInstances | OUI |
> | managedInstances/databases | OUI |
> | managedInstances/databases/backupShortTermRetentionPolicies | Non |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Non |
> | managedInstances/databases/vulnerabilityAssessments | Non |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | Non |
> | managedInstances/encryptionProtector | Non |
> | managedInstances/keys | Non |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Non |
> | managedInstances/vulnerabilityAssessments | Non |
> | servers | OUI | 
> | servers/administrators | Non | 
> | servers/communicationLinks | Non | 
> | servers/databases | OUI | 
> | servers/encryptionProtector | Non | 
> | servers/firewallRules | Non | 
> | servers/keys | Non | 
> | servers/restorableDroppedDatabases | Non | 
> | servers/serviceobjectives | Non | 
> | servers/tdeCertificates | Non | 


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | OUI | 
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Non | 
> | SqlVirtualMachines | OUI | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | storageAccounts | OUI | 
> | storageAccounts/blobServices | Non | 
> | storageAccounts/fileServices | Non | 
> | storageAccounts/queueServices | Non | 
> | storageAccounts/services | Non | 
> | storageAccounts/tableServices | Non | 
> | usages | Non | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | storageSyncServices | OUI | 
> | storageSyncServices/registeredServers | Non | 
> | storageSyncServices/syncGroups | Non | 
> | storageSyncServices/syncGroups/cloudEndpoints | Non | 
> | storageSyncServices/syncGroups/serverEndpoints | Non | 
> | storageSyncServices/workflows | Non | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | managers | OUI | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | streamingjobs | OUI | 
> | streamingjobs/diagnosticSettings | Non | 

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | CreateSubscription | Non | 
> | SubscriptionDefinitions | Non | 
> | SubscriptionOperations | Non | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | supporttickets | Non | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | providerRegistrations | OUI | 
> | les ressources | OUI | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | environments | OUI | 
> | environments/accessPolicies | Non | 
> | environments/eventsources | OUI | 
> | environments/referencedatasets | OUI | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | account | OUI | 
> | account/extension | OUI | 
> | account/project | OUI | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | apiManagementAccounts | Non | 
> | apiManagementAccounts/apiAcls | Non | 
> | apiManagementAccounts/apis | Non | 
> | apiManagementAccounts/apis/apiAcls | Non | 
> | apiManagementAccounts/apis/connectionAcls | Non | 
> | apiManagementAccounts/apis/connections | Non | 
> | apiManagementAccounts/apis/connections/connectionAcls | Non | 
> | apiManagementAccounts/apis/localizedDefinitions | Non | 
> | apiManagementAccounts/connectionAcls | Non | 
> | apiManagementAccounts/connections | Non | 
> | billingMeters | Non | 
> | certificates | OUI | 
> | connectionGateways | OUI | 
> | connections | OUI | 
> | customApis | OUI | 
> | deletedSites | Non | 
> | functions | Non | 
> | hostingEnvironments | OUI | 
> | hostingEnvironments/multiRolePools | Non | 
> | hostingEnvironments/multiRolePools/instances | Non | 
> | hostingEnvironments/workerPools | Non | 
> | hostingEnvironments/workerPools/instances | Non | 
> | publishingUsers | Non | 
> | de films | Non | 
> | resourceHealthMetadata | Non | 
> | runtimes | Non | 
> | serverFarms | OUI | 
> | serverFarms/workers | Non | 
> | sites | OUI | 
> | sites/domainOwnershipIdentifiers | Non | 
> | sites/hostNameBindings | Non | 
> | sites/instances | Non | 
> | sites/instances/extensions | Non | 
> | sites/premieraddons | OUI | 
> | sites/recommendations | Non | 
> | sites/resourceHealthMetadata | Non | 
> | sites/slots | OUI | 
> | sites/slots/hostNameBindings | Non | 
> | sites/slots/instances | Non | 
> | sites/slots/instances/extensions | Non | 
> | sourceControls | Non | 
> | validate | Non | 
> | verifyHostingEnvironmentVnet | Non | 

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | diagnosticSettings | Non | 
> | diagnosticSettingsCategories | Non | 

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | DeviceServices | OUI | 

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | components | Non | 
> | componentsSummary | Non | 
> | monitorInstances | Non | 
> | monitorInstancesSummary | Non | 
> | monitors | Non | 
> | notificationSettings | Non | 

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir les mêmes données qu’un fichier de valeurs séparées par des virgules, téléchargez [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).