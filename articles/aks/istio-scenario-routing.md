---
title: Routage intelligent et déploiement Canary avec Istio dans Azure Kubernetes Service (AKS)
description: Découvrez comment utiliser Istio pour fournir un routage intelligent et déployer des versions Canary sur un cluster Azure Kubernetes Service (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: bd660a2b6ffb96478c3170cc7013ff22518b758f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702205"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Utiliser le routage intelligent et le déploiement Canary avec Istio dans Azure Kubernetes Service (AKS)

[Istio][istio-github] est un maillage de services (service mesh) open source qui fournit un jeu de fonctionnalités de base dans les microservices d’un cluster Kubernetes. Ces fonctionnalités incluent la gestion du trafic, l’identité et la sécurité des services, l’application des stratégies et l’observabilité. Pour plus d’informations sur Istio, consultez la documentation officielle [What is Istio?][istio-docs-concepts] (Présentation d’Istio).

Cet article vous montre comment utiliser la fonctionnalité de gestion de trafic d’Istio. Un exemple d’application de vote AKS est utilisé pour explorer le routage intelligent et le déploiement de versions Canary.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Déployer l’application
> * Mettre à jour l’application
> * Déployer une version Canary de l’application
> * Finaliser le déploiement

## <a name="before-you-begin"></a>Avant de commencer

> [!NOTE]
> Ce scénario a été testé avec la version d’Istio `1.1.3`.

Les étapes détaillées de cet article supposent que vous avez créé un cluster AKS (Kubernetes versions `1.11` et ultérieures, avec RBAC activé) et que vous avez établi une connexion `kubectl` avec le cluster. Istio doit également être installé dans votre cluster.

Si vous avez besoin d’aide avec l’un de ces éléments, consultez les instructions dans le [guide de démarrage rapide d’AKS][aks-quickstart] et [Installer Istio dans AKS][istio-install].

## <a name="about-this-application-scenario"></a>À propos de ce scénario d’application

L’exemple d’application de vote AKS fournit deux options de vote (**Chats** ou **Chiens**) aux utilisateurs. Un composant de stockage conserve le nombre de votes pour chaque option. En outre, un composant d’analytique fournit des informations sur les votes en faveur de chaque option.

Dans ce scénario d’application, vous commencez par déployer la version `1.0` de l’application de vote et la version `1.0` du composant d’analytique. Le composant d’analytique fournit les décomptes des nombres de votes. L’application de vote et le composant d’analytique interagissent avec la version `1.0` du composant de stockage, qui s’appuie sur Redis.

Vous mettez à niveau le composant d’analytique vers la version `1.1`, qui, outre les décomptes, fournit les totaux et les pourcentages.

Un sous-ensemble d’utilisateurs testent la version `2.0` de l’application par le biais d’un contrôle de validité de mise en production. Cette nouvelle version utilise un composant de stockage qui est associé à une base de données MySQL.

Une fois que vous êtes certain que la version `2.0` fonctionne comme prévu pour votre sous-ensemble d’utilisateurs, vous la déployez pour tous vos utilisateurs.

## <a name="deploy-the-application"></a>Déployer l’application

Nous allons commencer par déployer l’application sur votre cluster Azure Kubernetes Service (AKS). Le diagramme suivant montre la configuration en place à la fin de cette section : la version `1.0` de tous les composants avec prise en charge des demandes entrantes par le biais de la passerelle d’entrée Istio :

![Composants de l’application de vote AKS et routage](media/istio/components-and-routing-01.png)

Les artefacts que vous devez suivre avec cet article sont disponibles dans le dépôt GitHub [Azure-Samples/aks-voting-app][github-azure-sample]. Vous pouvez télécharger les artefacts ou cloner le dépôt comme suit :

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Accédez au dossier suivant dans le dépôt téléchargé/cloné, puis exécutez toutes les étapes suivantes à partir de ce dossier :

```console
cd scenarios/intelligent-routing-with-istio
```

Commencez par créer un espace de noms dans votre cluster AKS pour l’exemple d’application de vote AKS nommée `voting` comme suit :

```azurecli
kubectl create namespace voting
```

Attribuez à l’espace de noms l’étiquette `istio-injection=enabled`. Cette étiquette indique à Istio d’injecter automatiquement les proxys istio en tant que side-cars dans tous vos pods dans cet espace de noms.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

Maintenant, nous allons créer les composants de l’application de vote AKS. Créez ces composants dans l’espace de noms `voting` créé à l’étape précédente.

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

L’exemple de sortie suivant affiche les ressources créées :

```console
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio a certaines exigences spécifiques concernant les pods et les services. Pour plus d’informations, consultez la [documentation sur les exigences d’Istio relatives aux pods et services][istio-requirements-pods-and-services].

Pour voir les pods qui ont été créés, utilisez la commande [kubectl get pods][kubectl-get] comme suit :

```azurecli
kubectl get pods -n voting
```

L’exemple de sortie suivant montre qu’il y a trois instances du pod `voting-app` et une seule instance des pods `voting-analytics` et `voting-storage`. Chacun des pods a deux conteneurs. Un de ces conteneurs est le composant, tandis que l’autre est le `istio-proxy` :

```console
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s
```

Pour voir des informations sur le pod, utilisez la commande [kubectl describe pod][kubectl-describe]. Remplacez le nom du pod par le nom d’un pod dans votre propre cluster AKS à partir de la sortie précédente :

```azurecli
kubectl describe pod voting-app-1-0-956756fd-d5w7z --namespace voting
```

Le conteneur `istio-proxy` a été automatiquement injecté par Istio en vue de la gestion du trafic réseau vers et depuis vos composants, comme l’illustre l’exemple de sortie suivant :

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.1.3
[...]
```

Vous ne pouvez pas vous connecter à l’application de vote tant que vous n’avez pas créé la [passerelle][istio-reference-gateway] Istio et le [service virtuel][istio-reference-virtualservice]. Ces ressources Istio routent le trafic à partir de la passerelle d’entrée Istio par défaut vers notre application.

> [!NOTE]
> Une **passerelle** est un composant à la périphérie du maillage de services qui reçoit le trafic HTTP et TCP entrant ou sortant.
> 
> Un **service virtuel** définit un ensemble de règles de routage pour un ou plusieurs services de destination.

Utilisez la commande `kubectl apply` pour déployer le fichier yaml de la passerelle et du service virtuel. N’oubliez pas de spécifier l’espace de noms sur lequel ces ressources sont déployées.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

L’exemple de sortie suivant montre la nouvelle passerelle et le nouveau service virtuel en cours de création :

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Obtenez l’adresse IP de la passerelle d’entrée Istio à l’aide de la commande suivante :

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

L’exemple de sortie suivant montre l’adresse IP de la passerelle d’entrée :

```
20.188.211.19
```

Ouvrez un navigateur et collez l’adresse IP. L’exemple d’application de vote AKS s’affiche.

![Application de vote AKS en cours d’exécution dans notre cluster AKS compatible Istio](media/istio/deploy-app-01.png)

Les informations en bas de l’écran indiquent que l’application utilise la version `1.0` de `voting-app` et la version `1.0` de `voting-storage` (Redis).

## <a name="update-the-application"></a>Mettre à jour l’application

Nous allons déployer une nouvelle version du composant d’analytique. Cette nouvelle version `1.1` affiche les totaux et les pourcentages en plus du nombre de votes pour chaque catégorie.

Le diagramme suivant montre la configuration en place à la fin de cette section : seule la version `1.1` de notre composant `voting-analytics` fait l’objet d’un routage de trafic à partir du composant `voting-app`. Bien que la version `1.0` de notre composant `voting-analytics` continue à s’exécuter et soit référencée par le service `voting-analytics`, les proxys Istio interdisent le trafic vers et depuis celle-ci.

![Composants de l’application de vote AKS et routage](media/istio/components-and-routing-02.png)

Nous allons déployer la version `1.1` du composant `voting-analytics`. Créez ce composant dans l’espace de noms `voting` :

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

L’exemple de sortie suivant affiche les ressources créées :

```console
deployment.apps/voting-analytics-1-1 created
```

Rouvrez l’exemple d’application de vote AKS dans un navigateur, en utilisant l’adresse IP de la passerelle d’entrée Istio obtenue à l’étape précédente.

Votre navigateur alterne entre les deux vues indiquées ci-dessous. Étant donné que vous utilisez un [service][kubernetes-service] Kubernetes pour le composant `voting-analytics` avec un seul sélecteur d’étiquette (`app: voting-analytics`), Kubernetes utilise le comportement de tourniquet par défaut entre les pods qui correspondent à ce sélecteur. Dans ce cas, il s’agit des versions `1.0` et `1.1` de vos pods `voting-analytics`.

![Version 1.0 du composant d’analytique en cours d’exécution dans notre application de vote AKS](media/istio/deploy-app-01.png)

![Version 1.1 du composant d’analytique en cours d’exécution dans notre application de vote AKS](media/istio/update-app-01.png)

Vous pouvez visualiser le passage d’une version à l’autre du composant `voting-analytics` comme suit. Pensez à utiliser l’adresse IP de votre propre passerelle d’entrée Istio.

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

L’exemple de sortie suivant montre la partie pertinente du site web retourné quand le site passe d’une version à l’autre :

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Verrouiller le trafic vers la version 1.1 de l’application

Maintenant, nous allons verrouiller le trafic uniquement vers la version `1.1` du composant `voting-analytics` et la version `1.0` du composant `voting-storage`. Vous définissez ensuite des règles de routage pour tous les autres composants.

> * Un **service virtuel** définit un ensemble de règles de routage pour un ou plusieurs services de destination.
> * Une **règle de destination** définit des stratégies de trafic et des stratégies propres aux versions.
> * Une **stratégie** définit les méthodes d’authentification pouvant être acceptées sur les charges de travail.

Utilisez la commande `kubectl apply` pour remplacer la définition du service virtuel sur votre composant `voting-app` et ajoutez des [règles de destination][istio-reference-destinationrule] et des [services virtuels][istio-reference-virtualservice] pour les autres composants. Vous allez ajouter une [stratégie][istio-reference-policy] à l’espace de noms `voting` pour vous assurer que toute les communications entre les services sont sécurisées à l’aide de certificats clients et Mutual TLS.

* Dans la stratégie, `peers.mtls.mode` est défini avec la valeur `STRICT` pour s’assurer que Mutual TLS est appliqué entre vos services au sein de l’espace de noms `voting`.
* Nous définissons également `trafficPolicy.tls.mode` sur `ISTIO_MUTUAL` dans toutes nos règles de destination. Istio fournit des services avec des identités fortes et sécurise les communications entre les services à l’aide de certificats clients et Mutual TLS qu’il gère en toute transparence.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

L’exemple de sortie suivant illustre la stratégie, les règles de destination et les services virtuels en cours de mise à jour/création :

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Si vous rouvrez l’application de vote AKS dans un navigateur, seule la nouvelle version `1.1` du composant `voting-analytics` est utilisée par le composant `voting-app`.

![Version 1.1 du composant d’analytique en cours d’exécution dans notre application de vote AKS](media/istio/update-app-01.png)

Vous pouvez visualiser que le routage est désormais uniquement effectué vers la version `1.1` de votre composant `voting-analytics` comme suit. Pensez à utiliser l’adresse IP de votre propre passerelle d’entrée Istio :

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

L’exemple de sortie suivant montre la partie pertinente du site web retourné :

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Vérifions maintenant qu’Istio utilise Mutual TLS pour sécuriser les communications entre chacun de nos services. Pour cela, nous utilisons la commande [authn tls-check][istioctl-authn-tls-check] sur le binaire client `istioctl`, qui prend la forme suivante.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Cet ensemble de commandes fournissent des informations sur l’accès aux services spécifiés, à partir de tous les pods qui sont dans un espace de noms et correspondent à un jeu d’étiquettes :

Bash

```bash
# mTLS configuration between each of the istio ingress pods and the voting-app service
kubectl get pod -n istio-system -l app=istio-ingressgateway | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.istio-system voting-app.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-analytics service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-analytics.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-storage service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
kubectl get pod -n voting -l app=voting-analytics,version=1.1 | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local
```

PowerShell

```powershell
# mTLS configuration between each of the istio ingress pods and the voting-app service
(kubectl get pod -n istio-system -l app=istio-ingressgateway | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".istio-system") voting-app.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-analytics service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-analytics.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-analytics,version=1.1 | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }
```

Cet exemple de sortie suivant montre que Mutual TLS est appliqué pour chacune de nos requêtes ci-dessus. La sortie indique également la stratégie et les règles de destination qui appliquent Mutual TLS :

```console
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Déployer une version Canary de l’application

Nous allons maintenant déployer une nouvelle version `2.0` des composants `voting-app`, `voting-analytics` et `voting-storage`. Le nouveau composant `voting-storage` utilise MySQL au lieu de Redis, et les composants `voting-app` et `voting-analytics` sont mis à jour pour pouvoir utiliser ce nouveau composant `voting-storage`.

Le composant `voting-app` prend désormais en charge les fonctionnalités d’indicateur de fonctionnalité. Cet indicateur de fonctionnalité vous permet de tester les fonctionnalités du déploiement de versions Canary d’Istio pour un sous-ensemble d’utilisateurs.

Le diagramme suivant montre la configuration en place à la fin de cette section.

* La version `1.0` du composant `voting-app`, la version `1.1` du composant `voting-analytics` et la version `1.0` du composant `voting-storage` sont en mesure de communiquer.
* La version `2.0` du composant `voting-app`, la version `2.0` du composant `voting-analytics` et la version `2.0` du composant `voting-storage` sont en mesure de communiquer.
* La version `2.0` du composant `voting-app` n’est accessible qu’aux utilisateurs pour lesquels est défini un indicateur de fonctionnalité spécifique. Cette modification est gérée à l’aide d’un indicateur de fonctionnalité par le biais d’un cookie.

![Composants de l’application de vote AKS et routage](media/istio/components-and-routing-03.png)

Tout d’abord, mettez à jour les règles de destination et les services virtuels Istio pour prendre en charge ces nouveaux composants. Ces mises à jour évitent de router le trafic de manière incorrecte vers les nouveaux composants et de donner un accès inapproprié aux utilisateurs :

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

L’exemple de sortie suivant montre les règles de destination et les services virtuels en cours de mise à jour :

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Ensuite, nous allons ajouter les objets Kubernetes pour les composants disponibles dans la nouvelle version `2.0`. Vous mettez également à jour le service `voting-storage` afin d’inclure le port `3306` pour MySQL :

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

L’exemple de sortie suivant montre que les objets Kubernetes ont été correctement mis à jour ou créés :

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Attendez que tous les pods version `2.0` soient en cours d’exécution. Utilisez la commande [kubectl get pods][kubectl-get] pour afficher tous les pods dans l’espace de noms `voting` :

```azurecli
kubectl get pods --namespace voting
```

Vous devez maintenant être en mesure de basculer entre la version `1.0` et la version `2.0` (contrôle de validité) de l’application de vote. Le commutateur d’indicateur de fonctionnalité en bas de l’écran définit un cookie. Le service virtuel `voting-app` se sert de ce cookie pour diriger les utilisateurs vers la nouvelle version `2.0`.

![Version 1.0 de l’application de vote AKS : indicateur de fonctionnalité non défini](media/istio/canary-release-01.png)

![Version 2.0 de l’application de vote AKS : indicateur de fonctionnalité défini](media/istio/canary-release-02.png)

Les nombres de vote sont différents d’une version à l’autre de l’application. Cette différence reflète le fait que vous utilisez deux back-ends de stockage différents.

## <a name="finalize-the-rollout"></a>Finaliser le déploiement

Une fois que vous avez testé avec succès le contrôle de validité de mise en production, mettez à jour le service virtuel `voting-app` pour router tout le trafic vers la version `2.0` du composant `voting-app`. Tous les utilisateurs voient alors la version `2.0` de l’application, que l’indicateur de fonctionnalité soit défini ou non :

![Composants de l’application de vote AKS et routage](media/istio/components-and-routing-04.png)

Mettez à jour toutes les règles de destination pour supprimer les versions des composants que vous souhaitez désactiver. Ensuite, mettez à jour tous les services virtuels pour arrêter le référencement de ces versions.

Dans la mesure où il n’y a plus de trafic vers les versions antérieures des composants, vous pouvez supprimer tous les déploiements pour ces composants de manière sécurisée.

![Composants de l’application de vote AKS et routage](media/istio/components-and-routing-05.png)

Vous avez maintenant correctement déployé une nouvelle version de l’application de vote AKS.

## <a name="clean-up"></a>Nettoyer 

Vous pouvez supprimer l’application de vote AKS que nous avons utilisée dans ce scénario à partir de votre cluster AKS en supprimant l’espace de noms `voting` comme suit :

```azurecli
kubectl delete namespace voting
```

L’exemple de sortie suivant montre que tous les composants de l’application de vote AKS ont été supprimés de votre cluster AKS.

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez explorer des scénarios supplémentaires à l’aide de l’[exemple d’application Bookinfo d’Istio][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
