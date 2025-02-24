---
title: Ajouter et vérifier des noms de domaines personnalisés - Azure Active Directory | Microsoft Docs
description: Concepts de gestion et procédures pour gérer un nom de domaine dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 908ae768ae471ab6f49452c99323c31d34772d45
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60472341"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Gestion des noms de domaine personnalisés dans Azure Active Directory

Un nom de domaine est une partie importante de l’identificateur de nombreuses ressources de répertoire : il fait partie du nom ou de l’adresse électronique d’un utilisateur, de l’adresse d’un groupe et parfois de l’URI d’ID d’une application. Une ressource dans Azure Active Directory (Azure AD) peut inclure un nom de domaine appartenant au répertoire qui contient la ressource. Seul un administrateur général peut gérer des domaines dans Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Définir le nom de domaine principal pour votre répertoire Azure AD

Lors de la création du répertoire, le nom de domaine initial, par exemple « contoso.onmicrosoft.com », est également le nom de domaine principal. Le domaine principal est le nom de domaine par défaut pour un nouvel utilisateur lorsque vous créez un nouvel utilisateur. Le fait de définir un nom de domaine principal simplifie le processus permettant à un administrateur de créer des utilisateurs dans le portail. Pour modifier le nom de domaine principal, procédez comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Azure Active Directory**.
3. Sélectionnez **Noms de domaine personnalisés**.
  
   ![Ouvrir la page de gestion des utilisateurs](./media/domains-manage/add-custom-domain.png)
4. Sélectionnez le nom du domaine que vous souhaitez choisir comme domaine principal.
5. Sélectionnez la commande **Définir comme principal**. Confirmez votre choix lorsque vous y êtes invité.
  
   ![Créer un nom de domaine principal](./media/domains-manage/make-primary-domain.png)

Vous pouvez modifier le nom de domaine principal de votre répertoire par n’importe quel domaine personnalisé vérifié qui n’est pas fédéré. La modification du domaine principal de votre répertoire ne change pas le nom des utilisateurs existants.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Ajouter des noms de domaine personnalisés à votre locataire Azure AD

Vous pouvez ajouter jusqu’à 900 noms de domaine managé. Si vous configurez tous vos domaines pour la fédération avec le répertoire Active Directory local, vous pouvez ajouter jusqu’à 450 noms de domaine dans chaque répertoire.

## <a name="add-subdomains-of-a-custom-domain"></a>Ajouter des sous-domaines d’un domaine personnalisé

Si vous souhaitez ajouter un nom de domaine de troisième niveau, tel que « europe.contoso.com » à votre répertoire, vous devez tout d’abord ajouter et vérifier le domaine de second niveau, tel que contoso.com. Le sous-domaine est automatiquement vérifié par Azure AD. Pour voir que le sous-domaine ajouté est vérifié, actualisez la liste des domaines dans le navigateur.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Que faire en cas de modification du bureau d’enregistrement DNS pour votre nom de domaine personnalisé ?

Si vous modifiez les bureaux d’enregistrement DNS, aucune tâche de configuration supplémentaire n’est à effectuer dans Azure AD. Vous pouvez continuer à utiliser le nom de domaine avec Azure AD sans interruption. Si vous utilisez votre nom de domaine personnalisé avec Office 365, Intune ou d’autres services s’appuyant sur des noms de domaine personnalisés dans Azure AD, consultez la documentation dédiée à ces services.

## <a name="delete-a-custom-domain-name"></a>Supprimer un nom de domaine personnalisé

Vous pouvez supprimer un nom de domaine personnalisé de votre répertoire Azure AD si votre organisation n’utilise plus ce nom de domaine ou si vous souhaitez l’utiliser avec un autre répertoire Azure AD.

Pour supprimer un nom de domaine personnalisé, vous devez d’abord vous assurer qu’aucune des ressources de votre répertoire ne s’appuie sur le nom de domaine. Vous ne pouvez pas supprimer un nom de domaine de votre répertoire si :

* L’utilisateur dispose d’un nom d’utilisateur, d’une adresse de messagerie ou d’une adresse de proxy qui incluent le nom de domaine.
* Le groupe dispose d’une adresse de messagerie ou d’une adresse de proxy qui incluent le nom de domaine.
* Une application dans Azure AD dispose d’une URI ID d’application qui inclut le nom de domaine.

Vous devez modifier ou supprimer n’importe quelle ressource de ce type dans votre répertoire Azure AD pour pouvoir supprimer le nom de domaine personnalisé.

### <a name="forcedelete-option"></a>Option ForceDelete

