---
title: Configurer des alertes sur les événements des journaux de diagnostic à partir de la passerelle VPN Azure
description: Étapes de configuration des alertes sur les événements des journaux de diagnostic de la passerelle VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: c84d457c51f71bdf315bbbcec674ff1186dd905f
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249013"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Configurer des alertes sur les événements des journaux de diagnostic à partir de la passerelle VPN

Cet article vous permet de configurer des alertes basées sur les événements des journaux de diagnostic depuis la passerelle VPN Azure avec Azure Log Analytics. 

Les journaux suivants sont disponibles dans Azure :

|***Nom*** | ***Description*** |
|---        | ---               |
|GatewayDiagnosticLog | Contient les journaux de diagnostic des événements de configuration de passerelle, des modifications principales et des événements de maintenance |
|TunnelDiagnosticLog | Contient des événements de changement d’état de tunnel. Les événements de connexion/déconnexion de tunnel affichent une brève explication du changement d’état, le cas échéant |
|RouteDiagnosticLog | Consigne les modifications apportées aux routes statiques et aux événements BGP qui se produisent sur la passerelle |
|IKEDiagnosticLog | Consigne les événements et messages de contrôle IKE sur la passerelle |
|P2SDiagnosticLog | Consigne les événements et messages de contrôle point à site sur la passerelle |

## <a name="setup"></a>Configurer des alertes

Les étapes de l’exemple suivant crée une alerte pour un événement de déconnexion qui implique un tunnel VPN de site à site :


1. Dans le portail Azure, recherchez **Log Analytics** sous **Tous les services** et sélectionnez **Espaces de travail Log Analytics**.

   ![Sélections permettant d’accéder aux espaces de travail Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Créer")

2. Sélectionnez **Créer** dans la page **Log Analytics**.

   ![Page Log Analytics avec le bouton Créer](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Sélectionner")

3. Sélectionnez **Créer** et renseignez les détails.

   ![Détails permettant la création d’un espace de travail Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Sélectionner")

4. Recherchez votre passerelle VPN dans le panneau **Monitor** > **Paramètres de diagnostic**.

   ![Sélections permettant de rechercher la passerelle VPN dans Paramètres de diagnostic](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Sélectionner")

5. Pour activer les diagnostics, double-cliquez sur la passerelle, puis sélectionnez **Activer les diagnostics**.

   ![Sélections permettant l’activation des diagnostics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Sélectionner")

6. Renseignez les détails, puis vérifiez que les options **Envoyer à Log Analytics** et **TunnelDiagnosticLog** sont sélectionnées. Choisissez l’espace de travail Log Analytics que vous avez créé à l’étape 3.

   ![Cases à cocher sélectionnées](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Sélectionner")

7. Accédez à la vue d’ensemble de la ressource de passerelle réseau virtuel et sélectionnez **Alertes** à partir de l’onglet **Supervision**. Ensuite, créez une règle d’alerte ou modifiez une règle d’alerte existante.

   ![Sélections permettant la création d’une règle d’alerte](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Sélectionner")

   ![Point à site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Sélectionner")
8. Sélectionnez l’espace de travail Log Analytics et la ressource.

   ![Sélections pour l’espace de travail et la ressource](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Sélectionner")

9. Sélectionnez **Recherche personnalisée dans les journaux** comme logique de signal sous **Ajouter une condition**.

   ![Sélections permettant une recherche personnalisée dans les journaux](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Sélectionner")

10. Entrez la requête suivante dans la zone de texte **Requête de recherche**. Remplacez les valeurs figurant dans <> comme il convient.

    ```
    AzureDiagnostics |
      where Category  == "TunnelDiagnosticLog" and ResourceId == toupper("<RESOURCEID OF GATEWAY>") and TimeGenerated > ago(5m) and
      remoteIP_s == "<REMOTE IP OF TUNNEL>" and status_s == "Disconnected"
    ```

    Définissez la valeur de seuil sur 0 et sélectionnez **Terminé**.

    ![Entrée d’une requête et sélection d’un seuil](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Sélectionner")

11. Dans la page **Créer une règle**, sélectionnez **Créer** sous la section **GROUPES D’ACTIONS**. Renseignez les détails, puis sélectionnez **OK**.

    ![Détails d’un nouveau groupe d’actions](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Sélectionner")

12. Dans la page **Créer une règle**, renseignez les détails pour **Personnaliser les actions** et assurez-vous que le nom correct apparaît dans la section **NOM DU GROUPE D’ACTIONS**. Sélectionnez **Créer une règle d’alerte** pour créer la règle.

    ![Sélections permettant la création d’une règle](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Sélectionner")

## <a name="next-steps"></a>Étapes suivantes

Pour configurer des alertes sur les métriques de tunnel, consultez [Configurer des alertes sur les métriques de passerelle VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
