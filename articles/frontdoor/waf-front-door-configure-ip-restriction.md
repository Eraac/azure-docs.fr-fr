---
title: Configurer une règle de restriction IP avec une règle de pare-feu d’applications web pour Azure Front Door Service
description: Découvrez comment configurer une règle de pare-feu d’application web pour limiter les adresses IP pour un point de terminaison de Service de porte d’entrée Azure existant.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 611a52f43b5b0e076ae37df9df86479ec894c6f4
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849161"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Configurer une règle de restriction IP avec un pare-feu d’applications web pour Azure Front Door Service
Cet article vous montre comment configurer des règles de restriction IP dans un pare-feu d’applications web (WAF) pour Azure Front Door Service à l’aide d’Azure CLI, Azure PowerShell ou un modèle Azure Resource Manager.

Une règle de contrôle d’accès basé sur l’adresse IP est une règle WAF personnalisée qui vous permet de contrôler l’accès à vos applications web. Elle remplit cette fonction en spécifiant une liste d’adresses IP ou de plages d’adresses IP dans un format CIDR (Classless Inter-Domain Routing).

Par défaut, votre application web est accessible depuis Internet. Si vous souhaitez limiter l’accès aux clients à partir d’une liste d’adresses IP connues ou de plages d’adresses IP, vous devez créer deux règles de correspondance IP. La première règle de correspondance IP contient la liste des adresses IP en tant que valeurs correspondantes et définit l’action sur **Autoriser**. La deuxième, dont la priorité est plus basse, bloque toutes les autres adresses IP en utilisant l’opérateur **ALL** et en définissant l’action sur **Bloquer**. Une fois une règle de restriction IP appliquée, les demandes provenant d’adresses qui ne figurent pas dans cette liste autorisée recevront une réponse 403 Interdit.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Configurer une stratégie WAF avec Azure CLI

### <a name="prerequisites"></a>Prérequis
Avant de commencer à configurer une stratégie de restriction d’IP, configurez votre environnement CLI et créez un profil Azure Front Door Service.

#### <a name="set-up-the-azure-cli-environment"></a>Configurer l'environnement Azure CLI
1. Installez [Azure CLI](/cli/azure/install-azure-cli) ou utilisez Azure Cloud Shell. Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. L’interface Azure CLI est préinstallée et configurée pour être utilisée avec votre compte. Sélectionnez le bouton **Essayez-le** dans les commandes CLI qui suivent, puis connectez-vous à votre compte Azure dans la session Cloud Shell qui s’ouvre. Une fois la session ouverte, saisissez `az extension add --name front-door` pour ajouter l’extension Azure Front Door Service.
 2. Si vous utilisez l’interface CLI localement dans Bash, connectez-vous à Azure à l’aide de `az login`.

