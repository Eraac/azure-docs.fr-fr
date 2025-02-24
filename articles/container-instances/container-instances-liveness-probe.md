---
title: Configurer les sondes probe liveness dans Azure Container Instances
description: Découvrez comment configurer les sondes probe liveness pour redémarrer les conteneurs non intègres dans Azure Container Instances.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: 28205d6db85d7a5051f283445d95dd2375e174c8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325864"
---
# <a name="configure-liveness-probes"></a>Configurer les probe liveness

Les applications en conteneur peuvent s’exécuter sur de longues périodes, ce qui provoque des états rompus qui nécessitent parfois une réparation (redémarrage du conteneur). Azure Container Instances prend en charge les sondes probe liveness pour inclure des configurations permettant le redémarrage du conteneur si des fonctionnalités critiques ne fonctionnent pas.

Cet article explique comment déployer un groupe de conteneurs avec probe liveness, illustrant le redémarrage automatique d’un conteneur non intègre simulé.

## <a name="yaml-deployment"></a>Déploiement YAML

Créez un fichier `liveness-probe.yaml` avec l’extrait de code suivant. Ce fichier définit un groupe composé d’un conteneur NGNIX qui finit par devenir non intègre.

```yaml
apiVersion: 2018-06-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Exécutez la commande suivante pour déployer ce groupe de conteneurs avec la configuration YAML ci-dessus :

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Commande de démarrage

Le déploiement définit une commande de démarrage à lancer lorsque le conteneur s’exécute pour la première fois, définie par la propriété `command` qui prend en entrée un tableau de chaînes. Dans cet exemple, il démarre une session Bash et crée un fichier nommé `healthy` dans le répertoire `/tmp` en exécutant cette commande :

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Il se met ensuite en veille pendant 30 secondes avant de supprimer le fichier, puis se remet en veille pendant 10 minutes.

### <a name="liveness-command"></a>Commande d’activité

Ce déploiement définit une `livenessProbe` prenant en charge une commande d’activité `exec` qui joue le rôle de vérification de l’activité. Si cette commande se termine sur une valeur différente de zéro, le conteneur sera arrêté et redémarré, avec un message signalant que le fichier `healthy` est introuvable. Si cette commande se termine correctement avec le code de sortie 0, aucune action ne sera entreprise.

La propriété `periodSeconds` signifie que la commande d’activité doit s’exécuter toutes les 5 secondes.

## <a name="verify-liveness-output"></a>Vérifier la sortie d’activité

Pendant les 30 premières secondes, le fichier `healthy` créé par la commande de démarrage existe. Lorsque la commande d’activité vérifie l’existence du fichier `healthy`, le code d’état retourne un zéro, ce qui signale une réussite ; par conséquent, aucun redémarrage ne se produit.

Après 30 secondes, `cat /tmp/healthy` commence à échouer, ce qui provoque l’apparition d’événements non intègres et l’arrêt d’événements.

Vous pouvez voir ces événements dans le portail Azure et dans Azure CLI.

![Événement non intègre sur le Portail][portal-unhealthy]

Si l’on consulte les événements sur le Portail Azure, les événements de type `Unhealthy` seront déclenchés à l’échec d’une commande d’activité. L’événement suivant sera de type `Killing`, ce qui signifie la suppression d’un conteneur, permettant ainsi le lancement d’un redémarrage. Le nombre de redémarrages du conteneur s’incrémentera à chaque fois.

Les redémarrages sont effectués sur place afin de préserver les ressources comme les adresses IP publiques et les contenus propres aux nœuds.

![Compteur de redémarrages sur le Portail][portal-restart]

Si la sonde probe liveness échoue constamment et déclenche trop de redémarrages, votre conteneur accusera un retard exponentiel.

## <a name="liveness-probes-and-restart-policies"></a>Probe liveness et stratégies de redémarrage

Les stratégies de redémarrage annulent et remplacent le comportement de redémarrage déclenché par les sondes probe liveness. Par exemple, si vous définissez `restartPolicy = Never` *et* une sonde probe liveness, le groupe de conteneurs ne redémarre pas en cas d’échec de la vérification de l’activité. Il respectera en revanche sa stratégie de redémarrage, soit `Never`.

## <a name="next-steps"></a>Étapes suivantes

Les scénarios basés sur des tâches peuvent nécessiter qu’une sonde probe liveness autorise les redémarrages automatiques si une fonction prérequise ne fonctionne pas correctement. Pour plus d’informations sur l’exécution des conteneurs basés sur des tâches, consultez [Exécuter des tâches en conteneur dans Azure Container Instances](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
