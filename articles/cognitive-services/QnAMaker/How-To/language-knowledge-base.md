---
title: Base de connaissances dans une langue autre que l’anglais - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker prend en charge le contenu de la base de connaissances dans de nombreuses langues. Toutefois, chaque service QnA Maker doit être réservé à une seule langue. La première base de connaissances créée ciblant un service QnA Maker particulier définit la langue de ce service.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: 1203748b43ea5911d4717f451bca2cbe82753386
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447413"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Prise en charge linguistique du contenu de la base de connaissances pour QnA Maker
QnA Maker prend en charge le contenu de la base de connaissances dans de nombreuses langues. Toutefois, chaque service QnA Maker doit être réservé à une seule langue. La première base de connaissances créée ciblant un service QnA Maker particulier définit la langue de ce service. Voir la [ici](../Overview/languages-supported.md) la liste complète des langues prises en charge.

La langue est automatiquement reconnue à partir du contenu des sources de données en cours d’extraction. Une fois que vous créez un nouveau Service QnA Maker et une base de connaissances dans ce service, vous pouvez vérifier que la langue a été correctement définie.

1. Accédez au [portail Azure](https://portal.azure.com/).

2. Sélectionnez **groupes de ressources** et accédez au groupe de ressources où le service QnA Maker est déployé, puis sélectionnez la ressource **Recherche Azure**.

    ![Sélectionnez la ressource Recherche Azure](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Sélectionnez l’index **testkb**. Cet index de Recherche Azure est toujours le premier créé, et il contient le contenu enregistré toutes les bases de connaissances de ce service. 

    ![Sélectionnez la base de connaissances de test](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. Sélectionnez la section **Champs** affichant les détails testkb.

    ![Sélectionnez Champs](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Cochez la case **Analyseur** pour afficher les détails de la langue.

    ![Sélectionnez Analyseur](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. Vous devez trouver que l’analyseur est défini sur une langue particulière. Cette langue a été détectée automatiquement lors de l’étape de création de base de connaissances. Elle ne peut pas être modifiée une fois que la ressource est créée.

    ![Analyseur sélectionné](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer un bot QnA avec Azure Bot Service](../Tutorials/create-qna-bot.md)
