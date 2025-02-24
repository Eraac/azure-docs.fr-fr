---
title: Guide pour installer et déployer l’agent Linux C de l’agent Azure Security Center pour IoT (préversion) | Microsoft Docs
description: Découvrez comment installer l’agent Azure Security Center pour IoT sur Linux 32 bits et 64 bits.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 7f5b98060486e6c55bb1702386cd5438f558254b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616849"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Déployer l’agent de sécurité Azure Security Center pour IoT basé sur C pour Linux

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ce guide explique comment installer et déployer l’agent de sécurité Azure Security Center (ASC) pour IoT basé sur C sur Linux.

Dans ce guide, vous apprendrez comment : 
> [!div class="checklist"]
> * Installer
> * Vérifier le déploiement
> * Désinstaller l’agent
> * Résolution des problèmes 

## <a name="prerequisites"></a>Prérequis

Pour d’autres plateformes et versions de l’agent, consultez [Choisir l’agent de sécurité adéquat](how-to-deploy-agent.md).

1. Pour déployer l’agent de sécurité, les droits d’administrateur local sont requis sur l’ordinateur sur lequel vous souhaitez effectuer l’installation (sudo).

1. [Créez un module de sécurité](quickstart-create-security-twin.md) pour l’appareil.

## <a name="installation"></a>Installation 

Pour installer et déployer l’agent de sécurité, procédez comme suit :


1. Téléchargez la version la plus récente sur votre machine depuis [GitHub](https://aka.ms/iot-security-github-c).

1. Extrayez le contenu du package et accédez au dossier _/Install_.

1. Ajoutez des autorisations en cours d’exécution au script **InstallSecurityAgent** en exécutant la commande suivante :
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Ensuite, exécutez la commande suivante : 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Pour plus d’informations sur les paramètres d’authentification, consultez [Guide pratique pour configurer l’authentification](concept-security-agent-authentication-methods.md).

Ce script effectue les opérations suivantes :

1. Installation des composants requis.

2. Ajout d’un utilisateur de service (avec connexion interactive désactivée).

3. Installation de l’agent en tant que **démon**, ce qui suppose que l’appareil utilise **systemd** pour la gestion des services.

4. Configuration de l’agent avec les paramètres d’authentification fournis. 

Pour obtenir de l’aide, exécutez le script avec le paramètre –help : 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Désinstaller l’agent

Pour désinstaller l’agent, exécutez le script avec le paramètre –-uninstall :

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>Résolution de problèmes
Vérifiez l’état du déploiement en exécutant la commande suivante :

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Étapes suivantes
- Lire la [vue d’ensemble](overview.md) du service ASC pour IoT
- Découvrir plus en détail l’[architecture](architecture.md) ASC pour IoT
- Activer le [service](quickstart-onboard-iot-hub.md)
- Consulter les [Questions fréquentes (FAQ)](resources-frequently-asked-questions.md)
- Comprendre les [alertes de sécurité](concept-security-alerts.md)