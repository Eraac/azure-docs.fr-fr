---
title: Migrer des machines virtuelles VMware locales vers Azure avec Azure Migrate Server Migration (migration basée sur un agent) | Microsoft Docs
description: Cet article explique comment effectuer une migration basée sur un agent de machines locales vers Azure avec Azure Migrate Server Migration
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: d222936f93f90573a46cd7f6216fbde8043332c7
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261409"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>Migrer des machines virtuelles VMware vers Azure (migration basée sur un agent)

Cet article explique comment effectuer une migration basée sur un agent de machines virtuelles VMware locales vers Azure avec l’outil Azure Migrate Server Migration.

[Azure Migrate](migrate-services-overview.md) offre un hub central pour suivre la découverte, l’évaluation et la migration vers Azure d’applications et de charges de travail locales, ainsi que d’instances de machine virtuelle AWS/GCP. Le hub fournit des outils Azure Migrate pour l’évaluation et la migration, ainsi que des offres d’éditeurs de logiciels indépendants (ISV) tiers.


Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Configurer l’environnement source et déployer une appliance de réplication Azure Migrate pour la migration basée sur un agent.
> * Configurer l’environnement cible pour la migration.
> * Configurer une stratégie de réplication.
> * Activez la réplication.
> * Exécuter un test de migration pour vérifier que tout fonctionne comme prévu.
> * Exécuter une migration complète vers Azure.

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario donné afin que vous puissiez configurer rapidement une preuve de concept. Ils utilisent les options par défaut dans la mesure du possible et n’affichent pas tous les paramètres et chemins possibles. Pour obtenir des instructions détaillées, passez en revue les procédures d’évaluation et de migration de VMware.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.

## <a name="before-you-begin"></a>Avant de commencer

Avant de migrer des machines virtuelles vers Azure, nous vous recommandons de tester l’évaluation de machines virtuelles VMware avec Azure Migrate Server Assessment. Configurez une évaluation comme suit :

1. Suivez le tutoriel pour [préparer Azure et VMware](tutorial-prepare-vmware.md) à des fins d’évaluation.
2. Suivez ensuite [ce tutoriel](tutorial-assess-vmware.md) pour configurer une appliance Azure Migrate à des fins d’évaluation, et découvrir et évaluer des machines virtuelles.


Bien qu’il soit recommandé d’exécuter une évaluation, vous n’êtes pas obligé de le faire avant de migrer des machines virtuelles.

## <a name="migration-methods"></a>Méthodes de migration

Vous pouvez migrer des machines virtuelles VMware vers Azure à l’aide de l’outil Azure Migrate Server Migration. Cet outil offre deux options pour la migration de machines virtuelles VMware :

- Réplication sans agent. Migrez des machines virtuelles sans avoir à installer quoi que ce soit.
- Migration basée sur un agent (ou réplication). Installez un agent (l’agent des services Mobilité) sur la machine virtuelle pour la réplication.

Pour savoir quelle migration effectuer, sans agent ou basée sur un agent, passez en revue les articles suivants :

