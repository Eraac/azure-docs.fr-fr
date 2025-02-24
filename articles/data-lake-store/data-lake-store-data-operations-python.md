---
title: 'Python : opérations de gestion du système de fichiers sur Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Découvrez comment utiliser le kit de développement logiciel (SDK) Python avec le système de fichiers de Data Lake Storage Gen1.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: f65b6f0bfefd7763b72e8853b0314830a8c5327b
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68232553"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Opérations de gestion du système de fichiers sur Azure Data Lake Storage Gen1 à l’aide de Python
> [!div class="op_single_selector"]
> * [Kit de développement logiciel (SDK) .NET](data-lake-store-data-operations-net-sdk.md)
> * [Kit SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Dans cet article, vous apprenez à utiliser le kit de développement logiciel (SDK) Python pour réaliser des opérations de gestion du système de fichiers sur Azure Data Lake Storage Gen1. Pour obtenir des instructions sur l’exécution des opérations de gestion des comptes sur Data Lake Storage Gen1 à l’aide de Python, consultez la section relative aux [opérations de gestion des comptes sur Data Lake Storage Gen1 à l’aide de Python](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Prérequis

* **Python** Pour télécharger Python, accédez [ici](https://www.python.org/downloads/). Cet article utilise Python 3.6.2.

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Compte Azure Data Lake Storage Gen1**. Suivez les instructions de [Prise en main d’Azure Data Lake Storage Gen1 avec le portail Azure](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Installer les modules

Pour utiliser Data Lake Storage Gen1 avec Python, vous devez installer trois modules.

* Le module `azure-mgmt-resource`, qui inclut des modules Azure pour Active Directory, etc.
* Le module `azure-mgmt-datalake-store`, qui inclut les opérations de gestion du compte Azure Data Lake Storage Gen1. Pour plus d’informations sur ce module, voir les [informations de référence sur le module azure-mgmt-datalake-store](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* Le module `azure-datalake-store`, qui inclut les opérations de gestion du système de fichiers Azure Data Lake Storage Gen1. Pour plus d’informations sur ce module, voir les [informations de référence sur le module azure-datalake-store file-system](https://azure-datalake-store.readthedocs.io/en/latest/).

Utilisez les commandes suivantes pour installer les modules.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Créer une application Python

1. Utilisez l’IDE de votre choix pour créer une application Python, par exemple, **mysample.py**.

2. Ajoutez les lignes suivantes pour importer les modules requis.

   ```
   ## Use this only for Azure AD service-to-service authentication
   from azure.common.credentials import ServicePrincipalCredentials

   ## Use this only for Azure AD end-user authentication
   from azure.common.credentials import UserPassCredentials

   ## Use this only for Azure AD multi-factor authentication
   from msrestazure.azure_active_directory import AADTokenCredentials

   ## Required for Azure Data Lake Storage Gen1 account management
   from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
   from azure.mgmt.datalake.store.models import DataLakeStoreAccount

   ## Required for Azure Data Lake Storage Gen1 filesystem management
   from azure.datalake.store import core, lib, multithread

   ## Common Azure imports
   from azure.mgmt.resource.resources import ResourceManagementClient
   from azure.mgmt.resource.resources.models import ResourceGroup

   ## Use these as needed for your application
   import logging, getpass, pprint, uuid, time
   ```

3. Enregistrez les modifications apportées à mysample.py.

## <a name="authentication"></a>Authentication

Dans cette section, nous aborderons les différentes méthodes permettant de s’authentifier auprès d’Azure AD. Voici les options disponibles :

* Pour en savoir plus sur l’authentification des utilisateurs accédant à votre application, consultez la rubrique [Authentification des utilisateurs finaux auprès de Data Lake Storage Gen1 avec Python](data-lake-store-end-user-authenticate-python.md).
* Pour en savoir plus sur l’authentification entre les services dans le cadre de votre application, consultez la rubrique [Authentification de service à service auprès de Data Lake Storage Gen1 à l’aide de Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Créer le client de système de fichiers

L’extrait de code suivant crée dans un premier temps le client du compte Data Lake Storage Gen1. Il utilise l’objet client pour créer le compte Data Lake Storage Gen1. Enfin, l’extrait de code crée un objet client de système de fichiers.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)

## <a name="create-a-directory"></a>Créer un répertoire

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Charger un fichier


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Téléchargement d’un fichier

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Supprimer un répertoire

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="next-steps"></a>Étapes suivantes
* [Opérations de gestion du compte sur Data Lake Storage Gen1 à l’aide de Python](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Voir aussi

* [Documents de référence sur la gestion de système de fichiers avec Azure Data Lake Storage Gen1 et Python](https://azure-datalake-store.readthedocs.io/en/latest)
* [Ouvrir des applications Big Data open source compatibles avec Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
