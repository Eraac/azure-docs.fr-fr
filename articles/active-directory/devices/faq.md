---
title: FAQ sur la gestion des appareils Azure Active Directory | Documents Microsoft
description: FAQ sur la gestion des appareils Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8802f9e5c84078725675d961ada7f8183c91c0ec
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481749"
---
# <a name="azure-active-directory-device-management-faq"></a>FAQ sur la gestion des appareils Azure Active Directory

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>Q : J’ai enregistré récemment l’appareil. Pourquoi ne puis-je pas voir l’appareil sous mes informations d’utilisateur dans le portail Azure ? Ou pourquoi le propriétaire de l’appareil est-il marqué N/A pour les appareils joints à Azure Active Directory (Azure AD) hybrides ?

**R :** Les appareils Windows 10 qui sont joints à Azure AD de manière hybride ne s’affichent pas en tant **qu’appareils UTILISATEUR**.
Vous devez utiliser la vue **Tous les appareils** dans le portail Azure. Vous pouvez également utiliser une cmdlet PowerShell [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0).

Seuls les appareils suivants sont répertoriés en tant **qu’appareils UTILISATEUR** :

- Tous les appareils personnels qui ne sont pas joints à Azure AD de manière hybride. 
- Tous les appareils non Windows 10 ou Windows Server 2016.
- Tous les appareils non Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>Q : Comment faire pour connaître l’état de l’inscription d’appareils du client ?

**R :** Dans le portail Azure, accédez à **Tous les appareils**. Recherchez l’appareil à l’aide de l’ID d’appareil. Vérifiez la valeur dans la colonne de type de jointure. Parfois, l’appareil peut avoir été réinitialisé ou réimagé. Il est donc essentiel de vérifier l’état d’enregistrement de l’appareil sur l’appareil :