- [Découvrez](server-migrate-overview.md) les options de migration VMware.
- [Passez en revue les limitations](server-migrate-overview.md#agentless-migration-limitations) de la migration sans agent.
- [Lisez cet article](tutorial-migrate-vmware.md) pour tester la migration sans agent.



## <a name="prerequisites"></a>Prérequis

Avant de commencer ce didacticiel, vous devez :

1. [Passer en revue](migrate-architecture.md) l’architecture de migration VMware.
2. Vérifier que le rôle Contributeur de machines virtuelles est attribué à votre compte Azure pour que vous soyez autorisé à effectuer les opérations suivantes :

    - Créer une machine virtuelle dans le groupe de ressources sélectionné
    - Créer une machine virtuelle dans le réseau virtuel sélectionné
    - Écrire sur un disque managé Azure. 

3. [Configurez un réseau Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Les machines locales sont répliquées sur des disques managés Azure. Quand vous basculez vers Azure pour la migration, les machines virtuelles Azure sont créées à partir de ces disques managés et jointes à un réseau Azure que vous spécifiez lors de la configuration de la migration.


## <a name="prepare-azure"></a>Préparer Azure

Si vous avez déjà exécuté une évaluation avec Azure Migrate Server Assessment, vous pouvez ignorer les instructions de cette section puisque vous avez déjà effectué les étapes nécessaires. 

Si vous n’avez pas exécuté d’évaluation, vous devez configurer les autorisations Azure avant de pouvoir migrer avec Azure Migrate Server Migration.

- **Créer un projet** : votre compte Azure doit être autorisé à créer un projet Azure Migrate. 
- **Inscrire l’appliance de réplication Azure Migrate** : l’appliance de réplication crée et inscrit une application Azure Active Directory dans votre compte Azure. Vous devez déléguer des autorisations pour cela.
- **Créer un coffre de clés** : pour migrer des machines virtuelles VMware à l’aide d’Azure Migrate Server Migration, Azure Migrate crée un coffre de clés dans le groupe de ressources afin de gérer les clés d’accès au compte de stockage de réplication dans votre abonnement. Pour créer le coffre, vous devez disposer d’autorisations d’attribution de rôle sur le groupe de ressources dans lequel réside le projet Azure Migrate. 


### <a name="assign-permissions-to-create-project"></a>Affecter des autorisations pour créer un projet

1. Dans le portail Azure, ouvrez l’abonnement, puis sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour afficher les autorisations.
3. Vous devez disposer d’autorisations **Contributeur** ou **Propriétaire**.
    - Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement.
    - Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous attribuer le rôle.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Affecter des autorisations pour inscrire l’appliance de réplication

Pour la migration basée sur un agent, déléguez des autorisations pour Azure Migrate Server Migration afin de créer et d’inscrire une application Azure AD dans votre compte. Pour affecter des autorisations, utilisez l’une des méthodes suivantes :

- Un administrateur général/locataire peut accorder des autorisations aux utilisateurs du locataire pour créer et inscrire des applications Azure AD.
- Un administrateur général/locataire peut affecter au compte le rôle Développeur d’applications (qui dispose des autorisations).

Il est intéressant de noter que :

- Les applications n’ont aucune autre autorisation d’accès sur l’abonnement que celles décrites ci-dessus.
- Vous avez uniquement besoin de ces autorisations pour inscrire une nouvelle appliance de réplication. Vous pouvez supprimer les autorisations une fois l’appliance de réplication configurée. 


#### <a name="grant-account-permissions"></a>Accorder des autorisations au compte

L’administrateur général/locataire peut accorder des autorisations comme suit :

1. Dans Azure AD, l’administrateur général/locataire doit accéder à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
2. L’administrateur doit affecter la valeur **Oui** à **Inscriptions des applications**.

    ![Autorisations Azure AD](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Il s’agit d’un paramètre par défaut qui n’est pas sensible. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

#### <a name="assign-application-developer-role"></a>Attribuer le rôle Développeur d’applications 

Un administrateur général/locataire peut attribuer à un compte le rôle Développeur d’applications. [Plus d’informations](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="assign-permissions-to-create-key-vault"></a>Affecter des autorisations pour créer un coffre de clés

Affectez des autorisations d’attribution de rôle sur le groupe de ressources dans lequel réside le projet Azure Migrate, comme ceci :

1. Dans le groupe de ressources du portail Azure, sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour afficher les autorisations. Vous avez besoin d’autorisations **Propriétaire** (ou **Contributeur** et **Administrateur de l’accès utilisateur**).
3. Si vous ne disposez pas des autorisations nécessaires, demandez-les au propriétaire du groupe de ressources. 


## <a name="prepare-on-premises-vmware"></a>Préparer des machines virtuelles VMware sur site

### <a name="prepare-an-account-for-automatic-discovery"></a>Préparer un compte pour la découverte automatique

Azure Migrate Server Migration doit accéder aux serveurs VMware pour :

- Détectez automatiquement les machines virtuelles. Au moins un compte en lecture seule est nécessaire.
- Orchestrer la réplication, le basculement et la restauration automatique. Vous avez besoin d’un compte pouvant exécuter des opérations telles que la création et la suppression de disques ou le démarrage de machines virtuelles.

Créez le compte comme suit :

1. Pour utiliser un compte dédié, créez un rôle au niveau du vCenter. Donnez un nom au rôle, tel que **Azure_Site_Recovery**.
2. Attribuez au rôle les autorisations résumées dans le tableau ci-dessous.
3. Créez un utilisateur sur le serveur vCenter ou sur l’hôte vSphere. Affectez le rôle à l’utilisateur.

#### <a name="vmware-account-permissions"></a>Autorisations du compte VMware

**Tâche** | **Rôle/Autorisations** | **Détails**
--- | --- | ---
**Détection des machines virtuelles** | Au moins un utilisateur en lecture seule<br/><br/> Objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).
**Réplication complète, basculement, restauration automatique** |  Créez un rôle (Azure_Site_Recovery) avec les autorisations nécessaires, puis attribuez le rôle à un utilisateur ou à un groupe d’utilisateurs VMware<br/><br/> Objet de centre de données -> Propager vers l’objet enfant, rôle = Azure_Site_Recovery<br/><br/> Banque de données -> Allouer de l’espace, parcourir la banque de données, opérations de fichier de bas niveau, supprimer le fichier, mettre à jour les fichiers de machine virtuelle<br/><br/> Réseau -> Attribution de réseau<br/><br/> Ressource -> Affecter les machines virtuelles au pool de ressources, migrer des machines virtuelles hors tension, migrer des machines virtuelles sous tension<br/><br/> Tâches -> Créer une tâche, Mettre à jour une tâche<br/><br/> Machine virtuelle -> Configuration<br/><br/> Machine virtuelle -> Interagir -> répondre à la question, connexion d’appareil, configurer un support de CD, configurer une disquette, mettre hors tension, mettre sous tension, installation des outils VMware<br/><br/> Machine virtuelle -> Stock -> Créer, inscrire, désinscrire<br/><br/> Machine virtuelle -> Approvisionnement -> Autoriser le téléchargement de machines virtuelles, autoriser le chargement de fichiers de machine virtuelle<br/><br/> Machine virtuelle -> Instantanés -> Supprimer les instantanés | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Préparer un compte pour l’installation du service Mobilité

Le service Mobilité doit être installé sur les machines virtuelles que vous souhaitez répliquer.

- L’appliance de réplication Azure Migrate peut effectuer une installation de type push de ce service quand vous activez la réplication pour une machine, ou vous pouvez l’installer manuellement ou à l’aide des outils d’installation.
- Dans ce tutoriel, nous allons installer le service Mobilité avec l’installation de type push.
- Pour l’installation de type push, vous devez préparer un compte dont Azure Migrate Server Migration peut se servir pour accéder à la machine virtuelle.

Préparez le compte comme suit :

1. Préparez un domaine ou un compte local avec les autorisations nécessaires pour l’installation sur la machine virtuelle.
2. Pour les machines virtuelles Windows, si vous n’utilisez pas de compte de domaine, désactivez le contrôle d’accès des utilisateurs distants sur la machine locale. Pour cela, ajoutez l’entrée DWORD **LocalAccountTokenFilterPolicy** dans le Registre, sous **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**.
3. Pour des machines virtuelles Linux, préparez un compte racine sur le serveur Linux source.


### <a name="check-vmware-requirements"></a>Vérifier les conditions requises VMware

Vérifiez que les machines virtuelles et serveurs VMware respectent les exigences de la migration vers Azure. 


> [!NOTE]
> La migration basée sur un agent avec Azure Migrate Server Migration est basée sur les fonctionnalités du service Azure Site Recovery. Certaines exigences peuvent pointer vers la documentation de Site Recovery.

1. [Vérifiez](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) les conditions requises pour les serveurs VMware.
2. [Vérifiez](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) les exigences de prise en charge des machines virtuelles pour la migration.
3. Vérifiez les paramètres des machines virtuelles. Les machines virtuelles locales que vous répliquez vers Azure doivent respecter les [exigences relatives aux machines virtuelles Azure](migrate-support-matrix-vmware.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>Ajouter l’outil Azure Migrate Server Migration

Si vous n’avez pas suivi le tutoriel d’évaluation des machines virtuelles VMware, configurez un projet Azure Migrate, puis ajoutez l’outil Azure Migrate Server Migration :

1. Dans le Portail Azure, sélectionnez **Tous les services**, puis recherchez **Azure Migrate**.
2. Sous **Services**, sélectionnez **Azure Migrate**.

    ![Configurer Azure Migrate](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. Dans **Vue d’ensemble**, cliquez sur **Évaluer et migrer des serveurs**.
4. Sous **Découvrir, évaluer et migrer des serveurs**, cliquez sur **Évaluer et migrer des serveurs**.

    ![Découvrir et évaluer des serveurs](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. Dans **Découvrir, évaluer et migrer des serveurs**, cliquez sur **Ajouter des outils**.
2. Dans **Projet de migration**, sélectionnez votre abonnement Azure, puis créez un groupe de ressources si vous n’en avez pas.
3. Dans **Détails du projet**, spécifiez le nom du projet et la géographie dans laquelle vous souhaitez créer le projet, puis cliquez sur **Suivant**.

    ![Créer un projet Azure Migrate](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    Vous pouvez créer un projet Azure Migrate dans les zones géographiques suivantes.

    **Zone géographique** | **Région**
    --- | ---
    Asie | Asie Sud-Est
    Europe | Europe Nord ou Europe Ouest
    États-Unis | USA Est ou USA Centre-Ouest

    La zone géographique spécifiée pour le projet est utilisée uniquement pour stocker les métadonnées collectées à partir des machines virtuelles locales. Vous pouvez sélectionner n’importe quelle région cible pour la migration réelle.
4. Dans **Sélectionner un outil d’évaluation**, sélectionnez **Ignorer l’ajout d’un outil d’évaluation pour l’instant** > **Suivant**.
5. Dans **Sélectionner un outil de migration**, sélectionnez **Azure Migrate : Server Migration** > **Suivant**.
6. Dans **Vérifier + ajouter des outils**, passez en revue les paramètres et cliquez sur **Ajouter des outils**.
7. L’outil ajouté apparaît alors dans le projet Azure Migrate > **Serveurs** > **Outils de migration**.

## <a name="set-up-the-replication-appliance"></a>Configurer l’appliance de réplication

La première étape de la migration consiste à configurer l’appliance de réplication. L’appliance de réplication est une machine virtuelle VMware locale unique et hautement disponible qui héberge les composants suivants :

- **Serveur de configuration** : Le serveur de configuration coordonne la communication entre les ordinateurs locaux et Azure.et gère la réplication des données.
- **Serveur de traitement**: Le serveur de processus fait office de passerelle de réplication. Il reçoit les données de réplication, les optimise grâce à une mise en cache, une compression et un chiffrement, puis les envoie à un compte de stockage de cache dans Azure. De plus, le serveur de processus installe l’agent du service Mobilité sur les machines virtuelles que vous souhaitez répliquer, puis effectue la détection automatique des machines virtuelles VMware locales.


Pour configurer l’appliance de réplication, vous téléchargez un modèle OVA (Open Virtualization Application) préparé. Vous importez ensuite le modèle dans VMware et créez la machine virtuelle de l’appliance de réplication. 

### <a name="download-the-replication-appliance-template"></a>Télécharger le modèle d’appliance de réplication

Téléchargez le modèle comme suit :

1. Dans le projet Azure Migrate, cliquez sur **Serveurs** sous **Objectifs de migration**.
2. Dans **Azure Migrate - Serveurs** > **Azure Migrate : Server Migration**, cliquez sur **Découvrir**.

    ![Détection des machines virtuelles](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , cliquez sur **Oui, avec l’hyperviseur vSphere VMware**.
4. Dans **Quel type de migration voulez-vous utiliser ?** , sélectionnez **Utilisation de la réplication basée sur un agent**.
5. Dans **Région cible**, sélectionnez la région Azure vers laquelle vous souhaitez migrer les machines.
6. Sélectionnez **Confirmer que la région cible pour la migration est « nom_région »** .
7. Cliquez sur **Créer des ressources**. Un coffre Azure Site Recovery est créé en arrière-plan.
    - Après avoir cliqué sur ce bouton, vous ne pourrez plus changer la région cible de ce projet.
    - Toutes les migrations suivantes sont effectuées dans cette région.

    ![Créer un coffre Recovery Services](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. Dans **Voulez-vous installer une nouvelle appliance de réplication ?** , sélectionnez **Installer une appliance de réplication**.
9. Cliquez sur **Télécharger** pour télécharger l’appliance de réplication. Cette action télécharge un modèle OVF que vous utilisez pour créer une machine virtuelle VMware qui exécute l’appliance.
    ![Télécharger OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Notez le nom du groupe de ressources et du coffre Recovery Services. Vous en aurez besoin lors du déploiement de l’appliance.


### <a name="import-the-template-in-vmware"></a>Importer le modèle dans VMware

Après avoir téléchargé le modèle OVF, importez-le dans VMware pour créer l’application de réplication sur une machine virtuelle VMware exécutant Windows Server 2016.

1. Connectez-vous au serveur VMware vCenter ou à l’hôte vSphere ESXi, à l’aide du client VMWare vSphere.
2. Dans le menu **Fichier**, sélectionnez **Déployer le modèle OVF** pour démarrer l’**Assistant Déploiement du modèle OVF**. 
3. Dans **Sélectionner une source**, saisissez l’emplacement du modèle OVF téléchargé.
4. Dans **Examiner les détails**, sélectionnez **Suivant**.
5. Dans **Sélectionner le nom et le dossier** et **Sélectionner la configuration**, acceptez les paramètres par défaut.
6. Dans **Sélectionner un stockage** > **Sélectionner le format de disque virtuel**, sélectionnez **Thick Provision Eager Zeroed** pour des performances optimales.
7. Dans le reste des pages de l’Assistant, acceptez les paramètres par défaut.
8. Dans **Prêt à terminer**, pour configurer la machine virtuelle avec les paramètres par défaut, sélectionnez **Mettre sous tension après le déploiement** > **Terminer**.

   > [!TIP]
   > Si vous souhaitez ajouter une carte réseau supplémentaire, décochez la case **Mettre sous tension après le déploiement** > **Terminer**. Par défaut, le modèle contient une seule carte réseau. mais vous pouvez en ajouter d’autres après le déploiement.

### <a name="kick-off-replication-appliance-setup"></a>Démarrer l’installation de l’appliance de réplication

1. À partir de la console du client vSphere de VMware, activez la machine virtuelle.
2. La machine virtuelle démarre sur une expérience d’installation de Windows Server 2016. Acceptez le contrat de licence et entrez un mot de passe administrateur.
3. Une fois l’installation terminée, connectez-vous à la machine virtuelle en tant qu’administrateur en utilisant le mot de passe d’administrateur.
4. L’outil de configuration d’appliance de réplication (outil de configuration Azure Site Recovery) démarre en quelques secondes la première fois que vous vous connectez.
5. Entrez le nom à utiliser pour inscrire l’appliance auprès d’Azure Migrate Server Migration. Cliquez ensuite sur **Suivant**.
6. L’outil vérifie que la machine virtuelle peut se connecter à Azure. Une fois la connexion établie, sélectionnez **Connecter** pour vous connecter à votre abonnement Azure.
7. Attendez que l’outil termine d’inscrire une application Azure AD pour identifier l’appliance. L’appliance redémarre.
1. Reconnectez-vous à la machine. En quelques secondes, l’Assistant Gestion de serveur de configuration démarre automatiquement.

### <a name="register-the-replication-appliance"></a>Inscrire l’appliance de réplication

Finissez l’installation et l’inscription de l’appliance de réplication.

1. Dans l’Assistant Gestion de serveur de configuration, sélectionnez **Configurer la connectivité**.
2. Sélectionnez la carte réseau (par défaut, il n’y en a qu’une) que l’appliance de réplication utilise pour la découverte des machines virtuelles et pour effectuer une installation de type push du service Mobilité sur les machines sources.
3. Sélectionnez la carte réseau que l’appliance de réplication utilise pour la connectivité avec Azure. Ensuite, sélectionnez **Enregistrer**. Vous ne pouvez pas modifier ce paramètre une fois qu’il a été configuré.
4. Si l’appliance se trouve derrière un serveur proxy, vous devez spécifier les paramètres du proxy.
    - Spécifiez le nom du proxy sous la forme **http://ip-address** ou **http://FQDN** . Les serveurs proxy HTTPS ne sont pas pris en charge.
5. Quand vous êtes invité à entrer l’abonnement, les groupes de ressources et les détails du coffre, ajoutez les détails que vous avez notés au moment du téléchargement du modèle d’appliance.
6. Dans **Installer le logiciel tiers**, acceptez le contrat de licence. Sélectionnez **Télécharger et installer** pour installer MySQL Server.
7. Sélectionnez **Installer VMware PowerCLI**. Assurez-vous que toutes les fenêtres du navigateur sont fermées avant de continuer. Sélectionnez **Continuer**.
8. Dans **Valider la configuration de l’appliance**, les conditions préalables sont vérifiées avant que vous ne poursuiviez.
9. Dans **Configurer le serveur vCenter Server/vSphere ESXi**, entrez le nom de domaine complet ou l’adresse IP du serveur vCenter ou de l’hôte vSphere, sur lequel sont situées les machines virtuelles que vous souhaitez répliquer. Entrez le numéro de port sur lequel le serveur écoute. Entrez un nom convivial à utiliser pour le serveur VMware dans le coffre.
10. Entrez les informations d’identification du compte que vous avez [créé](#prepare-an-account-for-automatic-discovery) pour la découverte VMware. Sélectionnez **Ajouter** > **Continuer**.
11. Dans **Configurer les informations d’identification de la machine virtuelle**, entrez les informations d’identification que vous avez [créées](#prepare-an-account-for-mobility-service-installation) pour l’installation de type push du service Mobilité au moment de l’activation de la réplication pour les machines virtuelles.  
    - Pour les machines Windows, le compte doit disposer des privilèges d’administrateur local sur les machines que vous souhaitez répliquer.
    - Pour Linux, fournissez les détails du compte racine.
12. Sélectionnez **Finaliser la configuration** pour terminer l’inscription.


Une fois l’appliance de réplication inscrite, Azure Migrate Server Assessment se connecte aux serveurs VMware en utilisant les paramètres spécifiés et découvre les machines virtuelles. Vous pouvez afficher les machines virtuelles découvertes dans **Gérer** > **Éléments découverts** sous l’onglet **Autre**.


## <a name="replicate-vms"></a>Répliquer des machines virtuelles

1. Dans le projet Azure Migrate > **Serveurs**, **Azure Migrate : Server Migration**, cliquez sur **Répliquer**.

    ![Répliquer des machines virtuelles](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. Dans **Répliquer** > **Paramètres de la source** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Oui, avec VMware vSphere**.
3. Dans **Appliance locale**, sélectionnez le nom de l’appliance Azure Migrate que vous avez configurée.
4. Dans **vCenter Server**, spécifiez le nom du serveur vCenter qui gère les machines virtuelles ou du serveur vSphere sur lequel les machines virtuelles sont hébergées.
5. Dans **Process Server**, sélectionnez le nom de l’appliance de réplication.
6. Dans **Informations d’identification de l’invité**, spécifiez le compte d’administrateur de machine virtuelle qui sera utilisé pour l’installation de type push du service Mobilité. Cliquez ensuite sur **Suivant : Machines virtuelles**.

    ![Répliquer des machines virtuelles](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. Dans **Machines virtuelles**, sélectionnez les machines à répliquer.

    - Si vous avez effectué une évaluation des machines virtuelles, vous pouvez appliquer les recommandations en matière de dimensionnement et de type de disque (Premium/Standard) qui apparaissent dans les résultats de l’évaluation. Pour ce faire, dans **Importer les paramètres de migration à partir d’une évaluation Azure Migrate ?** , sélectionnez l’option **Oui**.
    - Si vous n’avez pas exécuté d’évaluation ou ne souhaitez pas utiliser les paramètres de l’évaluation, sélectionnez l’option **Non**.
    - Si vous avez choisi d’utiliser l’évaluation, sélectionnez le groupe de machines virtuelles et le nom de l’évaluation.

8. Cochez chaque machine virtuelle que vous souhaitez migrer. Cliquez ensuite sur **Suivant : Paramètres de la cible**.
9. Dans **Paramètres de la cible**, sélectionnez l’abonnement et la région cible vers laquelle vous allez migrer, puis spécifiez le groupe de ressources dans lequel les machines virtuelles Azure résideront après la migration.
10. Dans **Réseau virtuel**, sélectionnez le réseau virtuel/sous-réseau Azure auquel les machines virtuelles Azure seront jointes après la migration.
11. Dans **Azure Hybrid Benefit** :

    - Sélectionnez **Non** si vous ne souhaitez pas appliquer Azure Hybrid Benefit. Cliquez ensuite sur **Suivant**.
    - Sélectionnez **Oui** si vous avez des machines Windows Server couvertes par des abonnements Software Assurance ou Windows Server actifs et que vous souhaitez appliquer l’avantage aux machines que vous migrez. Cliquez ensuite sur **Suivant**.

12. Dans **Compute**, vérifiez le nom de la machine virtuelle, sa taille, le type de disque du système d’exploitation et le groupe à haute disponibilité. La machine virtuelle doit satisfaire aux [exigences d’Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Taille de machine virtuelle** : si vous utilisez les recommandations de l’évaluation, la liste déroulante Taille de machine virtuelle contient la taille recommandée. Sinon, Azure Migrate choisit une taille qui correspond à la taille la plus proche dans l’abonnement Azure. Vous pouvez également choisir une taille manuelle dans **Taille de la machine virtuelle Azure**. 
    - **Disque de système d’exploitation** : spécifiez le disque de système d’exploitation (démarrage) pour la machine virtuelle. Le disque du système d’exploitation contient le chargeur et le programme d’installation du système d’exploitation. 
    - **Groupe à haute disponibilité** : si la machine virtuelle doit se trouver dans un groupe à haute disponibilité Azure après la migration, spécifiez-le ici. Ce groupe doit figurer dans le groupe de ressources cible que vous spécifiez pour la migration.

13. Dans **Disques**, indiquez si les disques de machine virtuelle doivent être répliqués sur Azure, puis sélectionnez le type de disque (SSD/HDD standard ou disques managés Premium) dans Azure. Cliquez ensuite sur **Suivant**.
    - Vous pouvez exclure des disques de la réplication.
    - Si vous excluez des disques, il ne seront pas présents sur la machine virtuelle Azure après la migration. 

14. Dans **Réviser + lancer la réplication**, passez en revue les paramètres, puis cliquez sur **Répliquer** pour démarrer la réplication initiale pour les serveurs.

> [!NOTE]
> Vous pouvez mettre à jour les paramètres de réplication à tout moment avant le démarrage de la réplication (**Gérer** > **Réplication des machines**). Vous ne pouvez pas modifier les paramètres après le démarrage de la réplication.




## <a name="track-and-monitor"></a>Suivre et superviser

- Quand vous cliquez sur **Répliquer**, un travail Démarrer la réplication commence. 
- Une fois ce travail terminé, les machines commencent leur réplication initiale vers Azure.
- Au terme de la réplication initiale, la réplication différentielle commence. Les modifications incrémentielles apportées aux disques locaux sont répliquées régulièrement sur les disques de réplica dans Azure.


Vous pouvez suivre l’état du travail dans les notifications du portail.

![Suivre le travail](./media/tutorial-migrate-vmware-agent/jobs.png)

Vous pouvez superviser l’état de la réplication en cliquant sur **Réplication de serveurs** dans **Azure Migrate : Server Migration**.
![Superviser la réplication](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Exécuter un test de migration


Quand la réplication différentielle commence, vous pouvez exécuter un test de migration avec les machines virtuelles avant d’exécuter une migration complète vers Azure. Avant la migration, nous vous recommandons vivement d’effectuer cette opération au moins une fois pour chaque machine.

- L’exécution d’un test de migration permet de vérifier que la migration fonctionne comme prévu, sans impact sur les machines locales qui restent opérationnelles durant la réplication. 
- Un test de migration simule la migration en créant une machine virtuelle Azure à l’aide de données répliquées (il s’agit en général d’une migration vers un réseau virtuel hors production dans votre abonnement Azure).
- Vous pouvez utiliser la machine virtuelle Azure de test répliquée pour valider la migration, tester les applications et résoudre les éventuels problèmes avant la migration complète.

Effectuez un test de migration comme suit :


1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Migration**, cliquez sur **Tester les serveurs migrés**.

     ![Tester les serveurs migrés](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Cliquez avec le bouton droit sur la machine virtuelle à tester, puis cliquez sur **Test de migration**.

    ![Tester la migration](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. Dans **Migration de test**, sélectionnez le réseau virtuel Azure dans lequel la machine virtuelle Azure se trouvera après la migration. Nous vous recommandons d’utiliser un réseau virtuel hors production.
4. Le travail **Migration de test** démarre. Supervisez le travail dans les notifications du portail.
5. Une fois la migration terminée, affichez la machine virtuelle Azure migrée dans **Machines virtuelles** dans le portail Azure. Le nom de la machine a le suffixe **-Test**.
6. Une fois le test terminé, cliquez avec le bouton droit sur la machine virtuelle Azure dans **Réplication des machines**, puis cliquez sur **Nettoyer la migration de test**.

    ![Nettoyer la migration](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Migrer des machines virtuelles

Après avoir vérifié que la migration de test fonctionne comme prévu, vous pouvez migrer des machines locales.

1. Dans le projet Azure Migrate > **Serveurs** > **Azure Migrate : Server Migration**, cliquez sur **Réplication de serveurs**.

    ![Réplication de serveurs](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. Dans **Réplication des machines**, cliquez avec le bouton droit sur la machine virtuelle > **Migrer**.
3. Dans **Migrer** > **Arrêter les machines virtuelles et effectuer une migration planifiée sans perte de données**, sélectionnez **Oui** > **OK**.
    - Par défaut, Azure Migrate arrête la machine virtuelle locale et exécute une réplication à la demande pour synchroniser toute modification apportée à la machine virtuelle depuis la dernière réplication. Cela permet d’éviter toute perte de données.
    - Si vous ne souhaitez pas arrêter la machine virtuelle, sélectionnez **Non**.
4. Un travail de migration démarre pour la machine virtuelle. Suivez le travail dans les notifications Azure.
5. Une fois le travail terminé, vous pouvez afficher et gérer la machine virtuelle à partir de la page **Machines virtuelles**.

## <a name="complete-the-migration"></a>Effectuer la migration

1. Une fois la migration terminée, cliquez avec le bouton droit sur la machine virtuelle > **Arrêter la migration**. Cette action arrête la réplication pour la machine locale et nettoie les informations d’état de la réplication pour la machine virtuelle.
2. Installez l’agent de machine virtuelle Azure pour [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sur les machines migrées.
3. Effectuez les éventuels ajustements post-migration de l’application, comme la mise à jour des chaînes de connexion de base de données et les configurations du serveur web.
4. Effectuez les tests finaux de réception de l’application et de la migration sur l’application migrée qui s’exécute maintenant dans Azure.
5. Réduisez le trafic vers l’instance de machine virtuelle Azure migrée.
6. Supprimez les machines virtuelles locales de votre inventaire des machines virtuelles locales.
7. Supprimez les machines virtuelles locales des sauvegardes locales.
8. Mettez à jour la documentation interne en y mentionnant le nouvel emplacement et la nouvelle adresse IP des machines virtuelles Azure. 

## <a name="post-migration-best-practices"></a>Bonnes pratiques après la migration

- Pour une meilleure résilience :
    - Sécurisez les données en sauvegardant les machines virtuelles Azure avec le service Sauvegarde Azure. [Plus d’informations](../backup/quick-backup-vm-portal.md)
    - Conservez les charges de travail en cours d’exécution et disponibles en continu en répliquant des machines virtuelles Azure vers une région secondaire avec Site Recovery. [Plus d’informations](../site-recovery/azure-to-azure-tutorial-enable-replication.md)
- Pour renforcer la sécurité :
    - Verrouillez et limitez l’accès du trafic entrant avec [l’administration juste-à-temps d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Limitez le trafic réseau vers les points de terminaison de gestion avec des [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Déployez [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) pour sécuriser les disques, et protégez les données contre le vol et les accès non autorisés.
    - Découvrez plus d’informations sur la [sécurisation des ressources IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) et visitez [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Pour la surveillance et la gestion :
-  Envisagez de déployer [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) pour surveiller l’utilisation et les coûts des ressources.


## <a name="next-steps"></a>Étapes suivantes

Prenez connaissance du [parcours de migration dans le cloud](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) dans le Framework d’adoption du cloud.
