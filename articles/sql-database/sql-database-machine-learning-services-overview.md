---
title: Présentation de Machine Learning Services avec R dans Azure SQL Database (préversion)
description: Cet article décrit Machine Learning Services (avec R) dans Azure SQL Database et en explique le fonctionnement.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 186b986fe1931365ee34efab2e04e58908402cc0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67427943"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Machine Learning Services avec R dans Azure SQL Database (préversion)

Machine Learning Services est une fonctionnalité Azure SQL Database pour l’exécution de scripts R dans les bases de données. La fonctionnalité inclut les packages Microsoft R pour l’analyse prédictive haute performance et le machine learning. Les données relationnelles peuvent être utilisées dans des scripts R au moyen de procédures stockées, de scripts T-SQL contenant des instructions R ou de code R contenant T-SQL.

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services (avec R) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> La préversion publique est disponible pour les bases de données uniques et les pools élastiques qui utilisent le modèle d’achat vCore dans les niveaux de service **Usage général** et **Critique pour l’entreprise**. Dans cette préversion publique initiale, le niveau de service **Hyperscale** et l'option de déploiement **Instance gérée** ne sont pas pris en charge. Actuellement, R est le seul langage pris en charge. Il n’existe aucune prise en charge de Python pour l’instant.
>
> La préversion est actuellement disponible dans les régions suivantes : Europe Ouest, Europe Nord, USA Ouest 2, USA Est, USA Centre Sud, USA Centre Nord, Canada Centre, Asie Sud-Est, Inde Sud et Australie Sud-Est.
>
> [Inscrivez-vous à la préversion](#signup) ci-après.

## <a name="what-you-can-do-with-r"></a>Fonctionnalités de R

Utilisez la puissance du langage R pour fournir des fonctionnalités avancées d’analytique et de machine learning en base de données. Les calculs et le traitement sont effectués là où les données résident, éliminant ainsi la nécessité de tirer (pull) les données sur le réseau. Vous pouvez également exploiter la puissance des packages R d’entreprise pour fournir des fonctionnalités avancées d’analytique à grande échelle.

Machine Learning Services inclut une distribution de base de R à laquelle Microsoft superpose des packages R d’entreprise. Les fonctions et algorithmes R de Microsoft sont conçus pour combiner mise à l’échelle et utilité dans les domaines suivants : analytique prédictive, modélisation statistique, visualisation des données et algorithmes de machine learning de pointe.

### <a name="r-packages"></a>Packages R

La plupart des packages R open source courants sont préinstallés dans Machine Learning Services. Les packages R suivants de Microsoft sont également inclus :

| Package R | Description|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open est la distribution améliorée de Microsoft R. Il s’agit d’une plate-forme open source complète pour l’analyse statistique et la science des données. Il est basé sur R et totalement compatible avec ce langage, et il inclut des capacités supplémentaires pour améliorer les performances et la reproductibilité. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR est la bibliothèque principale de R évolutive. Les fonctions de cette bibliothèque sont parmi les plus utilisées. La transformation et la manipulation des données, le résumé statistique, la visualisation et de nombreuses formes de modélisation et d’analyse sont rendues possibles avec ces bibliothèques. De plus, les fonctions de ces bibliothèques répartissent automatiquement les charges de travail entre les cœurs disponibles pour un traitement parallèle, avec la possibilité de travailler sur des blocs de données qui sont coordonnés et gérés par le moteur de calcul. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML ajoute des algorithmes de Machine Learning pour créer des modèles personnalisés pour l’analyse de texte, l’analyse d’images et l’analyse de sentiments. |

En plus des packages préinstallés, vous pouvez [installer des packages supplémentaires](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>S’inscrire à la version préliminaire

Pour vous inscrire à la préversion publique, procédez comme suit :

1. Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/free/) avant de commencer.

2. Envoyez un e-mail à Microsoft sur [sqldbml@microsoft.com](mailto:sqldbml@microsoft.com) pour vous inscrire à la préversion publique. La préversion publique de Machine Learning Services (avec R) dans SQL Database n’est pas activée par défaut.

Une fois que vous êtes inscrit dans le programme, Microsoft vous intègre à la préversion publique et active R à votre base de données existante ou nouvelle.

L’utilisation de Machine Learning Services avec R n’est pas recommandée pour des charges de travail de production avec la préversion publique.

## <a name="next-steps"></a>Étapes suivantes

- Prenez connaissance des [différences principales de Machine Learning SQL Services dans SQL Server](sql-database-machine-learning-services-differences.md).
- Pour savoir comment utiliser R pour interroger Machine Learning Services dans Azure SQL Database (version préliminaire), voir le [Guide de démarrage rapide](sql-database-connect-query-r.md).
- Pour commencer avec des scripts R simples, voir [Créer et exécuter des scripts R simples dans Azure SQL Database Machine Learning Services (préversion)](sql-database-quickstart-r-create-script.md).
