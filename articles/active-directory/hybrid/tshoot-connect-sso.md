---
title: 'Azure Active Directory Connect : Résoudre les problèmes d’authentification unique transparente | Microsoft Docs'
description: Cette rubrique explique comment résoudre les problèmes relatifs à l’authentification unique transparente Azure Active Directory
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/24/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: abfdad1db655c102dbfb300434eac952fe2154dc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60381889"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Résoudre les problèmes d’authentification unique transparente Azure Active Directory

Cet article fournit des informations sur les problèmes courants liés à l’authentification unique transparente (Seamless SSO) Azure Active Directory (Azure AD).

## <a name="known-issues"></a>Problèmes connus

- Dans certains cas, l’activation de l’authentification unique transparente peut prendre jusqu’à 30 minutes.
- Si vous désactivez et réactivez l’authentification unique transparente sur votre client, les utilisateurs ne recevront pas l’expérience d’authentification unique jusqu'à ce que leurs tickets Kerberos, généralement valides pour 10 heures, expirent.
- La prise en charge du navigateur Microsoft Edge n’est pas disponible.
- Si l’authentification unique transparente réussit, l’utilisateur n’a pas la possibilité de choisir l’option **Maintenir la connexion**. En raison de ce comportement, [les scénarios de mappage SharePoint et OneDrive](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) ne fonctionnent pas.
- Les clients Win32 Office 365 (Outlook, Word, Excel, etc.) dotés des versions 16.0.8730.xxxx et ultérieures sont pris en charge au moyen d’un flux non interactif. Les autres versions ne sont pas prises en charge ; dans ces versions, les utilisateurs entrent leur nom d’utilisateur, mais pas les mots de passe, pour se connecter. En ce qui concerne OneDrive, vous devez activer la [fonctionnalité de configuration silencieuse OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) pour une utilisation de l’authentification sans assistance.
- L’authentification unique transparente ne fonctionne pas en mode Navigation privée sur Firefox.
- L’authentification unique transparente ne fonctionne pas sur Internet Explorer quand le mode protégé amélioré est activé.
- L’authentification unique transparente ne fonctionne pas sur les navigateurs mobiles iOS et Android.
- Si un utilisateur fait partie de trop de groupes dans Active Directory, son ticket Kerberos sera probablement trop volumineux pour être traité, ce qui provoquera l’échec de l’authentification unique transparente. Les requêtes HTTPS Azure AD peuvent comporter des en-têtes dont la taille ne dépasse pas 50 Ko ; les tickets Kerberos doivent être plus petits pour laisser la place à d’autres artefacts d’Azure AD (en général, 2 à 5 Ko), comme les cookies. Nous vous recommandons de réduire les appartenances de l’utilisateur à des groupes, puis de réessayer.
- Si vous synchronisez 30 forêts Active Directory ou plus, vous ne pouvez pas activer l’authentification unique transparente via Azure AD Connect. En guise de solution de contournement, vous pouvez [activer manuellement](#manual-reset-of-the-feature) la fonctionnalité pour votre locataire.
- L’ajout de l’URL du service Azure AD (https://autologon.microsoftazuread-sso.com) à la zone Sites de confiance, plutôt que la zone Intranet local, *empêche les utilisateurs de se connecter*.
- L’authentification unique transparente utilise le type de chiffrement **RC4_HMAC_MD5** pour Kerberos. La désactivation de l’utilisation du type de chiffrement **RC4_HMAC_MD5** dans vos paramètres Active Directory empêche l’authentification unique transparente. Dans l’outil Éditeur de gestion des stratégies de groupe, vérifiez que la valeur de la stratégie pour **RC4_HMAC_MD5** sous **Configuration ordinateur -> Paramètres Windows -> Paramètres de sécurité -> Stratégies locales -> Options de sécurité -> « Sécurité réseau : Configurer les types de chiffrement autorisés pour Kerberos »** est définie sur **activé**. En outre, l’authentification unique transparente ne peut pas utiliser d’autres types de chiffrement ; pensez à vérifier qu’ils sont donc définis sur **désactivé**.

## <a name="check-status-of-feature"></a>Vérifier l’état de la fonctionnalité

Assurez-vous que la fonctionnalité Authentification unique transparente est toujours **activée** sur votre client. Vous pouvez vérifier l’état en accédant au volet **Azure AD Connect** dans le [Centre d’administration Azure Active Directory](https://aad.portal.azure.com/).

![Centre d'administration Azure Active Directory : Volet Azure AD Connect](./media/tshoot-connect-sso/sso10.png)

Cliquez pour afficher toutes les forêts Active Directory dans lesquelles l’authentification unique transparente est activée.

![Centre d'administration Azure Active Directory : Volet Authentification unique fluide](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Raisons des échecs de connexion dans le Centre d’administration Azure Active Directory (licence Premium requise)

Si votre locataire dispose d’une licence Azure AD Premium, vous pouvez également consulter le [rapport d’activité de connexion](../reports-monitoring/concept-sign-ins.md) dans le [Centre d’administration Azure Active Directory](https://aad.portal.azure.com/).

![Centre d'administration Azure Active Directory : Rapport de connexions](./media/tshoot-connect-sso/sso9.png)

Accédez à **Azure Active Directory** > **Connexions** dans le [Centre d’administration Azure Active Directory](https://aad.portal.azure.com/), puis sélectionnez une activité de connexion. Recherchez le champ **Code d’erreur de connexion**. Notez la valeur de ce champ et cherchez dans le tableau suivant la raison de l’échec et la solution à appliquer :

|Code d’erreur de connexion|Raison de l’échec de connexion|Résolution :
| --- | --- | ---
| 81001 | Le ticket Kerberos de l’utilisateur est trop volumineux. | Réduisez les appartenances à des groupes de l’utilisateur, puis réessayez.
| 81002 | Impossible de valider le ticket Kerberos de l’utilisateur. | Consultez la [liste de contrôle pour la résolution des problèmes](#troubleshooting-checklist).
| 81003 | Impossible de valider le ticket Kerberos de l’utilisateur. | Consultez la [liste de contrôle pour la résolution des problèmes](#troubleshooting-checklist).
| 81004 | Échec de la tentative d’authentification Kerberos. | Consultez la [liste de contrôle pour la résolution des problèmes](#troubleshooting-checklist).
| 81008 | Impossible de valider le ticket Kerberos de l’utilisateur. | Consultez la [liste de contrôle pour la résolution des problèmes](#troubleshooting-checklist).
| 81009 | Impossible de valider le ticket Kerberos de l’utilisateur. | Consultez la [liste de contrôle pour la résolution des problèmes](#troubleshooting-checklist).
| 81010 | L’authentification unique transparente a échoué, car le ticket Kerberos de l’utilisateur est arrivé à expiration ou n’est pas valide. | L’utilisateur doit se connecter à partir d’un appareil joint à un domaine au sein de votre réseau d’entreprise.
| 81011 | Impossible de trouver l’objet utilisateur à partir des informations contenues dans le ticket Kerberos de l’utilisateur. | Utilisez Azure AD Connect pour synchroniser les informations de l’utilisateur dans Azure AD.
| 81012 | L’utilisateur qui tente de se connecter à Azure AD est différent de l’utilisateur connecté à l’appareil. | L'utilisateur doit se connecter à partir d’un autre appareil.
| 81013 | Impossible de trouver l’objet utilisateur à partir des informations contenues dans le ticket Kerberos de l’utilisateur. |Utilisez Azure AD Connect pour synchroniser les informations de l’utilisateur dans Azure AD. 

## <a name="troubleshooting-checklist"></a>Liste de contrôle pour la résolution des problèmes

Utilisez la liste de contrôle suivante pour résoudre les problèmes d’authentification unique transparente :

- Vérifiez que la fonctionnalité d’authentification unique transparente est activée dans Azure AD Connect. Si vous ne pouvez pas activer la fonctionnalité (par exemple, en raison d’un port bloqué), vérifiez que toutes les [conditions préalables](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) sont bien respectées.
- Si vous avez activé [Azure AD Join](../active-directory-azureadjoin-overview.md) et l’authentification unique transparente sur votre client, assurez-vous que le problème ne vient pas d’Azure AD Join. L’authentification unique à partir d’Azure AD Join est prioritaire sur l’authentification unique transparente si l’appareil est inscrit auprès d’Azure AD et est joint au domaine. Avec l’authentification unique à partir d’Azure AD Join, l’utilisateur voit une vignette de connexion qui indique « Connecté à Windows ».
- Vérifiez que l’URL Azure AD (https://autologon.microsoftazuread-sso.com) fait partie des paramètres de la zone Intranet de l’utilisateur.
- Vérifiez que l’appareil d’entreprise est joint au domaine Active Directory. L’appareil _n’a pas_ besoin d’être [joint à Azure AD](../active-directory-azureadjoin-overview.md) pour que l’authentification unique fluide soit opérationnelle.
- Assurez-vous que l'utilisateur est connecté à l'appareil via un compte de domaine Active Directory.
- Vérifiez que le compte de l’utilisateur provient d’une forêt Active Directory dans laquelle l’authentification unique (SSO) transparente a été configurée.
- Vérifiez que l’appareil est connecté au réseau d’entreprise.
- Vérifiez que l’heure de l’appareil est synchronisée à la fois avec celle d’Active Directory et celle du contrôleur de domaine, et qu'elles ne comptent pas plus de cinq minutes d’écart.
- Vérifiez que le compte d’ordinateur `AZUREADSSOACC` est présent et activé dans chaque forêt AD pour laquelle l’authentification unique fluide doit être activée. Si le compte d’ordinateur a été supprimé ou est manquant, vous pouvez utiliser des [applets de commande PowerShell](#manual-reset-of-the-feature) pour les recréer.
- Affichez la liste des tickets Kerberos existants sur l’appareil à l’aide de la commande `klist` dans une invite de commandes. Vérifiez que les tickets émis pour le compte d’ordinateur `AZUREADSSOACC` y figurent. En règle générale, la durée de validité des tickets Kerberos des utilisateurs est de 10 heures. L'instance Active Directory est peut-être paramétrée différemment.
- Si vous avez désactivé et réactivé l’authentification unique transparente sur votre client, les utilisateurs ne recevront pas l’expérience d’authentification unique jusqu'à ce que leurs tickets Kerberos expirent.
- Videz les tickets Kerberos existants de l’appareil à l’aide de la commande `klist purge`, puis réessayez.
- Pour déterminer si des problèmes liés à JavaScript existent, passez en revue les journaux d’activité de console du navigateur (sous **Outils de développement**).
- Examinez les [journaux d’activité des contrôleurs de domaine](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Journaux d’activité des contrôleurs de domaine

Si vous activez l’audit des réussites sur votre contrôleur de domaine, chaque fois qu’un utilisateur se connecte à l’aide de l’authentification unique transparente, une entrée de sécurité est enregistrée dans le journal des événements. Vous trouverez ces événements de sécurité au moyen de la requête suivante. (Recherchez l'événement **4769** associé au compte d'ordinateur **AzureADSSOAcc$** .)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Réinitialisation manuelle de la fonctionnalité

Si vous n’avez pas réussi à résoudre le problème, vous pouvez réinitialiser manuellement la fonctionnalité pour votre locataire. Procédez comme suit sur le serveur sur site où vous exécutez Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Étape 1 : Importer le module PowerShell Authentification unique (SSO) transparente

1. Commencez par télécharger et installer [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Accédez au dossier `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importez le module PowerShell Authentification unique (SSO) transparente à l’aide de la commande suivante : `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Étape 2 : Obtenir la liste des forêts Azure Directory dans lesquelles l’authentification unique (SSO) transparente a été activée

1. Exécutez PowerShell ISE en tant qu’administrateur. Dans PowerShell, appelez `New-AzureADSSOAuthenticationContext`. Lorsque vous y êtes invité, fournissez les informations d’identification de l’administrateur général de votre locataire.
2. Appelez `Get-AzureADSSOStatus`. Cette commande vous fournit la liste des forêts Azure Directory (examinez la liste « Domaines ») dans lesquelles cette fonctionnalité a été activée.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Étape 3 : Désactiver l’authentification unique (Seamless SSO) pour chaque forêt Azure Directory dans laquelle vous avez configuré la fonctionnalité

1. Appelez `$creds = Get-Credential`. Quand vous y êtes invité, entrez les informations d’identification d’administrateur de domaine pour la forêt Azure Directory souhaitée.

   > [!NOTE]
   > Nous utilisons le nom d’utilisateur de l’administrateur de domaine, fourni dans le format des noms d’utilisateurs principaux (UPN) (johndoe@contoso.com), ou dans celui du nom de compte SAM de domaine complet (contoso\johndoe ou contoso.com\johndoe), pour rechercher la forêt AD souhaitée. Si vous utilisez le nom de compte SAM de domaine complet, nous utilisons la partie domaine du nom d’utilisateur pour [localiser le contrôleur de domaine de l’administrateur de domaine à l’aide de DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Si vous utilisez un UPN à la place, nous [le convertissons en nom de compte SAM de domaine complet](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) avant de trouver le contrôleur de domaine approprié.

2. Appelez `Disable-AzureADSSOForest -OnPremCredentials $creds`. Cette commande supprime le compte d’ordinateur `AZUREADSSOACC` du contrôleur de domaine sur site pour cette forêt Azure Directory spécifique.
3. Répétez les étapes précédentes pour chaque forêt Azure Directory dans laquelle vous avez configuré la fonctionnalité.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Étape 4 : Activer l’authentification unique (SSO) transparente pour chaque forêt Azure Directory

1. Appelez `Enable-AzureADSSOForest`. Quand vous y êtes invité, entrez les informations d’identification d’administrateur de domaine pour la forêt Azure Directory souhaitée.

   > [!NOTE]
   > Nous utilisons le nom d’utilisateur de l’administrateur de domaine, fourni dans le format des noms d’utilisateurs principaux (UPN) (johndoe@contoso.com), ou dans celui du nom de compte SAM de domaine complet (contoso\johndoe ou contoso.com\johndoe), pour rechercher la forêt AD souhaitée. Si vous utilisez le nom de compte SAM de domaine complet, nous utilisons la partie domaine du nom d’utilisateur pour [localiser le contrôleur de domaine de l’administrateur de domaine à l’aide de DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Si vous utilisez un UPN à la place, nous [le convertissons en nom de compte SAM de domaine complet](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) avant de trouver le contrôleur de domaine approprié.

2. Répétez l'étape précédente pour chaque forêt Azure Directory dans laquelle vous souhaitez configurer la fonctionnalité.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Étape 5. Activer la fonctionnalité pour votre locataire

Pour activer la fonctionnalité pour votre locataire, appelez `Enable-AzureADSSO -Enable $true`.
