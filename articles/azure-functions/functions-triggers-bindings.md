---
title: Déclencheurs et liaisons dans Azure Functions
description: Découvrez comment utiliser des déclencheurs et des liaisons pour connecter Azure Functions aux événements en ligne et aux services cloud.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 0896a829a24335f3e62581fa3b63e013478781bc
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480989"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Concepts des déclencheurs et liaisons Azure Functions

Dans cet article, vous allez découvrir des concepts généraux concernant les déclencheurs et les liaisons des fonctions.

Les déclencheurs sont à l’origine de l’exécution d’une fonction. Un déclencheur définit la façon dont une fonction est appelée. Chaque fonction est associée à un seul déclencheur. Les déclencheurs sont associés à des données, qui sont souvent fournies comme la charge utile de la fonction. 

La liaison d’une fonction permet de connecter de façon déclarative une autre ressource à la fonction. Les liaisons peuvent être connectées en tant que *liaisons d’entrée*, *liaisons de sortie*, ou les deux. Les données issues des liaisons sont fournies à la fonction en tant que paramètres.

Vous pouvez combiner différentes liaisons selon vos besoins. Les liaisons sont facultatives. Chaque fonction peut avoir une ou plusieurs liaisons d’entrée et de sortie.

Les déclencheurs et les liaisons vous évitent d’avoir à coder en dur l’accès aux autres services. Votre fonction reçoit des données (par exemple, le contenu d’un message de la file d’attente) dans les paramètres de fonction. Vous envoyez des données (par exemple pour créer un message de la file d’attente) en utilisant la valeur de retour de la fonction. 

Prenons les exemples suivants pour voir comment implémenter différentes fonctions.

| Exemple de scénario | Déclencheur | Liaison d’entrée | Liaison de sortie |
|-------------|---------|---------------|----------------|
| Un nouveau message en file d’attente arrive et exécute une fonction pour écrire des données dans une autre file d’attente. | File d’attente<sup>*</sup> | *Aucun* | File d’attente<sup>*</sup> |
|Une tâche planifiée lit le contenu du stockage d’objets blob et crée un document Cosmos DB. | Minuteur | Stockage Blob | Cosmos DB |
|Event Grid est utilisé pour lire une image du stockage d’objets blob et un document de Cosmos DB pour envoyer un e-mail. | Event Grid | Stockage d’objets blob et Cosmos DB | SendGrid |
| Webhook qui utilise Microsoft Graph pour mettre à jour une feuille Excel. | HTTP | *Aucun* | Microsoft Graph |

<sup>\*</sup> Représente différentes files d’attente

La liste de ces exemples n’est pas exhaustive. Elle est fournie pour montrer comment vous pouvez utiliser les déclencheurs et les liaisons ensemble.

###  <a name="trigger-and-binding-definitions"></a>Définitions de liaisons et de déclencheurs

La définition des déclencheurs et des liaisons varie selon l’approche de développement.

| Plateforme | Déclencheurs et liaisons configurés par… |
|-------------|--------------------------------------------|
| Bibliothèque de classes C# | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;la décoration des méthodes et des paramètres à l’aide d’attributs C# |
| Toutes les autres (y compris le portail Azure) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;la mise à jour de [function.json](./functions-reference.md) ([schéma](http://json.schemastore.org/function)) |

Le portail fournit une interface utilisateur pour cette configuration. Toutefois, vous pouvez modifier le fichier directement en ouvrant l’**Éditeur avancé** qui est disponible sous l’onglet **Intégrer** de votre fonction.

Dans .NET, le type de paramètre définit le type des données d’entrée. Par exemple, utilisez `string` pour établir une liaison au texte d’un déclencheur de file d’attente, un tableau d’octets à lire au format binaire et un type personnalisé pour désérialiser dans un objet.

Pour les langages dont le type est dynamique, tels que JavaScript, utilisez la propriété `dataType` dans le fichier *function.json*. Par exemple, pour lire le contenu d’une requête HTTP au format binaire, définissez `dataType` sur `binary` :

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Les autres options pour `dataType` sont `stream` et `string`.

## <a name="binding-direction"></a>Sens de la liaison

Tous les déclencheurs et liaisons ont une propriété `direction` dans le fichier [function.json](./functions-reference.md) :

- Pour les déclencheurs, le sens est toujours `in`
- Les liaisons d’entrée et de sortie utilisent `in` et `out`
- Certaines liaisons prennent en charge un sens spécial `inout`. Si vous utilisez `inout`, seule l’option **Éditeur avancé** est disponible sous l’onglet **Intégrer** du portail.

Lorsque vous utilisez des [attributs dans une bibliothèque de classes](functions-dotnet-class-library.md) pour configurer les déclencheurs et les liaisons, la direction est fournie dans un constructeur d’attribut ou déduite du type du paramètre.

## <a name="supported-bindings"></a>Liaisons prises en charge

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Pour plus d’informations sur les liaisons en préversion ou approuvées pour la production, consultez [Langages pris en charge](supported-languages.md).

## <a name="resources"></a>Ressources
- [Modèles et expressions de liaison](./functions-bindings-expressions-patterns.md)
- [Utilisation de la valeur de retour Azure Functions](./functions-bindings-return-value.md)
- [Guide pratique pour inscrire une expression de liaison](./functions-bindings-register.md)
- Test :
  - [Stratégies permettant de tester votre code dans Azure Functions](functions-test-a-function.md)
  - [Exécuter manuellement une fonction non déclenchée via HTTP](functions-manually-run-non-http.md)
- [Gestion des erreurs de liaison](./functions-bindings-errors.md)

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Inscrire des extensions de liaison Azure Functions](./functions-bindings-register.md)
