---
title: Comment gérer un compte Place de marché commerciale dans l’Espace partenaires
description: Découvrez comment gérer un compte Place de marché commerciale dans l’Espace partenaires.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: e0c9f1fcf2d9d7e806645f1bdf9e8a6c74245a60
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619329"
---
# <a name="how-to-manage-your-commercial-marketplace-account-in-partner-center"></a>Comment gérer votre compte Place de marché commerciale dans l’Espace partenaires 

Une fois que vous avez [créé un compte Espace partenaires](./create-account.md), vous pouvez gérer votre compte et les offres à l’aide du [tableau de bord de la Place de marché commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

Dans cet article, nous aborderons comment gérer votre compte Espace partenaires, notamment comment : 

- [Accéder à vos paramètres de compte Espace partenaires](#access-your-account-settings)
- [Trouver vos ID d’éditeur, ID de vendeur, ID utilisateur et locataires Azure AD](#account-details)
- [Mettre à jour les informations de contact](#contact-info)
- [Gérer les détails financiers (compte de paiement, profil fiscal, état de paiement en attente)](#financial-details)
- [Configurer les GUID de suivi pour surveiller l’utilisation du client](#tracking-guids)
- [Gérer les utilisateurs](#manage-users)
- [Gérer les groupes](#manage-groups)
- [Gérer les applications Azure AD](#manage-azure-ad-applications)
- [Définir des rôles et autorisations d’utilisateur](#define-user-roles-and-permissions)
- [Gérer les locataires Azure AD (comptes professionnels)](#manage-tenants)
- [Gérer les contrats de l’Espace partenaires](#agreements)


## <a name="access-your-account-settings"></a>Accéder à vos paramètres de compte

Si vous ne l’avez pas déjà fait, vous (ou l’administrateur de votre organisation) devez accéder aux [paramètres du compte](https://partner.microsoft.com/dashboard/account/management) de votre compte Espace partenaires pour :
- vérifier l’état de la vérification du compte de votre entreprise
- confirmer vos ID de vendeur, ID MPN, ID d’éditeur et informations de contact, y compris les informations de contact du vendeur et de l’approbateur de l’entreprise
- définir les détails financiers de votre entreprise, y compris les exonérations de taxe le cas échéant
- créer des comptes d’utilisateur pour toute personne qui utilise votre compte professionnel dans l’Espace partenaires

### <a name="open-developer-settings"></a>Ouvrir les paramètres du développeur

Les paramètres de compte se trouvent dans le coin supérieur droit du [tableau de bord de la Place de marché commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace) dans l’Espace partenaires. Sélectionnez l’icône d’engrenage (dans le coin supérieur droit du tableau de bord), puis  **Paramètres du développeur**. 

![Menu Paramètres de compte de l’Espace partenaires](./media/dashboard-developer-settings.png)

Dans le menu **Paramètres de compte**, vous serez en mesure d’afficher :
- **Détails du compte** : le type de compte et l’état du compte
- **ID de l’éditeur** : vos ID d’éditeur, ID de vendeur, ID utilisateur, locataires Azure AD, etc.
- **Informations de contact** : le nom d’affichage de l’éditeur, le nom de contact du vendeur, l’adresse e-mail, le numéro de téléphone et l’adresse
- **Détails financiers** : le compte de paiement, le profil fiscal et l’état de paiement en attente
- **Appareils** : tous les appareils de tests associés à votre compte
- **GUID de suivi** : tous les GUID de suivi associés à votre compte

### <a name="account-details"></a>Détails du compte

Dans la section Détails du compte, vous pouvez afficher des informations de base, telles que votre **type de compte** (entreprise ou personnel) et l’**état de la vérification** du compte. Pendant le processus de vérification du compte, ces paramètres affichent toutes les étapes nécessaires, y compris la vérification par e-mail, la vérification de l’emploi et la vérification de l’entreprise. Vous pouvez également mettre à jour votre adresse e-mail et renvoyer la vérification si nécessaire. 

### <a name="publisher-ids"></a>ID de l’éditeur

Dans la section ID d’éditeur, vous pouvez voir votre **ID de vendeur**, votre **ID MPN**, ainsi que votre **ID d’éditeur**. Ces valeurs sont assignées par Microsoft uniquement pour identifier votre compte de développeur et ne peuvent pas être modifiées.

### <a name="contact-info"></a>Informations de contact

Dans la section Informations de contact, vous pouvez voir le **nom d’affichage de l’éditeur**, les **informations de contact du vendeur** (le nom de contact, l’adresse e-mail, le numéro de téléphone et l’adresse du vendeur de l’entreprise) et de l’**approbateur de l’entreprise** (le nom, l’e-mail et le numéro de téléphone de la personne autorisée à approuver les décisions de l’entreprise). 

### <a name="financial-details"></a>Détails financiers

Dans la section Détails financiers, vous pouvez fournir ou mettre à jour vos informations financières si vous publiez des applications payantes, des compléments ou des services. 

Si vous souhaitez uniquement répertorier des offres gratuites, vous n’avez pas besoin de configurer un compte de paiement ou de remplir des formulaires fiscaux. Si vous changez d’avis ultérieurement et que vous décidez de ne pas vendre via Microsoft, vous pourrez configurer votre compte de paiement et remplir les formulaires fiscaux à ce moment-là. 

#### <a name="payout-account"></a>Compte de paiement

Un compte de paiement est le compte bancaire auquel les recettes sont envoyées en fonction de vos ventes. Ce compte doit se trouver dans le même pays que celui dans lequel vous avez enregistré votre compte Espace partenaires.

Pour configurer votre compte de paiement, vous devez **associer votre compte Microsoft** :
1. Dans **Paramètres de compte**, sous la section **Détails financiers**, sélectionnez **Associer votre compte Microsoft**. 
2. Lorsque vous y êtes invité, connectez-vous avec votre compte Microsoft. Ce compte ne peut pas être déjà associé à un autre compte Espace partenaires. 
3. Pour terminer la configuration de votre compte de paiement, déconnectez-vous complètement de l’Espace partenaires, puis reconnectez-vous avec votre compte Microsoft (plutôt qu’avec votre compte professionnel). 

Maintenant que votre compte Microsoft est associé, pour ajouter un compte de paiement, vous devez :
- **Choisir un mode de paiement** : Compte bancaire ou PayPal
- **Ajouter des informations de paiement** : Il peut s’agir du type de compte (courant ou épargne), du nom du titulaire du compte, le numéro de compte, le numéro de routage, l’adresse de facturation, le numéro de téléphone ou l’adresse e-mail PayPal. *Pour plus d’informations sur l’utilisation de PayPal comme mode de paiement du compte, et pour savoir s’il est pris en charge dans votre région, consultez [PayPal info](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> La modification de votre compte de paiement peut retarder vos paiements jusqu’à un cycle de paiement. Ce retard se produit parce que nous devons vérifier le changement de compte, comme nous l’avons fait lorsque vous avez configuré le compte de paiement pour la première fois. Vous serez payé pour le montant total une fois que votre compte aura été vérifié. Tout paiement dû dans le cycle de paiement actuel sera ajouté au cycle suivant  

#### <a name="tax-profile"></a>Profil fiscal

Examinez l’état actuel de votre profil de taxe, et confirmez si le **type d’entité** et les **informations du certificat de taxe** qui s’affichent sont corrects. Sélectionnez **Modifier** pour mettre à jour ou complétez les formulaires requis.

Afin d’établir votre statut fiscal, vous devez spécifier votre pays de résidence et votre nationalité, et compléter les formulaires fiscaux appropriés associés à votre pays/région.

Quel que soit votre pays de résidence ou votre nationalité, vous devez remplir les formulaires fiscaux américains pour vendre des offres via Microsoft. Les partenaires qui répondent à certaines exigences en matière de résidence aux États-Unis doivent remplir un formulaire IRS W-9. Les autres partenaires en dehors des États-Unis doivent remplir un formulaire IRS W-8. Vous pouvez remplir ces formulaires en ligne lorsque vous complétez votre profil fiscal.

Aucun numéro individuel de contribuable (ITIN) n’est nécessaire pour recevoir des paiements de la part de Microsoft ou pour bénéficier des avantages découlant des conventions fiscales.

Vous pouvez compléter et soumettre vos formulaires fiscaux par voie électronique dans l’Espace partenaires. En général, vous n’avez pas besoin d’imprimer ou d’envoyer de formulaires par courrier.

Les différents pays et régions ont des exigences fiscales différentes. La quantité exacte d’impôts que vous devez payer varie selon les pays et les régions dans lesquels vous vendez vos offres. Microsoft vous verse les recettes des ventes et les taxes d’utilisation en votre nom dans certains pays. Ces pays seront listés durant le processus d’intégration de votre offre. Dans d’autres pays, en fonction de l’endroit où vous êtes inscrit, vous devrez peut-être directement reverser les ventes et les taxes d’utilisation de vos ventes à l’autorité fiscale locale. En outre, les recettes de vente que vous recevez peuvent être considérées comme du revenu imposable. Nous vous encourageons vivement à contacter l’autorité compétente de votre pays ou région qui vous aidera à déterminer les informations fiscales appropriées pour les transactions commerciales de Microsoft.

##### <a name="withholding-rates"></a>Taux de retenue d’impôt
Les informations que vous envoyez dans vos formulaires fiscaux déterminent le taux de retenue d’impôt approprié. Le taux de retenue d’impôt s’applique uniquement aux ventes que vous réalisez aux États-Unis. Les ventes réalisées dans des régions en dehors des États-Unis ne sont pas soumises à la retenue d’impôt. Le taux de retenue d’impôt peut varier, mais pour la plupart des développeurs inscrits en dehors des États-Unis, le taux par défaut est de 30 %. Vous pouvez réduire ce taux si votre pays a signé une convention fiscale avec les États-Unis.

##### <a name="tax-treaty-benefits"></a>Avantages issus des conventions fiscales
Si vous êtes situé en dehors des États-Unis, vous pourrez peut-être tirer parti des avantages liés aux conventions fiscales. Ces avantages varient d’un pays à l’autre et peuvent vous aider à réduire le taux d’imposition retenu par Microsoft. Vous pouvez demander des avantages issus des conventions fiscales en complétant la partie II du formulaire W-8BEN. Nous vous conseillons de communiquer avec les ressources appropriées dans votre pays ou région pour déterminer si ces avantages peuvent s’appliquer à vous.

[En savoir plus sur les informations fiscales pour les développeurs d’applications/jeux Windows et les éditeurs de la Place de marché Azure](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>État de paiement en attente

Par défaut, Microsoft vous envoie les paiements sur une base mensuelle. Toutefois, vous avez la possibilité de mettre vos paiements en attente, ce qui empêche l’envoi des paiements à votre compte. Si vous choisissez de mettre vos paiements en attente, nous allons continuer d’enregistrer les revenus que vous gagnez et à vous fournir les informations dans votre **Synthèse des paiements**. Toutefois, nous n’enverrons les paiements à votre compte que lorsque vous aurez retiré la mise en attente. 

Pour mettre vos paiements en attente, accédez à **Paramètres de compte**. Sous **Détails financiers**, dans la section **État de paiement en attente**, placez le curseur sur **Activé**. Vous pouvez modifier votre état de paiement en attente à tout moment, mais n’oubliez pas que votre décision aura un impact sur le paiement mensuel suivant. Par exemple, si vous souhaitez mettre les paiements d’avril en attente, veillez à définir votre état de paiement en attente sur **Activé** avant la fin du mois de mars.

Une fois que vous avez défini votre état de paiement en attente sur **Activé**, tous les paiements seront bloqués jusqu’à ce que vous fassiez glisser le curseur vers **Désactivé**. En procédant ainsi, vous serez inclus dans le prochain cycle de paiement mensuel (à condition que les seuils de paiement applicables aient été atteints). Par exemple, si vous avez mis vos paiements en attente, mais que vous souhaitez générer un paiement en juin, veillez à définir votre état de paiement en attente sur **Désactiver** avant la fin du mois de mai.

> [!NOTE]
> Votre sélection pour l’**état de paiement en attente** s’applique à **toutes** les sources de revenus payés via l’Espace partenaires Microsoft, y compris la Place de marché Azure, AppSource, Microsoft Store, la publicité, etc.). Vous ne pouvez pas sélectionner des états d’attente différents pour chaque source de revenus.

### <a name="devices"></a>Appareils

Les paramètres de gestion des périphériques s’appliquent uniquement aux publications UWP. [Plus d’informations](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info)

### <a name="tracking-guids"></a>GUID de suivi

Les identificateurs globaux uniques (GUID) sont des nombres de référence uniques (avec 32 chiffres hexadécimaux) qui peuvent être utilisés pour surveiller votre utilisation d’Azure. 

Pour créer un GUID et effectuer son suivi, vous devez utiliser un générateur GUID. L’équipe de stockage Azure a créé un [formulaire générateur de GUID](https://aka.ms/StoragePartners) qui vous enverra par e-mail un GUID au format approprié et pourra être réutilisé dans les différents systèmes de suivi.

Nous vous recommandons de créer un GUID unique pour chaque offre et canal de distribution de chaque produit. Si vous ne souhaitez pas que les rapports soient scindés, vous pouvez choisir d'utiliser un GUID unique pour les différents canaux de distribution du produit.

Si vous déployez un produit à l'aide d'un modèle et qu'il est disponible à la fois sur la Place de marché Azure et sur GitHub, vous pouvez créer et enregistrer 2 GUID distincts :

*   Produit A sur la Place de marché Azure
*   Produit A sur GitHub

Les rapports sont établis à l'aide de la valeur de partenaire (ID partenaire Microsoft) et des GUID. Vous pouvez également suivre les GUID alignés sur chaque plan au sein de l’offre à un niveau plus granulaire.

Pour plus d’informations, consultez le [FAQ Suivi de l’utilisation d’Azure des clients avec des GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).



## <a name="multi-user-account-management"></a>Gestion des comptes multi-utilisateurs

L’Espace partenaires s’appuie sur [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) pour l’accès et la gestion des comptes multi-utilisateurs. L’Azure AD de votre organisation est automatiquement associé à votre compte Espace partenaires dans le cadre du processus d’inscription. 

## <a name="manage-users"></a>Manage Users

La section **Utilisateurs** de l’Espace partenaires (sous **Paramètres de compte**) vous permet d’utiliser Azure AD pour gérer les utilisateurs, groupes et applications Azure AD qui ont accès à votre compte Espace partenaires. Notez que pour gérer les utilisateurs, vous devez être connecté avec votre [compte professionnel](./company-work-accounts.md) (le locataire Azure AD associé). Pour gérer les utilisateurs d’un compte professionnel/locataire différent, vous devez vous déconnecter et puis vous reconnecter en tant qu’utilisateur avec les autorisateurs **Manager** sur ce compte/locataire. 

Une fois connecté avec votre compte professionnel (locataire Azure AD), vous pouvez :
- [Ajouter ou supprimer des utilisateurs](#add-or-remove-users)
- [Modifier le mot de passe d’un utilisateur](#change-a-user-password)
- [Ajouter ou supprimer des groupes](#add-or-remove-users)
- [Ajouter ou supprimer des applications Azure AD](#add-new-azure-ad-applications)
- [Gérer les clés d’une application Azure AD](#manage-keys-for-an-azure-ad-application)
- [Définir des rôles et autorisations d’utilisateur](#define-user-roles-and-permissions)


N’oubliez pas que tous les utilisateurs de l’Espace partenaires (y compris les groupes et applications Azure AD) doivent posséder un compte professionnel actif dans un [locataire Azure AD](#manage-tenants) associé à votre compte Espace partenaires. 

### <a name="add-or-remove-users"></a>Ajouter ou supprimer des utilisateurs

Votre compte doit disposer d’autorisations [**de niveau Manager**](#define-user-roles-and-permissions) pour le [compte professionnel (locataire Azure AD)](./company-work-accounts.md) dans lequel vous voulez ajouter ou modifier des utilisateurs.

#### <a name="add-existing-users"></a>Ajouter des utilisateurs existants

Pour ajouter des utilisateurs qui existent déjà dans le [compte professionnel (locataire Azure AD)](./company-work-accounts.md) de votre entreprise à votre compte Espace partenaires :

1. Accédez à **Utilisateurs** (sous **Paramètres de compte**) et sélectionnez **Ajouter des utilisateurs**.
2. Sélectionnez un ou plusieurs utilisateurs dans la liste qui s’affiche. Utilisez la zone de recherche pour rechercher des utilisateurs spécifiques.
*Si vous sélectionnez plusieurs utilisateurs à ajouter à votre compte Espace partenaires, vous devez leur attribuer le même rôle ou le même ensemble d’autorisations personnalisées. Pour ajouter plusieurs utilisateurs avec des rôles/autorisations différents, répétez ces étapes pour chaque rôle ou ensemble d’autorisations personnalisées.
3.  Lorsque vous avez terminé de sélectionner les utilisateurs, cliquez sur **Ajouter les éléments sélectionnés**.
5.  Dans la section **Rôles**, spécifiez le(s) rôle(s) ou les autorisations personnalisées pour les utilisateurs sélectionnés.
6.  Sélectionnez **Enregistrer**.

#### <a name="create-new-users"></a>Créer de nouveaux utilisateurs

Pour créer de nouveaux comptes d’utilisateur, vous devez disposer d’un compte avec des autorisations d’[**Administrateur général**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). 

1. Accédez à **Utilisateurs** (sous **Paramètres de compte**), sélectionnez **Ajouter des utilisateurs**, puis **Créer de nouveaux utilisateurs**.
1. Entrez le prénom, le nom de famille et le nom d’utilisateur pour chaque nouvel utilisateur. 
1. Si vous souhaitez que le nouvel utilisateur dispose d’un compte d’administrateur général dans le répertoire de votre organisation, cochez la case intitulée **Faire de cet utilisateur un administrateur général dans Azure AD, avec un contrôle total sur toutes les ressources du répertoire**. Cela offrira à l’utilisateur un accès complet à toutes les fonctionnalités d’administration dans l’Azure AD de l’entreprise. Il pourra ajouter et gérer les utilisateurs dans le compte professionnel de votre organisation (locataire Azure AD), mais pas dans l’Espace partenaires, sauf si vous accordez au compte les autorisations/rôles appropriés. 
1. Si vous avez coché la case **Faire de cet utilisateur un administrateur général**, vous devez fournir une **adresse e-mail de récupération de mot de passe** afin que l’utilisateur puisse récupérer son mot de passe si nécessaire.
1. Dans la section **Appartenance au groupe**, sélectionnez tous les groupes auxquels vous souhaitez que le nouvel utilisateur appartienne.
1. Dans la section **Rôles**, spécifiez le(s) rôle(s) ou les autorisations personnalisées pour l’utilisateur.
1. Sélectionnez **Enregistrer**.

La création d’un utilisateur dans l’Espace partenaires entraîne également la création d’un compte pour cet utilisateur dans le compte professionnel (locataire Azure AD) auquel vous êtes connecté. Les modifications apportées à un nom d’utilisateur dans l’Espace partenaires seront également apportées dans le compte professionnel de votre organisation (locataire Azure AD).

#### <a name="invite-new-users-by-email"></a>Inviter de nouveaux utilisateurs par e-mail

Pour inviter des utilisateurs qui ne font actuellement pas partie du compte professionnel de votre entreprise (locataire Azure AD) par courrier électronique, vous devez disposer d’un compte avec des autorisations d’[**Administrateur général**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). 

1. Accédez à **Utilisateurs** (sous **Paramètres de compte**), sélectionnez **Ajouter des utilisateurs**, puis **Inviter des utilisateurs par e-mail**.
2. Entrez une ou plusieurs adresses e-mail (jusqu’à dix), séparées par des virgules ou des points-virgules.
3. Dans la section **Rôles**, spécifiez le(s) rôle(s) ou les autorisations personnalisées pour l’utilisateur.
4. Sélectionnez **Enregistrer**.

Les utilisateurs que vous avez invités recevront un e-mail d’invitation à rejoindre votre compte Espace partenaires. Un nouveau compte d’utilisateur invité est créé dans votre compte professionnel (locataire Azure AD). Chaque utilisateur devra accepter l’invitation avant de pouvoir accéder à votre compte.

Si vous avez besoin de renvoyer une invitation, accédez à la page **Utilisateurs**, recherchez l’invitation dans la liste des utilisateurs, sélectionnez l’adresse e-mail (ou le texte indiquant *Invitation en attente*). Ensuite, en bas de la page, sélectionnez **Renvoyer l’invitation**.
 

> [!NOTE]
> Si votre organisation utilise l’[intégration d’annuaire](https://go.microsoft.com/fwlink/p/?LinkID=724033) pour synchroniser le service d’annuaire local avec votre compte Azure AD, vous ne pourrez pas créer de nouveaux utilisateurs, groupes ou applications Azure AD dans l’Espace partenaires. Vous (ou un autre administrateur de l’annuaire local) devez les créer directement dans le répertoire local avant de pouvoir les afficher et les ajouter dans l’Espace partenaires.

#### <a name="remove-a-user"></a>Supprimer un utilisateur

Pour supprimer un utilisateur de votre compte professionnel (locataire Azure AD), accédez à **Utilisateurs** (sous **Paramètres de compte**), sélectionnez l’utilisateur que vous souhaitez supprimer à l’aide de la case à cocher dans la colonne de droite, puis sélectionnez **Supprimer** dans les actions disponibles. Une fenêtre contextuelle s’affiche pour confirmer que vous souhaitez supprimer le ou les utilisateurs sélectionnés.

#### <a name="change-a-user-password"></a>Modifier le mot de passe utilisateur

Si l’un de vos utilisateurs a besoin de modifier son mot de passe, il peut le faire lui-même si vous avez fourni une **adresse e-mail de récupération de mot de passe** lors de la création du compte d’utilisateur. Vous pouvez également mettre à jour le mot de passe utilisateur en suivant les étapes ci-dessous. Pour modifier un mot de passe utilisateur dans votre compte professionnel d’entreprise (locataire Azure AD), vous devez être connecté sur un compte avec des autorisations d’[**Administrateur général**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). Notez que cela modifiera le mot de passe utilisateur dans votre locataire Azure AD, ainsi que le mot de passe permettant d’accéder à l’Espace partenaires.

1.  Depuis la page **Utilisateurs** (sous **Paramètres de compte**), sélectionnez le nom du compte d’utilisateur que vous souhaitez modifier.
2.  Cliquez sur le bouton **Réinitialiser le mot de passe** en bas de la page.
3.  Une page de confirmation s’affiche indiquant les informations de connexion de l’utilisateur, y compris un mot de passe temporaire. N’oubliez pas d’imprimer ou de copier ces informations et de les fournir à l’utilisateur, comme vous ne pourrez pas accéder au mot de passe temporaire une fois que vous aurez quitté cette page.


## <a name="manage-groups"></a>Gérer les groupes

Les groupes vous permettent de contrôler ensemble plusieurs rôles d’utilisateur et autorisations.

#### <a name="add-an-existing-group"></a>Ajouter un groupe existant

Pour ajouter un groupe qui existe déjà dans le compte professionnel (locataire Azure AD) de votre entreprise à votre compte Espace partenaires : 

1.  Depuis la page **Utilisateurs** (sous **Paramètres de compte**), sélectionnez **Ajouter des groupes**.
2.  Sélectionnez un ou plusieurs groupes dans la liste qui s’affiche. Utilisez la zone de recherche pour rechercher des groupes spécifiques.
Si vous sélectionnez plusieurs groupes à ajouter à votre compte Espace partenaires, vous devez leur attribuer le même rôle ou le même ensemble d’autorisations personnalisées. Pour ajouter plusieurs groupes avec des rôles/autorisations différents, répétez ces étapes pour chaque rôle ou ensemble d’autorisations personnalisées.
3.  Lorsque vous avez terminé de sélectionner les groupes, cliquez sur **Ajouter les éléments sélectionnés**.
4.  Dans la section **Rôles**, spécifiez le(s) rôle(s) ou les autorisations personnalisées pour les groupes sélectionnés. Tous les membres du groupe pourront accéder à votre compte Espace partenaires grâce aux autorisations que vous appliquez au groupe, quels que soient les rôles et autorisations associés à leur compte individuel.
5.  Sélectionnez **Enregistrer**.

Lorsque vous ajoutez un groupe existant, chaque utilisateur membre de ce groupe peut accéder à votre compte Espace partenaires grâce aux autorisations associées au rôle du groupe.

#### <a name="add-a-new-group"></a>Ajouter un groupe

Pour ajouter un nouveau groupe à votre compte Espace partenaires : 

1.  Depuis la page **Utilisateurs** (sous **Paramètres de compte**), sélectionnez **Ajouter des groupes**.
2.  Sur la page suivante, sélectionnez **Nouveau groupe**.
3.  Entrez le nom d’affichage du nouveau groupe.
4.  Spécifiez le(s) rôle(s) ou les autorisations personnalisées pour le groupe. Tous les membres du groupe pourront accéder à votre compte Espace partenaires grâce aux autorisations que vous lui appliquez, quels que soient les rôles et autorisations associés à leur compte individuel.
5.  Sélectionnez des utilisateurs pour le nouveau groupe dans la liste qui s’affiche. Utilisez la zone de recherche pour rechercher des utilisateurs spécifiques.
6.  Une fois les utilisateurs sélectionnés, cliquez sur **Ajouter les éléments sélectionnés** pour les ajouter au nouveau groupe.
7.  Sélectionnez **Enregistrer**.

Notez que ce nouveau groupe sera créé dans le compte professionnel de votre organisation (locataire Azure AD), et pas uniquement dans votre compte Espace partenaires.

#### <a name="remove-a-group"></a>Supprimer un groupe

Pour supprimer un groupe de votre compte professionnel (locataire Azure AD), accédez à **Utilisateurs** (sous **Paramètres de compte**), sélectionnez le groupe que vous souhaitez supprimer à l’aide de la case à cocher dans la colonne de droite, puis sélectionnez **Supprimer** dans les actions disponibles. Une fenêtre contextuelle s’affiche pour confirmer que vous souhaitez supprimer le ou les groupes sélectionnés.

## <a name="manage-azure-ad-applications"></a>Gérer des applications Azure AD

Vous pouvez autoriser les applications ou services faisant partie de l’Azure AD de votre entreprise à accéder à votre compte Espace partenaires. 

#### <a name="add-existing-azure-ad-applications"></a>Ajouter une application Azure AD existante 

Pour ajouter des applications qui existent déjà dans Azure Active Directory : 

1.  Depuis la page **Utilisateurs** (sous **Paramètres de compte**), sélectionnez **Ajouter des applications Azure AD**.
2.  Sélectionnez une ou plusieurs applications Azure AD dans la liste qui s’affiche. Utilisez la zone de recherche pour rechercher des applications Azure AD spécifiques. Si vous sélectionnez plusieurs applications Azure AD à ajouter à votre compte Espace partenaires, vous devez leur attribuer le même rôle ou le même ensemble d’autorisations personnalisées. Pour ajouter plusieurs applications Azure AD avec des rôles/autorisations différents, répétez ces étapes pour chaque rôle ou ensemble d’autorisations personnalisées.
3.  Une fois les applications Azure AD sélectionnées, cliquez sur **Ajouter les éléments sélectionnés**.
5.  Dans la section **Rôles**, spécifiez le(s) rôle(s) ou les autorisations personnalisées pour les applications Azure AD sélectionnées.
6.  Sélectionnez **Enregistrer**.

#### <a name="add-new-azure-ad-applications"></a>Ajouter de nouvelles applications Azure AD 

Si vous souhaitez accorder l’accès à l’Espace partenaires à un tout nouveau compte d’application Azure AD, vous pouvez en créer un dans la section **Utilisateurs**. Notez que ce nouveau compte sera créé dans le compte professionnel de votre organisation (locataire Azure AD), et pas uniquement dans votre compte Espace partenaires. Si vous utilisez principalement cette application Azure AD pour l’authentification à l’Espace partenaires, et que vous ne souhaitez pas que les utilisateurs y accèdent directement, vous pouvez entrer une adresse valide pour l’**URL de réponse** et l’**URI ID d’application**, tant que ces valeurs ne sont pas utilisées par une autre application Azure AD de votre répertoire.

1.  Depuis la page **Utilisateurs** (sous **Paramètres de compte**), sélectionnez **Ajouter des applications Azure AD**.
2.  Sur la page suivante, sélectionnez **Nouvelle application Azure AD**.
3.  Entrez l’**URL de réponse** pour la nouvelle application Azure AD. Il s’agit de l’URL grâce à laquelle les utilisateurs peuvent se connecter et utiliser votre application Azure AD (parfois également appelée URL de l’application ou URL de connexion). L’**URL de réponse** ne peut pas dépasser 256 caractères et doit être unique dans votre répertoire.
4.  Entrez l’**URI ID d’application** pour la nouvelle application Azure AD. Il s’agit d’un identificateur logique de l’application Azure AD qui est présenté lorsqu’une demande d’authentification unique est envoyée à Azure AD. Notez que l’**URI ID d’application** doit être unique pour chaque application Azure AD de votre répertoire. Il ne doit pas comporter plus de 256 caractères. Pour plus d’informations sur l’URI ID d’application, consultez [Intégration d’applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts).
5.  Dans la section **Rôles**, spécifiez le(s) rôle(s) ou les autorisations personnalisées pour l’application Azure AD.
6.  Sélectionnez **Enregistrer**.

Une fois que vous avez ajouté ou créé une application Azure AD, vous pouvez revenir à la section **Utilisateurs** et sélectionner le nom de l’application pour passer en revue les paramètres de l’application, tels que l’ID du locataire, l’ID client, l’URL de réponse et l’URI ID d’application.

#### <a name="remove-an-application"></a>Supprimer une application

Pour supprimer une application de votre compte professionnel (locataire Azure AD), accédez à **Utilisateurs** (sous **Paramètres de compte**), sélectionnez l’application que vous souhaitez supprimer à l’aide de la case à cocher dans la colonne de droite, puis sélectionnez **Supprimer** dans les actions disponibles. Une fenêtre contextuelle s’affiche pour confirmer que vous souhaitez supprimer la ou les applications sélectionnées.

#### <a name="manage-keys-for-an-azure-ad-application"></a>Gérer les clés d’une application Azure AD

Si votre application Azure AD lit et écrit des données dans Microsoft Azure AD, elle aura besoin d’une clé. Vous pouvez créer des clés pour une application Azure AD en modifiant ses informations dans l’Espace partenaires. Vous pouvez également supprimer les clés qui ne sont plus nécessaires.

1.  Depuis la page **Utilisateurs** (sous **Paramètres de compte**), sélectionnez le nom de l’application Azure AD. Vous verrez toutes les clés actives de l’application Azure AD, y compris la date à laquelle la clé a été créée et sa date d’expiration. 
2. Pour supprimer une clé qui n’est plus nécessaire, sélectionnez **Supprimer**.
3.  Pour ajouter une nouvelle clé, sélectionnez **Ajouter une nouvelle clé**.
4.  Vous verrez un écran affichant l’**ID client** et les **valeurs de clé**. Veillez à imprimer ou copier ces informations, car vous ne pourrez plus y accéder une fois que vous aurez quitté cette page.
4.  Si vous souhaitez créer plus de clés, sélectionnez **Ajouter une autre clé**.


### <a name="define-user-roles-and-permissions"></a>Définir des rôles et autorisations d’utilisateur

Les utilisateurs de votre entreprise peuvent obtenir les autorisations et rôles suivants dans le programme de la Place de marché commerciale dans l’Espace partenaires. 

Notez que les rôles de locataire Azure Active Directory (AAD) incluent l’administrateur général, l’administrateur d’utilisateurs et les rôles CSP. Les rôles non AAD sont les rôles qui ne gèrent pas le locataire, tels que l’administrateur MPN, l’administrateur de profil métier, l’administrateur de référence, l’administrateur des offres incitatives et l’utilisateur incitatif.


|**Rôle**|**autorisations**|
|----------------------------------|:---------------------------------|
|Administrateur général|• Peut accéder à tous les comptes/services Microsoft avec des privilèges complets
|      |• Crée des tickets de support pour l’Espace partenaires
||• Affiche les contrats, les listes de prix et les offres
||• Affiche, crée et gère les utilisateurs partenaires|
|Manager|• Peut accéder à toutes les fonctionnalités de compte Microsoft, à l’exception des paramètres fiscaux et de paiement
|      |• Peut gérer les utilisateurs, rôles et comptes professionnels (locataires)|
|Développeur|• Peut charger des packages, soumettre des applications et des modules complémentaires, et afficher le rapport d’utilisation pour obtenir des informations sur la télémétrie
|      |• Ne peut pas accéder aux paramètres de compte ou aux informations financières|
|Contributeur commercial|• Peut accéder aux informations financières et définir les détails de la tarification
|      |• Ne peut pas créer ou soumettre de nouveaux modules complémentaires ou applications|
|Contributeur financier|• Affiche les rapports de paiement
|      |• Ne peut pas apporter de modifications aux applications ou aux paramètres|
|Distributeur|• Peut répondre aux avis des clients et aux rapports non financiers
|      |• Ne peut pas apporter de modifications aux applications ou aux paramètres|

Pour plus d’informations sur la gestion des rôles et des autorisations dans d’autres domaines de l’Espace partenaires, tels que Azure Active Directory (AD), le fournisseur de solutions Cloud (CSP), le fournisseur de panneau de configuration (CPV), les utilisateurs invités ou Microsoft Partner Network (MPN), consultez [Attribuer des rôles et autorisations d’utilisateur dans l’Espace partenaires](https://docs.microsoft.com/partner-center/permissions-overview).


## <a name="manage-tenants"></a>Gérer les locataires

Un locataire Azure Active Directory (AD), également appelé « compte professionnel » dans cette documentation, est une représentation de la configuration de votre organisation dans le Portail Azure et vous aide à gérer une instance spécifique de services de cloud Microsoft pour les utilisateurs internes et externes. Si votre organisation est abonnée à un service de cloud Microsoft, tel que Azure, Microsoft Intune ou Office 365, un locataire Azure AD a été établi pour vous. 

Vous pouvez configurer plusieurs locataires à utiliser avec l’Espace partenaires. Les utilisateurs disposant du rôle **Manager** dans le compte de l’Espace partenaires peuvent ajouter et supprimer des locataires Azure AD du compte.  

### <a name="add-an-existing-tenant"></a>Ajouter un locataire existant

Pour associer un autre locataire Azure AD à votre compte Espace partenaires :

1.  Depuis la page **Locataires** (sous **Paramètres de compte**), sélectionnez **Associer un autre locataire Azure AD**.
2. Saisissez les informations d’identification Azure AD du locataire que vous souhaitez associer.
3. Passez en revue l’organisation et le nom de domaine du locataire Azure AD. Pour compléter l’association, sélectionnez **Confirmer**.

Si l’association est réussie, vous pourrez ajouter et gérer les utilisateurs de compte dans la section **Utilisateurs** dans l’Espace partenaires.

### <a name="create-a-new-tenant"></a>Créer un nouveau locataire

Pour créer un nouveau locataire Azure AD avec votre compte Espace partenaires :

1.  Depuis la page **Locataires** (sous **Paramètres de compte**), sélectionnez **Créer un nouveau locataire Azure AD**.
2. Entrez les informations de répertoire de votre nouveau locataire Azure AD :
    - **Nom de domaine** : Le nom unique que nous allons utiliser pour le domaine Azure AD, avec « .onmicrosoft.com ». Par exemple, si vous entrez « example », votre domaine Azure AD sera « example.onmicrosoft.com ».
    - **E-mail du contact** : Une adresse e-mail à laquelle nous pouvons vous contacter sur votre compte si nécessaire.
    - **Informations du compte utilisateur de l’administrateur général** : Le prénom, nom de famille, nom d’utilisateur et mot de passe que vous souhaitez utiliser pour le nouveau compte d’administrateur général.
3. Sélectionnez **Créer** pour confirmer les nouvelles informations du compte et du domaine.
4. Connectez-vous avec vos nouveaux nom d’utilisateur d’administrateur général Azure AD et mot de passe pour commencer à [ajouter et gérer les utilisateurs](#manage-users).

Pour plus d’informations sur la création de nouveaux locataires au sein du Portail Azure, plutôt que via le portail Espace partenaires, consultez l’article [Créer un nouveau locataire dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

### <a name="remove-a-tenant"></a>Supprimer un locataire

Pour supprimer un locataire depuis votre compte Espace partenaires, recherchez son nom dans la page **Locataires** (dans **Paramètres de compte**), puis sélectionnez **Supprimer**. Un message va s’afficher pour vous demander de confirmer la suppression du locataire. Une fois cette opération effectuée, les utilisateurs de ce locataire ne pourront plus se connecter au compte de l’Espace partenaires et les autorisations que vous avez configurées pour ces utilisateurs seront supprimées.

Lorsque vous supprimez un locataire, tous les utilisateurs qui ont été ajoutés au compte Espace partenaires depuis ce locataire ne pourront plus se connecter au compte.

> [!TIP]
> Vous ne pouvez pas supprimer un locataire si vous êtes actuellement connecté à l’Espace partenaires à l’aide d’un compte de ce locataire. Pour supprimer un locataire, vous devez vous connecter à l’Espace partenaires en tant que **Manager** pour un autre locataire associé au compte. S’il n’existe qu’un seul locataire associé au compte, ce locataire peut être supprimé uniquement après vous être connecté avec le compte Microsoft qui a ouvert le compte.


## <a name="agreements"></a>Accords

La section **Contrats** de l’Espace partenaires (sous **Paramètres de compte**) vous permet d’afficher une liste des contrats de publication que vous avez autorisés. Ces contrats sont répertoriés selon le nom, le numéro de version, la date à laquelle ils ont été acceptés et le nom de l’utilisateur qui a accepté le contrat. 

Les **actions nécessaires** peuvent apparaître en haut de cette page s’il existe des mises à jour de contrat qui nécessitent votre attention. Pour accepter un contrat modifié, commencez par lire la version du contrat liée, puis sélectionnez **Accepter le contrat**. 

Pour plus d’informations sur les contrats de fournisseurs de solutions Cloud (CSP) dans l’Espace partenaires, consultez [Contrats Cloud Microsoft par région et par langue](https://docs.microsoft.com/partner-center/agreements).

## <a name="next-steps"></a>Étapes suivantes

- [Créer une nouvelle offre SaaS](./create-new-saas-offer.md)
