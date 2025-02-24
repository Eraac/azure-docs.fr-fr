---
title: Limites et frontières - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker présente des limites de métadonnées pour certaines parties de la base de connaissances et du service. Il est important de maintenir votre base de connaissances à l’intérieur de ces limites pour les tests et la publication.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/22/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 43d0e7566102c882d4a2819237a795fdff425f75
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446490"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limites de la base de connaissances QnA Maker
Liste complète des limites dans QnA Maker.

## <a name="knowledge-bases"></a>Bases de connaissances

* Nombre maximal de bases de connaissances selon les [limites de niveau de la Recherche Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Niveau de la Recherche Azure** | **Gratuit** | **De base** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Nombre maximal de bases de connaissances publiées autorisées|2|14|49|199|199|2 999|

 Par exemple, si votre niveau comporte 15 index autorisés, vous pouvez publier 14 bases de connaissances (1 index par base de connaissances publiée). Le quinzième index, `testkb`, est utilisé pour toutes les bases de connaissances à des fins de création et de test. 

## <a name="extraction-limits"></a>Limites d’extraction
* Nombre maximal de fichiers pouvant être extraits et taille de fichier maximale : consultez [Tarifs de QnAMaker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)
* Nombre maximal de liens ciblés pouvant être analysés pour l’extraction de questions/réponses de pages HTML de FAQ : 20

## <a name="metadata-limits"></a>Limites de métadonnées
* Nombre maximal de champs de métadonnées par base de connaissances selon les [limites de niveau de la Recherche Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Niveau de la Recherche Azure** | **Gratuit** | **De base** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Nombre maximal de champs de métadonnées par service QnA Maker (pour tous les Kbits/s)|1 000|100*|1 000|1 000|1 000|1 000|

## <a name="knowledge-base-content-limits"></a>Limites de contenu de la base de connaissances
Limites globales sur le contenu de la base de connaissances :
* Longueur du texte de réponse : 25 000
* Longueur du texte de question : 1 000
* Longueur du texte de valeur/clé des métadonnées : 100
* Caractères pris en charge pour le nom des métadonnées : lettres, chiffres et _  
* Caractères pris en charge pour la valeur des métadonnées : tout sauf : et | 
* Longueur de nom de fichier : 200
* Formats de fichier pris en charge : « .tsv », « .pdf », « .txt », « .docx », « .xlsx ».
* Nombre maximal de questions alternatives : 300
* Nombre maximal de paires question-réponse : varie selon le [niveau Recherche Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits) choisi. Une paire question-réponse est mappée à un document dans l’index Recherche Azure. 

## <a name="create-knowledge-base-call-limits"></a>Créer des limites d’appel de base de connaissances :
Elles représentent les limites pour chaque action de création d’une base de connaissances, autrement dit, pour chaque clic sur *Créer une base de connaissances* ou pour chaque appel de l’API CreateKnowledgeBase.
* Nombre maximal de questions alternatives par réponse : 300
* Nombre maximal d’URL : 10
* Nombre maximal de fichiers : 10

## <a name="update-knowledge-base-call-limits"></a>Limites d’appel de mise à jour de la base de connaissances
Elles représentent les limites pour chaque action de mise à jour, autrement dit, pour chaque clic sur *Save and train* (Enregistrer et former) ou pour chaque appel de l’API UpdateKnowledgeBase.
* Longueur de chaque nom de source : 300
* Nombre maximal de questions alternatives ajoutées ou supprimées : 300
* Nombre maximal de champs de métadonnées ajoutés ou supprimés : 10
* Nombre maximal d’URL pouvant être actualisées : 5.

## <a name="next-steps"></a>Étapes suivantes

Apprenez quand et comment changer les niveaux de service :

* [QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku) : si vous avez besoin d’ajouter des fichiers sources supplémentaires ou des documents plus volumineux dans votre base de connaissances, au-delà de votre niveau actuel, mettez à jour le niveau tarifaire de votre service QnA Maker.
* [Service d’application](how-to/upgrade-qnamaker-service.md#upgrade-app-service) : Lorsque votre base de connaissances doit répondre à un plus grand nombre de demandes de votre application client, mettez à niveau le niveau tarifaire de vos services applicatifs.
* [Recherche Azure](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service) : Lorsque vous prévoyez de disposer de plusieurs bases de connaissances, mettez à niveau votre niveau tarifaire du service Recherche Azure.
