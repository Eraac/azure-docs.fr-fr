---
title: Utiliser le tunneling SSH pour accéder à Azure HDInsight
description: Apprenez à utiliser un tunnel SSH pour accéder de façon sécurisée à des ressources Web hébergées sur vos nœuds HDInsight sous Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: hrasheed
ms.openlocfilehash: 943bf0f4bba014c31a11bb30bf8d3b6a7c11a343
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299372"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-web-uis"></a>Utiliser le tunneling SSH pour accéder à l’interface utilisateur web d’Apache Ambari, JobHistory, NameNode, Apache Oozie et d’autres interfaces utilisateur web

Les clusters HDInsight donnent accès à l’IU web d’Apache Ambari via Internet, mais certaines fonctionnalités nécessitent un tunnel SSH. Par exemple, l’IU web pour le service Apache Oozie n’est pas accessible via Internet sans tunnel SSH.

## <a name="why-use-an-ssh-tunnel"></a>Raisons pour lesquelles utiliser un tunnel SSH

Plusieurs des menus d’Ambari fonctionnent uniquement via un tunnel SSH. Ces menus s’appuient sur des sites web et des services qui s’exécutent sur d’autres types de nœuds, tels que les nœuds Worker.

Les interfaces utilisateur web suivantes nécessitent un tunnel SSH :

* JobHistory
* NameNode
* Thread Stacks
* Interface utilisateur Web Oozie
* Interface HBase Master et interface de journaux d’activité

Si vous utilisez des actions de script pour personnaliser votre cluster, tous les services ou utilitaires que vous installez et qui exposent un service web nécessitent un tunnel SSH. Par exemple, si vous installez Hue à l'aide d'une action de script, vous devez utiliser un tunnel SSH pour accéder à l'interface utilisateur Web Hue.

> [!IMPORTANT]  
> Si vous bénéficiez d’un accès direct à HDInsight via un réseau virtuel, il est inutile d’utiliser des tunnels SSH. Pour avoir un exemple d’accès direct à HDInsight via un réseau virtuel, consultez le document [Connecter HDInsight à votre réseau local](connect-on-premises-network.md).

## <a name="what-is-an-ssh-tunnel"></a>Définition d’un tunnel SSH

