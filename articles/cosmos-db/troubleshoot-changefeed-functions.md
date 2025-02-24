---
title: Diagnostiquer et résoudre les problèmes lors de l’utilisation du déclencheur Azure Cosmos DB dans Azure Functions
description: Problèmes courants, solutions de contournement et procédures de diagnostic relatifs à l’utilisation du déclencheur Azure Cosmos DB avec Azure Functions
author: ealsur
ms.service: cosmos-db
ms.date: 05/23/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 09ea70ac302806b4cb0e97fde92dda4208e3d659
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734516"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-trigger-in-azure-functions"></a>Diagnostiquer et résoudre les problèmes lors de l’utilisation du déclencheur Azure Cosmos DB dans Azure Functions

Cet article aborde les problèmes courants, solutions de contournement et procédures de diagnostic relatifs à l’utilisation du [déclencheur Azure Cosmos DB](change-feed-functions.md) avec Azure Functions.

## <a name="dependencies"></a>Les dépendances

Les liaisons et le déclencheur Azure Cosmos DB dépendent des packages d’extension plutôt que du runtime Azure Functions de base. Mettez systématiquement ces packages à jour, car ils peuvent comprendre des correctifs et de nouvelles fonctionnalités capables de résoudre les problèmes éventuels que vous pouvez rencontrer :

