---
title: Transformation de nouvelle branche de mappage de flux de données pour Azure Data Factory
description: Transformation de nouvelle branche de mappage de flux de données pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: af2225d749283c7124f89d5a7cd735b2f6bfd121
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61348115"
---
# <a name="mapping-data-flow-union-transformation"></a>Transformation d’union des flux de données de mappage

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

L'union combinera plusieurs flux de données en un, avec l'union SQL de ces flux en tant que nouvelle sortie de la transformation d’union. Tous les schémas de chaque flux d’entrée sont combinés à l’intérieur de votre flux de données, sans avoir à disposer d’une clé de jointure.

Vous pouvez combiner des n flux dans le tableau de paramètres en sélectionnant l’icône « + » en regard de chaque ligne configurée, notamment la source de données et des flux de transformations existantes dans votre flux de données.

![Transformation d’union](media/data-flow/union.png "Union")

Dans ce cas, vous pouvez associer des métadonnées disparates provenant de plusieurs sources (dans cet exemple, trois fichiers sources différents) et les combiner en un seul flux :

![Vue d’ensemble de la transformation d’union](media/data-flow/union111.png "Union 1")

Pour ce faire, ajoutez des lignes supplémentaires dans les paramètres d’union, en incluant toutes les sources que vous souhaitez ajouter. Il n’est pas nécessaire d’avoir une clé de recherche ou de jointure commune :

![Paramètres de la transformation d’union](media/data-flow/unionsettings.png "Paramètres Union")

Si vous définissez une transformation Sélectionner après l’union, vous ne pourrez pas renommer des champs qui se chevauchent ou ceux qui n’étaient pas nommés à partir de sources sans en-tête. Cliquez sur « Inspecter » pour afficher les métadonnées combinées avec les 132 colonnes de cet exemple à partir de trois sources différentes :

![Transformation d’union finale](media/data-flow/union333.png "Union 3")

## <a name="name-and-position"></a>Nom et position

Lorsque vous choisissez une « union par nom », chaque valeur de colonne est supprimée dans la colonne correspondante de chaque source, avec un nouveau schéma de métadonnées concaténées.

Si vous choisissez une « union par position », chaque valeur de colonne est supprimée à la position d’origine de chaque source correspondante, ce qui entraîne un nouveau flux combiné de données où les données de chaque source sont ajoutées au même flux :

![Sortie d’union](media/data-flow/unionoutput.png "Sortie d’union")

## <a name="next-steps"></a>Étapes suivantes

Explorez les transformations similaires, y compris [Joindre](data-flow-join.md) et [Existe](data-flow-exists.md).