#### <a name="create-an-azure-front-door-service-profile"></a>Créer un profil Azure Front Door Service
Créez un profil Azure Front Door Service en suivant les instructions décrites dans [Démarrage rapide : Créer une porte d’entrée pour une application web globale hautement disponible](quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Créer une stratégie de pare-feu d’applications web (WAF)

Créer une stratégie WAF à l’aide de la commande [az network waf-policy create](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create). Dans l’exemple suivant, remplacez le nom de la stratégie *IPAllowPolicyExampleCLI* par un nom unique.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Ajouter une règle de contrôle d’accès IP personnalisée

Utilisez la commande [az network waf-policy custom-rule create](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) pour ajouter une règle de contrôle d'accès IP personnalisée pour une stratégie WAF que vous venez de créer.

Dans les exemples suivants :
-  Remplacez *IPAllowPolicyExampleCLI* par votre stratégie unique créée précédemment.
-  Remplacez *ip-address-range-1*, *ip-address-range-2* par votre propre plage.

Tout d’abord, créez l’adresse IP et autorisez la règle pour les adresses spécifiées.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ("<ip-address-range-1>","<ip-address-range-2>") \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Créez ensuite une règle **block all** avec une priorité inférieure à la règle **allow** précédente. Là encore, remplacez *IPAllowPolicyExampleCLI* dans l’exemple suivant par votre stratégie unique que vous avez créée précédemment.

```azurecli
az network waf-policy custom-rule create \
  --name IPDenyAllRule\
  --priority 2 \
  --rule-type MatchRule \
  --match-condition RemoteAddr Any
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
    
### <a name="find-the-id-of-a-waf-policy"></a>Rechercher l’ID d’une stratégie WAF 
Recherchez une stratégie WAF à l’aide de la commande [az network waf-policy show](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show). Là encore, remplacez *IPAllowPolicyExampleCLI* dans l’exemple suivant par votre stratégie unique que vous avez créée précédemment.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Lier une stratégie WAF à un hôte front-end Azure Front Door Service

Définissez l’ID *WebApplicationFirewallPolicyLink* d’Azure Front Door Service sur l’ID de la stratégie à l’aide de la commande [az network front-door update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update). Remplacez *IPAllowPolicyExampleCLI* par votre stratégie unique créée précédemment.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Dans cet exemple, la stratégieWAF est appliquée à **FrontendEndpoints [0]** . Vous pouvez lier la stratégie WAF à n’importe quel front-end en votre possession.
> [!Note]
> Vous devez définir la propriété **WebApplicationFirewallPolicyLink** une seule fois pour lier une stratégie WAF à un front-end Azure Front Door Service. Les mises à jour de stratégie suivantes sont automatiquement appliquées au front-end.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Configurer une stratégie WAF avec Azure PowerShell

### <a name="prerequisites"></a>Prérequis
Avant de commencer à configurer une stratégie de restriction d’IP, configurez votre environnement PowerShell et créez un profil Azure Front Door Service.

#### <a name="set-up-your-powershell-environment"></a>Configurer votre environnement PowerShell
Azure PowerShell fournit un ensemble de cmdlets qui utilisent le modèle [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pour gérer des ressources Azure.

Vous pouvez installer [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) sur votre ordinateur local et l’utiliser sur n’importe quelle session PowerShell. Suivez les instructions sur la page pour vous connecter à PowerShell avec vos informations d’identification Azure, puis installez le module Az.

1. Connectez-vous à Azure avec la commande suivante, puis utilisez une boîte de dialogue interactive pour vous connecter.
    ```
    Connect-AzAccount
    ```
 2. Avant d’installer le module Azure Front Door Service, assurez-vous que vous avez installé la version actuelle du module PowerShellGet. Exécutez la commande suivante, puis réouvrez PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installez le module Az.FrontDoor du collecteur de données à l’aide de la commande suivante. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Créer un profil Azure Front Door Service
Créez un profil Azure Front Door Service en suivant les instructions décrites dans [Démarrage rapide : Créer une porte d’entrée pour une application web globale hautement disponible](quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Définir une condition de correspondance IP
Utilisez la commande [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) pour définir une condition de correspondance IP.
Dans l’exemple suivante, remplacez *ip-address-range-1*, *ip-address-range-2* par votre propre plage.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
```
Créez une règle *match all condition* pour IP à l’aide de la commande suivante :
```powershell
$IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty Any        
  ```
    
### <a name="create-a-custom-ip-allow-rule"></a>Créer une règle d’autorisation IP personnalisée

Utilisez la commande [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) pour définir une action et une priorité. Dans l’exemple suivant, les requêtes d’adresses IP client qui correspondent à la liste seront autorisées.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Allow -Priority 1
```
Créez une règle **block all** avec une priorité inférieure à la règle IP **allow** précédente.
```powershell
$IPBlockAll = New-AzFrontDoorCustomRuleObject `
-Name "IPDenyAll" `
-RuleType MatchRule `
-MatchCondition $IPMatchALlCondition `
-Action Block `
-Priority 2
```

### <a name="configure-a-waf-policy"></a>Configurer une stratégie WAF
Recherchez le nom du groupe de ressources qui contient le profil Azure Front Door Service à l’aide de `Get-AzResourceGroup`. Configurez ensuite une stratégie WAF avec la règle IP **block all** à l’aide de la commande [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Lier une stratégie WAF à un hôte front-end Azure Front Door Service

Liez un objet de stratégie WAF à un hôte front-end existant et mettez à jour les propriétés d’Azure Front Door Service. Récupérez tout d’abord l’objet Azure Front Door Service avec la commande [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Définissez ensuite la propriété **WebApplicationFirewallPolicyLink** sur l’ID de la ressource de *$IPAllowPolicyExamplePS*, créée à l’étape précédente, via la commande [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor).

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Dans cet exemple, la stratégieWAF est appliquée à **FrontendEndpoints [0]** . Vous pouvez lier une stratégie WAF à n’importe quel front-end en votre possession. Vous devez définir la propriété **WebApplicationFirewallPolicyLink** une seule fois pour lier une stratégie WAF à un front-end Azure Front Door Service. Les mises à jour de stratégie suivantes sont automatiquement appliquées au front-end.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Configurer une stratégie WAF avec un modèle Resource Manager
Pour afficher le modèle qui crée une stratégie Azure Front Door Service et WAF avec des règles de restriction IP personnalisées, rendez-vous sur [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Créer un profil Azure Front Door Service](quickstart-create-front-door.md).
