---
title: Vue d’ensemble d’Azure Blueprint
description: Découvrez comment Azure Blueprints vous permet de créer, de définir et de déployer des artefacts dans votre environnement Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/08/2019
ms.topic: overview
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 5f403ee7d2bf4a0730ceaa9b5a95b3e7b11f3695
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428771"
---
# <a name="overview-of-the-azure-blueprints-service"></a>Vue d’ensemble du service Azure Blueprints

À l’instar d’un plan, « blueprint » en anglais, qui permet aux ingénieurs et architectes de décrire les paramètres de conception d’un projet, Azure Blueprint permet aux architectes cloud et aux membres de l’informatique centrale de définir un ensemble reproductible de ressources Azure qui implémentent et respectent les normes, modèles et exigences d’une organisation. Azure Blueprint permet aux équipes de développement de créer et de mettre en place rapidement de nouveaux environnements conformes aux exigences de l’organisation avec un ensemble de composants, comme la mise en réseau, visant à accélérer le développement et la livraison.

Les blueprints sont un moyen déclaratif d’orchestrer le déploiement de divers modèles de ressources et d’autres artefacts, notamment ceux-ci :

- Affectations de rôles
- Affectations de stratégies
- Modèles Microsoft Azure Resource Manager
- Groupes de ressources

Le service Azure Blueprints est soutenu par le service globalement distribué [Azure Cosmos DB](../../cosmos-db/introduction.md).
Les objets Blueprint sont répliqués dans plusieurs régions Azure. Cette réplication offre une faible latence, une haute disponibilité et un accès cohérent à vos objets blueprint, quelle que soit la région dans laquelle Blueprints déploie vos ressources.

## <a name="how-its-different-from-resource-manager-templates"></a>Différences par rapport aux modèles Resource Manager

Le service est conçu pour faciliter la _configuration de l’environnement_. Cette configuration se compose souvent d’un ensemble de groupes de ressources, de stratégies, d’attributions de rôles et de déploiements de modèle Resource Manager. Un blueprint est un package qui vous permet de regrouper ces types _d’artefact_. Vous pouvez composer ce package et gérer ses versions, notamment par le biais d’un pipeline CI/CD. Au final, chaque blueprint est affecté à un abonnement dans une opération unique qui peut faire l’objet d’un audit et d’un suivi.

Vous pouvez utiliser un modèle Resource Manager pour accomplir la plupart des choses que vous souhaitez ajouter au déploiement dans Azure Blueprint. Toutefois, un modèle Resource Manager est un document qui n’existe pas en mode natif dans Azure et qui est stocké localement ou dans le contrôle de code source. Le modèle peut servir aux déploiements d’une ou plusieurs ressources Azure, mais une fois ces ressources déployées, il n’existe aucune connexion et relation active au modèle.

Avec Blueprint, la relation entre la définition du blueprint (ce qui _doit_ être déployé) et l’affectation du blueprint (ce qui _a été_ déployé) est conservée. Cette connexion prend en charge le suivi et l’audit améliorés des déploiements. Les blueprints peuvent également mettre à niveau plusieurs abonnements régis par le même blueprint simultanément.

Il est inutile de choisir entre un modèle Resource Manager et un blueprint. Chaque blueprint peut comprendre zéro ou plusieurs _artefacts_ d’un modèle Resource Manager. Cette prise en charge implique que les précédents efforts visant à développer et à maintenir une bibliothèque de modèles Resource Manager peuvent être réutilisés dans Blueprint.

## <a name="how-its-different-from-azure-policy"></a>Différences par rapport à Azure Policy

Un blueprint est un package ou un conteneur qui permet de composer des ensembles spécifiques de normes, modèles et exigences en rapport avec l’implémentation de services cloud Azure, de stratégies de sécurité et de conceptions réutilisables à des fins de cohérence et de conformité.

