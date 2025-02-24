---
title: Envoyer les métriques personnalisées d’une ressource Azure à un magasin de métriques Azure Monitor à l’aide d’une API REST
description: Envoyer les métriques personnalisées d’une ressource Azure à un magasin de métriques Azure Monitor à l’aide d’une API REST
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: aa842979bf86410e9dab97d6209f336eb6b02bd3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60253871"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Envoyer les métriques personnalisées d’une ressource Azure à un magasin de métriques Azure Monitor à l’aide d’une API REST

Cet article vous montre comment envoyer des métriques personnalisées pour les ressources Azure au magasin de métriques Azure Monitor par le biais de l’API REST. Une fois que les métriques se trouvent dans Azure Monitor, vous pouvez les utiliser de la même façon que des métriques standard. Par exemple, vous pouvez créer des graphiques et des alertes à partir de ces métriques, ou les router vers d’autres outils externes.  

>[!NOTE]  
>L’API REST autorise uniquement l’envoi de métriques personnalisées pour les ressources Azure. Si vous souhaitez envoyer des métriques pour les ressources dans différents environnements ou en local, vous pouvez utiliser [Application Insights](../../azure-monitor/app/api-custom-events-metrics.md).    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Créer et autoriser un principal de service pour émettre des métriques 

Créez un principal de service dans votre locataire Azure Active Directory en suivant les instructions indiquées dans [Créer un principal du service](../../active-directory/develop/howto-create-service-principal-portal.md). 

Notez ce qui suit au cours de ce processus : 

- Vous pouvez indiquer n’importe quelle URL pour l’URL de connexion.  
- Créez un secret client pour cette application.  
- Enregistrez la clé et l’ID client pour les utiliser ultérieurement.  

Donnez à l’application créée à l’étape 1 (« Surveillance de l’éditeur de métriques ») les autorisations relatives à la ressource dont vous souhaitez obtenir des métriques. Si vous envisagez d’utiliser l’application pour émettre des métriques personnalisées concernant de nombreuses ressources, vous pouvez accorder ces autorisations au niveau du groupe de ressources ou de l’abonnement. 

## <a name="get-an-authorization-token"></a>Obtenir un jeton d’autorisation
Ouvrez une invite de commandes et exécutez la commande suivante :

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Enregistrez le jeton d’accès de la réponse.

![Access token (Jeton d’accès)](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>Émettre la métrique par le biais de l’API REST 

1. Collez le code JSON suivant dans un fichier et enregistrez-le sous le nom  **custommetric.json** sur votre ordinateur local. Mettez à jour le paramètre de temps dans le fichier JSON : 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20", 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. Dans la fenêtre d’invite de commandes, publiez les données métriques : 
   - **azureRegion**. Doit correspondre à la région de déploiement de la ressource pour laquelle vous générez des métriques. 
   - **resourceID**.  ID de ressource de la ressource Azure pour laquelle vous suivez les métriques.  
   - **AccessToken**. Collez le jeton que vous avez acquis précédemment.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Modifiez le timestamp et les valeurs dans le fichier JSON. 
1. Répétez les deux étapes précédentes plusieurs fois afin d’obtenir des données pendant plusieurs minutes.

## <a name="troubleshooting"></a>Résolution de problèmes 
Si vous recevez un message d’erreur lors du processus, tenez compte des informations de dépannage suivantes :

1. Vous ne pouvez pas générer de métriques si un abonnement ou un groupe de ressources est considéré comme votre ressource Azure. 
1. Vous ne pouvez pas envoyer une métrique datant de plus de 20 minutes au magasin. Le magasin de métriques est optimisé pour la génération d’alertes et la création de graphiques en temps réel. 
2. Le nombre de noms de dimensions doit correspondre aux valeurs, et inversement. Vérifiez les valeurs. 
2. Vous générez peut-être des métriques pour une région qui ne prend pas en charge les métriques personnalisées. Consultez [Régions prises en charge](../../azure-monitor/platform/metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Afficher vos métriques 

1. Connectez-vous au portail Azure. 

1. Dans le menu de gauche, sélectionnez **Surveiller**. 

1. Dans la page **Surveiller**, sélectionnez **Métriques**. 

   ![Sélection de l’option Métriques](./media/metrics-store-custom-rest-api/metrics.png) 

1. Modifiez la période d’agrégation afin d’indiquer **30 dernières minutes**.  

1. Dans la liste déroulante **Ressources**, sélectionnez la ressource pour laquelle vous avez généré la métrique.  

1. Dans la liste déroulante **Espaces de noms**, sélectionnez **QueueProcessing**. 

1. Dans la liste déroulante **Métriques**, sélectionnez **QueueDepth**.  

 
## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [métriques personnalisées](../../azure-monitor/platform/metrics-custom-overview.md).

