---
title: Authentification basée sur les certificats dans Azure Active Directory avec Azure Cosmos DB
description: Apprenez comment configurer une identité Azure AD pour l’authentification basée sur certificat pour accéder aux clés depuis Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: eb8c98df0f015244adf06a9b57f2223509f1f081
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082114"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-account"></a>Authentification par certificat d’une identité Azure AD pour accéder aux clés d’un compte Azure Cosmos

L’authentification par certificat permet d’authentifier votre application client en utilisant Azure Active Directory (Azure AD) avec un certificat client. Vous pouvez effectuer une authentification basée sur un certificat sur une machine sur laquelle vous avez besoin d’une identité, telle qu’une machine locale ou une machine virtuelle dans Azure. Votre application peut alors lire les clés Azure Cosmo DB sans avoir les clés directement dans l’application. Cet article décrit comment créer un exemple d’application Azure AD, la configurer pour l’authentification par certificat, se connecter à Azure en utilisant la nouvelle identité d’application, puis récupérer les clés de votre compte Azure Cosmos. Cet article utilise Azure PowerShell pour configurer les identités et fournit un exemple d’application C# qui authentifie et accède aux clés depuis votre compte Azure Cosmos.  

## <a name="prerequisites"></a>Prérequis

* Installez la [version la plus récente](/powershell/azure/install-az-ps) d’Azure PowerShell.

