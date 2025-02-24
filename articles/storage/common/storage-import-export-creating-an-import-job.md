---
title: Création d’un travail d’importation pour Azure Import/Export | Microsoft Docs
description: Découvrez comment créer une importation pour le service Microsoft Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: fa76f4fb5d4da5fd00bb9fa4ed862c6977a47e90
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61483075"
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>Création d’un travail d’importation pour le service Azure Import/Export

La création d’un travail d’importation pour le service Microsoft Azure Import/Export à l’aide de l’API REST implique les étapes suivantes :

- Préparation des disques avec l’outil Azure Import/Export.

- Obtention de l’emplacement vers lequel expédier le disque.

- Création du travail d’importation.

- Expédition des disques à Microsoft via un service de transport pris en charge.

- Mise à jour du travail d’importation avec les informations d’expédition.

  Consultez [Transfert de données vers le Stockage Blob à l’aide du service Microsoft Azure Import/Export](storage-import-export-service.md) pour obtenir une présentation du service Import/Export et un didacticiel qui explique comment utiliser le [portail Azure](https://portal.azure.com/) pour créer et gérer les travaux d’importation et d’exportation.

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Préparation des disques avec l’outil Azure Import/Export

Les étapes de préparation des disques à un travail d’importation sont toujours identiques, que le travail soit créé à partir du portail ou de l’API REST.

Voici une brève présentation de la préparation du disque. Consultez la [référence sur l’outil Azure Import/Export](storage-import-export-tool-how-to-v1.md) pour obtenir des instructions complètes. Vous pouvez télécharger l’outil Azure Import/Export [ici](https://go.microsoft.com/fwlink/?LinkID=301900).

La préparation de votre disque implique :

- L’identification des données à importer.

- L’identification des objets blob de destination dans le stockage Windows Azure.

- L’utilisation de l’outil Azure Import/Export pour copier vos données sur un ou plusieurs disques durs.

  L’outil Azure Import/Export génère également un fichier de manifeste pour chaque disque au cours de sa préparation. Un fichier de manifeste contient les éléments suivants :

- Une énumération de tous les fichiers destinés au téléchargement et les mappages de ces fichiers vers les objets blob.

- Les sommes de contrôle des segments de chaque fichier.

- Des informations sur les métadonnées et les propriétés à associer à chaque objet blob.

- Une liste d’actions à entreprendre si un objet blob qui est en cours de téléchargement a le même nom qu’un objet blob existant dans le conteneur. Les options possibles sont : a) remplacer l’objet blob par le fichier, b) conserver l’objet blob existant et ignorer le téléchargement du fichier, c) ajouter un suffixe au nom afin qu’il n’entre pas en conflit avec d’autres fichiers.

## <a name="obtaining-your-shipping-location"></a>Obtention de votre emplacement d’expédition

Avant de créer un travail d’importation, vous devez obtenir un nom et une adresse pour l’emplacement d’expédition en appelant l’opération [List Locations](https://docs.microsoft.com/rest/api/storageimportexport/locations/list). `List Locations` renvoie une liste d’emplacements et leurs adresses postales. Vous pouvez sélectionner un emplacement dans la liste renvoyée et expédier vos disques durs à cette adresse. Vous pouvez également utiliser l’opération `Get Location` pour obtenir directement l’adresse d’expédition relative à un emplacement spécifique.

 Suivez les étapes ci-dessous pour obtenir l’emplacement d’expédition :

-   Identifiez le nom de l’emplacement de votre compte de stockage. Cette valeur se trouve sous le champ **Emplacement** sur le **Tableau de bord** du compte de stockage dans le portail Azure. Elle peut également être obtenue en utilisant l’opération de l’API Gestion des services [Get Storage Account Properties](/rest/api/storagerp/storageaccounts).

-   Récupérez l’emplacement disponible pour traiter ce compte de stockage en appelant l’opération `Get Location`.

-   Si la propriété `AlternateLocations` de l’emplacement contient l’emplacement lui-même, alors il est possible d’utiliser cet emplacement. Sinon, appelez une nouvelle fois l’opération `Get Location` en utilisant l’un des autres emplacements. L’emplacement d’origine peut être fermé temporairement pour maintenance.

## <a name="creating-the-import-job"></a>Création du travail d’importation
Pour créer le travail d’importation, appelez l’opération [Put Job](/rest/api/storageimportexport/jobs). Vous devez fournir les informations suivantes :

-   Nom du travail.

-   nom du compte de stockage.

-   Nom de l’emplacement d’expédition, obtenu à l’étape précédente.

-   Type de travail (importation).

-   Adresse de retour à laquelle les disques doivent être envoyés une fois le travail d’importation terminé.

-   Liste des disques dans le travail. Pour chaque disque, vous devez inclure les informations suivantes obtenues lors de l’étape de préparation du disque :

    -   ID du disque

    -   Clé BitLocker

    -   Chemin relatif du fichier de manifeste sur le disque dur

    -   Hachage MD5 du fichier de manifeste encodé en Base16

## <a name="shipping-your-drives"></a>Expédition de vos disques
Vous devez expédier vos disques à l’adresse obtenue à l’étape précédente et vous devez fournir au service Import/Export le numéro de suivi du colis.

> [!NOTE]
>  Vous devez expédier vos disques via un service de transport pris en charge, qui vous fournira un numéro de suivi pour votre colis.

## <a name="updating-the-import-job-with-your-shipping-information"></a>Mise à jour du travail d’importation avec les informations d’expédition
Dès que vous avez votre numéro de suivi, appelez l’opération [Update Job Properties](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Update) pour mettre à jour le nom du transporteur, le numéro de suivi du travail et le numéro du compte du transporteur pour le retour d’expédition. Vous pouvez éventuellement spécifier le nombre de disques, ainsi que la date d’expédition.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation de l’API REST du service Import/Export](storage-import-export-using-the-rest-api.md)
