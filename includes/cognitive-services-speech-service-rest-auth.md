---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: 22a95be43f06e95a6067b179b3023ba94ee5795d
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362510"
---
## <a name="authentication"></a>Authentication

Chaque requête nécessite un en-tête d’autorisation. Ce tableau présente les en-têtes pris en charge pour chaque service :

| En-têtes d'autorisation pris en charge | Reconnaissance vocale | Synthèse vocale |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | OUI | Non |
| Autorisation : Support | OUI | OUI |

Lorsque vous utilisez l’en-tête `Ocp-Apim-Subscription-Key`, vous devez uniquement fournir votre clé d’abonnement. Par exemple :

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Lorsque vous utilisez l’en-tête `Authorization: Bearer`, vous devez envoyer une demande au point de terminaison `issueToken`. Dans cette requête, vous échangez votre clé d’abonnement contre un jeton d’accès valide pendant 10 minutes. Dans les sections suivantes, vous apprendrez à obtenir et utiliser un jeton.

### <a name="how-to-get-an-access-token"></a>Obtenir un jeton d’accès

Pour obtenir un jeton d’accès, vous devrez envoyer une demande au point de terminaison `issueToken` à l’aide de `Ocp-Apim-Subscription-Key` et de votre clé d’abonnement.

Ces régions et points de terminaison sont pris en charge :

[!INCLUDE [](./cognitive-services-speech-service-endpoints-token-service.md)]

Utilisez ces exemples pour créer votre demande de jeton d’accès.

#### <a name="http-sample"></a>Exemple HTTP

Cet exemple est une simple requête HTTP pour obtenir un jeton. Remplacez `YOUR_SUBSCRIPTION_KEY` par votre clé d’abonnement de service de reconnaissance vocale. Si votre abonnement n’est pas dans la région USA Ouest, remplacez l’en-tête `Host` par le nom d’hôte de votre région.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Le corps de la réponse contient le jeton d’accès au format JSON Web Token (JWT).

#### <a name="powershell-sample"></a>Exemple de code PowerShell

Cet exemple est un simple script PowerShell pour obtenir un jeton d’accès. Remplacez `YOUR_SUBSCRIPTION_KEY` par votre clé d’abonnement de service de reconnaissance vocale. Veillez à utiliser le point de terminaison correct pour la région correspondant à votre abonnement. Cet exemple est actuellement configuré pour l’Ouest des États-Unis.

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>Exemple cURL

cURL est un outil en ligne de commande disponible dans Linux (ainsi que dans le sous-système Windows pour Linux). Cette commande cURL montre comment obtenir un jeton d’accès. Remplacez `YOUR_SUBSCRIPTION_KEY` par votre clé d’abonnement de service de reconnaissance vocale. Veillez à utiliser le point de terminaison correct pour la région correspondant à votre abonnement. Cet exemple est actuellement configuré pour l’Ouest des États-Unis.

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Exemple de code C#

La classe C# montre comment obtenir un jeton d’accès. Transmettez votre clé d’abonnement du service Speech quand vous instanciez la classe. Si votre abonnement ne figure pas dans la région Ouest des États-Unis, modifiez la valeur `FetchTokenUri` afin qu’elle corresponde à la région de votre abonnement.

```cs
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

#### <a name="python-sample"></a>Exemple de code Python

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'


def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>Utiliser un jeton d’accès

Le jeton d’accès doit être envoyé au service en tant qu’en-tête `Authorization: Bearer <TOKEN>`. Chaque jeton d’accès est valide pour une durée de 10 minutes. Vous pouvez à tout moment obtenir un nouveau jeton, mais pour réduire la latence et le trafic réseau, nous recommandons d’utiliser le même jeton durant neuf minutes.

Voici un exemple de demande HTTP envoyée à l’API REST de synthèse vocale :

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
