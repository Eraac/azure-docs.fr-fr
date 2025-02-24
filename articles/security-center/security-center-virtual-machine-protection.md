---
title: Protection de vos machines et de vos applications dans Azure Security Center | Microsoft Docs
description: Ce document traite des recommandations de Security Center qui peuvent vous aider à protéger vos machines virtuelles, vos ordinateurs, vos applications web et vos environnements App Service.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: v-mohabe
ms.openlocfilehash: 4e0a8473dfadc151ab861a4d749d9ff5e1622fb9
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428390"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Protection de vos machines et de vos applications dans Azure Security Center
Azure Security Center analyse l’état de sécurité de vos ressources Azure, les serveurs non-Azure et les machines virtuelles. Lorsque Security Center identifie des failles de sécurité potentielles, il crée des recommandations qui vous guident tout au long du processus de configuration des contrôles nécessaires. Ces recommandations s’appliquent aux types de ressources Azure : machines virtuelles et ordinateurs, applications, mise en réseau, SQL et Identité et accès.

Cet article traite des recommandations qui s’appliquent aux machines et applications.

## <a name="monitoring-security-health"></a>Surveillance de l’intégrité de la sécurité
Vous pouvez surveiller l’état de sécurité de vos ressources dans le tableau de bord **Security Center - Vue d’ensemble**. La section **Ressources** indique le nombre de problèmes identifiés et l’état de sécurité de chaque type de ressource.

Vous pouvez consulter une liste de tous les problèmes en sélectionnant **Recommandations**. Pour plus d’informations sur la façon d’appliquer des recommandations, consultez [Implémentation des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md).

