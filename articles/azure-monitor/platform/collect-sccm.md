---
title: Connexion de Configuration Manager à Azure Monitor | Microsoft Docs
description: Cet article décrit comment connecter Configuration Manager à l’espace de travail dans Azure Monitor et commencer à analyser des données.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: 26ddb0cdd2728f9dff5d45494a14841cdc1a20cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922884"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Connexion de Configuration Manager à Azure Monitor
Vous pouvez connecter votre environnement System Center Configuration Manager à Azure Monitor pour synchroniser les données de regroupement d’appareils et faire référence à ces regroupements dans Azure Monitor et Azure Automation.  

## <a name="prerequisites"></a>Prérequis

Azure Monitor prend en charge la branche actuelle de System Center Configuration Manager, version 1606 et supérieure.  

## <a name="configuration-overview"></a>Présentation de la configuration
Les étapes suivantes récapitulent les étapes de configuration de l’intégration de Configuration Manager avec Azure Monitor.  

1. Dans le portail Azure, inscrivez Configuration Manager en tant qu’application web et/ou application API web, et assurez-vous de disposer de l’ID et de la clé secrète client résultant de l’inscription à partir d’Azure Active Directory. Pour plus d’informations sur cette étape, consultez [Utiliser le portail pour créer une application et un principal du service Active Directory pouvant accéder aux ressources](../../active-directory/develop/howto-create-service-principal-portal.md).
2. Dans le portail Azure, [attribuez à Configuration Manager (l’application web inscrite) l’autorisation d’accéder à Azure Monitor](#grant-configuration-manager-with-permissions-to-log-analytics).
3. Dans Configuration Manager, ajoutez une connexion à l’aide de l’Assistant Ajout de connexion OMS.
4. Dans Configuration Manager, mettez à jour les propriétés de connexion en cas d’expiration ou de perte du mot de passe ou de la clé secrète client.
5. [Téléchargez et installez Microsoft Monitoring Agent](#download-and-install-the-agent) sur l’ordinateur exécutant le rôle de système de site de point de connexion de service de Configuration Manager. L’agent envoie les données de Configuration Manager vers l’espace de travail Log Analytics dans Azure Monitor.
6. Dans Azure Monitor, [importez les collections de Configuration Manager](#import-collections) en tant que groupes d’ordinateurs.
7. Dans Azure Monitor, consultez les données de Configuration Manager en tant que [groupes d’ordinateurs](computer-groups.md).

Pour plus d’informations sur la connexion de Configuration Manager à Azure Monitor, consultez [Synchroniser des données de Configuration Manager sur l’espace de travail Microsoft Log Analytics](https://technet.microsoft.com/library/mt757374.aspx).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Accorder à Configuration Manager les autorisations d’accès à Log Analytics
Dans la procédure suivante, vous attribuez le rôle *Contributeur* dans votre espace de travail Log Analytics à l’application et au principal de service AD que vous avez créés précédemment pour Configuration Manager.  Si vous ne disposez pas déjà d’un espace de travail, consultez [Créer un espace de travail dans Azure Monitor](../../azure-monitor/learn/quick-create-workspace.md) avant de continuer.  Cela permet à Configuration Manager de s’authentifier et de se connecter à votre espace de travail Log Analytics.  

> [!NOTE]
> Vous devez spécifier des autorisations dans l’espace de travail Log Analytics pour Configuration Manager. Sinon, vous recevez un message d’erreur quand vous utilisez l’Assistant Configuration dans Configuration Manager.
>

1. Dans le portail Azure, cliquez sur **Tous les services** en haut à gauche. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
2. Dans votre liste d’espaces de travail Log Analytics, sélectionnez l’espace de travail à modifier.
3. Dans le volet gauche, sélectionnez **Contrôle d’accès (IAM)** .
4. Dans la page Contrôle d'accès (IAM), cliquez sur **Ajouter une attribution de rôle** pour afficher le volet **Ajouter une attribution de rôle**.
5. Dans le volet **Ajouter une attribution de rôle**, ouvrez la liste déroulante **Rôle** et sélectionnez le rôle **Contributeur**.  
6. Dans la liste déroulante **Attribuer l’accès à**, sélectionnez l’application Configuration Manager créée précédemment dans AD, puis cliquez sur **OK**.  

## <a name="download-and-install-the-agent"></a>Téléchargement et installation de l’agent
Consultez l’article [Connecter des ordinateurs Windows au service Azure Monitor dans Azure](agent-windows.md) afin de mieux comprendre les méthodes disponibles pour l’installation de Microsoft Monitoring Agent sur l’ordinateur qui héberge le rôle de système de site de point de connexion de service de Configuration Manager.  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>Ajouter une connexion Log Analytics à Configuration Manager
Pour que vous puissiez ajouter une connexion Log Analytics, il faut que votre environnement Configuration Manager ait un [point de connexion de service](https://technet.microsoft.com/library/mt627781.aspx) configuré pour le mode en ligne.

1. Dans l’espace de travail **Administration** de Configuration Manager, sélectionnez **Connecteur OMS**. Cette opération ouvre l’**Assistant Ajout de connexion Log Analytics**. Sélectionnez **Suivant**.

   >[!NOTE]
   >OMS est désormais appelée Log Analytics, qui est une fonctionnalité d’Azure Monitor.
   
2. Dans l’écran **Général**, vérifiez que vous avez effectué les actions suivantes et que vous disposez des détails de chaque élément, puis sélectionnez **Suivant**.

   1. Dans le portail Azure, vous avez inscrit Configuration Manager en tant qu’application web et/ou application API web, et vous disposez de [l’ID client résultant de l’inscription](../../active-directory/develop/quickstart-register-app.md).
   2. Dans le portail Azure, vous avez créé une clé secrète d’application pour l’application inscrite dans Azure Active Directory.  
   3. Dans le portail Azure, vous avez accordé à l’application web inscrite l’autorisation d’accéder à l’espace de travail Log Analytics dans Azure Monitor.  
      ![Page générale de l’Assistant Connexion à Log Analytics](./media/collect-sccm/sccm-console-general01.png)
3. Dans l’écran **Azure Active Directory**, configurez vos paramètres de connexion à l’espace de travail Log Analytics en complétant les champs **Locataire**, **ID client** et **Clé secrète du client**, puis sélectionnez **Suivant**.  
   ![Connexion à la page Azure Active Directory de l’Assistant Connexion à Log Analytics](./media/collect-sccm/sccm-wizard-tenant-filled03.png)
4. Si vous avez correctement accompli toutes les autres procédures, les informations de l’écran **Configuration de la connexion OMS** s’affichent automatiquement dans cette page. Les informations des paramètres de connexion doivent s’afficher pour **Abonnement Azure**, **Groupe de ressources Azure** et **Espace de travail Operations Management Suite**.  
   ![Connexion à la page Connexion à Log Analytics de l’Assistant Connexion à Log Analytics](./media/collect-sccm/sccm-wizard-configure04.png)
5. L’Assistant se connecte à l’espace de travail Log Analytics avec les informations que vous avez entrées. Sélectionnez les regroupements d’appareils à synchroniser avec le service, puis cliquez sur **Ajouter**.  
   ![Sélectionner les regroupements](./media/collect-sccm/sccm-wizard-add-collections05.png)
6. Vérifiez vos paramètres de connexion dans l’écran **Résumé**, puis sélectionnez **Suivant**. L’écran **Progression** affiche l’état de la connexion, puis doit afficher **Terminé**.

> [!NOTE]
> Vous devez connecter le site de niveau supérieur de votre hiérarchie à Azure Monitor. Si vous connectez un site principal autonome à Azure Monitor, puis que vous ajoutez un site d’administration centrale à votre environnement, vous devez supprimer la connexion et la recréer au sein de la nouvelle hiérarchie.
>
>

Après avoir lié Configuration Manager à Azure Monitor, vous pouvez ajouter ou supprimer des regroupements, et afficher les propriétés de la connexion.

## <a name="update-log-analytics-workspace-connection-properties"></a>Mettre à jour les propriétés de connexion de l’espace de travail Log Analytics
En cas d’expiration ou de perte de mot de passe ou de clé secrète du client, vous devez mettre à jour manuellement les propriétés de connexion Log Analytics.

1. Dans Configuration Manager, accédez à **Services cloud**, puis sélectionnez **Connecteur OMS** pour ouvrir la page **Propriétés de connexion OMS**.
2. Dans cette page, cliquez sur l’onglet **Azure Active Directory** pour afficher vos informations **Client**, **ID client** et **Expiration de clé secrète client**. **Vérifiez** votre **Clé secrète client** pour voir si elle a expiré.

## <a name="import-collections"></a>Importer des regroupements
Après l’ajout d’une connexion Log Analytics à Configuration Manager et l’installation de l’agent sur l’ordinateur exécutant le rôle de système de site de point de connexion de service de Configuration Manager, l’étape suivante consiste à importer des regroupements de Configuration Manager dans Azure Monitor en tant que groupes d’ordinateurs.

Après la configuration initiale de l’importation des regroupements d’appareils à partir de votre hiérarchie, les informations d’appartenance au regroupement sont récupérées toutes les 3 heures dans l’optique de maintenir l’appartenance à jour. Vous pouvez désactiver cette fonction à tout moment.

1. Dans le portail Azure, cliquez sur **Tous les services** en haut à gauche. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Espaces de travail Log Analytics**.
2. Dans la liste des espaces de travail Log Analytics, sélectionnez l’espace de travail avec lequel Configuration Manager est inscrit.  
3. Sélectionnez **Paramètres avancés**.
4. Sélectionnez **Groupes d’ordinateurs**, puis **SCCM**.  
5. Sélectionnez **Importer les appartenances aux regroupements Configuration Manager**, puis cliquez sur **Enregistrer**.  
   ![Groupes d’ordinateurs - Onglet SCCM](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Afficher les données de Configuration Manager
Après l’ajout d’une connexion Log Analytics à Configuration Manager et l’installation de l’agent sur l’ordinateur exécutant le rôle de système de site de point de connexion de service de Configuration Manager, les données de l’agent sont envoyées à l’espace de travail Log Analytics dans Azure Monitor. Dans Azure Monitor, vos regroupements Configuration Manager apparaissent sous la forme de [groupes d’ordinateurs](../../azure-monitor/platform/computer-groups.md). Vous pouvez afficher les groupes à partir de la page **Configuration Manager**, sous **Paramètres\Groupes d’ordinateurs**.

Une fois les regroupements importés, vous pouvez voir combien d’ordinateurs avec des appartenances à des regroupements ont été détectés. Vous pouvez également voir le nombre de regroupements importés.

![Groupes d’ordinateurs - Onglet SCCM](./media/collect-sccm/sccm-computer-groups02.png)

Lorsque vous cliquez sur l’un deux, la fenêtre Recherche s’ouvre, affichant tous les groupes importés ou tous les ordinateurs appartenant à chaque groupe. Dans [Recherche de journal](../../azure-monitor/log-query/log-query-overview.md), vous pouvez démarrer une analyse approfondie des données Configuration Manager.

## <a name="next-steps"></a>Étapes suivantes
* Utilisez [Recherche de journal](../../azure-monitor/log-query/log-query-overview.md) pour afficher des informations détaillées sur vos données Configuration Manager.
