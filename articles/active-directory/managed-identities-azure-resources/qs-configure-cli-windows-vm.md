---
title: Guide pratique pour configurer des identités managées assignées par le système et l’utilisateur sur une machine virtuelle Azure en utilisant Azure CLI
description: Instructions détaillées pour configurer des identités affectées par le système et l’utilisateur sur une machine virtuelle Azure en utilisant Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c61313190615c2f30a7d37202bc0f9bcf14d800
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66112881"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant Azure CLI

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Les identités managées pour ressources Azure fournissent des services Azure avec une identité managée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Cet article explique comment effectuer les opérations suivantes d’identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant Azure CLI :

- Activer et désactiver l’identité managée affectée par le système sur une machine virtuelle Azure
- Ajouter et supprimer une identité managée affectée par l’utilisateur sur une machine virtuelle Azure

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter la [différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#how-does-it-work)** .
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour exécuter les exemples de script d’Azure CLI, vous disposez de trois options :
    - Utilisez [Azure Cloud Shell](../../cloud-shell/overview.md) à partir du portail Azure (voir section suivante).
    - Utilisez l’interface intégrée Azure Cloud Shell via le bouton « Essayer », situé dans le coin supérieur droit de chaque bloc de code.
    - [Installez la dernière version d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) si vous préférez utiliser une console CLI locale. 
      
      > [!NOTE]
      > Les commandes ont été mises à jour pour refléter la dernière version [d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identité managée affectée par le système

Dans cette section, vous allez découvrir comment activer et désactiver l’identité managée affectée par le système sur une machine virtuelle Azure en utilisant Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Activer une identité managée affectée par le système lors de la création d’une machine virtuelle Azure

Pour créer une machine virtuelle Azure avec l’identité managée affectée par le système sur une machine virtuelle, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Aucune attribution de rôle d’annuaire Azure AD supplémentaire n’est requise.

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/reference-index#az-login). Utilisez un compte associé à l’abonnement Azure sur lequel vous souhaitez déployer la machine virtuelle :

   ```azurecli-interactive
   az login
   ```

2. Créez un [groupe de ressources](../../azure-resource-manager/resource-group-overview.md#terminology) pour l’imbrication et le déploiement de votre machine virtuelle et de ses ressources connexes, à l’aide de la commande [az group create](/cli/azure/group/#az-group-create). Vous pouvez ignorer cette étape si vous possédez déjà le groupe de ressources que vous souhaitez utiliser à la place :

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Créez une machine virtuelle à l’aide de la commande [az vm create](/cli/azure/vm/#az-vm-create). L’exemple suivant crée une machine virtuelle nommée *myVM* avec une identité managée affectée par le système, comme le demande le paramètre `--assign-identity`. Les paramètres `--admin-username` et `--admin-password` spécifient le nom d’utilisateur et le mot de passe d’administration du compte pour la connexion à la machine virtuelle. Mettez à jour ces valeurs en fonction de votre environnement : 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Activer une identité managée affectée par le système sur une machine virtuelle Azure existante

Pour activer l’identité managée affectée par le système sur une machine virtuelle, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Aucune attribution de rôle d’annuaire Azure AD supplémentaire n’est requise.

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/reference-index#az-login). Utilisez un compte associé à l’abonnement Azure qui contient la machine virtuelle.

   ```azurecli-interactive
   az login
   ```

2. Utilisez [az vm identity assign](/cli/azure/vm/identity/) avec la commande `identity assign` pour activer l’identité affectée par le système sur une machine virtuelle existante :

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Désactiver l’identité affectée par le système d’une machine virtuelle Azure

Pour désactiver l’identité managée affectée par le système sur une machine virtuelle, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Aucune attribution de rôle d’annuaire Azure AD supplémentaire n’est requise.

Si vous disposez d’une machine virtuelle qui n’a plus besoin de l’identité affectée par le système, mais a toujours besoin d’identités affectées par l’utilisateur, utilisez la commande suivante :

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Si vous disposez d’une machine virtuelle qui n’a plus besoin d’identité affectée par le système et qui n’a pas d’identité affectée par l’utilisateur, utilisez la commande suivante :

> [!NOTE]
> La valeur `none` est sensible à la casse. Elle doit être en minuscules. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```
> [!NOTE]
> Si vous avez approvisionné l’identité managée pour l’extension de machine virtuelle de ressources Azure (déconseillé), vous devez la supprimer à l’aide de [az vm extension delete](https://docs.microsoft.com/cli/azure/vm/). Pour plus d’informations, consultez [migrer à partir de l’extension de machine virtuelle vers le point de terminaison IMDS d’Azure pour l’authentification](howto-migrate-vm-extension.md).

## <a name="user-assigned-managed-identity"></a>Identité managée affectée par l’utilisateur

Dans cette section, vous allez découvrir comment ajouter et supprimer une identité managée affectée par l’utilisateur sur une machine virtuelle Azure en utilisant Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Attribuer une identité managée affectée par l’utilisateur lors de la création d’une machine virtuelle Azure

Pour affecter une identité managée affectée par l’utilisateur à une machine virtuelle lors de sa création, votre compte a besoin de l’affectation de rôle [Opérateur d’identité managée](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) et [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Aucune attribution de rôle d’annuaire Azure AD supplémentaire n’est requise.

1. Vous pouvez ignorer cette étape si vous disposez déjà d’un groupe de ressources que vous souhaitez utiliser. Créez un [groupe de ressources](~/articles/azure-resource-manager/resource-group-overview.md#terminology) pour contenir et déployer votre identité managée affectée par l’utilisateur en utilisant la commande [az group create](/cli/azure/group/#az-group-create). N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<LOCATION>` par vos propres valeurs. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Créez une identité managée affectée par l’utilisateur en utilisant la commande [az identity create](/cli/azure/identity#az-identity-create).  Le paramètre `-g` spécifie le groupe de ressources où l’identité managée affectée par l’utilisateur est créée, et le paramètre `-n` spécifie son nom.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   La réponse contient les détails de l’identité managée affectée par l’utilisateur qui a été créée, comme dans l’exemple suivant. La valeur d’ID de ressource attribuée à l’identité managée affectée par l’utilisateur est utilisée à l’étape suivante.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
       "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
       "location": "westcentralus",
       "name": "<USER ASSIGNED IDENTITY NAME>",
       "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
       "resourceGroup": "<RESOURCE GROUP>",
       "tags": {},
       "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
       "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Créez une machine virtuelle à l’aide de la commande [az vm create](/cli/azure/vm/#az-vm-create). L’exemple suivant crée une machine virtuelle associée à la nouvelle identité managée affectée par l’utilisateur, tel que spécifiée par le paramètre `--assign-identity`. N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Attribuer une identité managée affectée par l’utilisateur à une machine virtuelle Azure existante

Pour affecter une identité managée affectée par l’utilisateur à une machine virtuelle, votre compte a besoin de l’affectation de rôle [Opérateur d’identité managée](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) et [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Aucune attribution de rôle d’annuaire Azure AD supplémentaire n’est requise.

1. Créez une identité attribuée à l’utilisateur avec la commande [az identity create](/cli/azure/identity#az-identity-create).  Le paramètre `-g` spécifie le groupe de ressources où l’identité managée affectée par l’utilisateur est créée, et le paramètre `-n` spécifie son nom. N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs :

    > [!IMPORTANT]
    > La création d’identités managées affectées par l’utilisateur avec des caractères spéciaux (tel un trait de soulignement) dans le nom n’est pas prise en charge actuellement. Utilisez des caractères alphanumériques. Revenez ultérieurement pour des mises à jour.  Pour plus d’informations, consultez [FAQ et problèmes connus](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   La réponse contient les détails de l’identité managée affectée par l’utilisateur qui a été créée, comme dans l’exemple suivant. 

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Attribuez l’identité managée affectée par l’utilisateur à votre machine virtuelle en utilisant la commande [az vm identity assign](/cli/azure/vm). N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<VM NAME>` par vos propres valeurs. `<USER ASSIGNED IDENTITY NAME>` est la propriété `name` de ressource de l’identité managée affectée par l’utilisateur, telle que créée à l’étape précédente :

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Supprimer une identité managée affectée par l’utilisateur d’une machine virtuelle Azure

Pour supprimer une identité affectée par l’utilisateur à une machine virtuelle, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor). 

S’il s’agit de la seule identité managée affectée par l’utilisateur attribuée à la machine virtuelle, `UserAssigned` est supprimé de la valeur du type d’identité.  N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<VM NAME>` par vos propres valeurs. `<USER ASSIGNED IDENTITY>` sera la propriété `name` de l’identité managée affectée par l’utilisateur, qui est accessible dans la section identity de la machine virtuelle en utilisant la commande `az vm identity show` :

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

Si votre machine virtuelle ne dispose pas d’une identité managée affectée par le système, et si vous souhaitez supprimer toutes les identités affectées par l’utilisateur, utilisez la commande suivante :

> [!NOTE]
> La valeur `none` est sensible à la casse. Elle doit être en minuscules.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Si votre machine virtuelle dispose d’une identité affectées par le système et d’identité affectées par l’utilisateur, vous pouvez supprimer toutes les identités affectées par l’utilisateur en choisissant de n’utiliser que l’identité affectée par le système. Utilisez la commande suivante :

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Étapes suivantes
- [Vue d’ensemble des identités managées pour les ressources Azure](overview.md)
- Pour obtenir les guides de démarrages rapides complets sur la création de machines virtuelles Azure, consultez : 
  - [Créer une machine virtuelle Windows avec l’interface Azure CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Créer une machine virtuelle Linux avec Azure CLI](../../virtual-machines/linux/quick-create-cli.md) 

















