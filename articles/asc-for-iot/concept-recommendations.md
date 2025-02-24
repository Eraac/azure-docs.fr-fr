---
title: Comprendre les recommandations de sécurité Azure Security Center pour l’IoT (préversion) | Microsoft Docs
description: Découvrez le concept de recommandations de sécurité et la manière dont elles sont utilisées dans Azure Security Center pour l’IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 1ee71bbacdba7a14e94de41563a04be9c0f00d13
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618408"
---
# <a name="security-recommendations"></a>Recommandations de sécurité

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Security Center (ASC) pour l’IoT analyse les ressources Azure et les appareils IoT et propose des recommandations de sécurité actionnables visant à réduire la surface d’attaque et à aider les clients à se conformer aux meilleures pratiques de sécurité.

Vous trouverez dans cet article la liste des recommandations qui peuvent être déclenchées sur un hub IoT ou des appareils IoT.

## <a name="recommendations-for-iot-devices"></a>Recommandations pour les appareils IoT

Les recommandations portant sur les appareils offrent des insights et des suggestions d’amélioration de la posture de sécurité des appareils. 

| Severity | Nom                                                      | source de données | Description                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Moyenne   | Ports ouverts sur l’appareil                                      | Agent       | Un point de terminaison d’écoute a été trouvé sur l’appareil.                                                                                                                                                          |
| Moyenne   | Stratégie de pare-feu permissive détectée dans l’une des chaînes | Agent       | Une stratégie de pare-feu autorisée a été trouvée (ENTRÉE/SORTIE). Une stratégie de pare-feu doit par défaut refuser tout le trafic et définir des règles pour autoriser la communication nécessaire à l’appareil.                               |
| Moyenne   | Règle de pare-feu permissive détectée dans la chaîne d’entrée     | Agent       | Une règle contenant un modèle permissif pour un large éventail d’adresses IP ou de ports a été trouvée dans le pare-feu.                                                                                    |
| Moyenne   | Règle de pare-feu permissive détectée dans la chaîne de sortie    | Agent       | Une règle contenant un modèle permissif pour un large éventail d’adresses IP ou de ports a été trouvée dans le pare-feu.                                                                                   |
| Moyenne   | Échec de l’opération de validation du système par rapport à la référence           | Agent       | L’appareil n’est pas conforme aux [benchmarks CIS Linux](https://www.cisecurity.org/cis-benchmarks/).                                                                                                         |

### <a name="operational-recommendations-for-iot-devices"></a>Recommandations opérationnelles pour les appareils IoT

Les recommandations opérationnelles offrent des insights et des suggestions d’amélioration de la configuration de l’agent de sécurité.

| Severity | Nom                                    | source de données | Description                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Faible      | L’agent envoie des messages inutilisés          | Agent       | Au moins 10 % des messages de sécurité étaient inférieurs à 4 Ko au cours des dernières 24 heures.  |
| Faible      | Configuration du jumeau de sécurité non optimale | Agent       | La configuration du jumeau de sécurité n’est pas optimale.                                        |
| Faible      | Conflit de configuration du jumeau de sécurité    | Agent       | Des conflits ont été identifiés dans la configuration du jumeau de sécurité.                           |


## <a name="recommendations-for-iot-hub"></a>Recommandations pour IoT Hub

Les alertes de recommandation offrent des insights et des suggestions d’actions visant à améliorer la posture de sécurité de l’environnement.  

| Severity | Nom                                                     | source de données | Description                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Élevé     | Informations d’authentification identiques utilisées par plusieurs appareils | IoT Hub     | Des informations d’authentification IoT Hub sont utilisées par plusieurs appareils, ce qui peut indiquer qu’un appareil illégitime usurpe l’identité d’un appareil légitime. Les doublons d’informations d’identification augmentent le risque d’emprunt d’identité de l’appareil par un acteur malveillant. |
| Moyenne   | La stratégie de filtre IP par défaut devrait être de refuser                  | IoT Hub     | La configuration du filtre IP devrait comporter des règles concernant le trafic autorisé et, par défaut, refuser le reste du trafic.                                                                                                     |
| Moyenne   | Grande plage d’adresses IP dans une règle de filtre IP                   | IoT Hub     | La plage d’adresses IP source d’une des règles de filtre IP Autoriser est trop grande. Des règles trop permissives risquent d’exposer le hub IoT à des personnes malveillantes.                                                                                       |
| Faible      | Activer les journaux de diagnostic dans IoT Hub                       | IoT Hub     | Activez les journaux d’activité et conservez-les pendant jusqu’à un an. Le fait de conserver les journaux permet de recréer les pistes d’activités à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau.                                       |
|