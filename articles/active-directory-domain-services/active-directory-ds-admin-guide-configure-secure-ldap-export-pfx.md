---
title: Créer un fichier .PFX avec le certificat LDAP sécurisé (LDAPS) d’un domaine Azure AD Domain Services
description: Créer un certificat LDAP sécurisé pour un domaine managé Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: d89034610eb22108efc832d32ed9d8f593e9f12a
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474392"
---
# <a name="create-a-pfx-file-with-the-secure-ldap-ldaps-certificate-for-a-managed-domain"></a>Créer un fichier .PFX avec le certificat LDAP sécurisé (LDAPS) d’un domaine managé

## <a name="before-you-begin"></a>Avant de commencer

Effectuez la [Tâche 1 : Obtenir un certificat pour le protocole LDAP sécurisé](configure-ldaps.md).

## <a name="task-2-export-the-secure-ldap-certificate-to-a-pfx-file"></a>Tâche 2 : Exporter le certificat LDAP sécurisé dans un fichier .PFX

Avant de commencer cette tâche, obtenez le certificat LDAP sécurisé auprès d’une autorité de certification publique ou créez un certificat auto-signé.

Pour exporter le certificat LDAPS vers un fichier .PFX :

1. Appuyez sur le bouton **Démarrer** et tapez **R**. Dans la boîte de dialogue **Exécuter**, tapez **mmc** et cliquez sur **OK**.

    ![Démarrer la console MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. Sur l’invite **Contrôle de compte d’utilisateur**, cliquez sur **OUI** pour démarrer la console MMC (Microsoft Management Console) en tant qu’administrateur.
3. Dans le menu **Fichier**, cliquez sur **Ajouter/Supprimer un composant logiciel enfichable**.

    ![Ajouter le composant logiciel enfichable à la console MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. Dans la boîte de dialogue **Ajouter ou supprimer des composants logiciels enfichables**, sélectionnez le composant logiciel enfichable **Certificats** et cliquez sur le bouton **Ajouter >** .

    ![Ajouter le composant logiciel enfichable Certificats à la console MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. Dans l’Assistant **Composant logiciel enfichable Certificats**, sélectionnez **Compte d’ordinateur** et cliquez sur **Suivant**.

    ![Ajouter le composant logiciel enfichable Certificats pour le compte d’ordinateur](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. Sur la page **Sélection de l’ordinateur**, sélectionnez **L’ordinateur local (l’ordinateur sur lequel cette console s’exécute)** et cliquez sur **Terminer**.

    ![Ajouter le composant logiciel enfichable Certificats - Sélection de l’ordinateur](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. Dans la boîte de dialogue **Ajouter ou supprimer des composants logiciels enfichables**, cliquez sur **OK** et ajoutez le composant logiciel enfichable Certificats dans la console MMC.

    ![Ajouter le composant logiciel enfichable Certificats à la console MMC - Terminé](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. Dans la fenêtre MMC, cliquez pour développer la **racine de la console**. Vous devriez voir le composant logiciel enfichable Certificats se charger. Cliquez sur le champ **Certificats (ordinateur local)** pour le développer. Cliquez pour développer le nœud **Personnel**, suivi par le nœud **Certificats**.

    ![Banque de certificats personnels ouverte](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. Vous devez voir s’afficher le certificat auto-signé que nous avons créé. Vous pouvez examiner les propriétés du certificat pour vérifier que l’empreinte numérique correspond à celui qu’affichaient les fenêtres PowerShell quand vous avez créé le certificat.
10. Sélectionnez le certificat auto-signé et **cliquez avec le bouton droit sur ce dernier**. Dans le menu contextuel, sélectionnez **Toutes les tâches** et sélectionnez **Exporter...** .

    ![Exportation du certificat](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. Dans **l’Assistant Exportation de certificat**, cliquez sur **Suivant**.

    ![Assistant Exportation de certificat](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. Sur la page **Exportation de la clé privée**, cliquez sur **Oui, exporter la clé privée** et cliquez sur **Suivant**.

    ![Exportation du certificat - Clé privée](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > Vous DEVEZ exporter la clé privée avec le certificat. L’activation du protocole LDAP sécurisé pour votre domaine géré échoue si vous fournissez un fichier PFX qui ne contient pas la clé privée associée au certificat.
    >
    >

13. Sur la page **Format de fichier d’exportation**, sélectionnez le format de fichier **Échange d’informations personnelles - PKCS #12 (.PFX)** pour le certificat exporté.

    ![Exportation du certificat - Format de fichier](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > Seul le format de fichier .PFX est pris en charge. N’exportez pas le certificat au format de fichier .CER.
    >
    >

14. Sur la page **Sécurité**, sélectionnez l’option **Mot de passe** et saisissez le mot de passe de protection du fichier .PFX. N’oubliez pas ce mot de passe, car il est nécessaire pour la tâche suivante. Cliquez sur **Suivant**.

    ![Exportation du certificat - Mot de passe](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > Notez ce mot de passe. Vous en aurez besoin pour activer le protocole LDAP sécurisé pour ce domaine managé dans le cadre de la [Tâche 3 : Activer le protocole LDAP sécurisé pour le domaine managé](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).
    >
    >

15. Sur la page **Fichier à exporter** , spécifiez le nom du fichier et l’emplacement d’exportation prévus pour le certificat.

    ![Exportation du certificat - Chemin d’accès](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. Sur la page suivante, cliquez sur **Terminer** pour exporter le certificat en tant que fichier PFX. Vous devez voir apparaître une boîte de dialogue de confirmation lorsque le certificat est exporté.

    ![Exportation du certificat - Terminé](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)

## <a name="next-step"></a>Étape suivante

[Tâche 3 : Activer le protocole LDAP sécurisé pour le domaine managé](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
