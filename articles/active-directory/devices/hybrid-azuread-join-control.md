---
title: Validation contrôlée de la jonction Azure AD hybride - Azure AD
description: Apprenez à effectuer une validation contrôlée de la jonction Azure AD hybride avant de l’activer dans toute l’organisation en une fois
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d59104bf9c7675fdac2c245fff89ab1483b96b67
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481717"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Validation contrôlée de la jonction Azure AD hybride

Lorsque toutes les conditions préalables sont en place, les appareils Windows sont inscrits automatiquement en tant qu’appareils dans votre locataire Azure AD. L’état de ces identités d’appareils dans Azure AD est appelé jonction Azure AD hybride. Des informations supplémentaires sur les concepts abordés dans cet article sont disponibles dans les articles [Présentation de la gestion des appareils dans Azure Active Directory](overview.md) et [Planifier votre implémentation de jonction Azure Active Directory hybride](hybrid-azuread-join-plan.md).

Les organisations peuvent vouloir effectuer une validation contrôlée de la jonction Azure AD hybride avant de l’activer dans leur organisation entière en une fois. Cet article explique comment effectuer une validation contrôlée de jonction Azure AD hybride.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Validation contrôlée de la jonction Azure AD hybride sur des appareils Windows actuels

Pour les appareils qui fonctionnent avec le système d’exploitation d’ordinateur Windows, la version prise en charge est la Mise à jour anniversaire Windows 10 (version 1607) ou une version ultérieure. La meilleure pratique consiste à effectuer une mise à niveau vers la dernière version de Windows 10.

Pour effectuer une validation contrôlée de la jonction Azure AD hybride sur des appareils Windows actuels, vous devez :

1. Effacer l’entrée de point de connexion de service (SCP) à partir d’Active Directory (AD) le cas échéant
1. Configurer le paramètre de Registre côté client pour le point de connexion de service sur vos ordinateurs joints au domaine à l’aide d’un objet de stratégie de groupe
1. Si vous utilisez AD FS, vous devez également configurer le paramètre de Registre côté client pour le point de connexion de service sur votre serveur AD FS à l’aide d’un objet de stratégie de groupe  



### <a name="clear-the-scp-from-ad"></a>Effacer le point de connexion de service d’AD

Utilisez l’éditeur Active Directory Services Interfaces Editor (ADSI Edit) pour modifier les objets point de connexion de service dans Active Directory.

1. Lancez l’application de bureau **ADSI Edit** à partir d’un poste de travail d’administration ou d’un contrôleur de domaine en tant qu’administrateur d’entreprise.
1. Connectez-vous au **contexte d’appellation de configuration** de votre domaine.
1. Accédez à **CN=Configuration,DC=contoso,DC=com** > **CN=Services** > **CN=Device Registration Configuration**.
1. Cliquez avec le bouton droit sur l’objet Nœud terminal sous **CN=Device Registration Configuration** et sélectionnez **Properties** (Propriétés).
   1. Sélectionnez **keywords** (Mots clés) à partir de la fenêtre **Éditeur d’attributs** et cliquez sur **Modifier**.
   1. Sélectionnez les valeurs de **azureADId** et **azureADName** (une à la fois) et cliquez sur **Supprimer**.
1. Fermez **ADSI Edit**.


### <a name="configure-client-side-registry-setting-for-scp"></a>Configurer le paramètre de Registre côté client pour le point de connexion de service

Utilisez l’exemple suivant pour créer un objet de stratégie de groupe afin de déployer un paramètre de Registre configurant une entrée de point de connexion de service dans le Registre de vos appareils.

1. Ouvrez une console de gestion des stratégies de groupe et créez un objet Stratégie de groupe dans votre domaine.
   1. Nommez votre objet de stratégie de groupe nouvellement créé (par exemple, ClientSideSCP).
