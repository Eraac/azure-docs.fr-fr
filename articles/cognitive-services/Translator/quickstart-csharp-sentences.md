---
title: 'Démarrage rapide : Obtenir la longueur des phrases, C# - API de traduction de texte Translator Text'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez apprendre à déterminer la longueur de phrase à l’aide de .NET Core et de l’API de traduction de texte Translator Text.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/13/2019
ms.author: swmachan
ms.openlocfilehash: 1c711639d5566f8909543901e30bd774abbe8a0d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704583"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-c"></a>Démarrage rapide : Utiliser l’API de traduction de texte Translator Text et C# pour déterminer la longueur de phrase

Dans ce guide de démarrage rapide, vous allez apprendre à déterminer les longueurs de phrases à l’aide de .NET Core, de C# 7.1 ou version ultérieure et de l’API de traduction de texte Translator Text.

Pour suivre ce démarrage rapide, vous devrez disposer d’un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource Traduction de texte Translator Text. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/) pour obtenir une clé d’abonnement.

>[!TIP]
> Si vous souhaitez voir tout le code à la fois, le code source de cet exemple est disponible sur [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp).

## <a name="prerequisites"></a>Prérequis

* C# 7.1 ou version ultérieure
* [Kit de développement logiciel (SDK) .NET](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Package NuGet Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou l’éditeur de texte de votre choix
* Une clé d’abonnement Azure pour Translator Text

## <a name="create-a-net-core-project"></a>Créer un projet .NET Core

Ouvrez une nouvelle invite de commandes (ou une session Terminal Server) et exécutez les commandes suivantes :

```console
dotnet new console -o sentences-sample
cd sentences-sample
```

La première commande effectue deux opérations. Elle crée une application console .NET et un répertoire nommé `sentences-sample`. La deuxième commande bascule vers le répertoire de votre projet.

Ensuite, vous devez installer Json.Net. À partir du répertoire de votre projet, exécutez :

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Sélectionner la version du langage C#

Ce démarrage rapide requiert C# 7.1 ou version ultérieure. Il existe plusieurs façons de changer la version C# de votre projet. Dans ce guide, nous allons vous montrer comment ajuster le fichier `sentences-sample.csproj`. Pour toutes les options disponibles, comme la modification du langage dans Visual Studio, consultez [Sélectionner la version du langage C#](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Ouvrez votre projet, puis ouvrez `sentences-sample.csproj`. Vérifiez que `LangVersion` est défini sur 7.1 ou version ultérieure. En l’absence d’un groupe de propriétés pour la version du langage, ajoutez ces lignes :

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Ajouter les espaces de noms requis à votre projet

La commande `dotnet new console` que vous avez exécutée précédemment a créé un projet, notamment `Program.cs`. C’est dans ce fichier que vous placerez votre code d’application. Ouvrez `Program.cs` et remplacez les instructions using existantes. Ces instructions garantissent que vous avez accès à tous les types nécessaires pour générer et exécuter l’exemple d’application.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Créer des classes pour la réponse JSON

Ensuite, nous allons créer une classe qui est utilisée lors de la désérialisation de la réponse JSON retournée par l’API Translator Text.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class BreakSentenceResult
{
    public int[] SentLen { get; set; }
    public DetectedLanguage DetectedLanguage { get; set; }
}

public class DetectedLanguage
{
    public string Language { get; set; }
    public float Score { get; set; }
}
```

## <a name="create-a-function-to-determine-sentence-length"></a>Créer une fonction pour déterminer la longueur de phrase

Dans la classe `Program`, créez une fonction nommée `BreakSentence()`. Cette fonction prend quatre arguments : `subscriptionKey`, `host`, `route` et `inputText`.

```csharp
static public async Task BreakSentenceRequest(string subscriptionKey, string host, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-break-sentence-request"></a>Sérialiser la requête de rupture de phrase

Ensuite, vous devez créer et sérialiser l’objet JSON qui inclut le texte. N’oubliez pas que vous pouvez passer plusieurs objets dans le tableau `body`.

```csharp
object[] body = new object[] { new { Text = inputText } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Instancier le client et effectuer une requête

Ces lignes instancient `HttpClient` et `HttpRequestMessage` :

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Construire la requête et imprimer la réponse

Dans `HttpRequestMessage`, vous allez :

* Déclarer la méthode HTTP.
* Construire l’URI de requête.
* Insérer le corps de la requête (objet JSON sérialisé).
* Ajouter des en-têtes obligatoires.
* Effectuer une requête asynchrone.
* Imprimer la réponse

Ajoutez ce code à `HttpRequestMessage` :

```csharp
// Build the request.
// Set the method to POST
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(host + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
BreakSentenceResult[] deserializedOutput = JsonConvert.DeserializeObject<BreakSentenceResult[]>(result);
foreach (BreakSentenceResult o in deserializedOutput)
{
    Console.WriteLine("The detected language is '{0}'. Confidence is: {1}.", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    Console.WriteLine("The first sentence length is: {0}", o.SentLen[0]);
}
```

Si vous utilisez un abonnement multiservice Cognitive Services, vous devez également inclure la `Ocp-Apim-Subscription-Region` dans vos paramètres de requête. [En savoir plus sur l’authentification sur l’abonnement multiservice](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Assemblage

La dernière étape consiste à appeler `BreakSentenceRequest()` dans la fonction `Main`. Recherchez `static void Main(string[] args)` et remplacez-le par ce code :

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    string subscriptionKey = "YOUR_TRANSLATOR_TEXT_KEY_GOES_HERE";
    string host = "https://api.cognitive.microsofttranslator.com";
    string route = "/breaksentence?api-version=3.0";
    // Feel free to use any string.
    string breakSentenceText = @"How are you doing today? The weather is pretty pleasant. Have you been to the movies lately?";
    await BreakSentenceRequest(subscriptionKey, host, route, breakSentenceText);
}
```

Vous remarquerez que dans `Main`, vous déclarez `subscriptionKey`, `host`, `route` et le texte à évaluer `breakSentenceText`.

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Voilà, vous êtes prêt à exécuter votre exemple d’application. À partir de la ligne de commande (ou d’une session terminal), accédez au répertoire de votre projet, puis exécutez :

```console
dotnet run
```

## <a name="sample-response"></a>Exemple de réponse

Après avoir exécuté l’exemple, vous devriez voir ce qui suit sur le terminal :

```bash
The detected language is \'en\'. Confidence is: 1.
The first sentence length is: 25
```

Ce message est généré à partir de JSON brut, qui se présente comme suit :

```json
[
    {
        "detectedLanguage":
        {
            "language":"en",
            "score":1.0
        },
        "sentLen":[25,32,35]
    }
]
```

## <a name="clean-up-resources"></a>Supprimer des ressources

N’oubliez pas de supprimer toutes les informations confidentielles (telles que les clés d’abonnement) dans le code source de votre exemple d’application.

## <a name="next-steps"></a>Étapes suivantes

Consultez les informations de référence sur l’API pour comprendre tout ce que vous pouvez faire avec l’API Traduction de texte Translator Text.

> [!div class="nextstepaction"]
> [Informations de référence sur l'API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>Voir aussi

* [Traduire le texte](quickstart-csharp-translate.md)
* [Translittérer du texte](quickstart-csharp-transliterate.md)
* [Identifier la langue par entrée](quickstart-csharp-detect.md)
* [Obtenir des traductions alternatives](quickstart-csharp-dictionary.md)
* [Obtenir une liste des langues prises en charge](quickstart-csharp-languages.md)
* [Déterminer la longueur des phrases depuis une entrée](quickstart-csharp-sentences.md)
