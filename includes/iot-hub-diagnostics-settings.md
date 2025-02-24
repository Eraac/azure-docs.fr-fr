---
title: Fichier Include
description: Fichier Include
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 3893b79cee96c3928897f64f3601ebe4c490ebdd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177171"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Activer la journalisation avec les paramètres de diagnostic

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.

2. Sélectionnez **Paramètres de diagnostic**.

3. Sélectionnez **Activer les diagnostics**.

   ![Activer les diagnostics](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Nommez les paramètres de diagnostic.

5. Indiquez où vous voulez envoyer les journaux d’activité. Vous pouvez sélectionner n’importe quelle combinaison des trois options suivantes :

   * Archiver dans un compte de stockage
   * Diffuser vers un hub d’événements
   * Envoyer à Log Analytics

6. Choisissez les opérations à surveiller et activez les journaux d’activité de ces opérations. Les opérations sur lesquelles les paramètres de diagnostic peuvent établir des rapports sont :

   * connexions
   * Télémétrie d’appareil
   * Messages Cloud vers appareil
   * Opérations d’identité des appareils
   * Chargements de fichiers
   * Routage de messages
   * Opérations jumelles cloud à appareil
   * Opérations jumelles appareil à cloud
   * Opérations jumelles
   * Opérations de travail
   * Méthodes directes  
   * Traçage distribué (préversion)
   * Configurations
   * Flux d’appareil
   * Mesures de l’appareil

6. Enregistrez les nouveaux paramètres. 

Si vous voulez activer les paramètres de diagnostic avec PowerShell, utilisez le code suivant :

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Les nouveaux paramètres prennent effet au bout de 10 minutes environ. Après cela, les journaux d’activité apparaissent dans la cible d’archivage configurée, dans le panneau **Paramètres de diagnostic**. Pour en savoir plus sur la configuration des diagnostics, consultez [Collecter et utiliser des données de journaux à partir de vos ressources Azure](../articles/azure-monitor/platform/diagnostic-logs-overview.md).
