---
title: Résoudre les problèmes d’exécution de package dans le runtime d’intégration SSIS | Microsoft Docs
description: Cet article fournit des instructions pour la résolution des problèmes d’exécution de package SSIS dans le runtime d’intégration SSIS
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/15/2019
author: wenjiefu
ms.author: wenjiefu
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 05723a90725992e6b955524a2d35c82d3378ee3d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621856"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Résoudre les problèmes d’exécution de package dans le runtime d’intégration SSIS

Cet article contient des erreurs les plus courantes que vous pouvez rencontrer quand vous exécutez des packages SQL Server Integration Services (SSIS) dans le runtime d’intégration SSIS. Il décrit les causes possibles et les actions permettant de résoudre les erreurs.

## <a name="where-to-find-logs-for-troubleshooting"></a>Où trouver les journaux pour la résolution des problèmes

Utilisez le portail Azure Data Factory pour consulter la sortie de l’activité d’exécution de package SSIS. Cette sortie inclut le résultat de l’exécution, les messages d’erreur et l’ID d’opération. Pour plus d’informations, consultez [Superviser le pipeline](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Utilisez le catalogue SSIS (SSISDB) pour consulter les journaux des détails concernant l’exécution. Pour plus d’informations, consultez [Superviser les packages en cours d’exécution et autres opérations](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Erreurs courantes, causes et solutions

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Message d’erreur : « Délai d'expiration de la connexion dépassé. » ou « Le service a rencontré une erreur lors du traitement de votre demande. Réessayez. »

Voici des causes possibles et les actions recommandées :
* La source ou la destination de données est surchargée. Vérifiez la charge sur votre source ou votre destination de données et si elle dispose d’une capacité suffisante. Par exemple, si vous avez utilisé Azure SQL Database, envisagez d’effectuer un scale-up si la base de données est susceptible d’expirer.
* Le réseau entre le runtime d’intégration SSIS et la source ou la destination de données est instable, en particulier quand la connexion est inter-régions ou entre un emplacement local et Azure. Appliquez le modèle de nouvelle tentative dans le package SSIS en effectuant les étapes suivantes :
  * Vérifiez que vos packages SSIS peuvent se réexécuter en cas d’échec sans effets secondaires (par exemple, une perte de données ou une duplication des données).
  * Configurez **Nouvelle tentative** et **Intervalle avant nouvelle tentative** de l’activité **Exécuter le package SSIS** sous l’onglet **Général**. ![Définir des propriétés sous l’onglet Général](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Pour un composant source ou de destination ADO.NET et OLE DB, définissez **ConnectRetryCount** et **ConnectRetryInterval** dans le Gestionnaire de connexions dans le package SSIS ou l’activité SSIS.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Message d’erreur : « La source ADO NET n’a pas pu acquérir la connexion '...' » avec « Une erreur liée au réseau ou spécifique à une instance s’est produite lors de l’établissement d’une connexion à SQL Server. Le serveur est introuvable ou inaccessible. »

Ce problème signifie généralement que la source ou la destination de données n’est pas accessible à partir du runtime d’intégration SSIS. Les raisons peuvent varier. Essayez les actions suivantes :
* Vérifiez que vous passez correctement le nom/l’adresse IP de la source ou de la destination de données.
* Vérifiez que le pare-feu est correctement défini.
* Vérifiez que votre réseau virtuel est correctement configuré si votre source ou destination de données est locale :
  * Vous pouvez vérifier si le problème provient de la configuration du réseau virtuel en provisionnant une machine virtuelle Azure dans le même réseau virtuel. Vérifiez ensuite si la source ou la destination de données est accessible à partir de la machine virtuelle Azure.
  * Vous trouverez plus d’informations sur l’utilisation d’un réseau virtuel avec un runtime d’intégration SSIS dans [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Message d’erreur : « La source ADO NET n’a pas pu acquérir la connexion '...' » avec « Impossible de créer un gestionnaire de connexions managées. »

La cause possible est que le fournisseur ADO.NET utilisé dans le package n’est pas installé dans le runtime d’intégration SSIS. Vous pouvez installer le fournisseur en utilisant une configuration personnalisée. Vous trouverez plus d’informations sur la configuration personnalisée dans [Personnaliser la configuration du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Message d’erreur : « La connexion '...' est introuvable »

Un problème connu dans les versions antérieures de SQL Server Management Studio (SSMS) peut provoquer cette erreur. Si le package contient un composant personnalisé (par exemple, le Feature Pack SSIS-Azure ou des composants partenaires) qui n’est pas installé sur l’ordinateur où SSMS est utilisé pour effectuer le déploiement, SSMS supprime ce composant et provoque l’erreur. Mettez à niveau [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) vers la dernière version dans laquelle le problème a été résolu.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Message d’erreur : « Code de sortie de SSIS Executor : -1073741819 ».

* Cause possible et action recommandée :
  * Cette erreur peut être due à la limitation de la source et de la destination Excel quand plusieurs sources ou destinations Excel s’exécutent en parallèle en multithread. Vous pouvez contourner cette limitation en configurant vos composants Excel pour qu’ils s’exécutent en séquence, ou les séparer en différents packages et déclencher via « Tâche d’exécution de package » avec la propriété ExecuteOutOfProcess définie sur True.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Message d’erreur : « Espace insuffisant sur le disque »

Cette erreur signifie que le disque local est entièrement utilisé dans le nœud de runtime d’intégration SSIS. Vérifiez si votre package ou configuration personnalisée consomme beaucoup d’espace disque :
* Si le disque est consommé par votre package, il sera libéré une fois l’exécution du package terminée.
* Si le disque est consommé par votre configuration personnalisée, vous devez arrêter le runtime d’intégration SSIS, modifier votre script et redémarrer le runtime d’intégration. L’ensemble du conteneur d’objets blob Azure que vous avez spécifié pour la configuration personnalisée est copié dans le nœud de runtime d’intégration SSIS. Par conséquent, vérifiez du contenu inutile se trouve sous ce conteneur.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Message d’erreur : « Échec de la récupération de la ressource du maître. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException : Code : 300004. Description : Échec du chargement du fichier « *** ».

* Cause possible et action recommandée :
  * Si l’activité SSIS exécute un package à partir du système de fichiers (fichier de package ou fichier projet), cette erreur se produit si le fichier projet, de package ou de configuration n’est pas accessible avec les informations d’identification d’accès au package que vous avez fournies dans l’activité SSIS.
    * Si vous utilisez Azure Files :
      * Le chemin du fichier doit commencer par \\\\\<nom du compte de stockage\>.file.core.windows.net\\\<chemin du partage de fichiers\>
      * Le domaine doit être « Azure »
      * Le nom d’utilisateur doit être \<nom du compte de stockage\>
      * Le mot de passe doit être \<clé d’accès du stockage\>
    * Si vous utilisez un fichier local, vérifiez si le réseau virtuel, les informations d’identification et l’autorisation d’accès au package sont configurés correctement pour que votre runtime d’intégration Azure-SSIS puisse accéder à votre partage de fichiers local.

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Message d’erreur : « Le nom de fichier '...' spécifié dans la connexion n’est pas valide »

* Cause possible et action recommandée :
  * Le nom de fichier spécifié n’est pas valide
  * Vérifiez que vous utilisez le nom de domaine complet (FQDN) au lieu du nom court dans votre gestionnaire de connexions

### <a name="error-message-cannot-open-file-"></a>Message d’erreur : « Impossible d’ouvrir le fichier '...' »

Cette erreur se produit quand une exécution de package ne parvient pas à trouver un fichier sur le disque local dans le runtime d’intégration SSIS. Essayez les actions suivantes :
* N’utilisez pas le chemin absolu dans le package qui est en cours d’exécution dans le runtime d’intégration SSIS. Utilisez le répertoire de travail (.) ou le dossier temporaire (%TEMP%) de l’exécution actuelle à la place.
* Si vous avez besoin de conserver des fichiers sur des nœuds de runtime d’intégration SSIS, préparez les fichiers comme décrit dans [Personnaliser la configuration](how-to-configure-azure-ssis-ir-custom-setup.md). Tous les fichiers se trouvant dans le répertoire de travail sont nettoyés une fois l’exécution terminée.
* Utilisez Azure Files au lieu de stocker le fichier dans le nœud de runtime d’intégration SSIS. Pour plus d’informations, consultez [Utiliser des partages de fichiers Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Message d’erreur : « La base de données « SSISDB » a atteint son quota de taille.»

Le fait que la base de données SSISDB créée dans la base de données Azure SQL ou une instance gérée quand vous créez un runtime d’intégration SSIS a atteint son quota constitue une cause possible. Essayez les actions suivantes :
* Envisagez d’augmenter le nombre de DTU de votre base de données. Vous trouverez des informations détaillées dans [Limites de ressources SQL Database pour un serveur Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).
* Vérifiez si votre package génère de nombreux journaux. Si c’est le cas, vous pouvez configurer un travail élastique pour nettoyer ces journaux. Pour plus d’informations, consultez [Nettoyer les journaux SSISDB avec les travaux de base de données élastique Azure](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Message d’erreur : « La limite de requêtes pour la base de données est ... et elle a été atteinte. »

Si de nombreux packages s’exécutent en parallèle dans le runtime d’intégration SSIS, cette erreur peut se produire car SSISDB a atteint sa limite de requêtes. Envisagez d’augmenter le nombre de DTC de SSISDB pour résoudre ce problème. Vous trouverez des informations détaillées dans [Limites de ressources SQL Database pour un serveur Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Message d’erreur : « L’opération SSIS a échoué avec un état d’opération inattendu : ... »

L’erreur est principalement due à un problème temporaire. Par conséquent, tentez de réexécuter le package. Appliquez le modèle de nouvelle tentative dans le package SSIS en effectuant les étapes suivantes :

* Vérifiez que vos packages SSIS peuvent se réexécuter en cas d’échec sans effets secondaires (par exemple, une perte de données ou une duplication des données).
* Configurez **Nouvelle tentative** et **Intervalle avant nouvelle tentative** de l’activité **Exécuter le package SSIS** sous l’onglet **Général**. ![Définir des propriétés sous l’onglet Général](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Pour un composant source ou de destination ADO.NET et OLE DB, définissez **ConnectRetryCount** et **ConnectRetryInterval** dans le Gestionnaire de connexions dans le package SSIS ou l’activité SSIS.

### <a name="error-message-there-is-no-active-worker"></a>Message d’erreur : « Il n’y a aucun Worker Agent actif. »

Cette erreur signifie généralement que le runtime d’intégration SSIS a un état défectueux. Consultez le portail Azure pour connaître l’état et le détail des erreurs. Pour plus d’informations, consultez [Runtime d’intégration Azure-SSIS](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Message d’erreur : « Votre runtime d’intégration ne peut pas être mis à niveau et cessera finalement de fonctionner, car nous ne pouvons pas accéder au conteneur d’objets blob Azure que vous avez fourni pour la configuration personnalisée. »

Cette erreur se produit quand le runtime d’intégration SSIS ne peut pas accéder au stockage défini pour une configuration personnalisée. Vérifiez si l’URI de la signature d’accès partagé (SAP) que vous avez fourni est valide et n’a pas expiré.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Message d’erreur : « Fournisseur Microsoft OLE DB pour Analysis Services. 'Hresult : 0x80004005 Description :' Erreur COM : Erreur COM : mscorlib ; Une exception a été levée par la cible d’un appel »

Le fait que le nom d’utilisateur ou le mot de passe avec Azure Multi-Factor Authentication activé est configuré pour l’authentification Azure Analysis Services constitue une cause possible. Cette authentification n’est pas prise en charge dans le runtime d’intégration SSIS. Essayez d’utiliser un principal de service pour l’authentification Azure Analysis Services :
1. Préparez un principal de service comme décrit dans [Automatisation à l’aide de principaux de service](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal).
2. Dans le Gestionnaire de connexions, configurez **Utiliser un nom d'utilisateur et un mot de passe spécifiques** : définissez **AppID** comme nom d’utilisateur et **clientSecret** comme mot de passe.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Message d’erreur : « La source ADO NET Source n’a pas pu acquérir la connexion la connexion {GUID}. Message d’erreur : Échec de la connexion pour l’utilisateur 'NT AUTHORITY\ANONYMOUS LOGON' » lors de l’utilisation d’une identité managée

Vérifiez que vous ne configurez pas **Authentification par mot de passe Active Directory** comme méthode d’authentification du Gestionnaire de connexions quand le paramètre *ConnectUsingManagedIdentity* a la valeur **True** . Vous pouvez le configurer plutôt sur **Authentification SQL**, qui est ignoré si *ConnectUsingManagedIdentity* est défini.

### <a name="package-execution-takes-too-long"></a>L’exécution du package prend trop de temps

Voici des causes possibles et les actions recommandées :
* Un trop grand nombre d’exécutions de package ont été planifiées sur le runtime d’intégration SSIS. Toutes ces exécutions attendront leur tour dans une file d’attente.
  * Déterminer le nombre maximal à l’aide de cette formule : 
    
    Nombre max d’exécutions en parallèle par runtime d’intégration = Nombre de nœuds * nombre max d’exécutions en parallèle par nœud
  * Pour savoir comment définir le nombre de nœuds et le nombre maximal d’exécutions en parallèle par nœud, consultez [Créer un runtime d’intégration Azure-SSIS dans Azure Data Factory](create-azure-ssis-integration-runtime.md).
* Le runtime d’intégration SSIS est arrêté ou a un état défectueux. Pour savoir comment vérifier l’état du runtime d’intégration SSIS et les erreurs, consultez [Runtime d’intégration Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Nous vous recommandons également de définir un délai d’expiration sous l’onglet **Général** : ![Définir des propriétés sous l’onglet Général](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png).

### <a name="poor-performance-in-package-execution"></a>Problèmes de performances dans l’exécution des packages

Essayez les actions suivantes :

* Vérifiez que le runtime d’intégration SSIS se trouve dans la même région que la source et la destination de données.

* Définissez le niveau de journalisation de l’exécution de package sur **Performances** pour collecter des informations de durée pour chaque composant de l’exécution. Pour plus d’informations, consultez [Journalisation d’Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging).

* Vérifier les performances de nœuds de runtime d’intégration dans le portail Azure :
  * Pour plus d’informations sur la façon de superviser le runtime d’intégration SSIS, consultez [Runtime d’intégration Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Vous trouverez l’historique du processeur/de la mémoire pour le runtime d’intégration SSIS en consultant les métriques de la fabrique de données dans le portail Azure.
    ![Superviser les métriques du runtime d’intégration SSIS](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
