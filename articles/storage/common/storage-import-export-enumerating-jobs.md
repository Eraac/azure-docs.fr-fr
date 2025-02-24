---
title: Liste de tous vos travaux Azure Import/Export | Microsoft Docs
description: Découvrez comment répertorier tous les travaux d’un abonnement qui sont liés au service Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 0ae9e7fa76c8ecbb724cf0f494e648df989dff30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478689"
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Énumération des travaux dans le service Azure Import/Export
Pour énumérer tous les travaux d’un abonnement, appelez l’opération [Répertorier les travaux](/rest/api/storageimportexport/jobs). `List Jobs` retourne une liste de travaux, ainsi que les attributs suivants :

-   le type de travail (importation ou exportation) ;

-   l’état du travail actuel ;

-   le compte de stockage associé au travail.

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation de l’API REST du service Import/Export](storage-import-export-using-the-rest-api.md)
