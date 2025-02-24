---
title: Installer Azure IoT Edge sur Linux | Microsoft Docs
description: Instructions d’installation d’Azure IoT Edge sur Linux sur les appareils AMD64 exécutant Ubuntu
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: bbab0d8d0947c18cf8e6c178d12fdbd7b335d2b6
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485892"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Installer le runtime Azure IoT Edge sur Linux (x64)

Le runtime Azure IoT Edge est ce qui transforme un appareil en appareil IoT Edge. Le runtime peut être déployé sur un appareil de petite taille comme un Raspberry Pi ou de grande taille comme un serveur industriel. Une fois qu’un appareil est configuré avec le runtime IoT Edge, vous pouvez commencer à déployer une logique métier sur celui-ci à partir du cloud.

Pour en savoir plus, consultez [Présentation du runtime Azure IoT Edge et de son architecture](iot-edge-runtime.md).

Cet article liste les étapes pour installer le runtime Azure IoT Edge sur votre appareil Ubuntu Linux x64 (Intel/AMD) IoT Edge. Reportez-vous à [Systèmes Azure IoT Edge pris en charge](support.md#operating-systems) pour obtenir une liste des systèmes d’exploitation AMD64 pris en charge.

> [!NOTE]
> Chaque package des référentiels de logiciels Linux est soumis aux termes du contrat de licence qu’il contient (/usr/share/doc/*nom_package*). Lisez les termes du contrat de licence avant d’utiliser le package. Le fait d’installer et d’utiliser le package revient à accepter ces termes. Si vous n’acceptez pas les termes du contrat de licence, n’utilisez pas le package.

## <a name="install-the-latest-version"></a>Installer la dernière version

Utilisez les sections suivantes pour installer la version la plus récente du service Azure IoT Edge sur vos appareils. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>Inscrire la clé Microsoft et le flux des référentiels de logiciels

Préparez votre appareil pour l’installation du runtime IoT Edge.


Installez la configuration du référentiel. Choisissez le code **16.04** ou **18.04** comme approprié pour votre version d’Ubuntu :

> [!NOTE]
> Assurez-vous de choisir l’extrait de code dans la bonne boîte de code pour votre version d’Ubuntu.

* Pour **Ubuntu 16.04** :
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   ```

* Pour **Ubuntu 18.04** :
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
   ```
   
Copiez la liste générée.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Installer la clé publique Microsoft GPG

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Installer le runtime de conteneur

Azure IoT Edge s’appuie sur un runtime de conteneur [compatible avec OCI](https://www.opencontainers.org/). Pour les scénarios de production, nous vous recommandons d’utiliser le moteur [Moby](https://mobyproject.org/) fourni ci-dessous. C’est le seul moteur de conteneur officiellement pris en charge avec Azure IoT Edge. Les images conteneur Docker CE/EE sont compatibles avec le runtime Moby.

Effectuez la mise à jour d’apt.

   ```bash
   sudo apt-get update
   ```

Installez le moteur Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Installez l’interface de ligne de commande (CLI) Moby. L’interface CLI est utile pour le développement, mais facultative pour les déploiements de production.

   ```bash
   sudo apt-get install moby-cli
   ```

#### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Chargement d’une grande quantité de données à l’aide d’une requête personnalisée, en dessous avec des partitions physiques

De nombreux fabricants de périphériques embarqués livrent des images de périphériques qui contiennent des noyaux Linux personnalisés qui peuvent manquer de fonctionnalités nécessaires à la compatibilité de l’exécution des conteneurs. Si vous rencontrez des problèmes lors de l’installation du runtime conteneur [Moby](https://github.com/moby/moby) recommandé, vous pouvez dépanner votre configuration du noyau Linux en utilisant le script [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) fourni dans le dépôt officiel [Moby Github ](https://github.com/moby/moby) en exécutant les commandes suivantes sur le périphérique.

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Ceci fournira une sortie détaillée qui contient l’état des fonctionnalités du noyau utilisées par le runtime Moby. Nous vous conseillons de vérifier que tous les éléments sous `Generally Necessary` et `Network Drivers` sont activés pour vous assurer que votre noyau est entièrement compatible avec le runtime Moby.  Si vous avez identifié des fonctionnalités manquantes, vous pouvez les activer en reconstruisant votre noyau à partir des sources et en sélectionnant les modules associés à inclure dans le fichier kernel.config approprié.  De même, si vous utilisez un générateur de configuration du noyau comme defconfig ou menuconfig, vous devrez trouver et activer les fonctionnalités respectives et recompiler votre noyau en conséquence.  Une fois que vous avez déployé votre nouveau noyau modifié, exécutez à nouveau le script check-config pour vérifier que les fonctionnalités identifiées ont été activées avec succès.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Installer le démon de sécurité Azure IoT Edge

Le **démon de sécurité IoT Edge** fournit et gère les standards de sécurité sur l’appareil IoT Edge. Le démon se lance à chaque démarrage et amorce l’appareil en démarrant le reste du runtime IoT Edge.

La commande d’installation installe également la version standard du **libiothsm** si elle n’est pas déjà présente.

Effectuez la mise à jour d’apt.

   ```bash
   sudo apt-get update
   ```

Installez le démon de sécurité. Le package est installé sous `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

Une fois l’installation de IoT Edge réussie, la sortie vous invite à mettre à jour le fichier de configuration. Suivez les étapes de la section [Configurer le démon de sécurité Azure IoT Edge](#configure-the-azure-iot-edge-security-daemon) pour terminer le provisionnement de votre périphérique. 

## <a name="install-a-specific-version"></a>Installer une version spécifique

Si vous voulez installer une version spécifique d’Azure IoT Edge, vous pouvez cibler les fichiers de composants directement depuis le dépôt GitHub IoT Edge. Suivez les étapes suivantes pour obtenir tous les composants IoT Edge sur votre appareil : le moteur Moby et CLI, le libiothsm, et enfin le démon de sécurité IoT Edge.

1. Accédez aux [versions d’Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)et recherchez celle que vous souhaitez cibler. 

2. Développez la section **Ressources** pour cette version.

3. Des mises à jour du moteur Moby peuvent être disponibles dans une version donnée, ou non. Si vous voyez des fichiers qui commencent par **moby-engine** et **moby-cli**, utilisez les commandes suivantes pour mettre à jour ces composants. Si vous ne voyez aucun fichier Moby et que vous n’avez pas déjà installé Moby sur votre appareil, revenez sur les anciennes versions jusqu’à ce que vous les trouviez. 

   1. Trouvez le fichier **moby-engine** qui correspond à l’architecture de votre périphérique IoT Edge. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien.

   2. Utilisez le lien copié dans la commande suivante pour installer cette version du moteur Moby : 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Recherchez le fichier **moby-cli** qui correspond à l’architecture de votre périphérique IoT Edge. L’interface de ligne de commande Moby est un composant optionnel, mais peut être utile pendant le développement. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien. 

   4. Utilisez le lien copié dans la commande suivante pour installer cette version de l’interface de ligne de commande Moby : 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Chaque version devrait avoir de nouveaux fichiers pour le démon de sécurité IoT Edge et le hsmlib. Utilisez les commandes suivantes pour mettre à jour ces composants. 

   1. Trouvez le fichier **libiothsm-std** qui correspond à l’architecture de votre périphérique IoT Edge. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien. 

   2. Utilisez le lien copié dans la commande suivante pour installer cette version du hsmlib :

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```
   
   3. Trouvez le fichier **iotedge** qui correspond à l’architecture de votre périphérique IoT Edge. Cliquez avec le bouton droit de la souris sur le lien de fichier et copiez l’adresse du lien. 

   4. Utilisez le lien copié dans la commande suivante pour installer cette version du démon de sécurité de l’IoT Edge. 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Une fois l’installation de IoT Edge réussie, la sortie vous invite à mettre à jour le fichier de configuration. Suivez les étapes décrites dans la section suivante pour terminer l’approvisionnement de votre appareil. 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Configurer le démon de sécurité Azure IoT Edge

Configurez le runtime IoT Edge pour lier votre appareil physique à une identité d’appareil existante dans un IoT Hub Azure.

Le démon peut être configuré à l’aide du fichier de configuration situé à l’emplacement `/etc/iotedge/config.yaml`. Le fichier étant protégé en écriture par défaut, vous pouvez avoir besoin d’autorisations élevées pour le modifier.

Un appareil IoT Edge unique peut être approvisionné manuellement à l’aide d’une chaîne de connexion d’appareil fournie par IoT Hub. Vous pouvez également utiliser le service Device Provisioning pour approvisionner automatiquement des appareils. Ce service s’avère particulièrement utile lorsque vous devez approvisionner de nombreux appareils. Choisissez le script d’installation approprié selon votre choix en matière d’approvisionnement.

### <a name="option-1-manual-provisioning"></a>Option 1 : Provisionnement manuel

Pour provisionner manuellement un appareil, vous devez lui fournir une [chaîne de connexion d’appareil](how-to-register-device-portal.md) que vous pouvez créer en inscrivant un nouvel appareil dans votre hub IoT.

Ouvrez le fichier de configuration.

```bash
sudo nano /etc/iotedge/config.yaml
```

Recherchez la section de provisionnement du fichier. Supprimer les marques de commentaire du mode de provisionnement **manuel** et assurez-vous que le mode de provisionnement dps est commenté. Mettez à jour la valeur de **device_connection_string** avec la chaîne de connexion à partir de votre appareil IoT Edge.

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning:
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

Enregistrez et fermez le fichier.

   `CTRL + X`, `Y`, `Enter`

Après avoir entré les informations de provisionnement dans le fichier de configuration, redémarrez le démon :

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Option 2 : Provisionnement automatique

Pour provisionner automatiquement un appareil, [configurez le Service Device Provisioning et récupérez votre ID d’inscription d’appareil](how-to-auto-provision-simulated-device-linux.md). Le provisionnement automatique fonctionne uniquement avec des appareils qui possèdent une puce de Module de plateforme sécurisée (TPM). Par exemple, les appareils Raspberry Pi ne sont pas équipés d’une puce TPM par défaut.

Ouvrez le fichier de configuration.

```bash
sudo nano /etc/iotedge/config.yaml
```

Recherchez la section de provisionnement du fichier. Supprimez les marques de commentaire du mode de provisionnement **dps** et commentez la section Manuel. Mettez à jour les valeurs de **scope_id** et de **registration_id** avec les valeurs de votre IoT Hub Device Provisioning Service et de votre appareil IoT Edge avec TPM.

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

Enregistrez et fermez le fichier.

   `CTRL + X`, `Y`, `Enter`

Après avoir entré les informations de provisionnement dans le fichier de configuration, redémarrez le démon :

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Vérifier la réussite de l’installation

Si vous avez utilisé la procédure de **configuration manuelle** dans la section précédente, le runtime IoT Edge doit être correctement provisionné et en cours d’exécution sur votre appareil. Si vous avez utilisé la procédure de **configuration automatique**, vous devez suivre quelques étapes supplémentaires pour que le runtime puisse inscrire à votre place votre appareil avec votre hub IoT. Pour connaître les étapes suivantes, consultez [Créer et provisionner un appareil IoT Edge avec TPM simulé sur une machine virtuelle Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Vous pouvez vérifier l’état du démon IoT Edge à l’aide de la commande suivante :

```bash
systemctl status iotedge
```

Examinez les journaux d’activité du démon à l’aide de la commande suivante :

```bash
journalctl -u iotedge --no-pager --no-full
```

Enfin, répertoriez les modules en cours d’exécution à l’aide de la commande suivante :

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>Conseils et suggestions

Vous avez besoin de privilèges élevés pour exécuter les commandes `iotedge`. Après l’installation du runtime, déconnectez-vous de votre machine et reconnectez-vous pour mettre à jour vos autorisations automatiquement. Dans l’intervalle, utilisez **sudo** devant les commandes `iotedge`.

Sur les appareils aux ressources limitées, il est vivement recommandé d’affecter la valeur *false* à la variable d’environnement *OptimizeForPerformance*, conformément aux instructions figurant dans le [guide de résolution des problèmes](troubleshoot.md).

Si votre réseau comporte un serveur proxy, effectuez les étapes décrites dans [Configurer votre appareil IoT Edge pour communiquer via un serveur proxy](how-to-configure-proxy-support.md).

## <a name="uninstall-iot-edge"></a>Désinstaller IoT Edge

Si vous souhaitez supprimer l’installation d’IoT Edge de votre appareil Linux, utilisez les commandes suivantes à partir de la ligne de commande.

Supprimez le runtime IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Lorsque le runtime IoT Edge est supprimé, les conteneurs qu’il a créés sont arrêtés, mais existent toujours sur votre appareil. Affichez tous les conteneurs pour voir ceux qui restent.

```bash
sudo docker ps -a
```

Supprimez les conteneurs de votre appareil, dont les deux conteneurs de runtime.

```bash
sudo docker rm -f <container name>
```

Enfin, supprimez le runtime du conteneur de votre appareil.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’un appareil IoT Edge approvisionné avec le runtime installé, vous pouvez [déployer des modules IoT Edge](how-to-deploy-modules-portal.md).

Si vous ne parvenez pas à installer correctement le runtime IoT Edge, consultez la page de [résolution des problèmes](troubleshoot.md).

Pour mettre à jour une installation existante vers la dernière version d’IoT Edge, voir [Mettre à jour le runtime et le démon de sécurité IoT Edge](how-to-update-iot-edge.md).
