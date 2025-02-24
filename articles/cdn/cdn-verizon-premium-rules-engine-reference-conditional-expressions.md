---
title: Expressions conditionnelles du moteur de règles Azure CDN de Verizon Premium | Microsoft Docs
description: Documentation de référence sur les fonctionnalités et conditions de correspondance du moteur de règles Azure CDN de Verizon Premium.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: f790e37ae876c0640d55ebfb51abb43c6a705f04
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593220"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Expressions conditionnelles du moteur de règles Azure CDN de Verizon Premium

Cet article répertorie les descriptions détaillées des expressions conditionnelles pour le [moteur de règles](cdn-verizon-premium-rules-engine.md) Azure Content Delivery Network (CDN).

La première partie d’une règle est l’expression conditionnelle.

Expression conditionnelle | Description
-----------------------|-------------
IF | Une expression IF fait toujours partie de la première instruction dans une règle. Comme toutes les autres expressions conditionnelles, cette instruction IF doit être associée à une correspondance. Si aucune expression conditionnelle supplémentaire n’est définie, cette correspondance détermine le critère qui doit être rempli avant qu’un ensemble de fonctionnalités puisse être appliqué à une requête.
AND IF | Une expression AND IF ne peut être ajoutée qu’après les types d’expressions conditionnelles suivants : IF, AND IF. Elle indique qu’il existe une autre condition qui doit être remplie pour l’instruction IF initiale.
ELSE IF| Une expression ELSE IF spécifie une autre condition qui doit être remplie avant la mise en place d’un ensemble de fonctionnalités spécifiques à cette instruction ELSE IF. La présence d’une instruction ELSE IF indique la fin de l’instruction précédente. La seule expression conditionnelle pouvant être placée après une instruction ELSE IF est une autre instruction ELSE IF. Cela signifie qu’une instruction IF ELSE peut uniquement servir à spécifier une seule condition supplémentaire qui doit être respectée.

**Exemple**: ![Condition de correspondance CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Une règle ultérieure peut remplacer les actions spécifiées par une règle antérieure.
   > Exemple : une règle de fourre-tout sécurise toutes les requêtes par le biais de l’authentification basée sur un jeton. Une autre règle peut être créée directement en dessous pour générer une exception pour certains types de requêtes.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble d’Azure CDN](cdn-overview.md)
- [Informations de référence du moteur de règles](cdn-verizon-premium-rules-engine-reference.md)
- [Conditions de correspondance du moteur de règles](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Fonctionnalités du moteur de règles](cdn-verizon-premium-rules-engine-reference-features.md)
- [Remplacement du comportement HTTP par défaut à l’aide du moteur de règles](cdn-verizon-premium-rules-engine.md)