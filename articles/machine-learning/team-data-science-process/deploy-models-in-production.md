---
title: Déployer des modèles en production - Team Data Science Process
description: Guide pratique pour déployer des modèles en production, ce qui leur permet de jouer un rôle actif dans les décisions d’entreprise.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1a75c842989cfbaf7bb1880831fda2bc6994622b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60238345"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Déployer des modèles en production pour jouer un rôle actif dans les décisions d’entreprise

Le déploiement en production permet à un modèle de jouer un rôle actif dans une entreprise. Les prédictions issues d’un modèle déployé peuvent être utilisées pour les décisions d’entreprise.

## <a name="production-platforms"></a>Plateformes de production

Il existe différentes approches et plateformes pour mettre les modèles en production. Voici quelques options :

- [Où déployer des modèles avec le service Azure Machine Learning](../service/how-to-deploy-and-where.md)
- [Déploiement d’un modèle dans SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Avant de procéder au déploiement, vous devez vérifier que le niveau de la latence du modèle est suffisamment faible pour permettre l’utilisation de ce dernier dans l’environnement de production.
>

>[!NOTE]
>Pour un déploiement à l’aide de Microsoft Azure Machine Learning Studio, consultez [Déploiement d’un service web Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>Test A/B

Quand plusieurs modèles sont en production, il peut être utile d’effectuer un [test A/B](https://en.wikipedia.org/wiki/A/B_testing) pour comparer les performances des modèles. 
 
## <a name="next-steps"></a>Étapes suivantes

Des procédures pas à pas illustrant toutes les étapes de **scénarios spécifiques** sont également fournies. L’article [Exemples de procédures pas à pas](walkthroughs.md) les liste et les décrit brièvement, en les accompagnant de liens. Ces procédures illustrent comment combiner des outils et services locaux ou cloud dans un flux de travail ou un pipeline pour créer une application intelligente. 
