---
title: Déterminer les causes de non-conformité
description: De nombreuses raisons peuvent expliquer une ressource non conforme. Découvrez comment identifier l'origine d'une non-conformité.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fb7f238bb5c04bb03ee500b1b953895cc88c0596
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66298921"
---
# <a name="determine-causes-of-non-compliance"></a>Déterminer les causes de non-conformité

Lorsqu'une ressource Azure est jugée non conforme par rapport à une règle de stratégie, il est important d'identifier la partie de la règle concernée. Il est également important d'identifier la modification qui a transformé une ressource conforme en ressource non conforme. Pour ce faire, il existe deux moyens :

> [!div class="checklist"]
> - [Détails de conformité](#compliance-details)
> - [Historique des changements (préversion)](#change-history-preview)

## <a name="compliance-details"></a>Détails de conformité

Lorsqu’une ressource est non conforme, les détails de conformité de cette ressource sont disponibles sur la page **Conformité à la stratégie**. Le volet relatif aux détails de conformité comprend les informations suivantes :

- Détails de la ressource tels que son nom, son type, son emplacement et son ID
- État de conformité et timestamp de la dernière évaluation ayant trait à l'attribution de stratégie actuelle
- Liste des _motifs_ de non-conformité de la ressource

> [!IMPORTANT]
> Les détails de conformité d'une ressource _non conforme_ affichant la valeur actuelle des propriétés qui s'y rapportent, l’utilisateur doit pouvoir utiliser une opération en **lecture** pour le **type** de ressource. Par exemple, si la ressource _non conforme_ est **Microsoft.Compute/virtualmachines**, l’utilisateur doit pouvoir utiliser l'opération **Microsoft.Compute/virtualMachines/read**. Dans le cas contraire, une erreur d'accès s'affiche.

Pour afficher les détails de conformité, procédez comme suit :

1. Lancez le service Azure Policy dans le portail Azure en cliquant sur **Tous les services**, puis en recherchant et en cliquant sur **Stratégie**.

1. Sur la page **Vue d'ensemble** ou **Conformité**, sélectionnez une stratégie affichant un **état de conformité** _Non conforme_.

1. Sous l'onglet **Conformité des ressources** de la page **Conformité à la stratégie**, cliquez avec le bouton droit ou sélectionnez les points de suspension d’une ressource affichant un **état de conformité** _non conforme_. Sélectionnez ensuite **Afficher les détails de la conformité**.

   ![Option Afficher les détails de la conformité](../media/determine-non-compliance/view-compliance-details.png)

1. Le volet **Détails de conformité** affiche des informations issues de la dernière évaluation de la ressource ayant trait à l'attribution de stratégie actuelle. Dans cet exemple, le champ **Microsoft.Sql/servers/version** indique _12.0_ alors que la définition de la stratégie attendait _14.0_. Si la ressource est non conforme pour plusieurs raisons, ces différentes raisons sont répertoriées dans ce volet.

   ![Volet Détails de conformité et motifs de non conformité](../media/determine-non-compliance/compliance-details-pane.png)

   Pour une définition de stratégie **auditIfNotExists** ou **deployIfNotExists**, les détails incluent la propriété **details.type** et autres propriétés facultatives. Pour une liste, consultez [Propriétés auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) et [Propriétés deployIfNotExists](../concepts/effects.md#deployifnotexists-properties). **Dernière ressource évaluée** correspond à une ressource liée dans la section **Détails** de la définition.

   Exemple de définition partielle **deployIfNotExists** :

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   ![Volet Détails de conformité - *ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Pour protéger les données, lorsqu’une valeur de propriété correspond à un _secret_, la valeur actuelle est remplacée par des astérisques.

Ces détails expliquent les raisons pour lesquelles une ressource est actuellement non conforme, mais n'indiquent pas le moment où la modification apportée à la ressource l'a rendue non conforme. Pour plus d’informations, consultez [Historique des modifications (préversion)](#change-history-preview) ci-dessous.

### <a name="compliance-reasons"></a>Motifs de conformité

La matrice suivante mappe chaque _motif_ possible à la [condition](../concepts/definition-structure.md#conditions) responsable dans la définition de stratégie :

|Motif | Condition |
|-|-|
|La valeur actuelle doit contenir la valeur cible en tant que clé. |containsKey ou **not** notContainsKey |
|La valeur actuelle doit contenir la valeur cible. |contains ou **not** notContains |
|La valeur actuelle doit être égale à la valeur cible. |equals ou **not** notEquals |
|La valeur actuelle doit être inférieure à la valeur cible. |less ou **not** greaterOrEquals |
|La valeur actuelle doit être supérieure ou égale à la valeur cible. |greaterOrEquals ou **not** less |
|La valeur actuelle doit être supérieure à la valeur cible. |greater ou **not** lessOrEquals |
|La valeur actuelle doit être inférieure ou égale à la valeur cible. |lessOrEquals ou **not** greater |
|La valeur actuelle doit exister. |exists |
|La valeur actuelle doit être dans la valeur cible. |in ou **not** notIn |
|La valeur actuelle doit être identique à la valeur cible. |like ou **not** notLike |
|La valeur actuelle doit correspondre à la valeur cible (sensibilité à la casse). |match ou **not** notMatch |
|La valeur actuelle doit correspondre à la valeur cible (non-sensibilité à la casse). |matchInsensitively ou **not** notMatchInsensitively |
|La valeur actuelle ne doit pas contenir la valeur cible en tant que clé. |notContainsKey ou **not** containsKey|
|La valeur actuelle ne doit pas contenir la valeur cible. |notContains ou **not** contains |
|La valeur actuelle ne doit pas être égale à la valeur cible. |notEquals ou **not** equals |
|La valeur actuelle ne doit pas exister. |**not** exists  |
|La valeur actuelle ne doit pas être dans la valeur cible. |notIn ou **not** in |
|La valeur actuelle ne doit pas être identique à la valeur cible. |notLike ou **not** like |
|La valeur actuelle ne doit pas correspondre à la valeur cible (sensibilité à la casse). |notMatch ou **not** match |
|La valeur actuelle ne doit pas correspondre à la valeur cible (non-sensibilité à la casse). |notMatchInsensitively ou **not** matchInsensitively |
|Aucune ressource associée ne correspond aux détails de l'effet dans la définition de stratégie. |Aucune ressource du type défini dans **then.details.type** n'est associée à la ressource définie dans la portion **if**. |

## <a name="compliance-details-for-guest-configuration"></a>Détails de conformité pour Guest Configuration

Pour _auditer_ les stratégies de la catégorie _Guest Configuration_, plusieurs paramètres peuvent être évalués dans la machine virtuelle et vous devez revoir les détails par paramètre. Par exemple, si vous auditez une liste d'applications installées et que l'état d'attribution indique _Non conforme_, vous devez déterminer les applications spécifiques manquantes.

Vous ne serez peut-être pas autorisé à vous connecter directement à la machine virtuelle, mais devrez indiquer pourquoi la machine virtuelle est _Non conforme_. Par exemple, vous pourriez auditer les machines virtuelles jointes au domaine qui convient et inclure l'appartenance au domaine actuel dans les détails correspondants.

### <a name="azure-portal"></a>Portail Azure

1. Lancez le service Azure Policy dans le portail Azure en cliquant sur **Tous les services**, puis en recherchant et en cliquant sur **Stratégie**.

1. Sur la page **Vue d’ensemble** ou **Conformité**, sélectionnez une attribution de stratégie contenant une définition de stratégie Guest Configuration _Non conforme_.

1. Sélectionnez un stratégie d'_audit_ dans l'initiative _Non conforme_.

   ![Afficher les détails de définition d'audit](../media/determine-non-compliance/guestconfig-audit-compliance.png)

1. Dans l'onglet **Conformité des ressources**, les informations suivantes sont fournies :

   - **Nom** - Nom des attributions de Guest Configuration.
   - **Ressource parente** - Machine virtuelle affichant un état _Non conforme_ pour l’attribution de Guest Configuration sélectionnée.
   - **Type de ressource** - Nom complet _guestConfigurationAssignments_.
   - **Dernière évaluation** - Dernière fois que Guest Configuration a rendu compte à Azure Policy de l'état de la machine virtuelle cible.

   ![Afficher les détails de conformité](../media/determine-non-compliance/guestconfig-assignment-view.png)

1. Sélectionnez le nom de l'attribution de Guest Configuration dans la colonne **Nom** pour ouvrir la page **Conformité des ressources**.

1. Sélectionnez le bouton **Afficher une ressource** situé en haut de la page pour ouvrir la page **Affectation d'invité**.

La page **Affectation d'invité** affiche les détails de conformité disponibles. Chaque ligne représente une évaluation effectuée dans la machine virtuelle. Dans la colonne **Raison**, une phrase décrivant pourquoi l'attribution d'invité est _Non conforme_ s’affiche. Par exemple, si vous constatez que des machines virtuelles doivent être jointes à un domaine, la colonne **Raison** affiche un texte incluant l’appartenance au domaine actuel.

![Afficher les détails de conformité](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Vous pouvez également afficher les détails de conformité à partir d'Azure PowerShell. Assurez-vous tout d’abord que vous disposez bien du module Guest Configuration.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Vous pouvez afficher l’état actuel de toutes les attributions d’invités pour une machine virtuelle à l’aide de la commande suivante :

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Pour afficher uniquement sous forme de phrase la _raison_ qui explique pourquoi la machine virtuelle est _non conforme_, renvoyez uniquement la propriété enfant Raison.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Vous pouvez également générer un historique de conformité des attributions d’invités dans l’étendue de la machine virtuelle. La sortie de cette commande inclut les détails de chaque rapport portant sur la machine virtuelle.

> [!NOTE]
> La sortie peut renvoyer un important volume de données. Il est recommandé de stocker la sortie dans une variable.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

Pour simplifier cet affichage, utilisez le paramètre **ShowChanged**. La sortie de cette commande inclut uniquement les rapports faisant état d'une modification de l'état de conformité.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>Historique des modifications (préversion)

Dans le cadre d’une nouvelle **préversion publique**, l'historique des modifications des 14 derniers jours est disponible pour toutes les ressources Azure prenant en charge la [suppression du mode](../../../azure-resource-manager/complete-mode-deletion.md). L'historique des modifications indique quand une modification a été détectée et fournit un _différentiel visuel_ pour chaque modification. Une détection de modification est déclenchée lorsque les propriétés Resource Manager sont ajoutées, supprimées ou modifiées.

1. Lancez le service Azure Policy dans le portail Azure en cliquant sur **Tous les services**, puis en recherchant et en cliquant sur **Stratégie**.

1. Sur la page **Vue d'ensemble** ou **Conformité**, sélectionnez une stratégie dans un **état de conformité**.

1. Sous l'onglet **Conformité des ressources** de la page **Conformité à la stratégie**, sélectionnez une ressource.

1. Sélectionnez l'onglet **Historique des modifications (préversion)** de la page **Conformité des ressources**. La liste des modifications détectées, le cas échéant, s'affiche.

   ![Onglet Historique des modifications Azure Policy de la page Conformité des ressources](../media/determine-non-compliance/change-history-tab.png)

1. Sélectionnez une des modifications détectées. Le _différentiel visuel_ de la ressource est présenté sur la page **Historique des modifications**.

   ![Différentiel visuel de l'historique des modifications Azure Policy de la page Historique des modifications](../media/determine-non-compliance/change-history-visual-diff.png)

Le _différentiel visuel_ aide à identifier les modifications apportées à une ressource. Les modifications détectées peuvent ne pas être liées à l’état de conformité actuel de la ressource.

Les données de l'historique des modifications sont fournies par [Azure Resource Graph](../../resource-graph/overview.md). Pour interroger ces informations en dehors du portail Azure, consultez [Obtenir les modifications des ressources](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Étapes suivantes

- Consultez les exemples de la page [Exemples Azure Policy](../samples/index.md).
- Consultez la [Structure de définition Azure Policy](../concepts/definition-structure.md).
- Consultez la page [Compréhension des effets de Policy](../concepts/effects.md).
- Découvrez comment [créer des stratégies par programmation](programmatically-create.md).
- Découvrez comment [obtenir des données de conformité](getting-compliance-data.md).
- Découvrez comment [corriger des ressources non conformes](remediate-resources.md).
- Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../management-groups/overview.md).