Une [stratégie](../policy/overview.md) est un système d’autorisations par défaut et de refus explicites qui s’applique aux propriétés des ressources durant le déploiement et aux ressources existantes. Pour prendre en charge la gouvernance du cloud, une stratégie veille au respect des normes et des exigences au sein d’un abonnement.

L’inclusion d’une stratégie dans un blueprint permet de créer le modèle ou la conception approprié pendant l’affectation du blueprint. L’inclusion de stratégie permet de s’assurer que seuls les modifications approuvées ou attendues peuvent être apportées à l’environnement pour préserver la conformité à l’intention du blueprint.

Une stratégie peut constituer l’un des nombreux _artefacts_ d’une définition de blueprint. Les blueprints prennent également en charge l’utilisation de paramètres avec des stratégies et des initiatives.

## <a name="blueprint-definition"></a>Définition de blueprint

Un blueprint est composé _d’artefacts_. Les blueprints prennent actuellement en charge les ressources suivantes comme artefacts :

|Ressource  | Options de hiérarchie| Description  |
|---------|---------|---------|
|Groupes de ressources | Abonnement | Crée un groupe de ressources pour une utilisation par d’autres artefacts dans le blueprint.  Ces groupes de ressources réservés vous permettent d’organiser les ressources en totale conformité avec la structure souhaitée. Ils fournissent aussi un limiteur d’étendue pour les artefacts de stratégie et d’attribution de rôle inclus, et des modèles Azure Resource Manager. |
|Modèle Azure Resource Manager | Abonnement, groupe de ressources | Les modèles sont utilisés pour composer des environnements complexes. Exemples d’environnements : une batterie de serveurs SharePoint, une configuration de l’état Azure Automation ou un espace de travail Log Analytics. |
|Affectation de rôle | Abonnement, groupe de ressources | Permet l’affectation d’une stratégie ou d’une initiative à l’abonnement auquel le blueprint est affecté. La stratégie ou l’initiative doit se trouver à l’intérieur de l’étendue de l’emplacement de définition du blueprint. Si la stratégie ou l’initiative comporte des paramètres, ceux-ci sont affectés au moment de la création du blueprint ou durant son affectation. |
|Attribution de rôle | Abonnement, groupe de ressources | Ajoutez un utilisateur ou un groupe existant à un rôle intégré pour vous assurer que les personnes adéquates disposent d’un accès approprié à vos ressources. Vous pouvez définir des attributions de rôle pour l’ensemble de l’abonnement ou les imbriquer dans un groupe de ressources spécifique inclus dans le blueprint. |

### <a name="blueprint-definition-locations"></a>Emplacements de définition du blueprint

Quand vous créez une définition de blueprint, vous définissez l’emplacement d’enregistrement du blueprint. Les blueprints peuvent être enregistrés dans un [groupe d’administration](../management-groups/overview.md) ou dans un abonnement auquel vous avez accès en tant que **Contributeur**. Si l’emplacement est un groupe d’administration, le blueprint peut être affecté à n’importe quel abonnement enfant de ce groupe d’administration.

### <a name="blueprint-parameters"></a>Paramètres de blueprint

Les blueprints peuvent passer des paramètres à une stratégie/initiative ou à un modèle Azure Resource Manager.
Lors de l’ajout d’un _artefact_ à un blueprint, l’auteur fournit une valeur définie pour chaque affectation de blueprint ou autorise chaque affectation de blueprint à fournir une valeur au moment de l’affectation. Cette flexibilité permet de définir une valeur prédéterminée pour toutes les utilisations du blueprint ou de repousser cette décision au moment de l’affectation.

> [!NOTE]
> Un blueprint peut avoir ses propres paramètres, mais leur création n’est actuellement possible que si le blueprint est généré à partir de l’API REST (impossible si le blueprint est généré par le biais du portail).

