---
title: Adresses de gestion App Service Environment - Azure
description: Répertorie les adresses de gestion utilisées pour la commande d’un environnement App Service
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f76dd423cb3f7fbae6cc88d064e49dc2d56f1a1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60766051"
---
# <a name="app-service-environment-management-addresses"></a>Adresses de gestion App Service Environment

L’environnement ASE (App Service Environment) est un déploiement monolocataire d’Azure App Service, qui s’exécute sur votre réseau virtuel Azure.  Bien que l’environnement ASE s’exécute sur votre réseau virtuel, il doit toujours être accessible à partir d’un certain nombre d’adresses IP dédiées utilisées par Azure App Service pour gérer le service.  Dans le cas d’un environnement ASE, le trafic de gestion traverse le réseau contrôlé par l’utilisateur. Si ce trafic est bloqué ou incorrectement routé, l’environnement ASE est suspendu. Pour plus d’informations sur les dépendances réseau d’un ASE, consultez [Considérations relatives à la mise en réseau pour un environnement App Service Environment][networking]. Pour des informations générales sur l’environnement ASE, vous pouvez commencer par consulter la page [Présentation de l’environnement App Service Environment][intro].

Tous les environnements ASE ont une adresse IP virtuelle publique, qui reçoit le trafic de gestion. Le trafic de gestion entrant provenant de ces adresses provient des ports 454 et 455 sur l’adresse IP virtuelle publique de votre environnement ASE. Ce document répertorie les adresses IP sources App Service pour le trafic de gestion vers l’environnement ASE. Ces adresses se trouvent également dans la balise de service IP nommée AppServiceManagement.

Vous pouvez configurer les adresses indiquées ci-dessous dans une table de routage pour éviter les problèmes de routage asymétriques liés au trafic de gestion. Les routes agissent sur le trafic au niveau IP. Elles ne tiennent pas compte de la direction du trafic ou du fait que ce dernier fait partie d’un message de réponse TCP. Si l’adresse de réponse d’une requête TCP est différente de l’adresse à laquelle elle a été envoyée, vous avez un problème de routage asymétrique. Pour éviter les problèmes de routage asymétrique liés à votre trafic de gestion ASE, vous devez vérifier que les réponses sont retournées à partir de l’adresse à laquelle elles ont été envoyées. Pour plus d’informations sur la configuration de l’environnement ASE dans un environnement où le trafic sortant est envoyé en local, consultez la page [Configurer un environnement ASE avec le tunneling forcé][forcedtunnel].

## <a name="list-of-management-addresses"></a>Liste des adresses de gestion ##

| Région | Adresses |
|--------|-----------|
| Toutes les régions publiques | 13.64.115.203, 13.75.127.117, 13.94.141.115, 13.94.143.126, 13.94.149.179, 23.102.135.246, 23.102.188.65, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.124.47.188, 52.151.25.45, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 157.55.176.93, 157.55.208.185, 191.236.154.88 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Configuration d’un groupe de sécurité réseau

Avec les groupes de sécurité réseau, vous n’avez pas à vous soucier des adresses individuelles ni de la gestion de votre propre configuration. Une balise de service IP nommée AppServiceManagement est tenue à jour avec toutes les adresses. Pour utiliser cette balise de service IP dans votre NSG, accédez au portail, ouvrez l’IU des groupes de sécurité réseau, puis sélectionnez Règles de sécurité de trafic sortant. Si vous avez déjà une règle définie pour le trafic de gestion entrant, modifiez-la. Si ce groupe de sécurité réseau n’a pas été créé dans votre environnement ASE, ou s’il est entièrement nouveau, sélectionnez **Ajouter**. Dans la liste déroulante Source, sélectionnez **Balise de service**.  Sous la balise de service Source, sélectionnez **AppServiceManagement**. Définissez les plages de ports sources sur \*, la destination sur **N’importe lequel**, les plages de ports de destination sur **454-455**, le protocole sur **TCP**et l’action sur **Autoriser** . Si vous créez la règle, définissez également la priorité. 

![création d’un groupe de sécurité réseau avec la balise de service][1]

## <a name="configuring-a-route-table"></a>Configuration d’une table de routage

