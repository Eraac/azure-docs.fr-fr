---
title: Adresses IP entrantes/sortantes - Azure App Service | Microsoft Docs
description: Explique comment les adresses IP entrantes et sortantes sont utilisées dans App Service et comment trouver des informations sur ces adresses pour votre application.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: de9ae8e5c0cbf0997811db9624f6c6b92e03a5df
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66742933"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Adresses IP entrantes et sortantes dans Azure App Service

[Azure App Service](overview.md) est un service mutualisé, à l’exception des [environnements App Service](environment/intro.md). Les applications qui ne sont pas dans un environnement App Service (pas dans le [niveau Isolé](https://azure.microsoft.com/pricing/details/app-service/)) partagent l’infrastructure réseau avec d’autres applications. Par conséquent, les adresses IP entrantes et sortantes d’une application peuvent être différentes et peuvent même changer dans certaines situations. 

Les [environnements App Service](environment/intro.md) utilisent des infrastructures réseau dédiées, afin que les applications s’exécutant dans un environnement App Service obtiennent des adresses IP statiques dédiées pour les connexions entrantes et sortantes.

## <a name="when-inbound-ip-changes"></a>Lorsque l’adresse IP entrante change

Quel que soit le nombre d’instances scale-out, chaque application a une seule adresse IP entrante. L’adresse IP entrante peut changer lorsque vous effectuez l’une des actions suivantes :

- Supprimer une application, puis la recréer dans un autre groupe de ressources.
- Supprimer la dernière application dans une combinaison de groupe de ressources _et_ de région, puis la recréer.
- Supprimer une liaison SSL existante, comme pendant un renouvellement de certificat (voir [Renouveler des certificats](app-service-web-tutorial-custom-ssl.md#renew-certificates)).

## <a name="find-the-inbound-ip"></a>Rechercher l’IP entrante

Il vous suffit d’exécuter la commande suivante dans un terminal local :

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Obtenir une adresse IP entrante statique

Vous pouvez avoir besoin d’une adresse IP statique dédiée pour votre application. Pour obtenir une adresse IP entrante statique, vous devez configurer une [liaison SSL basée sur l’adresse IP](app-service-web-tutorial-custom-ssl.md#secure-a-custom-domain). Si vous n’avez pas besoin de la fonctionnalité SSL pour sécuriser votre application, vous pouvez même télécharger un certificat auto-signé pour cette liaison. Dans une liaison SSL basée sur l’adresse IP, le certificat est lié à l’adresse IP elle-même. App Service fournit donc une adresse IP statique pour que cela se produise. 

## <a name="when-outbound-ips-change"></a>Lorsque les adresses IP sortantes changent

Quel que soit le nombre d’instances scale-out, chaque application a un nombre défini d’adresses IP sortantes à un moment donné. Toute connexion sortante depuis l’application App Service, par exemple la connexion à une base de données principale, utilise l’une des adresses IP sortantes comme adresse IP d’origine. Vous ne pouvez pas savoir à l’avance quelle adresse IP sera utilisée par une instance d’application donnée pour établir la connexion sortante, de sorte que votre service principal doit ouvrir son pare-feu à toutes les adresses IP sortantes de votre application.

L’ensemble d’adresses IP sortantes de votre application change lorsque vous faites évoluer votre application entre les niveaux inférieurs (**De base**, **Standard** et **Premium**) et le niveau **Premium V2**.

Vous pouvez trouver toutes les adresses IP sortantes que votre application est susceptible d’utiliser, indépendamment des niveaux de tarification, en recherchant la propriété `possibleOutboundIPAddresses`, ou à l’aide du champ **Adresses IP sortantes supplémentaires** du panneau **Propriétés** du portail Azure. Consultez [Trouver des adresses IP sortantes](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Trouver des adresses IP sortantes

Pour trouver les adresses IP sortantes actuellement utilisées par votre application dans le portail Azure, cliquez sur **Propriétés** dans le volet de navigation gauche de votre application. Elles sont répertoriées dans le champ **Adresses IP sortantes**.

Vous pouvez obtenir les mêmes informations en exécutant la commande suivante dans [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Pour rechercher _toutes_ les adresses IP sortantes possibles pour votre application, indépendamment des niveaux de tarification, cliquez sur **Propriétés** dans la navigation à gauche de votre application. Elles sont répertoriées dans le champ **Adresses IP sortantes supplémentaires**.

Vous pouvez obtenir les mêmes informations en exécutant la commande suivante dans [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment limiter le trafic entrant par adresses IP sources.

> [!div class="nextstepaction"]
> [Restrictions d’adresse IP statique](app-service-ip-restrictions.md)
