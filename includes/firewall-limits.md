---
title: Fichier Include
description: Fichier Include
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 5/3/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8709d4d903bd31ff94d04ec61e226857e4190407
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177438"
---
| Ressource | Limite par défaut |
| --- | --- |
| Débit de données |30 Gbits/s<sup>1</sup> |
|Règles|10 000, combinaison de tous les types de règles.|
|Taille minimale d’AzureFirewallSubnet |/26|
|Plage de ports dans les règles de réseau et d’application|0-64,000. Nous nous efforçons actuellement d’assouplir cette limitation.|
|Table de routage|Par défaut, AzureFirewallSubnet a un itinéraire 0.0.0.0/0 avec la valeur NextHopType définie sur **Internet**.<br><br>Le Pare-feu Azure doit avoir une connectivité Internet directe. Si votre AzureFirewallSubnet prend connaissance d’un itinéraire par défaut pour votre réseau local via le protocole BGP, vous devez le remplacer par un UDR 0.0.0.0/0 avec la valeur **NextHopType** définie sur **Internet** pour garantir une connectivité Internet directe. Par défaut, Pare-feu Azure ne prend en charge le tunneling forcé vers un réseau local.<br><br>Toutefois, si votre configuration nécessite un tunneling forcé vers un réseau local, Microsoft le prendra en charge au cas par cas. Contactez le support technique afin qu’il puisse étudier votre cas. Si votre dossier est accepté, nous ajouterons votre abonnement à la liste verte afin de garantir le maintien de la connectivité Internet du pare-feu.|

<sup>1</sup>Pour augmenter ces limites, contactez le support Azure.
