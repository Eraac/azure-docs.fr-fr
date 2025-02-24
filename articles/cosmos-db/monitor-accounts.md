---
title: Superviser les métriques de performances et de stockage dans Azure Cosmos DB
description: Découvrez comment surveiller, dans votre compte Azure Cosmos DB, les mesures de performances (notamment les demandes et les erreurs de serveur) et les mesures d’utilisation (par exemple, l’espace de stockage utilisé).
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 02bbde9a2d744c79cc8a7e95b0732b775c4dc695
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241626"
---
# <a name="monitor-performance-and-storage-metrics-in-azure-cosmos-db"></a>Superviser les métriques de performances et de stockage dans Azure Cosmos DB

Vous pouvez surveiller vos comptes Azure Cosmos DB dans le [portail Azure](https://portal.azure.com/). Pour chaque compte Azure Cosmos DB, une suite complète de mesures est disponible pour surveiller le débit, le stockage, la disponibilité, la latence et la cohérence.

Vous pouvez consulter les mesures sur la page Compte, sur la nouvelle page Mesures ou dans Azure Monitor.

## <a name="view-performance-metrics-on-the-metrics-page"></a>Affichage des mesures de performances sur la page Mesures
1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Tous les services**, faites défiler jusqu’à **Bases de données**, puis cliquez sur **Azure Cosmos DB**. Ensuite, cliquez sur le nom du compte Azure Cosmos DB dont vous souhaitez afficher les métriques de performances.
2. Lorsque la nouvelle page se charge, dans le menu de la ressource, sous **Surveillance**, cliquez sur **Mesures**.
3. Lorsque la page Mesures s’ouvre, sélectionnez la collection à réviser à partir de la liste déroulante **Collection(s)** .

   Le portail Azure affiche la suite des mesures de collection disponibles. Notez que les mesures de débit, de stockage, de disponibilité, de latence et de cohérence sont fournies sous des onglets distincts. Pour obtenir des détails supplémentaires sur les mesures fournies, cliquez sur la double flèche en haut à droite de chaque volet de mesures.

   ![Capture d’écran du filtre de surveillance qui montre la suite de mesures](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Affichage des mesures de performances à l’aide de la surveillance Azure
1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Surveillance** sur la barre de gauche.
2. Dans le menu de ressources, cliquez sur **Métrique**.
3. Dans la fenêtre **Surveillance - Métriques**, dans le menu déroulant **Groupe de ressources**, sélectionnez le groupe de ressources associé au compte Azure Cosmos DB que vous souhaitez surveiller. 
4. Dans le menu déroulant **Ressources**, sélectionnez le compte de base de données à surveiller.
5. Dans la liste des **métriques disponibles**, sélectionnez les mesures à afficher. Utilisez la touche CTRL pour en sélectionner plusieurs. 

## <a name="view-performance-metrics-on-the-account-page"></a>Affichage des mesures de performances sur la page Compte
1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Tous les services**, faites défiler jusqu’à **Bases de données**, puis cliquez sur **Azure Cosmos DB**. Ensuite, cliquez sur le nom du compte Azure Cosmos DB dont vous souhaitez afficher les métriques de performances.
2. Le filtre **Surveillance** affiche les vignettes suivantes par défaut :
   
   * le nombre total de demandes pour le jour en cours ;
   * Stockage utilisé.
   
   ![Capture d’écran du filtre de surveillance qui affiche les requêtes, ainsi que l’utilisation du stockage](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. En cliquant sur la double flèche dans l’angle supérieur droit de la mosaïque **Demandes**, une page détaillée **Mesure** s’affiche.
4. La page **Mesure** vous donne des détails sur le nombre total de demandes. 

## <a name="set-up-alerts-in-the-portal"></a>Configuration d’alertes dans le portail
1. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Tous les services**, puis sur **Azure Cosmos DB**. Cliquez ensuite sur le nom du compte Azure Cosmos DB pour lequel vous souhaitez configurer les alertes de métriques de performances.
2. Dans le menu de ressources, cliquez sur **Règles d’alerte** pour ouvrir la page Règles d’alerte.  
   ![Capture d’écran, avec la partie Règles d’alerte sélectionnée](./media/monitor-accounts/madocdb10.5.png)
3. Dans la page **Règles d’alerte**, cliquez sur **Ajouter une alerte**.  
   ![Capture d’écran de la page Règles d’alerte, avec le bouton Ajouter une alerte mis en surbrillance](./media/monitor-accounts/madocdb11.png)
4. Dans la page **Ajouter une règle d’alerte**, indiquez :
   
   * le nom de la règle d'alerte que vous configurez ;
   * une description de la nouvelle règle d'alerte ;
   * la mesure de la règle d'alerte ;
   * la condition, le seuil et la période déterminant l'activation de l'alerte. Par exemple, un nombre d'erreurs de serveur supérieur à 5 au cours des 15 dernières minutes.
   * l'envoi ou non d'un courrier électronique à l'administrateur de service ou aux coadministrateurs lorsque l'alerte se déclenche ;
   * Des adresses électroniques supplémentaires pour les notifications d'alerte.  
     ![Capture d’écran de la page Ajouter une règle d’alerte](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>Surveiller un compte Azure Cosmos DB par programme
Les métriques au niveau du compte disponibles dans le portail, par exemple les demandes d'utilisation du stockage du compte et de total, ne sont pas disponibles via les API SQL. Toutefois, vous pouvez extraire des données d'utilisation au niveau de la collection à l’aide des API SQL. Pour extraire des données au niveau de la collection, procédez comme suit :

* Pour utiliser l’API REST, [effectuez une opération GET sur la collection](https://msdn.microsoft.com/library/mt489073.aspx). Les informations de quota et d'utilisation de la collection sont retournées dans les en-têtes x-ms-resource-quota et x-ms-resource-usage de la réponse.
* Pour utiliser le Kit de développement logiciel (SDK) .NET, utilisez la méthode [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx), qui retourne une valeur [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) contenant un nombre de propriétés d’utilisation, notamment **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** et plus encore.

Pour accéder à des mesures supplémentaires, utilisez le [Kit de développement logiciel (SDK) Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). Les définitions de mesures disponibles peuvent être récupérées en appelant :

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Les requêtes permettant de récupérer des mesures individuelles utilisent le format suivant :

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Pour plus d’informations, consultez [Récupération des mesures de ressources via l’API REST Azure Monitor](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/). Notez que « Azure Insights » a été renommé « Azure Monitor ».  Cette entrée de blog fait référence à l’ancien nom.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la planification de la capacité d’Azure Cosmos DB, consultez le [planificateur de capacité Azure Cosmos DB](https://www.documentdb.com/capacityplanner).