- Pour les appareils Windows 10 et Windows Server 2016 ou versions ultérieures, exécutez `dsregcmd.exe /status`.
- Pour les versions de système d’exploitation de niveau inférieur, exécutez `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>Q : Je vois l’enregistrement de l’appareil sous les informations UTILISATEUR dans le portail Azure. Et je vois l’état comme étant enregistré sur l’appareil. Ma configuration est-elle correcte pour l’utilisation de l’accès conditionnel ?

**R :** L’état de jointure de l’appareil, reflété par **deviceID**, doit correspondre à l’état sur Azure AD et répondre à tous les critères d’évaluation pour l’accès conditionnel. Pour plus d’informations, consultez [Exiger des appareils gérés pour accéder aux applications cloud avec l’accès conditionnel](../conditional-access/require-managed-devices.md).

---

### <a name="q-i-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered"></a>Q : J’ai supprimé mon appareil dans le portail Azure ou à l’aide de Windows PowerShell. Mais l’état local sur l’appareil indique qu’il est toujours inscrit.

**R :** Cette opération est intentionnelle. L’appareil n’a pas accès aux ressources dans le cloud. 

Si vous souhaitez inscrire à nouveau l’appareil, vous devez effectuer une action manuelle sur celui-ci. 

Pour effacer l’état de jointure des appareils Windows 10 et Windows Server 2016 sur site et joints à un domaine Active Directory, procédez comme suit :

1. Ouvrez une invite de commandes en tant qu’administrateur.
1. Entrez `dsregcmd.exe /debug /leave`.
1. Déconnectez-vous, puis reconnectez-vous pour déclencher la tâche planifiée qui inscrit à nouveau l’appareil auprès d’Azure AD. 

Pour les versions de système d’exploitation Windows de niveau inférieur des appareils sur site et joints à un domaine Active Directory, procédez comme suit :

1. Ouvrez une invite de commandes en tant qu’administrateur.
1. Entrez `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
1. Entrez `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>Q : Pourquoi le portail Azure affiche-t-il des entrées d’appareils dupliquées ?

**R :**

- Pour Windows 10 et Windows Server 2016, en cas de tentatives répétées visant à disjoindre et à joindre à nouveau le même appareil, des entrées dupliquées peuvent s’afficher. 
- Chaque utilisateur Windows qui utilise **Ajouter un compte professionnel ou scolaire** crée un nouvel enregistrement d’appareil avec le même nom d’appareil.
- Pour les versions de système d’exploitation Windows de niveau inférieur des appareils sur site et joints à un domaine Azure Directory, l’inscription automatique crée un nouvel enregistrement d’appareil avec le même nom d’appareil pour chaque utilisateur du domaine qui se connecte à l’appareil. 
- Une machine jointe Azure AD qui est réinitialisée, réinstallée et jointe à nouveau avec le même nom s’affiche en tant que nouvel enregistrement avec le même nom d’appareil.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>Q : L’inscription d’appareil Windows 10 dans Azure AD prend-elle en charge les modules de plateforme sécurisée en mode FIPS ?

**R :** Non, actuellement, l’inscription d’appareil sur Windows 10 pour tous les états d’appareil (jointure Azure AD Hybride, jointure Azure AD et Azure AD inscrit) ne prend pas en charge les modules de plateforme sécurisée (TPM) en mode FIPS. Pour une jointure ou une inscription à Azure AD réussies, le mode FIPS doit être désactivé pour le module de plateforme sécurisée sur ces appareils

---

**Q : Pourquoi un utilisateur peut-il toujours accéder aux ressources à partir d’un appareil que j’ai désactivé dans le portail Azure ?**

**R :** Une opération de révocation peut prendre jusqu’à une heure pour être entièrement appliquée.

>[!NOTE] 
>Pour les appareils inscrits, nous vous recommandons de réinitialiser l’appareil pour vous assurer que les utilisateurs ne puissent pas accéder aux ressources. Pour plus d’informations, consultez [Qu’est-ce que l’inscription d’appareil ?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>FAQ sur la jonction Azure AD

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>Q : Comment faire pour disjoindre un appareil joint à Azure AD localement sur l’appareil ?

**R :** 
- Pour les appareils joints à Azure AD hybrides, assurez-vous de désactiver l’inscription automatique. Alors, la tâche planifiée n’inscrit pas l’appareil à nouveau. Ensuite, ouvrez une invite de commandes en tant qu’administrateur et saisissez `dsregcmd.exe /debug /leave`. Ou exécutez cette commande en tant que script sur plusieurs appareils pour les disjoindre en bloc.
- Pour les appareils uniquement joints à Azure AD, assurez-vous d’avoir un administrateur local en mode hors connexion de compte ou créez-en un. Vous ne pouvez pas vous connecter avec des informations d’identification utilisateur Azure AD. Ensuite, accédez à **Paramètres** > **Comptes** > **Accès professionnel ou scolaire**. Sélectionnez **Se déconnecter** dans votre compte. Suivez les invites et fournissez les informations d’identification de l’administrateur local lorsque vous y êtes invité. Redémarrez l’appareil pour terminer le processus de disjonction.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>Q : Mes utilisateurs peuvent-ils se connecter à des appareils joints à Azure AD qui sont supprimés ou désactivés dans Azure AD ?

**R :** Oui. Windows possède une fonctionnalité de mise en cache du nom d’utilisateur et du mot de passe qui permet aux utilisateurs qui se sont précédemment connectés d’accéder au bureau rapidement, même sans connectivité réseau. 

Quand un appareil est supprimé ou désactivé dans Azure AD, ce fait n’est pas connu par l’appareil Windows. Par conséquent, les utilisateurs qui se sont précédemment connectés continuent d’avoir accès au bureau avec le nom d’utilisateur et le mot de passe mis en cache. Cependant, comme l’appareil est supprimé ou désactivé, les utilisateurs ne peuvent pas accéder aux ressources protégées par l’accès conditionnel basé sur l’appareil. 

Les utilisateurs qui ne se sont pas connectés précédemment ne peuvent pas accéder à l’appareil. La mise en cache du nom d’utilisateur et du mot de passe n’est pas activée pour eux. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>Q : Des utilisateurs désactivés ou supprimés peuvent-ils se connecter à des appareils joints à Azure AD ?

**R :** Oui, mais uniquement pour une durée limitée. Quand un utilisateur est supprimé ou désactivé dans Azure AD, ce fait n’est pas connu immédiatement par l’appareil Windows. Par conséquent, les utilisateurs qui se sont précédemment connectés peuvent avoir accès au bureau avec le nom d’utilisateur et le mot de passe mis en cache. 

En règle générale, il faut moins de quatre heures à l’appareil pour être au courant de l’état utilisateur. Alors, Windows bloque l’accès de ces utilisateurs au bureau. Quand l’utilisateur est supprimé ou désactivé dans Azure AD, tous ses jetons sont révoqués. Par conséquent, il ne peut pas accéder aux ressources. 

Les utilisateurs supprimés ou désactivés qui ne se sont pas connectés précédemment ne peuvent pas accéder à un appareil. La mise en cache du nom d’utilisateur et du mot de passe n’est pas activée pour eux. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>Q : Pourquoi mes utilisateurs rencontrent-ils des problèmes sur les appareils joints Azure AD après avoir modifié leur UPN ?

**R :** Actuellement, les UPN ne sont pas entièrement pris en charge sur les appareils joints Azure AD. Dès lors, leur authentification auprès d’Azure AD échoue après la modification de leur UPN. C'est la raison pour laquelle les utilisateurs rencontrent des problèmes liés à l'authentification unique et à l'accès conditionnel sur leurs appareils. Pour l'instant, les utilisateurs doivent se connecter à Windows via la vignette « Autre utilisateur » à l’aide de leur nouvel UPN pour résoudre ce problème. Nous mettons tout en œuvre pour résoudre ce problème. Cela étant, les utilisateurs qui se connectent avec Windows Hello Entreprise ne rencontrent pas ce problème. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>Q : Mes utilisateurs ne peuvent pas rechercher d’imprimantes à partir d’appareils joints à Azure AD. Comment activer l’impression à partir de ces appareils ?

**R :** Pour déployer des imprimantes pour les appareils joints à Azure AD, consultez [Déployer l’impression cloud hybride Windows Server avec l’authentification préalable](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Vous avez besoin d’un serveur Windows Server local pour déployer l’impression cloud hybride. Actuellement, le service d’impression cloud n’est pas disponible. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>Q : Comment faire pour me connecter à un appareil distant joint à Azure AD ?

**R :** Consultez [Se connecter à un PC joint à Azure Active Directory à distance](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>Q : Pourquoi mes utilisateurs voient-ils s’afficher *Vous ne pouvez pas accéder à cet emplacement à partir d’ici* ?

**R :** Avez-vous configuré certaines règles d’accès conditionnel pour exiger un état d’appareil spécifique ? Si l’appareil ne répond pas aux critères, les utilisateurs sont bloqués, et ce message s’affiche. Évaluez les règles de la stratégie d’accès conditionnel. Assurez-vous que l’appareil répond aux critères pour éviter le message.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>Q : Pourquoi certains de mes utilisateurs ne reçoivent pas d’invites pour l’authentification multifacteur Azure sur des appareils joints à Azure AD ?

**R :** Un utilisateur peut joindre ou inscrire un appareil à Azure AD à l’aide de l’authentification multifacteur. L’appareil lui-même devient alors un second facteur approuvé pour cet utilisateur. À chaque fois que le même utilisateur se connecte à l’appareil et accède à une application, Azure AD prend en compte l’appareil comme second facteur. Cela permet à cet utilisateur d’accéder en toute transparence aux applications sans invites d’authentification multifacteur supplémentaires. 

Ce comportement :

- S'applique pas aux appareils joints Azure AD et aux appareils inscrits sur Azure AD, mais pas aux appareils joints Azure AD hybrides.
- Ne s’applique pas à un autre utilisateur qui se connecte à cet appareil. Par conséquent, tous les autres utilisateurs qui accèdent à cet appareil reçoivent le défi de l’authentification multifacteur. Ensuite, ils peuvent accéder aux applications qui nécessitent l’authentification multifacteur.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>Q : Pourquoi est-ce que je reçois le message *nom d’utilisateur ou mot de passe incorrect* pour un appareil que je viens juste de joindre à Azure AD ?

**R :** Les raisons les plus courantes sont les suivantes :

- Vos informations d’identification ne sont plus valides.
- Votre ordinateur ne peut pas communiquer avec Azure Active Directory. Vérifiez les éventuels problèmes de connectivité réseau.
- Les connexions fédérées nécessitent que votre serveur de fédération prenne en charge des points de terminaison WS-Trust activés et accessibles. 
- Vous avez activé l’authentification directe. Par conséquent, votre mot de passe temporaire doit être modifié lors de la connexion.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>Q : Pourquoi la boîte de dialogue *Désolé... une erreur s’est produite !* s’affiche-t-elle lorsque j’essaye de joindre mon ordinateur à Azure AD ?

**R :** Cette erreur résulte de la configuration de l’inscription Azure Active Directory avec Intune. Assurez-vous que l’utilisateur qui tente de créer la jointure Azure AD dispose de la licence Intune appropriée. Pour plus d’informations, consultez [Configurer l’inscription des appareils Windows](https://docs.microsoft.com/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>Q : Pourquoi ma tentative d’inscription Azure AD d’un ordinateur a-t-elle échoué alors que je n’ai reçu aucune information d’erreur ?

**R :** Une cause possible est que vous vous êtes connecté à l’appareil à l’aide du compte administrateur local intégré. Créez un compte local distinct avant d’utiliser la jonction Azure Active Directory pour terminer la configuration. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Q : Que sont les certificats MS-Organization-P2P-Access présents sur nos appareils Windows 10 ?

**R :** Les certificats MS-Organization-P2P-Access sont émis par Azure AD pour les appareils joints à Azure AD et les appareils joints à Azure AD hybrides. Ces certificats sont utilisés pour activer l’approbation entre les appareils dans le même locataire pour les scénarios de bureau à distance. Un certificat est émis pour l’appareil et un autre est émis pour l’utilisateur. Le certificat de l’appareil est présent dans `Local Computer\Personal\Certificates` et est valide pendant une journée. Ce certificat est renouvelé (par l’émission d’un nouveau certificat) si l’appareil est toujours actif dans Azure AD. Le certificat utilisateur est présent dans `Current User\Personal\Certificates` et ce certificat est également valide pendant une journée, mais il est émis à la demande lorsqu’un utilisateur tente d’établir une session Bureau à distance vers un autre appareil joint à Azure AD. En cas d’expiration, il n’est pas renouvelé. Ces deux certificats sont émis à l’aide du certificat MS-Organization-P2P-Access présent dans `Local Computer\AAD Token Issuer\Certificates`. Ce certificat est émis par Azure AD lors de l’inscription de l’appareil. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q : Pourquoi plusieurs certificats expirés émis par MS-Organization-P2P-Access s’affichent-ils sur nos appareils Windows 10 ? Comment puis-je les supprimer ?

**R :** Un problème a été identifié sur Windows 10 version 1709 et versions antérieures où les certificats MS-Organization-P2P-Access expirés continuent d’exister sur le magasin de l’ordinateur en raison de problèmes de chiffrement. Vos utilisateurs peuvent rencontrer des problèmes de connectivité réseau, si vous utilisez des clients VPN (par exemple, Cisco AnyConnect) qui ne peuvent pas gérer le grand nombre de certificats expirés. Ce problème a été résolu dans Windows 10 version 1803 pour supprimer automatiquement les certificats MS-Organization-P2P-Access expirés. Vous pouvez résoudre ce problème en mettant à jour vos appareils vers Windows 10 1803. Si vous ne parvenez pas à mettre à jour, vous pouvez supprimer ces certificats sans causer d’impact négatif.  

---

## <a name="hybrid-azure-ad-join-faq"></a>FAQ sur les jonctions Azure AD Hybride

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>Q : Où puis-je trouver des informations de résolution des problèmes concernant le diagnostic d’échecs de jonctions Azure AD ?

**R :** Pour obtenir des informations de résolution des problèmes, consultez ces articles :

- [Résolution des problèmes des appareils hybrides Windows 10 et Windows Server 2016 joints à Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)
- [Dépanner des appareils hybrides de bas niveau joints à Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>Q : Pourquoi je vois un enregistrement inscrit en double sur Azure AD pour mon appareil Windows 10 joint à Azure AD Hybride dans la liste des appareils Azure AD ?

**R :** Lorsque vos utilisateurs ajoutent leur compte aux applications sur un appareil joint à un domaine, ils peuvent être invités à **Ajouter un compte à Windows ?** Si l’utilisateur a entré **Oui** à l’invite, l’appareil est inscrit auprès d’Azure AD. Le type d’approbation est marqué comme étant inscrit à Azure AD. Dès lors que vous activez une jonction Azure AD hybride dans votre organisation, l’appareil est également joint à Azure AD hybride. Ensuite, deux états s’affichent pour le même appareil. 

La jonction Azure AD Hybride est prioritaire sur l’état inscrit auprès d’Azure AD. Ainsi, votre appareil est considéré comme jonction Azure AD hybride pour toute authentification et pour toute évaluation de l’accès conditionnel. Vous pouvez supprimer sans problème l’enregistrement d’appareil inscrit à Azure AD depuis le portail Azure AD. Apprenez à [éviter ou nettoyer ce double état sur l’ordinateur Windows 10](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>Q : Pourquoi mes utilisateurs rencontrent-ils des problèmes sur les appareils joints Windows 10 Azure AD Hybride après avoir modifié leur UPN ?

**R :** Actuellement, les UPN ne sont pas entièrement pris en charge sur les appareils joints Azure AD hybrides. Les utilisateurs peuvent se connecter à l’appareil et accéder à leurs applications locales, mais l’authentification auprès d’Azure AD échoue après la modification d'un UPN. C'est la raison pour laquelle les utilisateurs rencontrent des problèmes liés à l'authentification unique et à l'accès conditionnel sur leurs appareils. Pour l’instant, vous devez déconnecter l’appareil d’Azure AD (exécutez « dsregcmd /leave » avec des privilèges élevés) et le reconnecter (ce qui s’effectue automatiquement) pour résoudre le problème. Nous mettons tout en œuvre pour résoudre ce problème. Cela étant, les utilisateurs qui se connectent avec Windows Hello Entreprise ne rencontrent pas ce problème. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>Q : Des appareils joints Azure AD hybrides Windows 10 nécessitent-ils une visibilité du contrôleur de domaine pour accéder aux ressources cloud ?

**R :** Non, sauf en cas de modification du mot de passe. Après qu’une Jointure Azure AD hybride Windows 10 hybride a été établie, et que l’utilisateur s’est connecté au moins une fois, l’appareil ne nécessite pas de visibilité du contrôleur de domaine pour accéder aux ressources cloud. Windows 10 peut obtenir une authentification unique pour des applications Azure AD en tout lieu avec une connexion internet, sauf lorsque le mot de passe est modifié. Les utilisateurs qui se connectent avec Windows Hello Entreprise pour obtenir une authentification unique se connectent à des applications Azure AD, même après un changement de mot de passe, même s’ils ne disposent pas d’une visibilité de leur contrôleur de domaine. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>Q : Que se passe-t-il si un utilisateur modifie son mot de passe et tente de se connecter à son appareil Windows 10 joint à une version hybride d’Azure AD en dehors du réseau d’entreprise ?

**R :** Si un mot de passe est modifié en dehors du réseau d’entreprise (par exemple, à l’aide d’Azure AD SSPR), la connexion de l’utilisateur avec le nouveau mot de passe échoue. Pour des appareils joints à une version hybride d’Azure AD, l’Active Directory local est l’autorité principale. Quand un appareil est hors de vue du contrôleur de domaine, il ne peut pas valider le nouveau mot de passe. Par conséquent, l’utilisateur doit établir une connexion avec le contrôleur de domaine (via un réseau privé virtuel ou à partir du réseau d’entreprise) avant de pouvoir se connecter à l’appareil avec son nouveau mot de passe. Autrement, il peut uniquement se connecter avec son ancien mot de passe en raison de la fonctionnalité de connexion mise en cache dans Windows. Cependant, l’ancien mot de passe est invalidé par Azure AD lors des demandes de jeton, ce qui empêche l’authentification unique et fait échouer les stratégies d’accès conditionnel basé sur l’appareil. Ce problème ne se produit pas si vous utilisez Windows Hello Entreprise. 

---

## <a name="azure-ad-register-faq"></a>FAQ sur les inscriptions Azure AD

### <a name="q-can-i-register-android-or-ios-byod-devices"></a>Q : Puis-je inscrire des appareils BYOD Android ou iOS ?

**R :** Oui, mais seulement avec le service d’inscription d’appareils d’Azure et seulement pour les clients hybrides. Cela n’est pas pris en charge avec le service DRS sur site dans Active Directory Federation Services (AD FS).

### <a name="q-how-can-i-register-a-macos-device"></a>Q : Comment puis-je inscrire un appareil macOS ?

**R :** Procédez comme suit :

1.  [Créez une stratégie de conformité](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
1.  [Définissez une stratégie d’accès conditionnel pour les appareils macOS](../active-directory-conditional-access-azure-portal.md) 

**Remarques :**

- Les utilisateurs qui sont inclus dans votre stratégie d’accès conditionnel ont besoin d’une [version d’Office pour macOS prise en charge](../conditional-access/technical-reference.md#client-apps-condition) pour accéder aux ressources. 
- Lors de la première tentative d’accès, vos utilisateurs sont invités à inscrire l’appareil par l’intermédiaire du portail d’entreprise.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [appareils inscrits Azure AD](concept-azure-ad-register.md)
- En savoir plus sur les [appareils joints à Azure AD](concept-azure-ad-join.md)
- En savoir plus sur les [appareils hybrides joints à Azure AD](concept-azure-ad-join-hybrid.md)
