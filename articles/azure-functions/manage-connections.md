---
title: Gérer les connexions dans Azure Functions
description: Découvrez comment éviter les problèmes de performances dans Azure Functions à l’aide de clients de connexion statiques.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: 69425129d5f049254a60032283ddc6ca2ab84d5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65872691"
---
# <a name="manage-connections-in-azure-functions"></a>Gérer les connexions dans Azure Functions

Functions dans une application de fonction partage des ressources. Parmi ces ressources partagées se trouvent les connexions : connexions HTTP, connexions de base de données et connexions aux services tels que Stockage Azure. L’exécution de nombreuses fonctions simultanément peut engendrer une pénurie de connexions disponibles. Cet article explique comment coder vos fonctions pour n’utiliser que le nombre de connexions nécessaire.

## <a name="connection-limit"></a>Limite de connexions

Le nombre de connexions disponibles est limité en partie, car une application de fonction s’exécute dans l’[environnement de bac à sable](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). L’une des restrictions imposées par le bac à sable sur votre code est une limite du nombre de connexions sortantes, qui s’élève actuellement à 600 connexions actives (1 200 au total) par instance. Quand vous atteignez cette limite, le runtime des fonctions écrit le message suivant dans les journaux : `Host thresholds exceeded: Connections`. Pour plus d’informations, voir [Functions service limits (Limites de service Functions)](functions-scale.md#service-limits).

Cette limite s’effectue par instance. Quand le [contrôleur de mise à l’échelle ajoute des instances d’application de fonction](functions-scale.md#how-the-consumption-and-premium-plans-work) pour gérer plus de requêtes, chaque instance dispose d’une limite de connexion indépendante. Cela signifie qu’il n’existe aucune limite globale de connexion et que vous pouvez avoir beaucoup plus de 600 connexions actives sur toutes les instances actives.

Lors du dépannage, assurez-vous que vous avez activé Application Insights pour votre application de fonction. Application Insights vous permet d’afficher les métriques pour vos applications de fonction comme les exécutions. Pour plus d’informations, consultez l’article [Afficher les données de télémétrie dans Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Clients statiques

Pour éviter d’avoir plus de connexions que nécessaire, réutilisez les instances clientes au lieu d’en créer à chaque appel de fonction. Nous vous recommandons de réutiliser les connexions clientes pour tous les langages dans lesquels vous êtes susceptible d’écrire votre fonction. Par exemple, les clients .NET, comme les clients [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) et Stockage Azure, peuvent gérer les connexions si vous utilisez un seul client statique.

Voici quelques recommandations à suivre quand vous utilisez un client spécifique au service dans une application Azure Functions :

- *Ne créez pas* un client à chaque appel de fonction.
- *Créez* un client statique unique qui peut être utilisé par chaque appel de fonction.
- *Envisagez* de créer un client statique unique dans une classe d’assistance partagée si différentes fonctions utilisent le même service.

## <a name="client-code-examples"></a>Exemples de code client

Cette section présente les meilleures pratiques en matière de création et d’utilisation de clients à partir de votre code Function.

### <a name="httpclient-example-c"></a>Exemple HttpClient (C#)

Voici un exemple de code Function C# créant une instance [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) statique :

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Voici une question fréquente sur [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) dans .NET : « Dois-je supprimer mon client ? » En règle générale, vous supprimez les objets qui implémentent `IDisposable` quand vous avez terminé de les utiliser. En revanche, vous ne supprimez pas un client statique, car vous n’avez pas terminé de l’utiliser quand la fonction prend fin. Vous souhaitez que le client statique existe pendant la durée de votre application.

### <a name="http-agent-examples-javascript"></a>Exemples d’agent HTTP (JavaScript)

Parce qu’elle fournit des meilleures options de gestion des connexions, vous devez utiliser la classe [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) native au lieu de méthodes non natives, telles que le module `node-fetch`. Les paramètres de connexion sont configurés à l’aide d’options sur la classe `http.agent`. Pour les options détaillées disponibles avec l’agent HTTP, voir [new Agent(\[options\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

La classe `http.globalAgent` globale utilisée par `http.request()` dispose de toutes ces valeurs définies par défaut. La méthode recommandée pour configurer des limites de connexion dans Functions consiste à définir un nombre maximal à l’échelle globale. L’exemple suivant définit le nombre maximal de sockets pour l’application de fonction :

```js
http.globalAgent.maxSockets = 200;
```

 L’exemple suivant crée une requête HTTP avec un agent HTTP personnalisé uniquement pour cette requête :

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Exemple de code DocumentClient (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) se connecte à une instance d’Azure Cosmos DB. La documentation d’Azure Cosmos DB recommande [d’utiliser un client Azure Cosmos DB singleton pendant la durée de vie de votre application](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). L’exemple suivant montre un modèle pour effectuer cette opération dans une fonction :

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>Exemple de code CosmosClient (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) se connecte à une instance Azure Cosmos DB. La documentation d’Azure Cosmos DB recommande [d’utiliser un client Azure Cosmos DB singleton pendant la durée de vie de votre application](../cosmos-db/performance-tips.md#sdk-usage). L’exemple suivant montre un modèle pour effectuer cette opération dans une fonction :

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const masterKey = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, auth: { masterKey } });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { result: itemArray } = await container.items.readAll().toArray();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>Connexions SqlClient

Le code de votre fonction peut utiliser le fournisseur de données .NET Framework pour SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) afin d’établir des connexions à une base de données relationnelle SQL. Il s’agit également du fournisseur sous-jacent pour les infrastructures de données qui s’appuient sur ADO.NET, comme [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Contrairement aux connexions [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) et [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), ADO.NET implémente par défaut le regroupement de connexions. Cependant, comme vous êtes toujours susceptible d’avoir un nombre insuffisant de connexions, vous devez optimiser les connexions à la base de données. Pour plus d’informations, consultez [Regroupement de connexions SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Certaines infrastructures de données, comme Entity Framework, obtiennent généralement les chaînes de connexion auprès de la section **ConnectionStrings** d’un fichier de configuration. Dans ce cas, vous devez ajouter explicitement les chaînes de connexion de base de données SQL à la collection **Chaînes de connexion** de vos paramètres d’application de fonction et dans le [fichier local.settings.json](functions-run-local.md#local-settings-file) de votre projet local. Si vous créez une instance de [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) dans votre code de fonction, vous devez stocker la valeur de la chaîne de connexion dans les **Paramètres d’application** avec vos autres connexions.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’intérêt que présentent les clients statiques, consultez [Antimodèle d’instanciation incorrect](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Pour obtenir des conseils permettant d’améliorer les performances d’Azure Functions, consultez [Optimisation des performances et de la fiabilité d’Azure Functions](functions-best-practices.md).