Le [tunneling Secure Shell (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) connecte un port de votre ordinateur local à un nœud principal sur HDInsight. Le trafic envoyé au port local est acheminé vers le nœud principal via une connexion SSH. La requête est résolue comme si elle avait été créée sur le nœud principal. La réponse est alors acheminée via le tunnel sur votre station de travail.

## <a name="prerequisites"></a>Prérequis

* Un client SSH. Pour plus d’informations, consultez [Se connecter à HDInsight (Apache Hadoop) à l’aide de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* Un navigateur web qui peut être configuré pour utiliser un proxy SOCKS5.

    > [!WARNING]  
    > La prise en charge du proxy SOCKS intégrée dans les paramètres Internet de Windows n’inclut pas SOCKS5 et ne fonctionne pas dans le cadre des étapes décrites dans ce document. Les navigateurs suivants s’appuient sur les paramètres de proxy Windows et ne fonctionnent pas dans le cadre des étapes décrites dans ce document :
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome repose également sur les paramètres de proxy Windows. Toutefois, vous pouvez installer des extensions qui prennent en charge SOCKS5. Nous vous recommandons [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="usessh"></a>Création d’un tunnel à l'aide de la commande SSH

Utilisez la commande suivante pour créer un tunnel SSH à l'aide de la commande `ssh` . Remplacez `sshuser` par un utilisateur SSH de votre cluster HDInsight et `clustername` par le nom de votre cluster HDInsight.

```cmd
ssh -C2qTnNf -D 9876 sshuser@clustername-ssh.azurehdinsight.net
```

Cette commande va permettre de créer une connexion qui achemine le trafic vers le port local 9876 du cluster via SSH. Les options sont :

* **D 9876** : port local qui achemine le trafic via le tunnel.
* **C** : compresse toutes les données car le trafic web est principalement du texte
* **2** : force le SSH à essayer le protocole version 2 uniquement
* **q** : mode silencieux
* **T** : désactive l’allocation pseudo-tty, puisque vous transférez simplement un port.
* **n** : empêche la lecture STDIN, puisque vous transférez simplement un port.
* **N** : n’exécute pas une commande à distance, puisque vous transférez simplement un port.
* **f** : s’exécute à l’arrière-plan

Une fois la commande terminée, le trafic envoyé au port 9876 sur l’ordinateur local est acheminé au nœud principal du cluster.

## <a name="useputty"></a>Création d’un tunnel à l'aide de PuTTY

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) est un client SSH graphique pour Windows. Si vous n’êtes pas familiarisé avec PuTTY, consultez la [documentation de PuTTY ](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). Pour créer un tunnel SSH à l’aide de PuTTY, procédez comme suit :

### <a name="create-or-load-a-session"></a>Créer ou charger une session

1. Ouvrez PuTTY et vérifiez que **Session** est sélectionné dans le menu de gauche. Si vous avez déjà enregistré une session, sélectionnez le nom de la session dans la liste **Sessions enregistrées**, puis sélectionnez **Charger**.

1. Si vous ne disposez pas d’une session enregistrée, entrez vos informations de connexion :
    * **Nom d’hôte (ou adresse IP)**  : adresse SSH pour le cluster HDInsight. Par exemple : **moncluster-ssh.azurehdinsight.net**
    * **Port** : 22
    * **Type de connexion** : SSH

1. Sélectionnez **Enregistrer**.

    ![Créer une session SSH](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. Dans la rubrique **Catégorie** située à gauche dans la boîte de dialogue, développez **Connexion** et **SSH**, puis sélectionnez **Tunnels**.

1. Indiquez les informations suivantes dans le formulaire des **Options de contrôle de transfert du port SSH** .
   
   * **Port source** : le port sur le client que vous souhaitez transférer. Par exemple : **9876**.

   * **Destination** : adresse SSH pour le cluster HDInsight. Par exemple : **moncluster-ssh.azurehdinsight.net**.

   * **Dynamique** : active le routage dynamique du proxy SOCKS.
     
     ![image des options de tunneling](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

1. Sélectionnez **Ajouter** pour ajouter les paramètres, puis cliquez sur **Ouvrir** pour ouvrir une connexion SSH.

1. Lorsque vous y êtes invité, connectez-vous au serveur.

## <a name="use-the-tunnel-from-your-browser"></a>Utilisation du tunnel à partir de votre navigateur

> [!IMPORTANT]  
> Les étapes décrites dans cette section utilisent le navigateur Mozilla FireFox, car il fournit les mêmes paramètres de proxy sur toutes les plateformes. D’autres navigateurs modernes, tels que Google Chrome, peuvent nécessiter une extension du type FoxyProxy pour fonctionner avec le tunnel.

1. Configurez le navigateur pour qu’il utilise **localhost** et le port que vous avez utilisé lors de la création du tunnel en tant que proxy **SOCKS v5**. Voici comment se présentent les paramètres Firefox : si vous avez utilisé un port autre que 9876, modifiez le port par celui que vous avez utilisé :
   
    ![image des paramètres de Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]  
   > La sélection de **DNS Distant** résout les requêtes DNS à l’aide du cluster HDInsight. Ce paramètre résout les éléments DNS en utilisant le nœud principal du cluster.

2. Vérifiez que le tunnel fonctionne en vous rendant sur un site tel que [https://www.whatismyip.com/](https://www.whatismyip.com/). L’adresse IP renvoyée doit être celle utilisée par le centre de données Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Vérification avec l'interface utilisateur Web Ambari

Une fois le cluster établi, suivez ces étapes pour vérifier que vous pouvez accéder à des interfaces utilisateur Web de service à partir du Web Ambari :

1. Dans votre navigateur, accédez à `http://headnodehost:8080`. L’adresse `headnodehost` est envoyée via le tunnel au cluster et renvoie le nœud principal sur lequel Ambari s’exécute. Lorsque vous y êtes invité, saisissez le nom de l’utilisateur admin (admin) et le mot de passe correspondant à votre cluster. Vous pouvez y être invité une seconde fois par l'interface utilisateur Web Ambari. Dans ce cas, saisissez de nouveau ces informations.

   > [!NOTE]  
   > Lorsque vous utilisez l’adresse `http://headnodehost:8080` pour vous connecter au cluster, vous vous connectez via le tunnel. La communication est sécurisée grâce au tunnel SSH plutôt que via HTTPS. Pour vous connecter via Internet à l’aide de HTTPS, utilisez `https://clustername.azurehdinsight.net`, où `clustername` est le nom du cluster.

2. Dans l'interface utilisateur Web Ambari, sélectionnez HDFS dans la liste située sur la gauche de la page.

    ![Image avec HDFS sélectionné](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. Lorsque les informations de service HDFS s’affichent, sélectionnez **Liens rapides**. Une liste des nœuds principaux du cluster s’affiche. Sélectionnez l’un des nœuds principaux, puis **Interface utilisateur NameNode**.

    ![Image avec le menu Liens rapides développé](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

    > [!NOTE]  
    > Lorsque vous sélectionnez __Liens rapides__, un indicateur d’attente peut apparaître. Cela peut se produire si votre connexion internet est lente. Patientez une minute ou deux pour recevoir les données du serveur, puis réessayez.
    >
    > Certaines entrées du menu **Liens rapides** peuvent être tronquées par le côté droit de l’écran. Dans ce cas, développez le menu à l’aide de la souris et utilisez la touche fléchée droite pour faire défiler l’écran vers la droite afin d’afficher le reste du menu.

4. Une page similaire à celle ci-dessous s’affiche :

    ![Image de l’interface utilisateur NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

    > [!NOTE]  
    > Notez l’URL de cette page ; elle doit être similaire à `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`. Cette URI utilise le nom de domaine complet (FQDN) interne du nœud et est uniquement accessible en cas d’utilisation d’un tunnel SSH.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à créer et utiliser un tunnel SSH, consultez le document suivant pour découvrir d’autres façons d’utiliser Ambari :

* [Gérer des clusters HDInsight avec Apache Ambari](hdinsight-hadoop-manage-ambari.md)