---
title: Autoriser les utilisateurs pour Ambari Views - Azure HDInsight
description: Guide pratique pour gérer les autorisations des utilisateurs et des groupes pour les clusters HDInsight avec le Pack Sécurité Entreprise activé.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/26/2017
ms.author: hrasheed
ms.openlocfilehash: 28f30270ab0a6c057ee583ccebc2a8540980c6cc
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442187"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Autoriser les utilisateurs à accéder à Apache Ambari Views

Les [clusters HDInsight avec le Pack Sécurité Entreprise (PSE) activé](./domain-joined/hdinsight-security-overview.md) offrent des fonctionnalités d’entreprise, notamment l’authentification par le biais d’Azure Active Directory. Vous pouvez synchroniser [de nouveaux utilisateurs](hdinsight-sync-aad-users-to-cluster.md) ajoutés aux groupes Azure AD qui ont accès au cluster, permettant ainsi à ces utilisateurs d’effectuer certaines actions. L’utilisation d’utilisateurs, de groupes et d’autorisations dans [Apache Ambari](https://ambari.apache.org/) est prise en charge à la fois pour les clusters HDInsight Pack Sécurité Entreprise et pour les clusters HDInsight standard.

Les utilisateurs Active Directory peuvent se connecter aux nœuds de cluster à l’aide de leurs informations d’identification de domaine. Ils ont également la possibilité d’utiliser ces identifiants pour authentifier les interactions du cluster auprès d’autres points de terminaison approuvés, comme [Hue](https://gethue.com/), Ambari Views, ODBC, JDBC, PowerShell et les API REST.

> [!WARNING]  
> Ne modifiez pas le mot de passe pour l’agent de surveillance Ambari (hdinsightwatchdog) sur votre cluster HDInsight basé sur Linux. La modification du mot de passe élimine la possibilité d’utiliser les actions de script ou d’effectuer des opérations de mise à l’échelle sur votre cluster.

Si ce n’est déjà fait, suivez [ces instructions](./domain-joined/apache-domain-joined-configure.md) pour provisionner un nouveau cluster PSE.

## <a name="access-the-ambari-management-page"></a>Accéder à la page de gestion Ambari

Pour ouvrir la **page de gestion Ambari** sur [l’interface utilisateur web d’Apache Ambari](hdinsight-hadoop-manage-ambari.md), accédez à **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** . Entrez le nom d’utilisateur de l’administrateur du cluster et le mot de passe que vous avez défini lors de la création du cluster. Ensuite, sur le tableau de bord Ambari, sélectionnez **Gérer Ambari** sous le menu **admin** :

![Gérer Ambari](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-apache-hive-views"></a>Accorder des autorisations sur les affichages Apache Hive

Ambari s’accompagne d’instances d’affichages pour [Apache Hive](https://hive.apache.org/) et [Apache Tez](https://tez.apache.org/), entre autres. Pour accorder l’accès à une ou plusieurs instances d’affichages Hive, accédez à la **page de gestion Ambari**.

1. Sur la page de gestion, sélectionnez le lien **Affichages** sous le titre de menu **Affichages**, à gauche.

    ![Lien Affichages](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. Sur la page Affichages, développez la ligne **HIVE**. Par défaut, un affichage Hive est créé lorsque lors de l’ajout au cluster du service Hive. Vous pouvez également créer d’autres instances d’affichages Hive selon vos besoins. Sélectionnez un affichage Hive :

    ![Affichages : affichage Hive](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. Allez en bas de la page Affichage. Sous la section *Autorisations*, vous avez deux possibilités pour accorder des autorisations sur l’affichage à des utilisateurs de domaine :

**Accorder une autorisation à ces utilisateurs** ![Accorder une autorisation à ces utilisateurs](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png)

**Accorder une autorisation à ces groupes** ![Accorder une autorisation à ces groupes](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png)

1. Pour ajouter un utilisateur, appuyez sur le bouton **Ajouter un utilisateur**.

   * Commencez à taper le nom de l’utilisateur : une liste déroulante de noms déjà définis apparaît.

     ![Remplissage automatique des utilisateurs](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

   * Sélectionnez ou terminez de taper le nom d’utilisateur. Pour ajouter ce nom d’utilisateur comme nouvel utilisateur, appuyez sur le bouton **Nouveau**.

   * Pour enregistrer vos modifications, cochez la **case bleue**.

     ![Utilisateur entré](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

1. Pour ajouter un groupe, appuyez sur le bouton **Ajouter un groupe**.

   * Commencez à taper le nom du groupe. Le processus de sélection d’un nom de groupe existant ou d’ajout d’un nouveau groupe est le même que pour les utilisateurs.
   * Pour enregistrer vos modifications, cochez la **case bleue**.

     ![Groupe entré](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

Il est utile d’ajouter directement des utilisateurs à un affichage afin de pouvoir affecter à un utilisateur l’autorisation de l’utiliser sans qu’il soit un membre d’un groupe disposant d’autorisations supplémentaires. Pour réduire la quantité de traitement administratif, il est parfois plus simple d’affecter des autorisations à des groupes.

## <a name="grant-permissions-to-apache-tez-views"></a>Accorder des autorisations sur les affichages Apache Tez

Les instances d’affichages [Apache Tez](https://tez.apache.org/) autorisent les utilisateurs à surveiller et à déboguer toutes les tâches Tez, soumises par des requêtes [Apache Hive](https://hive.apache.org/) et des scripts [Apache Pig](https://pig.apache.org/). Par défaut, une instance d’affichage Tez est créée lors de la configuration du cluster.

Pour affecter des utilisateurs et des groupes à une instance d’affichage Tez, développez la ligne **TEZ** sur la page Affichages, comme nous l’avons décrit précédemment.

![Affichages - Affichage Tez](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

Pour ajouter des utilisateurs ou des groupes, répétez les étapes 3, 4 et 5 de la section précédente.

## <a name="assign-users-to-roles"></a>Affecter des utilisateurs aux rôles

Il existe cinq rôles de sécurité pour les utilisateurs et les groupes, listés par ordre décroissant d’autorisations d’accès :

* Administrateur de cluster
* Opérateur de cluster
* Administrateur de services
* Opérateur de service
* Utilisateur de cluster

Pour gérer les rôles, accédez à la **page de gestion Ambari**, puis sélectionnez le lien **Rôles** dans le groupe de menus *Clusters*, à gauche.

![Lien Rôles du menu](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

Pour afficher la liste des autorisations accordées à chaque rôle, cliquez sur le point d’interrogation bleu près de l’en-tête du tableau **Rôles**, sur la page Rôles.

![Lien Rôles du menu](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

Cette page présente deux affichages différents qui permettent de gérer les rôles des utilisateurs et des groupes : le mode Bloc et le mode Liste.

### <a name="block-view"></a>Mode Bloc

Le mode Bloc affiche chaque rôle dans sa propre ligne, et propose les options **Affecter des rôles à ces utilisateurs** et **Affecter des rôles à ces groupes** décrites précédemment.

![Mode Bloc Rôles](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>Mode Liste

Le mode Liste propose des fonctionnalités de modification rapide dans deux catégories : les utilisateurs et les groupes.

* La catégorie Utilisateurs du mode Liste affiche la liste de tous les utilisateurs, ce qui permet de sélectionner un rôle pour chaque utilisateur dans la liste déroulante.

    ![Mode Liste Rôles - utilisateurs](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  La catégorie Groupes du mode Liste affiche tous les groupes, ainsi que le rôle affecté à chacun. Dans notre exemple, la liste des groupes est synchronisée à partir des groupes Azure Active Directory spécifiés dans la propriété **Accéder au groupe d’utilisateurs** des paramètres de domaine du cluster. Consultez [Créer un cluster HDInsight avec le Pack Sécurité Entreprise activé](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp).

    ![Mode Liste Rôles - groupes](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    Dans l’image ci-dessus, le groupe « hiveusers » a le rôle *Utilisateur du cluster*. Il s’agit d’un rôle en lecture seule qui permet aux utilisateurs de ce groupe d’afficher, mais non de modifier, les configurations de service et les mesures du cluster.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Ouvrir une session Ambari en tant qu’utilisateur avec accès en lecture seule

Nous avons affecté à notre utilisateur de domaine Azure AD « hiveuser1 » des autorisations sur les affichages Hive et Tez. Si l’on lance l’interface utilisateur Web d’Ambari et que l’on entre les informations d’identification de domaine de cet utilisateur (nom d’utilisateur Azure AD au format e-mail et mot de passe), il est redirigé vers la page Ambari Views. À ce stade, l’utilisateur peut sélectionner l’affichage de son choix, à condition qu’il soit accessible. Il ne peut pas visiter les autres parties du site, notamment le tableau de bord, les services, les hôtes, les alertes les pages d’administration.

![Utilisateur avec accès en lecture seule](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Ouvrir une session Ambari en tant qu’utilisateur du cluster

Nous avons affecté le rôle *Utilisateur du cluster* à notre utilisateur de domaine Azure AD « hiveuser2 ». Ce rôle peut accéder au tableau de bord et à tous les éléments de menu. Un utilisateur du cluster a moins d’options autorisées qu’un administrateur. Par exemple, hiveuser2 peut afficher les configurations de chacun des services, mais il ne peut pas les modifier.

![Utilisateur avec le rôle d’utilisateur du cluster](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Étapes suivantes

* [Configurer des stratégies Apache Hive dans HDInsight avec le Pack Sécurité Entreprise](./domain-joined/apache-domain-joined-run-hive.md)
* [Gérer des clusters HDInsight avec le Pack Sécurité Entreprise activé](./domain-joined/apache-domain-joined-manage.md)
* [Utiliser l’affichage Apache Hive avec Apache Hadoop dans HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Synchroniser les utilisateurs Azure AD sur le cluster](hdinsight-sync-aad-users-to-cluster.md)