* Pour Azure Functions V2, consultez [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Pour Azure Functions V1, consultez [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Sauf indication explicite, le runtime mentionné dans cet article fait toujours référence à Azure Functions V2.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Utiliser le Kit de développement logiciel (SDK) Azure Cosmos DB de manière indépendante

La prise en charge des liaisons et du déclencheur Azure Cosmos DB représente la principale fonctionnalité de ce package d’extension. Celui-ci comprend également le [Kit de développement logiciel (SDK) Azure Cosmos DB .NET](sql-api-sdk-dotnet-core.md), ce qui est utile si vous souhaitez interagir par programmation avec Azure Cosmos DB sans utiliser de déclencheurs et de liaisons.

Si souhaitez utiliser le Kit de développement logiciel (SDK) Azure Cosmos DB, assurez-vous de n’ajouter aucune autre référence de package NuGet à votre projet. Au lieu de cela, **laissez la référence du Kit de développement logiciel (SDK) résoudre l’ensemble du package d’extension d’Azure Functions**. Utiliser le Kit de développement logiciel (SDK) Azure Cosmos DB indépendamment des déclencheurs et des liaisons

En outre, si vous créez manuellement votre propre instance du [client de Kit de développement logiciel (SDK) Azure Cosmos DB](./sql-api-sdk-dotnet-core.md), vous devez suivre le modèle en utilisant une seule instance du client [à l’aide d’une approche par modèle Singleton](../azure-functions/manage-connections.md#documentclient-code-example-c) . Ce processus vous permettra d’éviter les problèmes de socket éventuels lors de vos opérations.

## <a name="common-scenarios-and-workarounds"></a>Scénarios courants et solutions de contournement

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>La fonction Azure échoue et le message d’erreur « La collection n’existe pas » s’affiche

La fonction Azure échoue et le message d’erreur suivant s’affiche : « La collection de source "nom-de-la-collection" ("nom-de-la-base-de-données" pour une base de données) ou la collection de baux "nom-de-la-collection2" ("nom-de-la-base-de-données2" pour une base de données) n’existe pas. Les deux collections doivent exister avant le démarrage de l’écouteur. Pour créer automatiquement la collection de baux, définissez "CreateLeaseCollectionIfNotExists" sur "true" ».

Cela signifie qu’au moins l’un des deux conteneurs Azure Cosmos nécessaires au fonctionnement du déclencheur n’existent pas ou que la fonction Azure ne peut pas y accéder. **L’erreur indiquera elle-même les conteneurs et la base de données Azure Cosmos que le déclencheur recherche** en fonction de votre configuration.

1. Vérifiez l’attribut `ConnectionStringSetting` et assurez-vous qu’il **fait référence à un paramètre qui existe dans votre application de fonction Azure**. La valeur de cet attribut ne doit pas être la chaîne de connexion, mais le nom du paramètre de configuration.
2. Vérifiez que `databaseName` et `collectionName` existent dans votre compte Azure Cosmos. Si vous utilisez le remplacement de valeur automatique (à l’aide des modèles `%settingName%`), assurez-vous que le nom du paramètre existe dans votre application de fonction Azure.
3. Si vous ne spécifiez pas une collection `LeaseCollectionName/leaseCollectionName`, la valeur par défaut est « baux ». Vérifiez l’existence de ce type de conteneur. Bien que ce soit facultatif, vous pouvez définir l’attribut `CreateLeaseCollectionIfNotExists` dans votre déclencheur sur `true` pour le créer automatiquement.
4. Assurez-vous que la [configuration du pare-feu de votre compte Azure Cosmos](how-to-configure-firewall.md) ne bloque pas la fonction Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Le démarrage de la fonction Azure échoue et le message d’erreur « La collection de débit partagé doit avoir une clé de partition » s’affiche

Dans les versions précédentes, l’extension Azure Cosmos DB ne prenait pas en charge l’utilisation de conteneurs de baux créés au sein d’une [base de données de débit partagé](./set-throughput.md#set-throughput-on-a-database). Pour résoudre ce problème, mettez à jour l’extension [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) pour obtenir la dernière version.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Le démarrage de la fonction Azure échoue et le message d’erreur « Si elle est partitionnée, la collection de baux doit avoir une clé de partition correspondant à id » s’affiche.

Cette erreur signifie que votre conteneur de baux actuel est partitionné et que le chemin d’accès de la clé de partition n’est pas `/id`. Pour résoudre ce problème, vous devez recréer le conteneur de baux en utilisant `/id` comme clé de partition.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Lorsque vous essayez d’exécuter le déclencheur, le message « La valeur ne peut pas être Null. Nom du paramètre : o » s’affiche dans vos journaux Azure Functions.

Ce problème se produit si vous utilisez le portail Azure et que vous essayez de sélectionner le bouton **Exécuter** sur l’écran lors de l’inspection d’une fonction Azure qui utilise le déclencheur. Il n’est pas nécessaire de sélectionner Exécuter pour démarrer le déclencheur. Il démarrera automatiquement lorsque la fonction Azure sera déployée. Si vous souhaitez vérifier le flux de journaux de la fonction Azure sur le portail Azure, il vous suffit d’accéder à votre conteneur surveillé et d’insérer de nouveaux éléments, puis vous verrez votre déclencheur s’exécuter automatiquement.

### <a name="my-changes-take-too-long-be-received"></a>Mes modifications prennent top de temps à s’effectuer

Il existe plusieurs causes possibles pour ce scénario et vous devez vérifier chacune d’entre elles :

1. Votre fonction Azure est-elle déployée dans la même région que votre compte Azure Cosmos ? Pour une latence réseau optimale, la fonction Azure et votre compte Azure Cosmos doivent être colocalisés dans la même région Azure.
2. Les modifications se produisant dans votre conteneur Azure Cosmos sont-elles continues ou sporadiques ?
Dans le deuxième cas, il peut y avoir un délai entre le stockage de vos modifications et leur récupération par la fonction Azure. En effet, en interne, lorsque le déclencheur recherche les modifications dans votre conteneur Azure Cosmos et n’en trouve aucune en attente de lecture, il se met en veille pendant une durée configurable (par défaut, cinq secondes) avant de recommencer à chercher de nouvelles modifications (ce qui permet d’éviter une consommation trop élevée d’unités de requête). Vous pouvez configurer la durée de veille à l’aide du paramètre `FeedPollDelay/feedPollDelay` dans la [configuration](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) de votre déclencheur (la valeur indiquée est en millisecondes).
3. Votre conteneur Azure Cosmos peut avoir une [limitation de débit](./request-units.md).
4. Vous pouvez utiliser l’attribut `PreferredLocations` dans votre déclencheur pour spécifier une liste de régions Azure séparée par des virgules permettant de définir un ordre de connexion privilégié et personnalisé.

### <a name="some-changes-are-missing-in-my-trigger"></a>Il manque certaines modifications dans mon déclencheur

Si vous découvrez que certaines modifications dans votre conteneur Azure Cosmos ne sont pas récupérées par la fonction Azure, vous devez suivre une étape pour inspecter en amont.

Lorsque votre fonction Azure reçoit les modifications, elle les traite souvent et peut éventuellement envoyer les résultats à une autre destination. Lorsque vous recherchez des modifications manquantes, assurez-vous de **mesurer les modifications qui sont reçues au niveau du point d’ingestion** (au démarrage de la fonction Azure) et non à la destination.

S’il manque des modifications à la destination, cela peut signifier que des erreurs se produisent lors de l’exécution de la fonction Azure après la réception des modifications.

Dans ce scénario, la meilleure méthode consiste à ajouter `try/catch blocks` dans votre code et à l’intérieur de boucles susceptibles de traiter les modifications afin de détecter tout échec au niveau d’un sous-ensemble d’éléments particulier, puis de s’en occuper de manière appropriée (envoyez-les vers un autre stockage pour les analyser davantage ou réessayez). 

> [!NOTE]
> Par défaut, le déclencheur Azure Cosmos DB n’essaiera pas de traiter à nouveau les modifications si une exception non prise en charge est survenue lors de l’exécution du code. Cela signifie que les modifications ne sont pas arrivées à destination en raison d’une erreur lors de leur traitement.

Si vous découvrez que certaines modifications n’ont pas été reçues par votre déclencheur, l’existence d’une **autre fonction Azure en cours d’exécution** constitue le scénario le plus courant. Il peut s’agir d’une autre fonction Azure déployée dans Azure ou exécutée en local sur la machine d’un développeur qui a **exactement la même configuration** (conteneurs surveillés et de baux identiques). Dans ce dernier cas, cette fonction Azure vole un sous-ensemble des modifications que votre fonction Azure est censée traiter.

En outre, il est possible de confirmer ce scénario si vous connaissez le nombre d’instances d’application de fonction Azure en cours d’exécution dont vous disposez. Si vous examinez votre conteneur de baux et comptez le nombre d’éléments de bail qu’il contient, les valeurs distinctes de la propriété `Owner` devraient correspondre au nombre d’instances de votre application de fonction. Si les instances d’application de fonction Azure ont plus de propriétaires que prévu, cela signifie que ces propriétaires supplémentaires « volent » les modifications.

Un moyen simple pour contourner cette situation consiste à appliquer un préfixe `LeaseCollectionPrefix/leaseCollectionPrefix` à votre fonction avec une valeur nouvelle/différente. Vous pouvez également tester en utilisant un nouveau conteneur de baux.

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Une liaison peut uniquement être établie avec IReadOnlyList<Document> ou JArray

Cette erreur se produit si votre projet Azure Functions (ou tout autre projet référencé) contient une référence NuGet manuelle au Kit de développement logiciel (SDK) Azure Cosmos DB et qu’elle présente une version différente de celle fournie par [l’extension Azure Functions Cosmos DB](./troubleshoot-changefeed-functions.md#dependencies).

Pour contourner cette situation, supprimez la référence NuGet manuelle qui a été ajoutée et laissez la référence du Kit de développement logiciel (SDK) Azure Cosmos DB résoudre le package d’extension d’Azure Functions Cosmos DB.

## <a name="next-steps"></a>Étapes suivantes

* [Activer la supervision dans vos applications Azure Functions](../azure-functions/functions-monitoring.md)
* [Résolution des problèmes relatifs au Kit de développement logiciel (SDK) .NET Azure Cosmos DB](./troubleshoot-dot-net-sdk.md)