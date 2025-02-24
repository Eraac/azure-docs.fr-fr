---
title: Se connecter à l’API Azure Media Services v3 - Python
description: Apprenez à vous connecter à l’API Azure Media Services v3 avec Python.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 971e36b600a2c6be516e39ce84ca5780a2f23bbd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60733094"
---
# <a name="connect-to-media-services-v3-api---python"></a>Se connecter à l’API Media Services v3 - Python

Cet article vous montre comment vous connecter à l’API Azure Media Services v3 en utilisant la méthode de connexion au principal du service.

## <a name="prerequisites"></a>Prérequis

- Télécharger Python sur le site [python.org](https://www.python.org/downloads/)
- Définir la variable d’environnement `PATH`
- [Créer un compte Media Services](create-account-cli-how-to.md). Veillez à mémoriser le nom du groupe de ressources et le nom du compte Media Services.
- Suivez les étapes de l’article [Accéder aux API](access-api-cli-how-to.md). Enregistrez l’ID d’abonnement, l’ID d’application (ID client), la clé d’authentification (secret), et l’ID locataire dont vous aurez besoin dans une prochaine étape.

## <a name="install-the-modules"></a>Installer les modules

Pour utiliser Azure Media Services avec Python, vous devez installer ces modules.

* Le module `azure-mgmt-resource`, qui inclut des modules Azure pour Active Directory.
* Le module `azure-mgmt-media`, qui comprend les entités Media Services.

Ouvrez l’outil en ligne de commande et utilisez les commandes suivantes pour installer les modules.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Se connecter au client Python

1. Créer un fichier avec une extension `.py`
1. Ouvrir le fichier dans l’éditeur de votre choix
1. Ajoutez le code suivant au fichier. Le code importe les modules nécessaires et crée l’objet d’informations d’identification Active Directory nécessaire pour vous connecter à Media Services.

      Définir les valeurs de variables pour les valeurs que vous avez obtenues à l’étape [Accéder aux API](access-api-cli-how-to.md)

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Exécuter le fichier

## <a name="next-steps"></a>Étapes suivantes

- Utilisez le [SDK Python](https://aka.ms/ams-v3-python-sdk).
- Passez en revue la documentation [Informations de référence sur Python](https://aka.ms/ams-v3-python-ref) de Media Services.