Pour plus d’informations, consultez [Paramètres de blueprint](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Publication de blueprint

Quand vous créez un blueprint, celui-ci est initialement en mode **Brouillon**. Quand il est prêt à être affecté, vous devez le **publier**. La publication nécessite la définition d’une **Version** (chaîne composée de lettres, de chiffres et de traits d’union d’une longueur maximale de 20 caractères) avec, en option, des **Notes de changement**. La **Version** permet de différencier le blueprint si des changements sont apportés par la suite et d’affecter des versions différentes. Ce contrôle de version vous permet donc d’affecter différentes **Versions** du même blueprint au même abonnement. Lorsque des changements sont apportés au blueprint, la **Version** **Publiée** est toujours présente, ainsi que les **Changements non publiés**. Une fois les changements finalisés, le blueprint mis à jour est **publié** avec une nouvelle **Version** unique et peut être affecté.

## <a name="blueprint-assignment"></a>Affectation de blueprint

Chaque **Version** **Publiée** d’un blueprint peut être affectée à un abonnement existant. Dans le portail, le blueprint par défaut correspond à la dernière **Version** **Publiée**. Si des paramètres d’artefact (ou des paramètres de blueprint) sont présents, ils sont définis durant le processus d’affectation.

## <a name="permissions-in-azure-blueprints"></a>Autorisations dans Azure Blueprint

Pour utiliser des blueprints, vous devez disposer d’autorisations accordées par le biais du [contrôle d’accès en fonction du rôle](../../role-based-access-control/overview.md) (RBAC). Pour créer des blueprints, votre compte doit avoir les autorisations suivantes :

- `Microsoft.Blueprint/blueprints/write` - Créer une définition de blueprint
- `Microsoft.Blueprint/blueprints/artifacts/write` - Créer des artefacts sur une définition de blueprint
- `Microsoft.Blueprint/blueprints/versions/write` - Publier un blueprint

Pour supprimer des blueprints, votre compte doit avoir les autorisations suivantes :

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> Les autorisations de définition du blueprint doivent être accordées ou héritées sur l’étendue de l’abonnement ou du groupe d’administration où il est enregistré.

Pour affecter ou annuler l’affectation d’un blueprint, votre compte doit avoir les autorisations suivantes :

- `Microsoft.Blueprint/blueprintAssignments/write` - Affecter un blueprint
- `Microsoft.Blueprint/blueprintAssignments/delete` - Annuler l’affectation d’un blueprint

> [!NOTE]
> Comme les affectations de blueprint sont créées sur un abonnement, les autorisations d’affectation de blueprint et d’annulation d’affectation de blueprint doivent être accordées sur une étendue d’abonnement ou être héritées dans une étendue d’abonnement.

Toutes les autorisations ci-dessus sont incluses dans le rôle **Propriétaire**. Le rôle **Contributeur** est autorisé à créer et supprimer des blueprints, mais pas à en affecter. Si ces rôles intégrés ne répondent pas à vos besoins de sécurité, songez à créer un [rôle personnalisé](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Si vous utilisez une identité managée affectée par le système, le principal de service pour Azure Blueprint nécessite le rôle **Propriétaire** sur l’abonnement affecté pour pouvoir activer le déploiement. Si vous utilisez le portail, ce rôle est automatiquement accordé et révoqué pour le déploiement. Si vous utilisez l’API REST, ce rôle doit être accordé manuellement, mais il est toujours automatiquement révoqué une fois le déploiement terminé. Si vous utilisez une identité managée affectée par l’utilisateur, seul l’utilisateur qui crée l’attribution de blueprint nécessite les autorisations de **Propriétaire**.

## <a name="video-overview"></a>Présentation vidéo

La présentation suivante d’Azure Blueprints est issue d’Azure Friday. Pour télécharger la vidéo, accédez à [Azure Fridays - An overview of Azure Blueprints](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) sur Channel 9.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Étapes suivantes

- [Créer un blueprint : portail](create-blueprint-portal.md)
- [Créer un blueprint : API REST](create-blueprint-rest-api.md)
