---
title: Faire fonctionner des appareils hors connexion - Azure IoT Edge | Microsoft Docs
description: Découvrez comment des appareils et des modules IoT Edge peuvent fonctionner sans connexion Internet pendant de longues périodes de temps, et comment IoT Edge permet aussi aux appareils IoT standard de continuer à fonctionner hors connexion.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4f3e5c1566271573b43e24a1749b42daa7530555
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051956"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Comprendre les fonctionnalités hors connexion étendues pour les appareils, modules et appareils enfants IoT Edge

Azure IoT Edge prend en charge les opérations hors connexion étendues sur vos appareils IoT Edge et permet également certaines opérations hors connexion sur les appareils enfants non IoT Edge. Dès lors qu’un appareil IoT Edge s’est connecté au moins une fois à IoT Hub, cet appareil et tous les appareils enfants assignés peuvent continuer à fonctionner même s’ils sont déconnectés de façon prolongée ou intermittente. 


## <a name="how-it-works"></a>Fonctionnement

Quand un appareil IoT Edge passe en mode hors connexion, le hub IoT Edge assure trois rôles. Tout d’abord, il stocke tous les messages à acheminer en amont jusqu’à ce que l’appareil puisse se reconnecter. Ensuite, il authentifie, pour le compte d’IoT Hub, les modules et les appareils enfants afin qu’ils puissent continuer à fonctionner. Enfin, il rend possible la communication entre les appareils enfants qui communiquent normalement par le biais d’IoT Hub. 

L’exemple de scénario IoT Edge suivant montre le fonctionnement en mode hors connexion :

1. **Configurer des appareils**

   Sur les appareils IoT Edge, les fonctionnalités hors connexion sont automatiquement activées. Pour étendre ces fonctionnalités à d’autres appareils IoT, vous devez déclarer une relation parent-enfant entre les différents appareils dans IoT Hub. Ensuite, vous configurez les appareils enfants pour qu’ils fassent confiance à l’appareil parent qui leur est affecté et acheminent les communications vers le cloud par l’intermédiaire du parent en tant que passerelle. 

2. **Synchroniser avec IoT Hub**

   Après l’installation du runtime IoT Edge, l’appareil IoT Edge doit être connecté au moins une fois pour pouvoir se synchroniser avec IoT Hub. Lors de cette synchronisation, l’appareil IoT Edge obtient des informations sur tous les appareils enfants qui lui sont assignés. De plus, l’appareil IoT Edge met à jour en toute sécurité son cache local pour permettre les opérations hors connexion et il récupère les paramètres du stockage local des messages de télémétrie. 

3. **Passer en mode hors connexion**

   Même quand ils ne sont plus connectés à IoT Hub, l’appareil IoT Edge, ses modules déployés et ses appareils IoT enfants peuvent continuer à fonctionner pendant une durée indéterminée. Les modules et les appareils enfants hors connexion peuvent démarrer et redémarrer en s’authentifiant auprès du hub IoT Edge. Les données de télémétrie liées qui sont transmises en amont à IoT Hub sont stockées localement. La communication entre les modules ou entre les appareils IoT enfants est maintenue par le biais de méthodes ou messages directs. 

4. **Reconnecter et resynchroniser avec IoT Hub**

   Après la restauration de la connexion à IoT Hub, l’appareil IoT Edge est resynchronisé. Les messages stockés localement sont remis dans l’ordre dans lequel ils ont été stockés. Les éventuelles différences entre les propriétés désirées et rapportées des modules et des appareils sont rapprochées. L’appareil IoT Edge apporte les modifications nécessaires aux appareils IoT enfants qui lui sont assignés.

## <a name="restrictions-and-limits"></a>Restrictions et limites

