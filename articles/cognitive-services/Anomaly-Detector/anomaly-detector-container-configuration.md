---
title: Guide pratique pour configurer un conteneur pour l’API Détecteur d’anomalies
titleSuffix: Azure Cognitive Services
description: L’environnement d’exécution de conteneur de l’API Détecteur d’anomalies est configuré avec les arguments de la commande `docker run`. Ce conteneur a plusieurs paramètres obligatoires et quelques paramètres facultatifs.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: cb0a12df6696e76050d4c53bd75e07134b3dc27c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721720"
---
# <a name="configure-anomaly-detector-containers"></a>Configurer des conteneurs Détecteur d’anomalies

L’environnement d’exécution de conteneur **Détecteur d’anomalies** est configuré à l’aide des arguments de la commande `docker run`. Ce conteneur a plusieurs paramètres obligatoires et quelques paramètres facultatifs. Plusieurs [exemples](#example-docker-run-commands) de commande sont disponibles. Les paramètres propres aux conteneurs correspondent aux paramètres de facturation. 

# <a name="configuration-settings"></a>Paramètres de configuration

Ce conteneur a les paramètres de configuration suivants :

|Obligatoire|Paramètre|Objectif|
|--|--|--|
|OUI|[ApiKey](#apikey-configuration-setting)|Utilisé pour le suivi des informations de facturation.|
|Non|[ApplicationInsights](#applicationinsights-setting)|Vous permet d’ajouter la prise en charge de la télémétrie [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) à votre conteneur.|
|OUI|[Billing](#billing-configuration-setting)|Spécifie l’URI de point de terminaison de la ressource de service sur Azure.|
|OUI|[Eula](#eula-setting)| Indique que vous avez accepté la licence pour le conteneur.|
|Non|[Fluentd](#fluentd-settings)|Écrire les données des journaux et, éventuellement, des métriques, sur un serveur Fluentd.|
|Non|[Proxy HTTP](#http-proxy-credentials-settings)|Configurer un proxy HTTP pour effectuer des requêtes sortantes.|
|Non|[Logging](#logging-settings)|Fournit la prise en charge de la journalisation ASP.NET Core pour votre conteneur. |
|Non|[Mounts](#mount-settings)|Lire et écrire des données de l’ordinateur hôte sur le conteneur, et du conteneur sur l’ordinateur hôte.|

> [!IMPORTANT]
> Les paramètres [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) et [`Eula`](#eula-setting) sont utilisés conjointement, et vous devez fournir des valeurs valides pour les trois ; à défaut, votre conteneur ne démarrera pas. Pour plus d’informations sur l’instanciation d’un conteneur à l’aide de ces paramètres de configuration, consultez [Facturation](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Paramètre de configuration ApiKey

Le paramètre `ApiKey` spécifie la clé de ressource Azure utilisée pour effectuer le suivi des informations de facturation pour le conteneur. Vous devez spécifier une valeur pour ApiKey, laquelle doit être une clé valide pour la ressource _Détecteur d’anomalies_ spécifiée pour le paramètre de configuration [`Billing`](#billing-configuration-setting).

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : Gestion des ressources du **Détecteur d’anomalies**, sous **Clés**

## <a name="applicationinsights-setting"></a>Paramètre ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Paramètre de configuration Billing

Le paramètre `Billing` permet de spécifier l’URI de point de terminaison de la ressource _Détecteur d’anomalies_ dans Azure afin de mesurer les informations de facturation du conteneur. Vous devez donner à ce paramètre de configuration une valeur correspondant à un URI de point de terminaison valide pour une ressource _Détecteur d’anomalies_ dans Azure.

Vous trouverez ce paramètre à l’emplacement suivant :

* Portail Azure : Vue d’ensemble du **Détecteur d’anomalies**, avec l’étiquette `Endpoint`

|Obligatoire| Nom | Type de données | Description |
|--|------|-----------|-------------|
|OUI| `Billing` | Chaîne | URI du point de terminaison de facturation<br><br>Exemple :<br>`Billing=https://westus2.api.cognitive.microsoft.com` |

## <a name="eula-setting"></a>Paramètre Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Paramètres Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Paramètres des informations d'identification du proxy HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Paramètres de journalisation
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Paramètres de montage

Utilisez des montages de liaisons pour lire et écrire des données vers et à partir du conteneur. Vous pouvez spécifier un montage d’entrée ou de sortie en spécifiant l’option `--mount` dans la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Les conteneurs Détecteur d’anomalies n’utilisent pas de montage d’entrée ou de sortie pour stocker les données d’entraînement ou de service. 

La syntaxe exacte de l’emplacement de montage d’hôte varie en fonction du système d’exploitation hôte. De plus, l’emplacement de montage de l’[ordinateur hôte](anomaly-detector-container-howto.md#the-host-computer) peut ne pas être accessible en raison d’un conflit entre les autorisations utilisées par le compte de service Docker et les autorisations de l’emplacement de montage de l’hôte. 

|Facultatif| Nom | Type de données | Description |
|-------|------|-----------|-------------|
|Non autorisé| `Input` | Chaîne | Les conteneurs Détecteur d’anomalies n’utilisent pas cet élément.|
|Facultatif| `Output` | Chaîne | Cible du montage de sortie. La valeur par défaut est `/output`. Il s’agit de l’emplacement des journaux d’activité. Les journaux d’activité de conteneur sont inclus. <br><br>Exemple :<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Exemples de commandes docker run 

Les exemples suivants utilisent les paramètres de configuration pour illustrer comment écrire et utiliser des commandes `docker run`.  Une fois en cours d’exécution, le conteneur continue à s’exécuter jusqu’à ce que vous l’[arrêtiez](anomaly-detector-container-howto.md#stop-the-container).

* **Caractère de continuation de ligne** : Les commandes Docker dans les sections suivantes utilisent la barre oblique inverse, `\`, comme caractère de continuation de ligne pour un interpréteur de commandes bash. Remplacez-la ou supprimez-la en fonction des exigences de votre système d’exploitation hôte. Par exemple, le caractère de continuation de ligne pour les fenêtres est un accent circonflexe, `^`. Remplacez la barre oblique inverse par l’accent circonflexe. 
* **Ordre des arguments** : Ne changez pas l’ordre des arguments, sauf si vous avez une connaissance approfondie des conteneurs Docker.

Remplacez la valeur entre accolades, `{}`, par vos propres valeurs :

| Placeholder | Valeur | Format ou exemple |
|-------------|-------|---|
|{BILLING_KEY} | La clé de point de terminaison de la ressource Détecteur d’anomalies. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Valeur de point de terminaison de facturation, région comprise.|`https://westus2.api.cognitive.microsoft.com`|

> [!IMPORTANT]
> Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](anomaly-detector-container-howto.md#billing).
> La valeur ApiKey est la **Clé** figurant dans la page des clés de ressources Détecteur d’anomalies Azure. 

## <a name="anomaly-detector-container-docker-examples"></a>Exemples Docker de conteneurs Détecteur d’anomalies

Les exemples Docker suivants s’appliquent au conteneur Détecteur d’anomalies. 

### <a name="basic-example"></a>Exemple de base 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Exemple de journalisation avec arguments de ligne de commande

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```