Vous pouvez appliquer l’option **ForceDelete** à un nom de domaine dans le [Centre d’administration Azure AD](https://aad.portal.azure.com) ou à l’aide de [l’API Microsoft Graph](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta). Ces options utilisent une opération asynchrone et mettent à jour toutes les références depuis le nom de domaine personnalisé comme « user@contoso.com » vers le nom de domaine par défaut initial comme « user@contoso.onmicrosoft.com ». 

Pour appeler **ForceDelete** dans le Portail Azure, vous devez vous assurer qu’il y a moins de 1 000 références au nom de domaine, et toutes les références dans lesquelles Exchange est le service d’approvisionnement doivent être mises à jour ou supprimées dans le [ Centre d’administration Exchange](https://outlook.office365.com/ecp/). Cela inclut les listes distribuées et les groupes de sécurité à extension messagerie ; pour plus d’informations, consultez [Supprimer les groupes de sécurité à extension messagerie](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups). En outre, l’opération **ForceDelete** échoue si l’un des énoncés suivants est vrai :

* Vous avez acheté un domaine via les services d’abonnement de domaine Office 365
* Vous êtes un partenaire qui administre le compte d’un autre locataire

Les actions suivantes sont effectuées dans le cadre de l’opération **ForceDelete** :

* Renommage des éléments UPN, EmailAddress et ProxyAddress des utilisateurs avec des références au nom de domaine personnalisé vers le nom de domaine par défaut initial.
* Renommage de l’élément EmailAddress des groupes avec des références au nom de domaine personnalisé vers le nom de domaine par défaut initial.
* Renommage des éléments identifierUris des applications avec des références au nom de domaine personnalisé vers le nom de domaine par défaut initial.

Une erreur est renvoyée quand :

* Le nombre d’objets à renommer est supérieur à 1 000
* L’une des applications à renommer est une application mutualisée

### <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

**Q : Pourquoi la suppression du domaine échoue-t-elle avec une erreur indiquant que je dispose de groupes Exchange contrôlés sur ce nom de domaine ?** <br>
**R :** Aujourd'hui, certains groupes tels que les groupes de sécurité à extension messagerie et les listes distribuées sont approvisionnés par Exchange et doivent être nettoyés manuellement dans le [Centre d'administration Exchange](https://outlook.office365.com/ecp/). Des éléments ProxyAddresses, qui reposent sur le nom de domaine personnalisé, peuvent être en attente et devront être mis à jour manuellement vers un autre nom de domaine. 

**Q : Je suis connecté en tant qu'administrateur\@contoso.com, mais je ne peux pas supprimer le nom de domaine « contoso.com ».**<br>
**R :** Vous ne pouvez pas référencer le nom de domaine personnalisé que vous cherchez à supprimer dans votre nom de compte d'utilisateur. Assurez-vous que le compte d’administrateur général utilise le nom de domaine par défaut initial (.onmicrosoft.com) comme admin@contoso.onmicrosoft.com. Connectez-vous avec un compte d’administrateur général différent comme admin@contoso.onmicrosoft.com ou un autre nom de domaine personnalisé comme « fabrikam.com » où le compte est admin@fabrikam.com.

**Q : J'ai cliqué sur le bouton Supprimer un domaine et je vois l'état `In Progress` pour l'opération de suppression. Combien de temps cela prend-il ? Que se passe-t-il en cas d’échec ?**<br>
**R :** L'opération de suppression de domaine est une tâche d'arrière-plan asynchrone qui renomme toutes les références au nom de domaine. Cette opération dure normalement une à deux minutes. En cas d’échec de suppression du domaine, vérifiez que vous n’avez pas :

* Des applications configurées sur le nom de domaine avec l’appIdentifierURI
* Des groupes à extension de messagerie référençant le nom de domaine personnalisé
* Plus de 1 000 références au nom de domaine

Si vous remarquez que l’une des conditions n’a pas été respectée, nettoyez manuellement les références et essayez une nouvelle fois de supprimer le domaine.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Utiliser PowerShell ou API Graph pour gérer les noms de domaine

La plupart des tâches de gestion des noms de domaine dans Azure Active Directory peuvent également être accomplies à l’aide de Microsoft PowerShell, ou encore par programmation à l’aide d’API Graph d’Azure AD.

* [Utilisation de PowerShell pour gérer les noms de domaine dans Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Utilisation d’API Graph pour gérer les noms de domaine dans Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter des noms de domaines personnalisés](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Supprimer les groupes de sécurité à extension messagerie](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [ForceDelete a custom domain name with Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta) (Appliquer une opération ForceDelete sur un nom de domaine personnalisé avec l’API Microsoft Graph)
