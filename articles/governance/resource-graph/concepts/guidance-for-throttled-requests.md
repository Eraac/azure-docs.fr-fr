---
title: Instructions pour les requêtes limitées
description: Découvrez comment optimiser vos requêtes afin d’éviter que les requêtes à Azure Resource Graph ne soient limitées.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c644d230846d9c644c3845348431eef36c8279c8
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276562"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Conseils pour les requêtes limitées dans Azure Resource Graph

Lors de la programmation et de l’utilisation fréquente des données Azure Resource Graph, vous devez prendre en compte la façon dont la limitation affecte les résultats des requêtes. Changer la manière dont les données sont demandées peut vous aider, vous et votre organisation, à éviter toute limitation et à maintenir le flux de données en temps voulu concernant vos ressources Azure.

Cet article traite de quatre domaines et modèles en rapport avec la création de requêtes dans Azure Resource Graph :

- Présentation des en-têtes de limitation
- Traitement par lot des requêtes
- Échelonnement des requêtes
- Impact de la pagination

## <a name="understand-throttling-headers"></a>Présentation des en-têtes de limitation

Azure Resource Graph alloue un quota à chaque utilisateur en fonction d’une fenêtre de temps. Par exemple, un utilisateur peut envoyer au maximum 15 requêtes durant chaque fenêtre de cinq secondes sans être limité. La valeur de quota est déterminée par de nombreux facteurs et est susceptible de changer.

Dans chaque réponse de requête, Azure Resource Graph ajoute deux en-têtes de limitation :

- `x-ms-user-quota-remaining` (int) : quota de ressources restant pour l'utilisateur. Cette valeur correspond au nombre de requêtes.
- `x-ms-user-quota-resets-after` (hh:mm:ss) : délai à attendre avant la réinitialisation de la consommation du quota d’un utilisateur.

Pour illustrer le fonctionnement des en-têtes, jetons un œil à une réponse de requête qui a les en-têtes et valeurs `x-ms-user-quota-remaining: 10` et `x-ms-user-quota-resets-after: 00:00:03`.

- Au cours des trois secondes suivantes, 10 requêtes au plus peuvent être envoyées sans limitation.
- Dans trois secondes, les valeurs de `x-ms-user-quota-remaining` et `x-ms-user-quota-resets-after` seront réinitialisées respectivement à `15` et `00:00:05`.

Pour voir un exemple d’utilisation des en-têtes pour effectuer une _interruption_ lors des demandes de requête, consultez l’exemple fourni dans [Interroger en parallèle](#query-in-parallel).

## <a name="batching-queries"></a>Traitement par lot des requêtes

Traiter les requêtes par lot d’après l’abonnement, le groupe de ressources ou la ressource est plus efficace que paralléliser les requêtes. Le coût du quota d’une requête plus grande est souvent inférieur à celui de nombreuses requêtes petites et ciblées. Nous vous recommandons de choisir une taille de lot inférieure à _300_.

- Exemple d’approche mal optimisée

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Exemple n°1 d’approche de traitement par lot optimisée

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int batchSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / batchSize; ++i)
  {
      var currSubscriptionBatch = subscriptionIds.Skip(i * batchSize).Take(batchSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionBatch,
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Exemple n°2 d’approche de traitement par lot optimisée

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int batchSize = 100;
  for (var i = 0; i <= resourceIds.Count / batchSize; ++i)
  {
      var resourceIdBatch = string.Join(",",
          resourceIds.Skip(i * batchSize).Take(batchSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Échelonnement des requêtes

En raison de la manière dont la limitation est appliquée, nous vous recommandons d’échelonner les requêtes. Autrement dit, au lieu d’envoyer 60 requêtes en même temps, échelonnez-les dans quatre fenêtres de cinq secondes :

- Planification de requêtes sans échelonnement

  | Nombre de requêtes         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Intervalle de temps (sec) | 0-5 | 5-10 | 10-15 | 15-20 |

- Planification de requêtes avec échelonnement

  | Nombre de requêtes         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Intervalle de temps (sec) | 0-5 | 5-10 | 10-15 | 15-20 |

Voici un exemple du respect des en-têtes de limitation lors de l’interrogation d’Azure Resource Graph :

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>Interroger en parallèle

Bien que le traitement par lot soit préférable à la parallélisation, il y a des moments où les requêtes ne peuvent pas facilement être traitées par lot. Dans ces cas-là, vous souhaiterez peut-être interroger Azure Resource Graph en envoyant plusieurs requêtes de manière parallèle. Voici un exemple montrant comment effectuer une _interruption_ sur la base des en-têtes de limitation dans ce genre de scénario :

```csharp
IEnumerable<IEnumerable<string>> queryBatches = /* Batches of queries  */
// Run batches in parallel.
await Task.WhenAll(queryBatches.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>Pagination

Étant donné qu’Azure Resource Graph retourne au maximum 1000 entrées dans une même réponse de requête, vous devrez peut-être [paginer](./work-with-data.md#paging-results) vos requêtes afin d’obtenir le jeu de données complet que vous recherchez. Toutefois, certains clients Azure Resource Graph gèrent la pagination différemment des autres.

- Kit de développement logiciel (SDK) C#

  Quand vous utilisez le SDK ResourceGraph, vous devez gérer la pagination en passant le jeton d’évitement ($skiptoken) retourné à partir de la réponse de requête précédente à la requête paginée suivante. Cette conception signifie que vous devez recueillir les résultats de tous les appels paginés et les combiner à la fin. Dans ce cas, chaque requête paginé que vous envoyez prend un quota de requête :

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLI / Azure PowerShell

  Quand vous utilisez Azure CLI ou Azure PowerShell, les requêtes envoyées à Azure Resource Graph sont automatiquement paginées afin d’extraire au maximum 5000 entrées. Les résultats de requête retournent une liste combinée d’entrées à partir de tous les appels paginés. Dans ce cas, en fonction du nombre d’entrées dans le résultat de requête, une requête paginée peut consommer plus d’un quota de requête. Par exemple, dans l’exemple ci-dessous, une seule exécution de la requête peut consommer jusqu’à cinq quotas de requête :

  ```azurecli-interactive
  az graph query -q 'project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>Vous êtes toujours limité ?

Si vous êtes encore limité après avoir appliqué les recommandations ci-dessus, contactez l’équipe à l’adresse [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com).

Fournissez les détails suivants :

- Votre cas d’usage spécifique et les axes stratégiques qui imposent de disposer d’une limite plus élevée.
- À combien de ressources avez-vous accès ? Combien d’entre elles sont retournées par une requête unique ?
- Quels sont les types de ressources qui vous intéressent ?
- Quel est votre modèle de requête ? X requêtes toutes les Y secondes, etc.

## <a name="next-steps"></a>Étapes suivantes

- Examiner le langage utilisé dans les [requêtes de démarrage](../samples/starter.md).
- Examiner des utilisations avancées dans les [Requêtes avancées](../samples/advanced.md).
- Apprendre à [explorer les ressources](explore-resources.md).