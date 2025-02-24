---
title: Journalisation des instances de conteneur avec les journaux d’activité Azure Monitor
description: Découvrez comment envoyer des journaux à partir d’instances de conteneurs Azure vers des journaux Azure Monitor.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: overview
ms.date: 07/09/2019
ms.author: danlep
ms.openlocfilehash: 4099bc0b15f02faade02f47aeb00fb7c4b4a3332
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325882"
---
# <a name="container-instance-logging-with-azure-monitor-logs"></a>Journalisation des instances de conteneur avec les journaux d’activité Azure Monitor

Les espaces de travail Log Analytics fournissent un emplacement centralisé pour le stockage et l’interrogation des données de journaux, non seulement à partir de ressources Azure, mais également à partir de ressources locales et d’autres clouds. Azure Container Instances inclut une prise en charge de l’envoi de données vers des journaux d’activité Azure Monitor.

Pour envoyer des données d’instance de conteneur vers les journaux Azure Monitor, vous devez spécifier une clé d’espace de travail et un ID d’espace de travail Log Analytics lors de la création d’un groupe de conteneurs. Les sections suivantes décrivent la création d’un groupe de conteneurs dans lequel la journalisation et l’interrogation des journaux d’activité sont activées.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prérequis

Pour activer la journalisation dans vos instances de conteneur, vous avez besoin des éléments suivants :

* [Espace de travail Log Analytics](../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (ou [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Obtenir les informations d’identification de Log Analytics

Azure Container Instances doit disposer d’une autorisation pour envoyer des données à votre espace de travail Log Analytics. Pour accorder cette autorisation et activer la journalisation, vous devez fournir l’ID d’espace de travail Log Analytics et une de ses clés (principale ou secondaire) lorsque vous créez le groupe de conteneurs.

Pour obtenir l’ID et la clé primaire de l’espace de travail Log Analytics :

1. Accédez à votre espace de travail Log Analytics dans le portail Azure
1. Sous **Paramètres**, sélectionnez **Paramètres avancés**.
1. Sélectionnez **Sources connectées** > **Serveurs Windows** (ou **Serveurs Linux**, l’ID et les clés sont les mêmes pour les deux)
1. Notez :
   * **ID DE L’ESPACE DE TRAVAIL**
   * **CLÉ PRIMAIRE**

## <a name="create-container-group"></a>Créer un groupe de conteneurs

Maintenant que vous disposez de l’ID et de la clé primaire de l’espace de travail Log Analytics, vous êtes prêt à créer un groupe de conteneurs dans lequel la journalisation est activée.

Les exemples suivants montrent deux façons de créer un groupe de conteneurs avec un seul conteneur [fluentd][fluentd] : Azure CLI et Azure CLI avec un modèle YAML. Le conteneur Fluentd produit plusieurs lignes de sortie dans sa configuration par défaut. Cette sortie étant envoyée à votre espace de travail Log Analytics, elle convient parfaitement à l’affichage et à l’interrogation des journaux d’activité.

### <a name="deploy-with-azure-cli"></a>Déploiement avec l’interface de ligne de commande Azure

Pour procéder au déploiement avec Azure CLI, spécifiez les paramètres `--log-analytics-workspace` et `--log-analytics-workspace-key` dans la commande [az container create][az-container-create]. Avant d’exécuter la commande suivante, remplacez les deux valeurs de l’espace de travail par celles que vous avez obtenues à l’étape précédente (et mettez à jour le nom du groupe de ressources).

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Déployer avec YAML

Utilisez cette méthode si vous préférez déployer des groupes de conteneurs avec YAML. Le code YAML suivant définit un groupe de conteneurs à un seul conteneur. Copiez le code YAML dans un nouveau fichier, puis remplacez `LOG_ANALYTICS_WORKSPACE_ID` et `LOG_ANALYTICS_WORKSPACE_KEY` par les valeurs obtenues à l’étape précédente. Enregistrez le fichier sous le nom **deploy-aci.yaml**.

```yaml
apiVersion: 2018-10-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Ensuite, exécutez la commande suivante pour déployer le groupe de conteneurs. Remplacez `myResourceGroup` par un groupe de ressources de votre abonnement (ou créez d’abord un groupe de ressources nommé « myResourceGroup ») :

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Vous devriez recevoir une réponse à partir des détails du déploiement de conteneur Azure peu de temps après l’émission de la commande.

## <a name="view-logs-in-azure-monitor-logs"></a>Voir les journaux d’activité Azure Monitor

Une fois que vous avez déployé le groupe de conteneurs, l’affichage dans le portail Azure des premières entrées de journal peut prendre plusieurs minutes (jusqu’à 10). Pour voir les journaux du groupe de conteneurs :

1. Accédez à votre espace de travail Log Analytics dans le portail Azure
1. Sous **Général**, sélectionnez **Journaux**.  
1. Tapez la requête suivante : `search *`
1. Sélectionnez **Exécuter**.

Vous devez voir plusieurs résultats affichés par la requête `search *`. Si vous ne voyez aucun résultat dans un premier temps, patientez quelques minutes, puis sélectionnez le bouton **Exécuter** pour réexécuter la requête. Par défaut, les entrées de journal sont affichées au format **Tableau**. Vous pouvez ensuite développer une ligne pour afficher le contenu d’une entrée de journal.

![Résultats de recherche dans les journaux dans le portail Azure][log-search-01]

## <a name="query-container-logs"></a>Interroger les journaux d’activité d’un conteneur

Les journaux Azure Monitor incluent un [langage de requête][query_lang] étendu permettant de tirer (pull) les informations des milliers de lignes que peuvent contenir les journaux.

L’agent de journalisation d’Azure Container Instances envoie des entrées vers la table `ContainerInstanceLog_CL` dans votre espace de travail Log Analytics. La structure de base d’une requête est la table source (`ContainerInstanceLog_CL`) suivie d’une série d’opérateurs séparés par une barre verticale (`|`). Vous pouvez chaîner plusieurs opérateurs pour affiner les résultats et effectuer des fonctions avancées.

Pour afficher un exemple de résultats de requête, collez la requête suivante dans la zone de texte de requête (sous « Show legacy language converter » [Afficher le convertisseur de langue hérité], puis sélectionnez le bouton **Exécuter** pour exécuter la requête. Cette requête affiche toutes les entrées de journal dont le champ « Message » contient le mot « Avertissement » :

```query
ContainerInstanceLog_CL
| where Message contains("warn")
```

Des requêtes plus complexes sont également prises en charge. Par exemple, cette requête n’affiche que les entrées de journal pour le groupe de conteneurs « mycontainergroup001 » générées au cours de la dernière heure :

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Étapes suivantes

### <a name="azure-monitor-logs"></a>Journaux d’activité Azure Monitor

Pour plus d’informations sur l’interrogation des journaux d’activité et la configuration d’alertes dans les journaux d’activité Azure Monitor, consultez :

* [Présentation des recherches dans les journaux d’activité Azure Monitor](../log-analytics/log-analytics-log-search.md)
* [Alertes unifiées dans Azure Monitor](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Surveiller le processeur et la mémoire du conteneur

Pour plus d’informations sur la surveillance des ressources processeur et mémoire de l’instance de conteneur, consultez :

* [Surveiller les ressources des conteneurs dans Azure Container Instances](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create