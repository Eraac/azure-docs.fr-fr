---
title: Architecture de connectivité dans Azure Database pour MySQL
description: Décrit l’architecture de connectivité pour le serveur Azure Database pour MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 7a7ac843960e253b3172d1ed22fe5b59633897dc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062465"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Architecture de connectivité dans Azure Database pour MySQL
Cet article présente l’architecture de connectivité d’Azure Database pour MySQL, ainsi que la façon dont le trafic est dirigé vers l’instance Azure Database pour MySQL des clients dans Azure et en dehors.

## <a name="connectivity-architecture"></a>Architecture de connectivité
La connexion à l’instance Azure Database pour MySQL est établie via une passerelle qui est responsable du routage des connexions entrantes vers l’emplacement physique de votre serveur dans nos clusters. Le schéma suivant illustre le flux de trafic.

![Vue d’ensemble de l’architecture de connectivité](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Quand le client se connecte à la base de données, il obtient une chaîne de connexion qui se connecte à la passerelle. Cette passerelle a une adresse IP publique qui écoute le port 3306. À l’intérieur du cluster de bases de données, le trafic est transféré vers l’instance Azure Database pour MySQL appropriée. Par conséquent, pour vous connecter à votre serveur, notamment à partir de réseaux d’entreprise, il est nécessaire d’ouvrir le pare-feu côté client pour autoriser le trafic sortant à atteindre nos passerelles. Vous trouverez ci-dessous une liste complète des adresses IP utilisées par nos passerelles en fonction de la région.

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>Adresses IP de passerelle Azure Database pour MySQL
Le tableau suivant répertorie les adresses IP principales et secondaires de la passerelle Azure Database pour MySQL pour toutes les régions de données. L’adresse IP principale est l’adresse IP actuelle de la passerelle et l’adresse IP secondaire est une adresse IP de basculement en cas de défaillance de la première. Comme mentionné, les clients doivent autoriser le trafic sortant vers les deux adresses IP. La deuxième adresse IP n’écoute pas les services tant qu’elle n’est pas activée par Azure Database pour MySQL de manière à accepter les connexions.

| **Nom de la région** | **Adresse IP principale** | **Adresse IP secondaire** |
|:----------------|:-------------|:------------------------|
| Australie Est | 13.75.149.87 | 40.79.161.1 |
| Sud-Est de l’Australie | 191.239.192.109 | 13.73.109.251 |
| Brésil Sud | 104.41.11.5 | |
| Centre du Canada | 40.85.224.249 | |
| Est du Canada | 40.86.226.166 | |
| USA Centre | 23.99.160.139 | 13.67.215.62 |
| Chine Est 1 | 139.219.130.35 | |
| Chine Est 2 | 40.73.82.1 | |
| Chine Nord 1 | 139.219.15.17 | |
| Chine Nord 2 | 40.73.50.0 | |
| Asie Est | 191.234.2.139 | 52.175.33.150 |
| USA Est 1 | 191.238.6.43 | 40.121.158.30 |
| USA Est 2 | 191.239.224.107 | 40.79.84.180 * |
| France Centre | 40.79.137.0 | 40.79.129.1 |
| Centre de l’Allemagne | 51.4.144.100 | |
| Inde Centre | 104.211.96.159 | |
| Sud de l’Inde | 104.211.224.146 | |
| Inde Ouest | 104.211.160.80 | |
| Japon Est | 191.237.240.43 | 13.78.61.196 |
| Japon Ouest | 191.238.68.11 | 104.214.148.156 |
| Centre de la Corée | 52.231.32.42 | |
| Corée du Sud | 52.231.200.86 |  |
| USA Centre Nord | 23.98.55.75 | 23.96.178.199 |
| Europe Nord | 191.235.193.75 | 40.113.93.91 |
| USA Centre Sud | 23.98.162.75 | 13.66.62.124 |
| Asie Sud-Est | 23.100.117.95 | 104.43.15.0 |
| Sud du Royaume-Uni | 51.140.184.11 | |
| Ouest du Royaume-Uni | 51.141.8.11| |
| Europe Ouest | 191.237.232.75 | 40.68.37.158 |
| USA Ouest 1 | 23.99.34.75 | 104.42.238.205 |
| USA Ouest 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *USA Est 2* possède également l'adresse IP tertiaire `52.167.104.0`.

## <a name="next-steps"></a>Étapes suivantes

* [Créer et gérer des règles de pare-feu Base de données Azure pour MySQL à l’aide du portail Azure](./howto-manage-firewall-using-portal.md)
* [Créer et gérer des règles de pare-feu Azure Database pour MySQL à l’aide de l’interface de ligne de commande Azure](./howto-manage-firewall-using-cli.md)