* Si vous n’avez pas [d’abonnement Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.

## <a name="register-an-app-in-azure-ad"></a>Inscrire une application dans Azure AD

Dans cette étape, vous inscrirez un exemple d’application web dans votre compte Azure AD. Cette application est ensuite utilisée pour lire les clés de votre compte Azure Cosmos. Suivez cette procédure pour inscrire une application : 

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Ouvrez le panneau **Azure Active Directory**, accédez au panneau Inscriptions des applications, puis sélectionnez **Nouvelle inscription**. 

   ![Nouvelle inscription d’application dans Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Remplissez le formulaire **Inscrire une application**, notamment les détails suivants :  

   * **Nom** : entrez un nom pour votre application. Cela peut être n’importe quel nom, tel que « sampleApp ».
   * **Types de comptes pris en charge** : choisissez **Comptes dans cet annuaire organisationnel uniquement (répertoire par défaut)** afin de permettre aux ressources de votre répertoire actuel d’accéder à cette application. 
   * **URL de redirection** : choisissez l’application de type **Web** et fournissez une URL pointant vers l’emplacement où votre application est hébergée. Il peut s’agir de n’importe quelle URL. Pour cet exemple, vous pouvez fournir une URL de test comme `https://sampleApp.com`, même si l’application n’existe pas.

   ![Inscription d’un exemple d’application web](./media/certificate-based-authentication/register-sample-web-app.png)

1. Une fois le formulaire rempli, sélectionnez **Inscrire**.

1. Une fois l’application inscrite, notez l’ **	ID (du client) d’application** et l’**ID d’objet**. Ces informations vous seront utiles plus tard. 

   ![Obtenir les ID de l’application et de l’objet](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Installer le module AzureAD

Dans cette étape, vous allez installer le module Azure AD PowerShell. Ce module est nécessaire pour obtenir l’ID de l’application que vous avez inscrite à l’étape précédente et associer un certificat autosigné à cette application. 

1. Ouvrez Windows PowerShell ISE avec des droits d’administrateur. Si vous n’avez pas déjà fait, installez le module AZ PowerShell et connectez-vous à votre abonnement. Si vous avez plusieurs abonnements, vous pouvez définir le contexte de votre abonnement actuel, comme indiqué dans les commandes suivantes :

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Installer et importer le module [AzureAD](/powershell/module/azuread/?view=azureadps-2.0)

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Connexion à votre compte Azure AD

Connectez-vous à votre compte AD Azure où vous avez inscrit l’application. Utilisez la commande Connect-AzureAD pour vous connecter à votre compte, entrez les informations d’identification de votre compte Azure dans la fenêtre contextuelle. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Créer un certificat auto-signé

Ouvrez une autre instance de Windows PowerShell ISE et exécutez les commandes suivantes pour créer un certificat autosigné et lire la clé associée au certificat :

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Créer les informations d’identification basées sur le certificat 

Ensuite, exécutez les commandes suivantes pour obtenir l’ID d’objet de votre application et créer les informations d’identification basées sur le certificat. Dans cet exemple, nous définissons l’expiration du certificat après un an. Mais de votre côté, vous pouvez la définir à n’importe quelle date de fin.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

La commande ci-dessus donne une sortie similaire à la capture d’écran ci-dessous :

![Sortie de la création d’informations d’identification basées sur un certificat](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Configurez votre compte Azure Cosmos pour utiliser la nouvelle identité

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Accédez à votre compte Azure Cosmos, ouvrez le panneau **Contrôle d’accès (IAM)** .

1. Sélectionnez **Ajouter**, puis **Ajouter une attribution de rôle**. Ajoutez le sampleApp que vous avez créé à l’étape précédente avec le rôle **Contributeur**, comme indiqué dans la capture d’écran suivante :

   ![Configurer le compte Azure Cosmos pour utiliser la nouvelle identité](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Une fois le formulaire rempli, sélectionnez **Enregistrer**


## <a name="access-the-keys-from-powershell"></a>Accéder aux clés à partir de PowerShell

Dans cette étape, vous vous connecterez à Azure en utilisant l’application et le certificat que vous avez créé et accéderez aux clés de votre compte Azure Cosmos. 

1. Tout d’abord, effacez les informations d’identification de compte Azure que vous avez utilisées pour vous connecter à votre compte. Vous pouvez effacer les informations d’identification à l’aide de la commande suivante :

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Vérifiez ensuite que vous pouvez vous connecter au Portail Microsoft Azure à l’aide des informations d’identification de l’application, puis accéder aux clés Azure Cosmos DB :

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

La commande précédente affichera les clés primaire et secondaire principales de votre compte Azure Cosmos. Vous pouvez consulter le journal d’activité de votre compte Azure Cosmos pour valider la réussite de la requête d’obtention des clés, ainsi que le déclenchement de l’événement par l’application « sampleApp ». 
 
![Valider l’appel d’obtention des clés dans Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>Accéder aux clés à partir d’une application C# 

Vous pouvez également valider ce scénario en accédant aux clés à partir d’une application C#. Par exemple, l’application de console C# suivante peut accéder aux clés de base de données Azure Cosmos DB en utilisant l’application inscrite dans Active Directory. Avant d’exécuter le code, mettez à jour les détails du compte tenantId, clientID, certName, nom du groupe de ressources, ID d’abonnement, Nom du compte Azure Cosmos. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
 
 
        /// <summary>
        /// Get an access token from Azure AD using client credentials.
        /// If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each
        /// </summary>
        private static async Task<AuthenticationResult> GetAccessToken(AuthenticationContext authContext, string resourceUri, ClientAssertionCertificate cert)
        {
            //
            // Get an access token from Azure AD using client credentials.
            // If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each.
            //
            AuthenticationResult result = null;
            int retryCount = 0;
            bool retry = false;
 
            do
            {
                retry = false;
                errorCode = 0;
 
                try
                {
                    result = await authContext.AcquireTokenAsync(resourceUri, cert);
                }
                catch (AdalException ex)
                {
                    if (ex.ErrorCode == "temporarily_unavailable")
                    {
                        retry = true;
                        retryCount++;
                        Thread.Sleep(3000);
                    }
 
                    Console.WriteLine(
                        String.Format("An error occurred while acquiring a token\nTime: {0}\nError: {1}\nRetry: {2}\n",
                        DateTime.Now.ToString(),
                        ex.ToString(),
                        retry.ToString()));
 
                    errorCode = -1;
                }
 
            } while ((retry == true) && (retryCount < 3));
            return result;
        }
    }
}
```

Ce script génère les clés primaire et secondaire principales, comme illustré dans la capture d’écran suivante :

![Sortie de l’application csharp](./media/certificate-based-authentication/csharp-application-output.png)

Tout comme dans la section précédente, vous pouvez consulter le journal d’activité de votre compte Azure Cosmos pour valider le déclenchement de l’événement de la requête d’obtention des clés par l’application « sampleApp ». 


## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser les clés Azure Cosmos à l’aide d’Azure Key Vault](access-secrets-from-keyvault.md)

* [Attributs de sécurité pour Azure Cosmos DB](cosmos-db-security-attributes.md)