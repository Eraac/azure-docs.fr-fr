---
title: Gérer des données de référence dans un environnement Azure Time Series Insights avec C# | Microsoft Docs
description: Cet article décrit comment gérer des données de référence pour un environnement Azure Time Series Insights en créant une application personnalisée écrite dans le langage C# (c-sharp) .NET.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 0a482ae44c796e1519ffad0a604510166b1ac63c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165608"
---
# <a name="manage-ga-reference-data-for-an-azure-time-series-insights-environment-by-using-c"></a>Gérer des données de référence GA pour un environnement Azure Time Series Insights à l’aide de C#

Cet article décrit un exemple de projet C# que vous pouvez compiler pour gérer des données de référence pour un environnement Azure Time Series Insights.

## <a name="prerequisites"></a>Prérequis

Effectuez les étapes suivantes avant de compiler et d'exécuter l’exemple de code :

1. [Créez un jeu de données de référence](time-series-insights-add-reference-data-set.md).

1. Configurez le jeton d'autorisation d'accès de l'application. Assurez-vous que le jeton d’accès est obtenu via l’API Azure Active Directory. Vous devez transmettre le jeton dans l’en-tête `Authorization` de chaque demande de l’API de requête.

   Pour plus d'informations sur la configuration d'applications non interactives, voir [Authentification et autorisation](time-series-insights-authentication-and-authorization.md).

1. Modifiez l’exemple de code pour remplacer les exemples de constantes, désignées dans **#DUMMY#** , au début du code.