Vous pouvez placer les adresses de gestion dans une table de routage avec un tronçon suivant Internet pour vous assurer que tout le trafic de gestion entrant sera en mesure de revenir par le même chemin. Ces routes sont nécessaires lors de la configuration du tunneling forcé. Pour créer la table de routage, vous pouvez utiliser le portail, PowerShell ou Azure CLI.  Utilisez les commandes ci-dessous à partir d’une invite de commandes PowerShell si vous souhaitez créer une table de routage à l’aide d’Azure CLI. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    az network route-table create --name $rt --resource-group $rg --location $location
    az network route-table route create -g $rg --route-table-name $rt -n 13.64.115.203 --next-hop-type Internet --address-prefix 13.64.115.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.75.127.117 --next-hop-type Internet --address-prefix 13.75.127.117/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.141.115 --next-hop-type Internet --address-prefix 13.94.141.115/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.143.126 --next-hop-type Internet --address-prefix 13.94.143.126/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.149.179 --next-hop-type Internet --address-prefix 13.94.149.179/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.135.246 --next-hop-type Internet --address-prefix 23.102.135.246/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.188.65 --next-hop-type Internet --address-prefix 23.102.188.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.120.64 --next-hop-type Internet --address-prefix 40.83.120.64/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.121.56 --next-hop-type Internet --address-prefix 40.83.121.56/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.125.161 --next-hop-type Internet --address-prefix 40.83.125.161/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.124.47.188 --next-hop-type Internet --address-prefix 40.124.47.188/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.151.25.45 --next-hop-type Internet --address-prefix 52.151.25.45/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.152.214 --next-hop-type Internet --address-prefix 52.165.152.214/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.153.122 --next-hop-type Internet --address-prefix 52.165.153.122/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.154.193 --next-hop-type Internet --address-prefix 52.165.154.193/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.158.140 --next-hop-type Internet --address-prefix 52.165.158.140/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.174.22.21 --next-hop-type Internet --address-prefix 52.174.22.21/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.177.147 --next-hop-type Internet --address-prefix 52.178.177.147/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.184.149 --next-hop-type Internet --address-prefix 52.178.184.149/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.190.65 --next-hop-type Internet --address-prefix 52.178.190.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.195.197 --next-hop-type Internet --address-prefix 52.178.195.197/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.56.50 --next-hop-type Internet --address-prefix 52.187.56.50/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.59.251 --next-hop-type Internet --address-prefix 52.187.59.251/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.19 --next-hop-type Internet --address-prefix 52.187.63.19/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.37 --next-hop-type Internet --address-prefix 52.187.63.37/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.224.105.172 --next-hop-type Internet --address-prefix 52.224.105.172/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.225.177.153 --next-hop-type Internet --address-prefix 52.225.177.153/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.14.230 --next-hop-type Internet --address-prefix 65.52.14.230/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.172.237 --next-hop-type Internet --address-prefix 65.52.172.237/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.193.203 --next-hop-type Internet --address-prefix 65.52.193.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.57.58 --next-hop-type Internet --address-prefix 70.37.57.58/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.89.222 --next-hop-type Internet --address-prefix 70.37.89.222/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.141 --next-hop-type Internet --address-prefix 104.44.129.141/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.243 --next-hop-type Internet --address-prefix 104.44.129.243/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.255 --next-hop-type Internet --address-prefix 104.44.129.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.134.255 --next-hop-type Internet --address-prefix 104.44.134.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.208.54.11 --next-hop-type Internet --address-prefix 104.208.54.11/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.176.93 --next-hop-type Internet --address-prefix 157.55.176.93/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.208.185 --next-hop-type Internet --address-prefix 157.55.208.185/32
    az network route-table route create -g $rg --route-table-name $rt -n 191.236.154.88 --next-hop-type Internet --address-prefix 191.236.154.88/32

Après avoir créé la table de routage, vous devez la définir sur votre sous-réseau ASE.  

## <a name="get-your-management-addresses-from-api"></a>Récupérer les adresses de gestion avec l’API ##

Vous pouvez lister les adresses de gestion qui correspondent à votre environnement ASE avec l’appel d’API suivant.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

L’API retourne un document JSON qui contient toutes les adresses de trafic entrant de votre environnement ASE. La liste des adresses inclut les adresses de gestion, l’adresse IP virtuelle utilisée par votre environnement ASE et la plage d’adresses de sous-réseau ASE elle-même.  

Pour appeler l’API avec [armclient](https://github.com/projectkudu/ARMClient), utilisez les commandes suivantes en indiquant votre ID d’abonnement, votre groupe de ressources et le nom de l’environnement ASE.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