Les fonctionnalités hors connexion étendues décrites dans cet article sont disponibles dans [IoT Edge version 1.0.4 ou ultérieure](https://github.com/Azure/azure-iotedge/releases). Les versions antérieures fournissent toutefois certaines fonctionnalités hors connexion. Les appareils IoT Edge existants qui n’ont pas de fonctionnalités hors connexion étendues ne peuvent pas être mis à niveau en changeant de version de runtime. Pour bénéficier de ces fonctionnalités, ils doivent être reconfigurés avec une nouvelle identité d’appareil IoT Edge. 

La prise en charge des fonctionnalités hors connexion étendues est proposée dans toutes les régions où IoT Hub est disponible, **à l’exception** de la région USA Est.

Seuls des appareils non IoT Edge peuvent être ajoutés en tant qu’appareils enfants. 

Les appareils IoT Edge et leurs appareils enfants assignés peuvent fonctionner en mode hors connexion pendant une durée indéterminée dès lors qu’ils ont été synchronisés une fois. Toutefois, le stockage des messages dépend du paramètre de durée de vie (TTL) et de l’espace disque disponible pour stocker les messages. 

## <a name="set-up-parent-and-child-devices"></a>Configurer des appareils parents et enfants

Pour étendre les fonctionnalités hors connexion étendues d’un appareil IoT Edge aux appareils IoT enfants assignés, vous devez passer par deux étapes. Tout d’abord, déclarez les relations parent-enfant dans le Portail Azure. Ensuite, créez une relation d’approbation entre l’appareil parents et les appareils enfants, puis configurez l’acheminement des communications vers le cloud par l’intermédiaire du parent en tant que passerelle. 

### <a name="assign-child-devices"></a>Assigner des appareils enfants

Les appareils enfants sont des appareils non IoT Edge inscrits auprès du même IoT Hub. Un appareil parent peut avoir plusieurs appareils enfants, mais un appareil enfant n’a qu’un appareil parent. Pour paramétrer les périphériques enfants sur un appareil Edge, trois moyens sont à votre disposition : le Portail Azure, l’interface de ligne de commande Azure et le SDK du service IoT Hub. 

Les sections suivantes fournissent des exemples sur la manière dont vous pouvez déclarer la relation parent/enfant dans IoT Hub pour les appareils IoT existants. Si vous créez des identités d’appareil pour vos appareils enfants, consultez [Authentifier un appareil en aval auprès d’Azure IoT Hub](how-to-authenticate-downstream-device.md) pour plus d’informations.

#### <a name="option-1-iot-hub-portal"></a>Option 1 : Portail IoT Hub

Vous pouvez déclarer les relations parent-enfant au moment où vous créez un appareil. Pour les appareils existants, vous pouvez déclarer les relations dans la page de détails de l’appareil IoT Edge parent ou de l’appareil IoT enfant. 

   ![Gérer les appareils enfants à partir de la page de détails de l’appareil IoT Edge](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Option 2 : Utiliser l’outil de ligne de commande `az`

À l’aide de l’interface de ligne de commande [Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) avec l’[extension IoT](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 ou version ultérieure), vous pouvez gérer les relations parent-enfant avec les sous-commandes [device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest). L’exemple ci-dessous utilise une requête pour affecter tous les appareils non IoT Edge dans le hub à des appareils enfants d’un appareil IoT Edge. 

```shell
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name 
```

Vous pouvez modifier la [requête](../iot-hub/iot-hub-devguide-query-language.md) pour sélectionner un autre sous-ensemble des appareils. L’exécution de la commande peut prendre plusieurs secondes si vous spécifiez un grand nombre d’appareils.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Option 3 : Utiliser le SDK IoT Hub Service 

Enfin, vous pouvez gérer les relations parent-enfants par programme en utilisant le SDK IoT Hub Service C#, Java ou Node.js. Voici un [exemple d’affectation d’un appareil enfant](https://aka.ms/set-child-iot-device-c-sharp) avec le SDK C#.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Définir un appareil parent en tant que passerelle

Vous pouvez considérer une relation parent/enfant comme une passerelle transparente, dans laquelle l’appareil enfant possède sa propre identité dans IoT Hub mais communique avec le cloud via son parent. Pour une communication sécurisée, l’appareil enfant doit pouvoir vérifier que l’appareil parent provient d’une source fiable. Autrement, des tiers risqueraient de configurer des appareils malveillants pour usurper l’identité des parents et intercepter les communications. 

Une façon de créer cette relation d’approbation est décrite en détail dans les articles suivants : 
* [Configurer un appareil IoT Edge en tant que passerelle transparente](how-to-create-transparent-gateway.md)
* [Connecter un appareil en aval (enfant) à une passerelle Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Spécifier les serveurs DNS 

Pour améliorer la robustesse, il est vivement recommandé de spécifier les adresses de serveur DNS utilisées dans votre environnement. Découvrez les deux options permettant de [définir le serveur DNS dans l’article sur la résolution des problèmes](troubleshoot.md#resolution-7).

## <a name="optional-offline-settings"></a>Paramètres hors connexion facultatifs

Si vos appareils se déconnectent, l’appareil parent IoT Edge stocke tous les messages destinés au cloud jusqu’à ce que la connexion soit rétablie. Le module hub IoT Edge gère le stockage et le transfert des messages hors connexion. Pour les appareils qui peuvent rester hors connexion pendant de longues périodes, optimisez les performances en configurant deux paramètres de hub IoT Edge. 

Tout d’abord, augmentez le temps de réponse en temps réel pour que le hub IoT Edge conserve les messages suffisamment longtemps et que votre appareil ait le temps de se reconnecter. Ajoutez ensuite l’espace disque supplémentaire pour le stockage des messages. 

### <a name="time-to-live"></a>Durée de vie

Le paramètre de durée de vie définit la durée (en secondes) pendant laquelle un message peut attendre d’être remis avant d’expirer. La durée par défaut est de 7 200 secondes (deux heures). La valeur maximale n’est limitée que par la valeur maximale d’une variable entière, qui est d’environ 2 milliards. 

Ce paramètre est une propriété désirée du hub IoT Edge, qui est stockée dans le jumeau du module. Vous pouvez le configurer dans le Portail Azure ou directement dans le manifeste de déploiement. 

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="additional-offline-storage"></a>Stockage hors connexion supplémentaire

Les messages sont stockés par défaut dans le système de fichiers du conteneur du hub IoT Edge. Si l’espace de stockage n’est pas suffisant pour vos besoins hors connexion, vous pouvez dédier un stockage local sur l’appareil IoT Edge. Créez une variable d’environnement pour le hub IoT Edge qui pointe vers un dossier de stockage dans le conteneur. Utilisez ensuite les options de création pour lier ce dossier de stockage à un dossier situé sur la machine hôte. 

Vous pouvez configurer des variables d’environnement et les options de création pour le module du hub IoT Edge dans la section **Configurer les paramètres avancés du runtime Edge** du Portail Microsoft Azure. Vous pouvez aussi les configurer directement dans le manifeste de déploiement. 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": {
            "HostConfig": {
                "Binds": ["<HostStoragePath>:<ModuleStoragePath>"],
                "PortBindings": {
                    "8883/tcp": [{"HostPort":"8883"}],
                    "443/tcp": [{"HostPort":"443"}],
                    "5671/tcp": [{"HostPort":"5671"}]
                }
            }
        }
    },
    "env": {
        "storageFolder": {
            "value": "<ModuleStoragePath>"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Remplacez `<HostStoragePath>` et `<ModuleStoragePath>` par le chemin de stockage de votre hôte et de votre module. Les deux chemins doivent être des chemins absolus. Dans les options de création, liez les chemins d’accès de stockage de l’hôte et du module. Ensuite, créez une variable d’environnement qui pointe vers le chemin d’accès de stockage du module.  

Par exemple, `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` signifie que le répertoire **/etc/iotedge/storage** sur votre système hôte est mappé au répertoire **/iotedge/stockage/** sur le conteneur. Ou autre exemple pour les systèmes Windows, `"Binds":["C:\\temp:C:\\contemp"]` signifie que le répertoire **C:\\temp** sur votre système hôte est mappé au répertoire **C:\\contemp** sur le conteneur. 

Vous trouverez aussi plus de détails sur les options de création dans la [documentation de docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer une passerelle transparente pour les connexions parents/enfants de votre appareil : 

* [Configurer un appareil IoT Edge en tant que passerelle transparente](how-to-create-transparent-gateway.md)
* [Authentifier un appareil en aval auprès d’Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Connecter un appareil en aval à une passerelle Azure IoT Edge](how-to-connect-downstream-device.md)
