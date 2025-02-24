---
title: Méthodes d'authentification d’Azure Security Center pour l’IoT (préversion) | Microsoft Docs
description: Découvrez les différentes méthodes d’authentification disponibles avec le service Azure Security Center pour l’IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: d5701ae37d64e25fba981cd85deed2c4e4d87a15
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618337"
---
# <a name="security-agent-authentication-methods"></a>Méthodes d'authentification de l’agent de sécurité 

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article décrit les différentes méthodes d’authentification utilisables avec l’agent AzureIoTSecurity pour s’authentifier auprès d’IoT Hub.

Un module de sécurité est nécessaire pour chaque appareil intégré à Azure Security Center (ASC) pour l’IoT dans IoT Hub. Pour authentifier l’appareil, ASC pour l’IoT peut appliquer deux méthodes. Choisissez la plus adaptée à votre solution IoT actuelle. 

> [!div class="checklist"]
> * Option Module de sécurité
> * Option Appareil

## <a name="authentication-methods"></a>Méthodes d’authentification

Il existe deux méthodes permettant à l’agent AzureIoTSecurity d’effectuer l’authentification :

 - Mode d'authentification **Module**<br>
   Le Module est authentifié indépendamment du jumeau d’appareil.
   Utilisez ce type d’authentification si vous souhaitez que l’agent de sécurité utilise une méthode d’authentification dédiée par le biais du module de sécurité (clé symétrique uniquement).
        
 - Mode d'authentification **Appareil**<br>
    Dans cette méthode, l’agent de sécurité s’authentifie tout d’abord avec l’identité d’appareil. Après l’authentification initiale, l’agent ASC pour l’IoT effectue un appel **REST** à IoT Hub avec l’API REST et les données d’authentification de l’appareil. L’agent ASC pour l’IoT demande ensuite à IoT Hub la méthode d’authentification du module de sécurité et les données. Dans la dernière étape, l’agent ASC pour l’IoT effectue une authentification vis-à-vis du module ASC pour l’IoT.
    
    Utilisez ce type d’authentification si vous souhaitez que l’agent de sécurité réutilise une méthode d’authentification d’appareil existante (certificat auto-signé ou clé symétrique). 

Pour connaître la configuration, voir [Paramètres d’installation de l’agent de sécurité](#security-agent-installation-parameters).
                                
## <a name="authentication-methods-known-limitations"></a>Limitations connues des méthodes d’authentification

- Le mode d’authentification **Module** ne gère que l’authentification par clé symétrique.
- Le certificat signé par l’autorité de certification n’est pas pris en charge par le mode d’authentification **Appareil**.  

## <a name="security-agent-installation-parameters"></a>Paramètres d’installation de l’agent de sécurité

Lors du [déploiement d’un agent de sécurité](how-to-deploy-agent.md), les informations d’authentification doivent être indiquées comme arguments,
documentés dans le tableau suivant.


|Paramètre|Description|Options|
|---------|---------------|---------------|
|**identity**|mode d'authentification| **Module** ou **Device**|
|**type**|Type d'authentification|**SymmetricKey** ou **SelfSignedCertificate**|
|**filePath**|Chemin d’accès complet absolu du fichier contenant le certificat ou la clé symétrique| |
|**gatewayHostname**|FQDN du hub IoT|Exemple : ContosoIotHub.azure-devices.net|
|**deviceId**|ID de périphérique|Exemple : MyDevice1|
|**certificateLocationKind**|Emplacement de stockage des certificats|**LocalFile** ou **Store**|


Si vous utilisez le script d’installation de l’agent de sécurité, la configuration suivante est effectuée automatiquement.

Pour changer manuellement l’authentification de l’agent de sécurité, modifiez le fichier de configuration. 

## <a name="change-authentication-method-after-deployment"></a>Changer de méthode d’authentification après le déploiement

Si l’agent de sécurité est déployé avec un script d’installation, un fichier de configuration est automatiquement créé.

Pour changer de méthode d’authentification après le déploiement, il faut modifier manuellement ce fichier.


### <a name="c-based-security-agent"></a>Agent de sécurité C#

Modifiez _Authentication.config_ avec les paramètres suivants :

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>Agent de sécurité C

Modifiez _LocalConfiguration.json_ avec les paramètres suivants :

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Voir aussi
- [Vue d’ensemble des agents de sécurité](security-agent-architecture.md)
- [Déployer un agent de sécurité](how-to-deploy-agent.md)
- [Accéder aux données de sécurité brutes](how-to-security-data-access.md)
