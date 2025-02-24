---
title: En savoir plus à l’aide d’un bac à sable Apache Hadoop – émulateur – Azure HDInsight
description: 'Pour en savoir plus sur l’utilisation de l’écosystème Apache Hadoop, vous pouvez configurer un bac à sable (sandbox) Hadoop à partir de Hortonworks sur une machine virtuelle Azure. '
keywords: émulateur Hadoop, bac à sable (sandbox) hadoop
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 2cb99cfe765e1d3444f362e591812f5088c78c0e
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393148"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Bien démarrer avec un bac à sable Apache Hadoop, un émulateur sur un ordinateur virtuel

Apprenez à installer le bac à sable (sandbox) Apache Hadoop de Hortonworks sur une machine virtuelle pour vous familiariser avec l’écosystème Hadoop. Le bac à sable fournit un environnement de développement local pour se familiariser avec Hadoop, Hadoop Distributed File System (HDFS) et la soumission de tâches. Une fois que vous êtes familiarisé avec Hadoop, vous pouvez commencer à l’utiliser sur Azure en créant un cluster HDInsight. Pour plus d’informations sur la prise en main, consultez [Prise en main de Hadoop sur HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Conditions préalables
* [Oracle VirtualBox](https://www.virtualbox.org/) Téléchargez et installez l’application à partir d’[ici](https://www.virtualbox.org/wiki/Downloads).


## <a name="download-and-install-the-virtual-machine"></a>Téléchargement et installation de la machine virtuelle
1. Accédez à la [Cloudera télécharge](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

2. Cliquez sur **VIRTUALBOX** sous **choisir le Type Installation** pour télécharger le dernier bac à sable de Hortonworks sur une machine virtuelle. Connectez-vous ou complétez le formulaire d’intérêt de produit.

1. Cliquez sur le bouton **HDP bac à sable (dernière version)** pour commencer le téléchargement.

Pour obtenir des instructions sur la configuration du bac à sable, consultez [bac à sable Guide de déploiement et installer](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

Pour télécharger un bac à sable de version HDP plus anciens, consultez les liens situés sous **les Versions antérieures**.

## <a name="start-the-virtual-machine"></a>Démarrage de la machine virtuelle

1. Ouvrez Oracle VirtualBox pour machine virtuelle.
2. À partir du menu **Fichier**, cliquez sur **Importer l’appliance**, puis spécifiez l’image de sandbox Hortonworks.
1. Sélectionnez Hortonworks Sandbox, cliquez sur **Démarrer**, puis sur **Démarrage normal**. Une fois le processus de démarrage de la machine virtuelle terminé, celle-ci affiche les instructions de connexion.

    ![Démarrage normal](./media/apache-hadoop-emulator-get-started/normal-start.png)
2. Ouvrez un navigateur web et accédez à l’URL affichée (généralement `http://127.0.0.1:8888`).

## <a name="set-sandbox-passwords"></a>Définition de mots de passe Sandbox

1. Dans la page de **prise en main** de Hortonworks Sandbox, sélectionnez **View Advanced Options (Options d’affichage avancées)** . Utilisez les informations de cette page pour vous connecter au bac à sable avec SSH. Utilisez le nom et le mot de passe fournis.

   > [!NOTE]
   > Si vous n’avez pas de client SSH installé, vous pouvez utiliser le client SSH en ligne fourni par la machine virtuelle à l’adresse **http://localhost:4200/** .

    La première fois que vous vous connectez avec SSH, vous serez invité à modifier le mot de passe du compte racine. Entrez un nouveau mot de passe que vous utiliserez lorsque vous vous connecterez à l’aide de SSH.

2. Une fois connecté, entrez la commande suivante :

        ambari-admin-password-reset

    Lorsque vous y êtes invité, indiquez le mot de passe du compte d’administrateur Ambari. Il est utilisé pour accéder à l’interface utilisateur web d’Ambari.

## <a name="use-hive-commands"></a>Utilisation de commandes Hive

1. À partir d’une connexion SSH au bac à sable, utilisez la commande suivante pour démarrer l’interpréteur de commandes Hive :

        hive
2. Une fois l’interpréteur de commandes a démarré, procédez comme suit pour afficher les tables fournies avec le bac à sable :

        show tables;
3. Utilisez la commande suivante pour récupérer 10 lignes de la table `sample_07` :

        select * from sample_07 limit 10;

## <a name="next-steps"></a>Étapes suivantes
* [Apprendre à utiliser Visual Studio avec Hortonworks Sandbox](../hdinsight-hadoop-emulator-visual-studio.md)
* [Se familiariser avec Hortonworks Sandbox](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Didacticiel Hadoop - Prise en main de HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