1. Modifiez l’objet de stratégie de groupe et recherchez le chemin suivant : **Configuration de l’ordinateur** > **Préférences** > **Paramètres Windows** > **Registre**
1. Cliquez avec le bouton droit sur le Registre, puis sélectionnez **Nouveau** > **Élément de Registre**
   1. Sous l’onglet **Général**, configurez ce qui suit :
      1. Action : **Mettre à jour**
      1. Hive : **HKEY_LOCAL_MACHINE**
      1. Chemin de la clé : **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nom de la valeur : **TenantId**
      1. Type de valeur : **REG_SZ**
      1. Données de la valeur : Le GUID ou **ID de répertoire** de votre instance Azure AD (cette valeur se trouve dans le **portail Azure** > **Azure Active Directory**  > **Propriétés** > **ID de répertoire**)
   1. Cliquez sur **OK**
1. Cliquez avec le bouton droit sur le Registre, puis sélectionnez **Nouveau** > **Élément du Registre**
   1. Sous l’onglet **Général**, configurez ce qui suit :
      1. Action : **Mettre à jour**
      1. Hive : **HKEY_LOCAL_MACHINE**
      1. Chemin de la clé : **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nom de la valeur : **TenantName**
      1. Type de valeur : **REG_SZ**
      1. Données de la valeur : Votre **nom de domaine** vérifié dans Azure AD (par exemple `contoso.onmicrosoft.com` ou tout autre nom de domaine vérifié dans votre répertoire)
   1. Cliquez sur **OK**
1. Fermez l’éditeur pour l’objet de stratégie de groupe nouvellement créé.
1. Liez l’objet de stratégie de groupe nouvellement créé à l’unité d’organisation souhaitée contenant des ordinateurs joints au domaine qui appartiennent à votre analyse de lancement contrôlée.

### <a name="configure-ad-fs-settings"></a>Configurer les paramètres AD FS

Si vous utilisez AD FS, vous devez d’abord configurer le point de connexion de service côté client en suivant les instructions mentionnées ci-dessus, mais en liant l’objet de stratégie de groupe à vos serveurs AD FS. Cette configuration est nécessaire pour qu’AD FS établisse la source des identités d’appareils comme étant Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Validation contrôlée de la jonction Azure AD hybride sur des appareils Windows de bas niveau

Pour inscrire des ordinateurs Windows de bas niveau, les organisations doivent installer [Microsoft Workplace Join pour les ordinateurs non-Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) à partir du Centre de téléchargement Microsoft.

Vous pouvez déployer le package à l’aide d’un système de distribution de logiciels, comme  [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Le package prend en charge les options d’installation sans assistance standard avec le paramètre quiet. La branche actuelle de Configuration Manager offre des avantages supplémentaires par rapport aux versions précédentes, comme la possibilité d’effectuer le suivi des inscriptions effectuées.

Le programme d’installation crée une tâche planifiée sur le système, qui s’exécute dans le contexte de l’utilisateur. La tâche est déclenchée lorsque l’utilisateur se connecte à Windows. La tâche joint en mode silencieux l’appareil à Azure AD, au moyen des informations d’identification de l’utilisateur, après son authentification auprès d’Azure AD.

Pour contrôler l’inscription des appareils, vous devez déployer le package Windows Installer uniquement sur un groupe sélectionné d’appareils Windows de bas niveau.

> [!NOTE]
> Si aucun point de connexion de service n’est configuré dans Active Directory, vous devez suivre la même approche décrite pour [configurer le paramètre de Registre côté client pour le point de connexion de service](#configure-client-side-registry-setting-for-scp) sur vos ordinateurs joints au domaine à l’aide d’un objet de stratégie de groupe.


Une fois que vous avez vérifié que tout fonctionne comme prévu, vous pouvez inscrire automatiquement le reste de vos appareils Windows actuels et de bas niveau avec Azure AD en [configurant le point de connexion de service à l’aide d’Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Étapes suivantes

[Planifier l’implémentation de la jonction Azure Active Directory hybride](hybrid-azuread-join-plan.md)
