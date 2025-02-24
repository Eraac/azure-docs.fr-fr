---
title: 'Azure Active Directory Domain Services : Prise en main | Microsoft Docs'
description: Activer Azure Active Directory Domain Services à l’aide du portail Azure
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: iainfou
ms.openlocfilehash: fd6db8a9ac6759b55273940d4a100fc3c4df7692
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473640"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Activer Azure Active Directory Domain Services à l’aide du portail Azure
Cet article explique comment activer Azure Active Directory Domain Services (Azure AD DS) au moyen du portail Azure.


## <a name="before-you-begin"></a>Avant de commencer
Pour effectuer les tâches décrites dans cet article, vous avez besoin de ce qui suit :

* Un **abonnement Azure**valide.
* Un **répertoire Azure AD** , synchronisé avec un répertoire local ou un répertoire cloud uniquement.
* **L’abonnement Azure Étape 2 doit être associé à votre répertoire Azure AD**.
* Vous devez disposer des privilèges **Administrateur global** dans votre répertoire Azure AD pour activer Azure AD Domain Services.


## <a name="enable-azure-ad-domain-services"></a>Activer Azure AD Domain Services

Pour lancer l’Assistant **Activer Azure AD Domain Services**, procédez comme suit :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Dans le volet de gauche, cliquez sur **Créer une ressource**.
3. Dans la page **Nouveau**, tapez **Domain Services** dans la barre de recherche.

    ![Rechercher Domain Services](./media/getting-started/search-domain-services.png)

4. Dans la liste des suggestions de recherche, cliquez pour sélectionner **Azure AD Domain Services**. Dans la page **Azure AD Domain Services**, cliquez sur le bouton **Créer**.

    ![Vue Services de domaine](./media/getting-started/domain-services-blade.png)

5. L’Assistant **Activer Azure AD Domain Services** est lancé.


## <a name="task-1-configure-basic-settings"></a>Tâche 1 : Configurer les paramètres de base
Dans la page **Fonctions de base** de l’Assistant, spécifiez le nom de domaine DNS pour le domaine managé. Vous pouvez également choisir le groupe de ressources et l’emplacement Azure sur lequel le domaine managé doit être déployé.

![Configurer les fonctions de base](./media/getting-started/domain-services-blade-basics.png)

1. Choisissez le **Nom de domaine DNS** pour votre domaine managé.

   > [!NOTE]
   > **Indications pour la sélection d’un nom de domaine DNS**
   > * **Nom de domaine intégré :** par défaut, l'Assistant spécifie pour vous le nom de domaine par défaut/intégré du répertoire (avec un suffixe **.onmicrosoft.com**). Si vous choisissez d’activer l’accès LDAP sécurisé au domaine géré sur Internet, vous risquez de rencontrer des problèmes pour créer un enregistrement DNS public ou obtenir un certificat LDAP sécurisé d’une autorité de certification publique pour ce nom de domaine. Microsoft possède le domaine *.onmicrosoft.com* et les autorités de certification n’émettront pas de certificats pour ce domaine.
   > * **Noms de domaine personnalisés :** Vous pouvez également entrer un nom de domaine personnalisé. Dans cet exemple, le nom de domaine personnalisé est *contoso100.com*.
   > * **Suffixes de domaine non routables :** nous déconseillons généralement l'utilisation d'un suffixe de nom de domaine non routable. Par exemple, il est préférable d’éviter de créer un domaine avec le nom de domaine DNS 'contoso.local'. Le suffixe DNS '.local' n’est pas routable et peut entraîner des problèmes de résolution DNS.
   > * **Restrictions de préfixe de domaine :** Le préfixe du nom de domaine spécifié (par exemple, *contoso100* dans le nom de domaine *contoso100.com*) doit contenir au maximum 15 caractères. Vous ne pouvez pas créer de domaine managé avec un préfixe de plus de 15 caractères.
   > * **Conflits de noms de réseau :** Vérifiez que le nom de domaine DNS choisi pour le domaine géré n’existe pas au sein du réseau virtuel. Plus spécifiquement, vérifiez les poins suivants :
   >     * Vous disposez d’un domaine Active Directory présentant le nom de domaine DNS au sein du réseau virtuel.
   >     * Le réseau virtuel dans lequel vous envisagez d’activer le domaine managé a une connexion VPN avec votre réseau local. Dans ce scénario, veillez à ne pas avoir de domaine portant le même nom de domaine DNS sur votre réseau local.
   >     * Il existe un service cloud portant ce nom sur le réseau virtuel.

2. Sélectionnez l’**Abonnement** Azure dans lequel vous souhaitez créer le domaine managé.

3. Sélectionnez le **Groupe de ressources** auquel le domaine managé doit appartenir. Choisissez entre les options **Créer** ou **Utiliser l’existant** pour sélectionner le groupe de ressources.

4. Choisissez l’**Emplacement** Azure dans lequel créer le domaine managé. Dans la page **Réseau** de l’Assistant, vous voyez uniquement les réseaux virtuels appartenant à l’emplacement que vous avez sélectionné.

5. Cliquez sur **OK** pour accéder à la page **Réseau** de l’Assistant.


## <a name="next-step"></a>Étape suivante
[Tâche 2 : Configurer les paramètres réseau](active-directory-ds-getting-started-network.md)