> [!NOTE]
> Affichez l’exemple de code GA sur [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Dépendances du projet

Ajoutez des packages NuGet `Microsoft.IdentityModel.Clients.ActiveDirectory` et `Newtonsoft.Json` pour cet exemple.

## <a name="c-sample-code"></a>Exemple de code C#

```csharp
// Copyright (c) Microsoft Corporation.  All rights reserved.

using System;
using System.IO;
using System.Net;
using System.Threading.Tasks;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace TimeSeriesInsightsReferenceDataSampleApp
{
    public static class Program
    {
        // SET the environment fqdn.
        private static string EnvironmentFqdn = "#DUMMY#.env.timeseries.azure.com";

        // SET the environment reference data set name used when creating it.
        private static string EnvironmentReferenceDataSetName = "#DUMMY#";

        // For automated execution under application identity,
        // use application created in Active Directory.
        // To create the application in AAD, follow the steps provided here:
        // https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization

        // SET the application ID of application registered in your Azure Active Directory
        private static string ApplicationClientId = "#DUMMY#";

        // SET the application key of the application registered in your Azure Active Directory
        private static string ApplicationClientSecret = "#DUMMY#";

        // SET the Azure Active Directory tenant.
        private static string Tenant = "#DUMMY#.onmicrosoft.com";

        private static async Task DemoReferenceDataAsync()
        {
            // 1. Acquire an access token.
            string accessToken = await AcquireAccessTokenAsync();

            // 2. Put a new reference data item.
            {
                HttpWebRequest request = CreateWebRequest(accessToken);
                string input = @"
{
    ""put"": [{
        ""DeviceId"": ""Fan1"",
        ""Type"": ""Fan"",
        ""BladeCount"": ""3""
    }]
}";
                await SendRequestAsync(request, input);
                string output = await GetResponseAsync(request);
                PrintInputOutput(action: "Put", input: input, output: output);
            }

            // 3. Get reference data item.
            {
                HttpWebRequest request = CreateWebRequest(accessToken);
                string input = @"
{
    ""get"": [{
        ""DeviceId"": ""Fan1""
    }]
}";
                await SendRequestAsync(request, input);
                string output = await GetResponseAsync(request);
                PrintInputOutput(action: "Get", input: input, output: output);
            }

            // 4. Patch an existing reference data item.
            // Update BladeCount and add Colour.
            {
                HttpWebRequest request = CreateWebRequest(accessToken);
                string input = @"
{
    ""patch"": [{
        ""DeviceId"": ""Fan1"",
        ""BladeCount"": ""4"",
        ""Colour"": ""Brown""
    }]
}";
                await SendRequestAsync(request, input);
                string output = await GetResponseAsync(request);
                PrintInputOutput(action: "Patch", input: input, output: output);
            }

            // 5. Delete a property from an existing reference data item.
            {
                HttpWebRequest request = CreateWebRequest(accessToken);
                string input = @"
{
    ""deleteproperties"": [{
        ""key"": {
            ""DeviceId"": ""Fan1""
    },
        ""properties"": [""BladeCount""]
    }]
}";
                await SendRequestAsync(request, input);
                string output = await GetResponseAsync(request);
                PrintInputOutput(action: "DeleteProperties", input: input, output: output);
            }

            // 6. Delete an existing reference data item.
            {
                HttpWebRequest request = CreateWebRequest(accessToken);
                string input = @"
{
    ""delete"": [{
        ""DeviceId"": ""Fan1""
    }]
}";
                await SendRequestAsync(request, input);
                string output = await GetResponseAsync(request);
                PrintInputOutput(action: "Delete", input: input, output: output);
            }
        }

        private static async Task<string> AcquireAccessTokenAsync()
        {
            if (ApplicationClientId == "#DUMMY#" || ApplicationClientSecret == "#DUMMY#" || Tenant.StartsWith("#DUMMY#"))
            {
                throw new Exception(
                    $"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization"} to update the values of 'ApplicationClientId', 'ApplicationClientSecret' and 'Tenant'.");
            }

            var authenticationContext = new AuthenticationContext(
                $"https://login.windows.net/{Tenant}",
                TokenCache.DefaultShared);

            AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
                resource: "https://api.timeseries.azure.com/",
                clientCredential: new ClientCredential(
                    clientId: ApplicationClientId,
                    clientSecret: ApplicationClientSecret));

            // Show interactive logon dialog to acquire token on behalf of the user.
            // Suitable for native apps, and not on server-side of a web application.
            //AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
            //    resource: "https://api.timeseries.azure.com/",
            //    // Set well-known client ID for Azure PowerShell
            //    clientId: "1950a258-227b-4e31-a9cf-717495945fc2",
            //    // Set redirect URI for Azure PowerShell
            //    redirectUri: new Uri("urn:ietf:wg:oauth:2.0:oob"),
            //    parameters: new PlatformParameters(PromptBehavior.Auto));

            return token.AccessToken;
        }

        private static HttpWebRequest CreateWebRequest(string accessToken)
        {
            Uri uri = new UriBuilder("https", EnvironmentFqdn)
            {
                Path = $"referencedatasets/{EnvironmentReferenceDataSetName}/$batch",
                Query = "api-version=2016-12-12"
            }.Uri;
            HttpWebRequest request = WebRequest.CreateHttp(uri);
            request.Method = "POST";
            request.Headers.Add("x-ms-client-application-name", "TimeSeriesInsightsReferenceDataSample");
            request.Headers.Add("Authorization", "Bearer " + accessToken);
            return request;
        }

        private static async Task SendRequestAsync(HttpWebRequest request, string json)
        {
            using (var stream = await request.GetRequestStreamAsync())
            using (var streamWriter = new StreamWriter(stream))
            {
                await streamWriter.WriteAsync(json);
                await streamWriter.FlushAsync();
                streamWriter.Close();
            }
        }

        private static async Task<string> GetResponseAsync(HttpWebRequest request)
        {
            using (WebResponse webResponse = await request.GetResponseAsync())
            using (var sr = new StreamReader(webResponse.GetResponseStream()))
            {
                string result = await sr.ReadToEndAsync();
                return result;
            }
        }

        private static void PrintInputOutput(string action, string input, string output)
        {
            Console.WriteLine("=== {0} request ===", action);
            Console.WriteLine("Input: {0}", JsonConvert.SerializeObject(JsonConvert.DeserializeObject<JObject>(input), Formatting.Indented));
            Console.WriteLine("Output: {0}", JsonConvert.SerializeObject(JsonConvert.DeserializeObject<JObject>(output), Formatting.Indented));
            Console.WriteLine();
        }

        static void Main(string[] args)
        {
            Task.Run(async () => await DemoReferenceDataAsync()).Wait();
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Consultez [API Données de référence](/rest/api/time-series-insights/ga-reference-data-api).