Pour obtenir la liste complète des recommandations Compute et App services, consultez [Recommandations](security-center-virtual-machine-protection.md#compute-and-app-recommendations).

Pour continuer, sélectionnez **Compute et applications** sous **Ressources**, ou dans le menu principal de Security Center.
![Tableau de bord Security Center](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>Surveiller Compute et App Services
Sous **Compute et applications**, figurent les onglets suivants :

- **Vue d’ensemble** : la surveillance et les recommandations identifiées par Security Center.
- **Machines virtuelles et ordinateurs** : liste de vos machines virtuelles et ordinateurs, et état actuel de leur sécurité.
- **Services cloud** : liste des rôles web et de travail contrôlés par Security Center.
- **App services** : liste de vos environnements App Service et état actuel de leur sécurité.
- **Conteneurs (préversion)**  : liste de vos conteneurs hébergés sur des machines IaaS Linux et évaluation de la sécurité de leurs configurations de Docker.
- **Ressources Compute (préversion)**  : liste des suggestions pour vos ressources Compute, telles que des clusters Service Fabric et des Event hubs.

Pour continuer, sous **Hygiène de sécurité de la ressource**, sélectionnez **Compute et applications**.

![Calcul](./media/security-center-virtual-machine-recommendations/compute.png)

Chaque onglet peut contenir plusieurs sections, et dans chaque section vous pouvez sélectionner une option afin d’afficher plus de détails sur les étapes recommandées pour résoudre le problème en question.

### Machines virtuelles et ordinateurs non surveillés <a name="unmonitored-vms-and-computers"></a>
Une machine virtuelle ou un ordinateur n’est pas surveillé par Security Center si la machine n’exécute pas l’extension Microsoft Monitoring Agent. Un ordinateur peut avoir un agent local déjà installé, par exemple l’agent direct OMS ou l’agent de System Center Operations Manager. Les machines sur lesquelles sont installés ces agents sont considérées comme non surveillées, car ces agents ne sont pas entièrement pris en charge par Security Center. Pour tirer pleinement parti de toutes les fonctionnalités de Security Center, vous devez utiliser l’extension Microsoft Monitoring Agent.

Vous pouvez installer l’extension sur la machine virtuelle ou l’ordinateur non surveillé, en plus de l’agent local déjà installé. Configurez les deux agents de la même manière en les connectant au même espace de travail. Ainsi, Security Center pourra interagir avec l’extension Microsoft Monitoring Agent et collecter des données. Consultez la section [Activer l’extension de machine virtuelle](../azure-monitor/learn/quick-collect-azurevm.md) pour obtenir des instructions sur la façon d’installer l’extension Microsoft Monitoring Agent.

Consultez la section [Problèmes d’intégrité de l’agent de surveillance](security-center-troubleshooting-guide.md#mon-agent) pour en savoir plus sur la raison pour laquelle Security Center ne peut pas surveiller correctement les machines virtuelles et ordinateurs initialisés pour l’approvisionnement automatique.

### <a name="recommendations"></a>Recommandations
Cette section contient un ensemble de recommandations pour chaque machine virtuelle et ordinateur, les rôles web et de travail, les applications web Azure App Service et l’environnement Azure App Service surveillés par Security Center. La première colonne indique la recommandation. La deuxième colonne précise le nombre total de ressources qui sont concernées par cette recommandation. La troisième colonne renseigne sur la gravité du problème.

Chaque recommandation dispose d’un ensemble d’actions pouvant être effectuées après les avoir sélectionnées. Par exemple, si vous sélectionnez **Mises à jour système manquantes**, le nombre de machines virtuelles et d’ordinateurs ayant des correctifs manquants et la gravité de la mise à jour manquante apparaissent.

**Appliquer les mises à jour système** contient un résumé des mises à jour critiques dans un format graphique, l’un pour Windows et l’autre pour Linux. La deuxième partie contient un tableau avec les informations suivantes :

- **NOM** : nom de la mise à jour manquante.
- **NO. DE MACHINES VIRTUELLES ET D’ORDINATEURS** : nombre total de machines virtuelles et d’ordinateurs sur lesquels cette mise à jour n’a pas été installée.
- **GRAVITÉ DE LA MISE À JOUR** : Donne le niveau de gravité de chaque recommandation :

    - **Critique** : Existence d’une vulnérabilité sur une ressource importante (application, machine virtuelle, groupe de sécurité réseau). Le problème doit être analysé.
    - **Important** : Certaines étapes supplémentaires sont nécessaires pour terminer un processus ou éliminer une vulnérabilité.
    - **Modéré** : Existence d’une vulnérabilité devant être prise en compte, mais qui ne nécessite aucune attention immédiate. Par défaut, les recommandations de niveau Faible ne sont pas affichées, mais vous pouvez filtrer les recommandations pour les faire apparaître.


- **STATE** : état actuel de la recommandation :

    - **Ouverte** : la suggestion n’a pas encore été prise en compte.
    - **En cours** : la recommandation est actuellement appliquée aux ressources ; aucune action de votre part n’est nécessaire.
    - **Résolue** : la recommandation a déjà été achevée. (Une fois problème résolu, l’entrée a été grisée).

Pour afficher des informations détaillées sur les recommandations, cliquez sur le nom de la mise à jour manquante dans la liste.


> [!NOTE]
> Les recommandations de sécurité ci-dessus sont les mêmes que celles figurant sous la mosaïque **Recommandations**. Pour plus d’informations sur la résolution des recommandations, consultez l’article [Implémentation des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md).
>
>

### <a name="vms-and-computers"></a>Machines virtuelles et ordinateurs
La section Machines virtuelles et ordinateurs vous fournit une vue d’ensemble de toutes les recommandations relatives aux machines virtuelles et aux ordinateurs. Chaque colonne représente un ensemble de recommandations.

![Recommandations relatives aux machines virtuelles et aux ordinateurs](./media/security-center-virtual-machine-recommendations/vm-computers.png)

Cette liste contient quatre types d’icônes :

![Ordinateur extérieur à Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Ordinateur ne relevant pas d’Azure.

![Machine virtuelle Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Machine virtuelle Azure Resource Manager.

![Machine virtuelle Azure Classic](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Machine virtuelle Azure Classic.


![Machines virtuelles identifiées à partir de l’espace de travail](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Machines virtuelles identifiées uniquement à partir de l’espace de travail qui fait partie de l’abonnement affiché. Cela inclut les machines virtuelles à partir d’autres abonnements ce rapport à l’espace de travail dans cet abonnement, ainsi que les machines virtuelles qui ont été installés avec l’agent direct Operations Manager et n’avoir aucun ID de ressource.

L’icône qui s’affiche sous chaque recommandation vous aide à identifier rapidement la machine virtuelle et l’ordinateur qui requièrent votre attention, ainsi que le type de recommandation. Vous pouvez également utiliser les filtres pour rechercher dans la liste par **Type de ressource** et **Gravité**.

Pour explorer au niveau du détail les suggestions de sécurité pour chaque machine virtuelle, cliquez sur celle-ci.
Ce panneau comporte des informations détaillées sur la sécurité de la machine virtuelle ou de l’ordinateur. L’action recommandée et le niveau de gravité de chaque problème sont affichés en bas.
![Services cloud](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Services cloud
Pour les services cloud, une suggestion est créée lorsque la version du système d’exploitation est obsolète.

![Services cloud](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Si vous avez une recommandation (ce qui n’est pas le cas dans l’exemple précédent), vous devez suivre les étapes de la recommandation pour mettre à jour la version du système d’exploitation. Lorsqu’une mise à jour est disponible, vous recevez une alerte (rouge ou orange en fonction de la gravité du problème). Lorsque vous sélectionnez cette alerte dans les lignes WebRole1 (exécute Windows Server avec votre application web automatiquement déployée sur IIS) ou WorkerRole1 (exécute Windows Server avec votre application web automatiquement déployée sur IIS), vous obtenez des informations détaillées sur cette suggestion.

Pour voir une explication plus normative concernant cette recommandation, cliquez sur **Mettre à jour la version du système d’exploitation** sous la colonne **DESCRIPTION**.



![Mettre à jour la version du système d’exploitation](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>App Services
Vous devez activer App Service dans votre abonnement pour afficher les informations App Service. Pour obtenir des instructions sur l’activation de cette fonctionnalité, consultez [Protéger App Service avec Azure Security Center](security-center-app-services.md).
[!NOTE]
> La surveillance d’App Service est uniquement disponible en préversion, au niveau Standard du Security Center.


Sous **App Services**, vous trouverez la liste de vos environnements App Service et le résumé de l'intégrité basé sur l’évaluation effectuée par Security Center.

![App Services](./media/security-center-virtual-machine-recommendations/app-services.png)

Cette liste contient trois types d’icônes :

![Environnement App Services](./media/security-center-virtual-machine-recommendations/ase.png) Environnement App Services.

![Application web](./media/security-center-virtual-machine-recommendations/web-app.png) Application web.

![Application de fonction](./media/security-center-virtual-machine-recommendations/function-app.png) Application de fonction.

1. Sélectionnez une application web. Une vue récapitulative s’ouvre avec trois onglets :

   - **Recommandations** : basées sur les évaluations effectuées par Security Center et qui ont échoué.
   - **Évaluations passées** : liste des évaluations effectuées par Security Center et qui ont réussi.
   - **Évaluations non disponibles** : liste des évaluations qui n’ont pas été exécutées en raison d’une erreur ou la recommandation n’est pas pertinente pour l’App Service en question.

   Sous **Recommandations** se trouve la liste des recommandations pour l’application web sélectionnée, avec le niveau de gravité pour chaque recommandation.

   ![Recommandations App Services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Sélectionnez une suggestion pour voir sa description et la liste des ressources défectueuses, saines et non analysées.

   - La colonne **Évaluations passées** contient la liste des évaluations réussies.  L’état de gravité de ces évaluations est toujours vert.

   - Sélectionnez une évaluation réussie dans la liste pour obtenir une description de l’évaluation et la liste des ressources défectueuses, saines et non analysées. Il existe un onglet pour les ressources défectueuses, mais cette liste est toujours vide car l’évaluation a réussi.

     ![Correction d’App Service](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>Groupes identiques de machines virtuelles
Security Center détecte automatiquement si vous avez des groupes identiques et vous recommande d’installer Microsoft Monitoring Agent sur ceux-ci. 

Pour installer Microsoft Monitoring Agent : 

1. Sélectionnez la suggestion **Installer l’agent de surveillance sur les groupes de machines virtuelles identiques**. Vous obtenez la liste des jeux identiques non contrôlés.
2. Sélectionnez un groupe identique défectueux. Suivez les instructions pour installer l’agent de surveillance à l’aide d’un espace de travail rempli existant, ou créez-en un. Veillez à définir l’espace de travail [niveau tarifaire](security-center-pricing.md) s’il ne l’est pas.

   ![Installer MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Si vous souhaitez définir de nouveaux groupes identiques pour installer automatiquement Microsoft Monitoring Agent :
1. Accédez à Azure Policy et cliquez sur **Définitions**.
2. Recherchez la stratégie **agent déployer une Analytique de journal pour les jeux de mise à l’échelle de machine virtuelle Windows** et cliquez dessus.
3. Cliquez sur **Affecter**.
4. Définir l’**Étendue** et l’**Espace de travail Log Analytics**, puis cliquez sur **Assigner**.

Si vous souhaitez paramétrer tous les groupes identiques existants pour installer Microsoft Monitoring Agent, dans Azure Policy, accédez à **Correction**, puis appliquez la stratégie existante aux groupes identiques existants.


## <a name="compute-and-app-recommendations"></a>Suggestions de calcul et d’applications
|Type de ressource|Degré de sécurisation|Recommandation|Description|
|----|----|----|----|
|App Service|20|L'application web ne doit pas être accessible via HTTPS|Limitez l’accès des applications web via le protocole HTTPS uniquement.|
|App Service|20|Function App ne doit pas être accessible via HTTPS|Limitez l’accès d’applications de fonction via HTTPS uniquement.|
|App Service|5.|Journaux de diagnostic dans les Services d’application doivent être activées.|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |
|App Service|10|Le débogage à distance doit être désactivé pour l'application web|Désactivez le débogage pour les applications web si vous n’en avez plus besoin. Le débogage distant requiert que des ports d’entrée soient ouverts sur une Function App.|
|App Service|10|Le débogage à distance devrait être désactivé pour Function App|Désactivez le débogage pour Function App si vous n’en avez plus besoin. Le débogage distant requiert que des ports d’entrée soient ouverts sur une Function App.|
|App Service|10|Ne pas autoriser toutes (’*’) les ressources à accéder à votre application| Ne pas autoriser la définition du paramètre WEBSITE_LOAD_CERTIFICATES sur "". La définition du paramètre sur ‘’ signifie que tous les certificats sont chargés dans votre magasin de certificats personnels d’applications web. Cela peut conduire à un abus du principe des privilèges minimum, car il est peu probable que le site ait besoin d’accéder à tous les certificats lors de l’exécution.|
|App Service|20|CORS ne devrait pas autoriser toutes les ressources à accéder à vos applications web|Autorisez uniquement les domaines requis à interagir avec votre application web. Le partage des ressources cross-origin (CORS) ne devrait pas autoriser tous les domaines à accéder à votre application web.|
|App Service|20|CORS ne devrait pas autoriser toutes les ressources à accéder à votre Function App| Recommande d’autoriser uniquement les domaines requis à interagir avec votre application de fonction. Le partage des ressources cross-origin (CORS) ne devrait pas autoriser tous les domaines à accéder à votre application de fonction.|
|Ressources de calcul (Batch)|1|Règles d’alerte de métrique doivent être configurés sur les comptes Batch|Configurer les règles d'alerte pour les métriques sur le compte Batch et activer les métriques Pool Delete Complete Events et Pool Delete Start Events|
|Ressources de calcul (Service Fabric)|10|Clusters service Fabric doivent utiliser uniquement Azure Active Directory pour l’authentification du client|Effectuez l'authentification cliente uniquement par le biais d'Azure Active Directory dans Service Fabric.|
|Ressources de calcul (compte Automation)|5.|Variables du compte Automation doivent être chiffrées.|Activez le chiffrement des ressources variables du compte Automation lors du stockage de données sensibles.|
|Ressources de calcul (recherche)|5.|L’activation des journaux de diagnostic pour les services de recherche de l’audit|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Service Bus)|5.|Journaux de diagnostic dans le Bus de Service doivent être activés.|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Stream Analytics)|5.|Journaux de diagnostic dans Azure Stream Analytique doivent être activés.|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Batch)|5.|Activer les journaux de diagnostic dans les comptes Batch|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Event Hub)|5.|Journaux de diagnostic dans le concentrateur d’événements doivent être activés.|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Logic Apps)|5.|Activer les journaux de diagnostic dans Logic Apps|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d'activité à des fins d'investigation en cas d'incident de sécurité ou de compromission du réseau. |
|Ressources de calcul (Service Fabric)|15|Définir la propriété ClusterProtectionLevel sur EncryptAndSign dans Service Fabric|Service Fabric fournit trois niveaux de protection (None, Sign et EncryptAndSign) pour la communication nœud à nœud à l’aide d’un certificat de cluster principal.  Définissez le niveau de protection pour vous assurer que tous les messages de nœud à nœud sont chiffrés et signés numériquement. |
|Ressources de calcul (Service Bus)|1|Supprimer toutes les règles d'autorisation, sauf RootManageSharedAccessKey de l'espace de noms Service Bus |Les clients Service Bus ne doivent pas utiliser une stratégie d'accès au niveau de l'espace de noms qui donne accès à l'ensemble des files d'attente et rubriques d'un espace de noms. Pour respecter le modèle de sécurité basé sur le privilège minimum, vous devez créer des stratégies d'accès au niveau de l'entité pour les files d'attente et les rubriques afin de limiter l'accès à l'entité spécifique.|
|Ressources de calcul (Event Hub)|1|Toutes les règles d’autorisation sauf RootManageSharedAccessKey doit être supprimé à partir de l’espace de noms Event Hub|Les clients Event Hub ne doivent pas utiliser une stratégie d'accès au niveau de l'espace de noms qui donne accès à l'ensemble des files d'attente et rubriques d'un espace de noms. Pour respecter le modèle de sécurité basé sur le privilège minimum, vous devez créer des stratégies d'accès au niveau de l'entité pour les files d'attente et les rubriques afin de limiter l'accès à l'entité spécifique.|
|Ressources de calcul (Event Hub)|5.|Règles d’autorisation sur l’entité du concentrateur d’événements doivent être définis.|Auditez des règles d'autorisation sur l'entité Event Hub pour accorder un accès à privilèges minimum.|
|Ordinateur|50|Installer l'agent d'analyse sur vos machines|Installer l'agent d'analyse pour activer la collecte de données, l'analyse des mises à jour, l'analyse de la ligne de base et la protection du point de terminaison sur chaque machine.|
|Ordinateur|50|Activer le provisionnement et la collecte automatiques des données pour vos abonnements |Activez le provisionnement et la collecte automatiques des données pour les machines dans vos abonnements afin d’activer la collecte de données, l'analyse des mises à jour, l'analyse de la ligne de base et la protection du point de terminaison sur chaque machine ajoutée à vos abonnements.|
|Ordinateur|40|Résoudre les problèmes d'intégrité de l'agent d'analyse sur vos machines|Pour une protection Security Center complète, corriger les problèmes de l’agent de surveillance sur vos machines en suivant les instructions du guide de résolution des problèmes| 
|Ordinateur|40|Résoudre les problèmes d'intégrité de la protection du point de terminaison sur vos machines|Pour une protection Security Center complète, corrigez les problèmes de l’agent de surveillance sur vos machines en suivant les instructions du guide de résolution des problèmes.|
|Ordinateur|40|Résoudre les problèmes de données d'analyse manquantes sur vos machines|Résolvez les problèmes de données d’analyse manquantes sur les machines virtuelles et les ordinateurs. Les données d’analyse manquantes sur vos machines entraînent des évaluations de sécurité manquantes telles que l'analyse des mises à jour, l'analyse de la ligne de base et un manque de solution d’analyse de protection du point de terminaison.|
|Ordinateur|40|Mises à jour système doivent être installés sur vos machines|Installer les mises à jour critiques et de sécurité système manquantes afin de sécuriser vos machines virtuelles et ordinateurs Windows et Linux
|Ordinateur|15|Ajouter un pare-feu d’applications web| Déployez une solution de pare-feu d’applications web (WAF) pour sécuriser vos applications web. |
|Ordinateur|40|Mettre à jour la version du système d'exploitation pour vos rôles de service cloud|Mettez à jour la version du système d'exploitation (SE) pour les rôles de votre service cloud vers la version la plus récente disponible pour la gamme de votre SE.|
|Ordinateur|35|Des vulnérabilités dans la configuration de la sécurité sur vos ordinateurs doivent être mis à jour.|Corrigez les vulnérabilités dans la configuration de la sécurité sur vos machines afin de les protéger des attaques.|
|Ordinateur|35|Corriger les vulnérabilités dans la configuration de sécurité sur vos conteneurs|Corriger les vulnérabilités dans la configuration de sécurité sur les ordinateurs où Docker est installé afin de les protéger contre les attaques.|
|Ordinateur|25|Activer les contrôles d'applications adaptatifs|Activez les contrôles d’applications pour contrôler les applications qui peuvent s’exécuter sur vos machines virtuelles hébergées dans Azure. Cela vous permet de renforcer la protection de vos machines virtuelles contre les logiciels malveillants. Security Center utilise le machine learning pour analyser les applications en cours d’exécution sur chaque machine virtuelle et vous permet d’appliquer des règles d’autorisation. Cette fonctionnalité simplifie le processus de configuration et de gestion des règles d’autorisation des applications.|
|Ordinateur|20|Installer la solution de protection de point de terminaison sur vos machines|Installez une solution de protection de point de terminaison sur vos machines virtuelles pour les protéger des menaces et des vulnérabilités.|
|Ordinateur|20|Redémarrer vos machines pour appliquer les mises à jour système|Redémarrez vos machines pour appliquer les mises à jour système et protéger la machine contre les vulnérabilités.|
|Ordinateur|15|Chiffrement de disque doit être appliqué sur des machines virtuelles|Chiffrez vos disques de machine virtuelle à l’aide d’Azure Disk Encryption à la fois pour les machines virtuelles Windows et Linux. zure Disk Encryption (ADE) s’appuie sur les fonctionnalités standard de l’industrie BitLocker de Windows et DM-Crypt de Linux pour fournir le chiffrement du disque de données et du système d’exploitation, afin de protéger vos données et de respecter les engagements de sécurité et de conformité de votre organisation dans le coffre de clés Azure client. Lorsque vos besoins de conformité et de sécurité vous imposent de chiffrer les données de bout en bout à l’aide de vos clés de chiffrement, y compris le chiffrement du disque éphémère (temporaire et attaché localement), utilisez Azure Disk Encryption. Par ailleurs, par défaut, les disques managés sont chiffrés au repos à l’aide d’Azure Storage Service Encryption, où les clés de chiffrement sont des clés gérées par Microsoft dans Azure. Si cela répond à vos exigences de sécurité et de conformité, vous pouvez utiliser le chiffrement de disque managé par défaut pour répondre à vos besoins.|
|Ordinateur|30|Installer une solution d'évaluation des vulnérabilités sur vos machines virtuelles|Installer une solution d'évaluation des vulnérabilités sur vos machines virtuelles|
|Ordinateur|15|Ajouter un pare-feu d’applications web| Déployez une solution de pare-feu d’applications web (WAF) pour sécuriser vos applications web. |
|Ordinateur|30|Des vulnérabilités doivent être mis à jour par une solution d’évaluation des vulnérabilités|Les machines virtuelles pour lesquelles une solution tierce d’évaluation des vulnérabilités est déployée sont évaluées en permanence afin d’y détecter d’éventuelles vulnérabilités au niveau de l’application et du système d’exploitation. Chaque fois que ces vulnérabilités sont détectées, elles sont disponibles pour plus d’informations dans le cadre de la recommandation.|
|Ordinateur|30|Installer une solution d'évaluation des vulnérabilités sur vos machines virtuelles|Installer une solution d'évaluation des vulnérabilités sur vos machines virtuelles|
|Ordinateur|1|Machines virtuelles doivent être migrés vers les nouvelles ressources AzureRM|Profitez des améliorations apportées à Azure Resource Manager pour renforcer la sécurité de vos machines virtuelles : contrôle d'accès plus puissant, audit amélioré, déploiement et gouvernance basés sur Resource Manager, accès aux identités managées, accès au coffre de clés pour les secrets, authentification basée sur Azure AD, prise en charge des étiquettes et des groupes de ressources pour faciliter la gestion de la sécurité, etc. |
|Ordinateur|30|Des vulnérabilités doivent être mis à jour par une solution d’évaluation des vulnérabilités|Les machines virtuelles pour lesquelles une solution tierce d’évaluation des vulnérabilités est déployée sont évaluées en permanence afin d’y détecter d’éventuelles vulnérabilités au niveau de l’application et du système d’exploitation. Chaque fois que ces vulnérabilités sont détectées, elles sont disponibles pour plus d’informations dans le cadre de la recommandation.|
|Jeu de mise à l’échelle de machine virtuelle |4|Journaux de diagnostic dans des machines virtuelles identiques doivent être activés.|Activez les journaux d’activité et conservez-les pendant jusqu’à un an. Cela vous permet de recréer les pistes d’activité à des fins d’investigation. Cela est utile lorsqu’un incident de sécurité se produit, ou quand votre réseau est compromis.|
|Jeu de mise à l’échelle de machine virtuelle|35|Des vulnérabilités dans la configuration de la sécurité sur vos machines virtuelles identiques doivent être mis à jour.|Corriger les vulnérabilités dans la configuration de la sécurité sur vos groupes de machines virtuelles identiques afin de les protéger des attaques. |
|Jeu de mise à l’échelle de machine virtuelle|5.|Corriger les échecs d’intégrité de la protection de point de terminaison sur les groupes de machines virtuelles identiques|Corrigez les échecs d’intégrité de la protection du point de terminaison sur vos groupes de machines virtuelles identiques afin de les protéger des menaces et des vulnérabilités. |
|Jeu de mise à l’échelle de machine virtuelle|10|Protection de point de terminaison doit être installée sur les machines virtuelles|Installer une solution de protection de point de terminaison sur vos groupes de machines virtuelles identiques, pour les protéger des menaces et des vulnérabilités. |
|Jeu de mise à l’échelle de machine virtuelle|40|Mises à jour système sur les machines virtuelles identiques doivent être installés.|Installer les mises à jour critiques et de sécurité système manquantes afin de sécuriser vos groupes de machines virtuelles identiques Windows et Linux. |
 





## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les rubriques suivantes :


* [Comprendre les recommandations d’Azure Security Center pour les machines virtuelles](security-center-virtual-machine-recommendations.md)
* [Surveiller l’identité et l’accès dans Azure Security Center](security-center-identity-access.md)
* [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md)
* [Protection de votre service SQL Azure dans Azure Security Center](security-center-sql-service-recommendations.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.

