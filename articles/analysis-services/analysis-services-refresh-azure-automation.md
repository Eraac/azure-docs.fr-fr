---
title: Actualiser des modèles Azure Analysis Services avec Azure Automation | Microsoft Docs
description: Découvrez comment coder des actualisations de modèle à l’aide d’Azure Automation.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 4cae93cff594ad561973f8029ea7335dc4c60263
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66356996"
---
# <a name="refresh-with-azure-automation"></a>Actualiser avec Azure Automation

Azure Automation et les runbooks PowerShell vous permettent d’effectuer des opérations d’actualisation de données automatiques sur vos modèles tabulaires Azure Analysis.  

L’exemple présenté dans cet article utilise les [modules SqlServer PowerShell](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Un exemple de runbook PowerShell illustrant l’actualisation d’un modèle est fourni plus loin dans cet article.  

## <a name="authentication"></a>Authentication

Tous les appels doivent être authentifiés avec un jeton Azure Active Directory (OAuth 2) valide.  L’exemple fourni dans cet article utilise un principal de service (SPN) pour s’authentifier auprès d’Azure Analysis Services.

Pour en savoir plus sur la création d’un principal de service, voir [Créer un principal de service à l’aide du portail Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="prerequisites"></a>Conditions préalables

> [!IMPORTANT]
> L’exemple suivant suppose que le pare-feu Azure Analysis Services est désactivé. Si le pare-feu est activé, l’adresse IP publique de l’initiateur de la demande doit figurer sur la liste verte du pare-feu.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Installez les modules SqlServer à partir de la galerie PowerShell.

1. Dans votre compte Azure Automation, cliquez sur **Modules**, puis **Parcourir la galerie**.

2. Dans la barre de recherche, recherchez **SqlServer**.

    ![Rechercher des modules](./media/analysis-services-refresh-azure-automation/1.png)

3. Sélectionnez SqlServer, puis cliquez sur **Importer**.
 
    ![Importer un module](./media/analysis-services-refresh-azure-automation/2.png)

4. Cliquez sur **OK**.
 
### <a name="create-a-service-principal-spn"></a>Créer un principal de service (SPN)

Pour en savoir plus sur la création d’un principal de service, consultez [Créer un principal du service à l’aide du portail Azure](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Configurer des autorisations dans Azure Analysis Services
 
Le principal du service que vous créez doit disposer d’autorisations d’administrateur du serveur sur le serveur. Pour plus d’informations, consultez [Ajouter un principal de service au rôle d’administrateur du serveur](analysis-services-addservprinc-admins.md)

## <a name="design-the-azure-automation-runbook"></a>Concevoir le runbook Azure Automation

1. Dans le compte Automation, créez une ressources **Informations d’identification** qui sera utilisée pour stocker le principal de service en toute sécurité.

    ![Créer des informations d’identification](./media/analysis-services-refresh-azure-automation/6.png)

2. Entrez les détails des informations d’identification.  Pour **Nom d’utilisateur**, entrez l’**ID de client du SPN**, pour **Mot de passe**, entrez le **Secret du SPN**.

    ![Créer des informations d’identification](./media/analysis-services-refresh-azure-automation/7.png)

3. Importer le runbook Automation

    ![Importer un Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Recherchez le fichier **Refresh-Model.ps1**, fournissez un **nom** et un **description**, puis cliquez sur **Créer**.

    ![Importer un Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. Une fois le runbook créé, il passe automatiquement en mode d’édition.  Sélectionnez **Publier**.

    ![Publier un runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > La ressource d’informations d’identification créée précédemment est récupérée par le runbook à l’aide de la commande **Get-AutomationPSCredential**.  Cette commande est ensuite transmise à la commande PowerShell **Invoke-ProcessASADatabase** pour effectuer l’authentification auprès d’Azure Analysis Services.

6. Testez le runbook en cliquant sur **Démarrer**.

    ![Démarrer le runbook](./media/analysis-services-refresh-azure-automation/11.png)

7. Renseignez les paramètres **DATABASENAME**, **ANALYSISSERVER** et **REFRESHTYPE**, puis cliquez sur **OK**. Le paramètre **WEBHOOKDATA** n’est pas obligatoire lorsque le runbook est exécuté manuellement.

    ![Démarrer le runbook](./media/analysis-services-refresh-azure-automation/12.png)

Si le runbook a été correctement exécuté, vous recevrez une sortie telle que la suivante :

![Exécution réussie](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Utiliser un runbook Azure Automation autonome

Le runbook peut être configuré pour déclencher l’actualisation du modèle Azure Analysis Services de manière planifiée.

Vous pouvez le configurer comme suit :

1. Dans le runbook Automation, cliquez sur **Planifications**, puis sur **Ajouter une planification**.
 
    ![Créer une planification](./media/analysis-services-refresh-azure-automation/14.png)

2. Cliquez sur **Planification** > **Créer une planification**, puis renseignez les détails.

    ![Configurer une planification](./media/analysis-services-refresh-azure-automation/15.png)

3. Cliquez sur **Créer**.

4. Renseignez les paramètres de la planification. Ils seront utilisés à chaque déclenchement du runbook. Le paramètre **WEBHOOKDATA** doit être laissé vide lors de l’exécution via une planification.

    ![Configurer les paramètres](./media/analysis-services-refresh-azure-automation/16.png)

5. Cliquez sur **OK**.

## <a name="consume-with-data-factory"></a>Utiliser avec Data Factory

Pour utiliser le runbook à l’aide d’Azure Data Factory, commencez par créer abord un **Webhook** pour le runbook. Le **Webhook** fournit une URL qui peut être appelée via une activité web Azure Data Factory.

> [!IMPORTANT]
> Pour pouvoir créer un **Webhook**, l’état du runbook doit être **publié**.

1. Dans votre runbook Automation, cliquez sur **Webhooks**, puis sur **Ajouter un Webhook**.

   ![Ajouter un Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Attribuez eu Webhook un nom et une date d’expiration.  Le nom identifie uniquement le Webhook à l’intérieur du runbook Automation, et ne fait pas partie de l’URL.

   >[!CAUTION]
   >Veillez à copier l’URL avant de fermer l’Assistant, car vous ne pourrez pas la récupérer une fois fermée.
    
   ![Configurer un Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Les paramètres pour le Webhook peuvent rester vides.  Lors de la configuration de l’activité web d’Azure Data Factory, les paramètres peuvent être transmis dans le corps de l’appel web.

3. Dans Data Factory, configurez une **activité web**.

### <a name="example"></a>Exemples

   ![Exemple d’activité web.](./media/analysis-services-refresh-azure-automation/19.png)

L’**URL** est celle créée à partir du Webhook.

Le **corps** est un document JSON qui doit contenir les propriétés suivantes :


|Propriété  |Valeur  |
|---------|---------|
|**AnalysisServicesDatabase**     |Nom de la base de données Azure Analysis Services. <br/> Exemple : AdventureWorksDB         |
|**AnalysisServicesServer**     |Nom du serveur Azure Analysis Services. <br/> Par exemple : https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |Type d’actualisation à effectuer. <br/> Exemple : Complet         |

Exemple de corps JSON :

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Ces paramètres sont définis dans le script PowerShell du runbook.  Lors de l’exécution de l’activité web, la charge utile JSON transmise est WEBHOOKDATA.

Elle est désérialisée et stockée sous la forme de paramètres PowerShell qui sont ensuite utilisés par la commande PowerShell Invoke-ProcesASDatabase.

![Webhook désérialisé](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Utiliser un Worker hybride avec Azure Analysis Services

Une machine virtuelle Azure avec une adresse IP publique statique peut être utilisée en tant qu’Azure Automation Hybrid Worker.  Vous pouvez ajouter cette adresse IP publique au pare-feu Azure Analysis Services.

> [!IMPORTANT]
> Vérifiez que l’adresse IP publique de machine virtuelle est configurée comme statique.
>
>Pour en savoir plus sur la configuration des Workers hybrides Azure Automation, voir [Automatiser les ressources de votre centre de données ou de votre cloud à l’aide d’un Runbook Worker hybride](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

Une fois un Worker hybride configuré, créez un Webhook comme décrit dans la section [Consommer avec Data Factory](#consume-with-data-factory).  La seule différence ici consiste à sélectionner l’option **Exécuter sur** > **Worker hybride** lors de la configuration du Webhook.

Exemple de Webhook à l’aide d’un Worker hybride :

![Exemple de Webhook de Worker hybride](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Exemple de Runbook PowerShell

L’extrait de code suivant montre comment effectuer l’actualisation de modèle Azure Analysis Services à l’aide d’un runbook PowerShell.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Étapes suivantes

[Exemples](analysis-services-samples.md)  
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)
