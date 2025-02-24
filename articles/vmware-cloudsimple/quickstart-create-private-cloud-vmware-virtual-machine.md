---
title: Démarrage rapide - Créer une machine virtuelle VMware sur un cloud privé
description: Explique comment créer une machine virtuelle VMware VM sur un cloud privé CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 33354ce09ad6ba1a9a7c08a8cd3b945f3788011a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595679"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Créer des machines virtuelles VMware sur votre cloud privé

Pour créer des machines virtuelles sur votre cloud privé, commencez par accéder au portail CloudSimple depuis le portail Azure.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Accéder au portail CloudSimple

1. Sélectionnez **Tous les services**.
2. Recherchez **Services CloudSimple**.
3. Sélectionnez le service CloudSimple où vous voulez créer votre cloud privé.
4. Depuis la page **Vue d’ensemble**, cliquez sur **Accéder au portail CloudSimple** pour ouvrir un nouvel onglet de navigateur pour le portail CloudSimple.  Si vous y êtes invité, connectez-vous avec vos informations d’identification Azure.  

    ![Lancer le portail CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Lancer l’interface utilisateur Web vCenter

Vous pouvez maintenant lancer vCenter pour configurer des stratégies et des machines virtuelles.

Pour accéder à vCenter, démarrez depuis le portail CloudSimple. Sur la page d’accueil, sous **Tâches courantes**, cliquez sur **Lancer le client vSphere**.  Sélectionnez le cloud privé, puis cliquez sur **Lancer le client vSphere** sur le cloud privé.

   ![Lancer le client vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Charger un modèle ISO ou vSphere

> [!WARNING]
> Pour les chargements ISO, utilisez le client vSphere HTML5.  L'utilisation du client Flash peut entraîner une erreur.

1. Obtenez le modèle ISO ou vSphere que vous voulez charger sur vCenter pour créer une machine virtuelle et la rendre disponible sur votre système local.

2. Dans vCenter, cliquez sur l'icône **Disque** et sélectionnez **vsanDatastore**. Cliquez sur **Fichiers**, puis sur **Nouveau dossier**.

    ![ISO vCenter](media/vcenter-create-folder.png)

3. Créez un dossier pour y stocker les fichiers ISO.

4. Accédez nouveau dossier créé, puis cliquez sur **Charger des fichiers**. Suivez les instructions à l'écran pour télécharger le fichier ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Créer une machine virtuelle dans vCenter

1. Dans vCenter, cliquez sur l'icône **Hôtes et clusters**.

2. Cliquez avec le bouton droit de la souris sur **Charge de travail**, puis sélectionnez **Nouvelle machine virtuelle**.
    
    ![Créer une machine virtuelle](media/create-vcenter-virtual-machine-01.png)

3. Sélectionnez **Créer une machine virtuelle**, puis cliquez sur **Suivant**.

    ![Assistant de création d’une nouvelle machine virtuelle](media/create-vcenter-virtual-machine-02.png)

4. Nommez la machine, sélectionnez le dossier **Workload VM's** (Machine virtuelle de charge de travail), puis cliquez sur **Suivant**.

    ![Sélectionner le nom et le dossier](media/create-vcenter-virtual-machine-03.png)

5. Sélectionnez la ressource de calcul **Charge de travail**, puis cliquez sur **Suivant**.

    ![Sélectionner la ressource de calcul](media/create-vcenter-virtual-machine-04.png)

6. Sélectionnez **vsanDatastore**, puis cliquez sur **Suivant**.

    ![Sélectionner Stockage](media/create-vcenter-virtual-machine-05.png)

7. Conservez la sélection de compatibilité ESXi 6.5 par défaut, puis cliquez sur **Suivant**.

    ![Sélectionner la compatibilité](media/create-vcenter-virtual-machine-06.png)

8. Sélectionnez l'OS invité de l'ISO pour la machine virtuelle, puis cliquez sur **Suivant**.

    ![Personnaliser le SE invité](media/create-vcenter-virtual-machine-07.png)

9. Sélectionnez les options de disque dur et de réseau. Sélectionnez **Datastore ISO file** (Fichier ISO de magasin de données) comme nouveau lecteur CD/DVD.  Si vous voulez autoriser le trafic de l'adresse IP publique vers cette machine virtuelle, sélectionnez le réseau **vm-1**.

    ![Sélectionner la personnalisation du matériel](media/create-vcenter-virtual-machine-08.png)

10. Une fenêtre de sélection s'ouvre. Sélectionnez le fichier que vous avez chargé précédemment dans le dossier des fichiers ISO et des modèles, puis cliquez sur **OK**.

    ![Sélectionner le fichier ISO](media/create-vcenter-virtual-machine-10.png)

11. Vérifiez les paramètres, puis cliquez sur **OK** pour créer la machine virtuelle.

    ![Options de vérification](media/create-vcenter-virtual-machine-11.png)

La machine virtuelle est maintenant ajoutée aux ressources de calcul de la charge de travail et est prête à l'emploi. 

![Nouvelle machine virtuelle dans vCenter](media/create-vcenter-virtual-machine-12.png)

L'installation de base est maintenant terminée. Vous pouvez commencer à utiliser votre cloud privé de la même manière que vous utiliseriez votre infrastructure de machines virtuelles sur site.

Les sections suivantes contiennent des informations facultatives sur la configuration des serveurs DNS et DHCP pour les charges de travail du cloud privé et la modification de la configuration réseau par défaut.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Ajouter des utilisateurs et des sources d'identité à vCenter (facultatif)

CloudSimple attribue un compte utilisateur vCenter par défaut avec un nom d'utilisateur **cloudowner@cloudsimple.local** . Aucune autre configuration de compte n'est nécessaire pour commencer.  CloudSimple attribue normalement aux administrateurs les privilèges dont ils ont besoin pour effectuer des opérations normales.  Configurez votre répertoire actif sur site ou Azure AD en tant que [source d'identité supplémentaire](https://docs.azure.cloudsimple.com/set-vcenter-identity/) sur votre nuage privé.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Créer un serveur DNS et DHCP (facultatif)

Les applications et charges de travail exécutées dans un environnement de cloud privé nécessitent une résolution de nom et des services DHCP pour la recherche et l’attribution d’adresses IP. Une infrastructure DHCP et DNS appropriée est nécessaire pour fournir ces services. Vous pouvez configurer une machine virtuelle dans vCenter pour fournir ces services dans votre environnement de cloud privé.

### <a name="prerequisites"></a>Prérequis

* Un groupe de ports distribués avec un réseau VLAN configuré

* Configurer l’acheminement vers des serveurs DNS sur site ou basés sur Internet

* Modèle de machine virtuelle ou ISO pour créer une machine virtuelle

Les liens suivants fournissent des conseils sur la configuration de serveurs DHCP et DNS sur Linux et Windows.

### <a name="linux-based-dns-server-setup"></a>Configuration d’un serveur DNS basé sur Linux

Linux offre différents packages pour la configuration de serveurs DNS.  Voici un lien vers des instructions de configuration d'un serveur DNS BIND open source.

[Exemple de configuration](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>Configuration Windows

Ces articles Microsoft expliquent comment configurer un serveur Windows en tant que serveur DNS et serveur DHCP.
<br>
[Serveur Windows en tant que serveur DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Serveur Windows en tant que serveur DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Personnaliser la configuration de mise en réseau (facultatif)

Les pages Network (Réseau) du portail CloudSimple vous permettent de spécifier la configuration des tables de pare-feu et des adresses IP publiques des machines virtuelles.

### <a name="allocate-public-ips"></a>Allouer des adresses IP publiques

1. Accédez à **Network > Public IP** (Réseau > Adresse IP publique) dans le portail CloudSimple.
2. Cliquez sur **Allouer des adresses IP publiques**.
3. Entrez un nom pour identifier l’entrée de l’adresse IP.
4. Sélectionnez l’emplacement de votre cloud privé.
5. Utilisez le curseur pour modifier le délai d'inactivité si nécessaire.
6. Entrez l'adresse IP locale à laquelle vous voulez attribuer une adresse IP publique.
7. Entrez un nom DNS associé si vous le souhaitez.
8. Cliquez sur **Done**.

    ![Adresse IP publique](media/quick-create-pc-public-ip.png)

La tâche d'attribution de l'adresse IP publique commence. Vous pouvez vérifier l'état de la tâche sur la page **Activity > Tasks** (Activité > Tâches). Une fois l'attribution terminée, la nouvelle entrée s’affiche sur la page des adresses IP publiques.

La machine virtuelle à laquelle cette adresse IP doit être mappée doit être configurée avec l'adresse locale spécifiée ci-dessus. La procédure de configuration d'une adresse IP est spécifique au système d'exploitation de la machine virtuelle. Consultez la documentation du système d'exploitation de votre machine virtuelle pour connaître la procédure appropriée.

#### <a name="example"></a>Exemples

Par exemple, voici les détails pour Ubuntu 16.04.

Ajoutez la méthode statique à la configuration de la famille d'adresses inet dans le fichier ```/etc/network/interfaces```. Modifiez l’adresse, le masque de réseau et les valeurs de la passerelle. Pour cet exemple, nous utilisons l'interface eth0, l'adresse IP interne 192.168.24.10, l'adresse de passerelle 192.168.24.1 et le masque réseau 255.255.255.0. 

Modifiez le fichier ```interfaces``` .

```
sudo vi /etc/network/interfaces
```

Mettez à jour la section suivante dans le fichier ```interfaces```.

```
auto eth0
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```

Désactivez l'interface.

```
sudo ifdown eth0
```

Réactivez l’interface réseau.

```
sudo ifup eth0
```

Par défaut, tout le trafic entrant en provenance d'Internet est **refusé**. Si vous souhaitez ouvrir un autre port, créez une [table de pare-feu](https://docs.azure.cloudsimple.com/firewall/).

Après avoir configuré une adresse IP interne comme adresse IP statique, vérifiez que vous pouvez accéder à Internet depuis la machine virtuelle.

```
ping 8.8.8.8
```

Vérifiez que vous pouvez accéder à la machine virtuelle depuis Internet en utilisant l'adresse IP publique.

Assurez-vous que les règles de pare-feu (iptable) de la machine virtuelle ne bloquent pas le port 80 entrant.

```
netstat -an | grep 80
```

Démarrez un serveur HTTP qui écoute sur le port 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

or

```
python3 -m http.server 80
```

Démarrez un navigateur sur votre bureau et pointez-le sur le port 80 pour que l'adresse IP publique puisse parcourir les fichiers de votre machine virtuelle. 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Règles de pare-feu CloudSimple par défaut pour les adresses IP publiques

* Trafic VPN : Tout le trafic entre (de/vers) le VPN et tous les réseaux de charges de travail et le réseau de gestion est autorisé.
* Trafic interne dans le nuage privé : Tout le trafic est-ouest entre (de/vers) les réseaux de charges de travail et le réseau de gestion (montré ci-dessus) est autorisé.
* Trafic Internet :
    * Tout le trafic entrant en provenance d'Internet vers les réseaux de charge de travail et le réseau de gestion est refusé.
    * Tout le trafic sortant vers Internet à partir des réseaux de charges de travail ou du réseau de gestion est autorisé.

Vous pouvez également modifier la façon dont votre trafic est sécurisé à l'aide de la fonction Règles de pare-feu. Pour plus d’informations, voir [Configurer les règles et tables de pare-feu](https://docs.azure.cloudsimple.com/firewall/).

## <a name="install-solutions-optional"></a>Installer des solutions (facultatif)
Vous pouvez installer des solutions sur votre cloud privé CloudSimple pour profiter pleinement de votre environnement de cloud privé vCenter. Vous pouvez configurer des fonctions de sauvegarde, de récupération d'urgence, de réplication, entre autres, pour protéger vos machines virtuelles. VMware Site Recovery Manager (VMware SRM) et Veeam Backup & Replication en sont des exemples.

Pour installer une solution, vous devez demander des privilèges supplémentaires pour une période limitée. Consultez [Élever les privilèges](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges).

## <a name="next-steps"></a>Étapes suivantes

* [Consommer des machines virtuelles VMware sur Azure](quickstart-create-vmware-virtual-machine.md)
* [Se connecter à un réseau local à l’aide d’Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
* [Configurer des passerelles VPN sur le réseau CloudSimple](https://docs.azure.cloudsimple.com/vpn-gateway)
