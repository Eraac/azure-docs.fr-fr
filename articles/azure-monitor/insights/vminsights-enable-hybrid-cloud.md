---
title: Activer Azure Monitor (préversion) pour un environnement hybride | Microsoft Docs
description: Cet article décrit comment activer des Azure Monitor pour machines virtuelles pour un environnement de cloud hybride contenant une ou plusieurs machines virtuelles.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/07/2019
ms.author: magoedte
ms.openlocfilehash: bc26cc0654aac9416bf31ffccf426648e3a8b8d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122544"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Activer Azure Monitor pour machines virtuelles (préversion) pour un environnement hybride

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Cet article explique comment activer Azure Monitor pour machines virtuelles (préversion) pour des machines virtuelles ou des ordinateurs physiques hébergés dans votre centre de données ou un autre environnement cloud. À la fin de ce processus, vous aurez commencé à analyser vos machines virtuelles dans votre environnement et saurez reconnaître les problèmes de performances ou de disponibilité. 

Avant de commencer, veillez à consulter les [conditions préalables](vminsights-enable-overview.md) et vérifiez que votre abonnement et vos ressources répondent aux exigences. Passez en revue les exigences et les méthodes de déploiement pour l’[agent Log Analytique Linux et Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>L’agent Map Dependency Agent Azure Monitor pour machines virtuelles ne transmet pas de données par lui-même et ne requiert pas de modifications des pare-feu ni des ports. Log Analytics Agent transmet toujours les données Map au service Azure Monitor, directement ou par le biais de la [passerelle Operations Management Suite](../../azure-monitor/platform/gateway.md), si vos stratégies de sécurité n’autorisent pas les ordinateurs du réseau à se connecter à Internet.

Les étapes pour effectuer cette tâche sont récapitulées comme suit :

1. Installez l’agent Log Analytics pour Windows ou Linux. Avant d’installer l’agent, passez en revue l’article [Présentation de l’agent Log Analytics](../platform/log-analytics-agent.md) pour comprendre les conditions requises pour le système et les méthodes de déploiement.

2. Téléchargez et installez Map Dependency Agent Azure Monitor pour machines virtuelles pour [Windows](https://aka.ms/dependencyagentwindows) ou [Linux](https://aka.ms/dependencyagentlinux).

3. Activez la collecte des compteurs de performance.

4. Déployez Azure Monitor pour machines virtuelles.

## <a name="install-the-dependency-agent-on-windows"></a>Installer l’agent de dépendances sous Windows
Vous pouvez installer Dependency Agent manuellement sur les ordinateurs Windows en exécutant `InstallDependencyAgent-Windows.exe`. Si vous exécutez ce fichier exécutable sans aucune option, il démarre un assistant de configuration que vous pouvez suivre pour une installation interactive de l’agent.

>[!NOTE]
>Des privilèges d’*administrateur* sont requis pour installer ou désinstaller l’agent.

Le tableau suivant répertorie les paramètres pris en charge par le programme de configuration pour l’agent à partir de la ligne de commande.

| Paramètre | Description |
|:--|:--|
| /? | Retourne la liste des options de ligne de commande. |
| /S | Effectue une installation silencieuse sans interaction utilisateur. |

Par exemple, pour exécuter le programme d’installation avec le paramètre `/?`, saisissez **InstallDependencyAgent-Windows.exe /?** .

Par défaut, les fichiers Dependency Agent pour Windows sont installés dans le répertoire *C:\Program Files\Microsoft Dependency Agent*. Si le démarrage de l’agent de dépendances échoue une fois l’installation terminée, recherchez des informations détaillées sur l’erreur dans les journaux d’activité. Le répertoire des journaux d’activité est *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Installer l’agent de dépendances sous Linux
Dependency Agent est installé sur les ordinateurs Linux avec *InstallDependencyAgent-Linux64.bin*, script shell comportant un fichier binaire à extraction automatique. Vous pouvez exécuter le fichier à l’aide de `sh` ou ajouter des autorisations d’exécution au fichier lui-même.

>[!NOTE]
> L’accès racine est requis pour installer ou configurer l’agent.
>

| Paramètre | Description |
|:--|:--|
| -help | Récupérez la liste des options de ligne de commande. |
| -s | Effectuez une installation silencieuse sans invite utilisateur. |
| --check | Vérifiez les autorisations et le système d’exploitation, sans installer l’agent. |

Par exemple, pour exécuter le programme d’installation avec le paramètre `-help`, saisissez **InstallDependencyAgent-Linux64.bin -help**.

Installez Dependency Agent pour Linux en tant que racine en exécutant la commande `sh InstallDependencyAgent-Linux64.bin`.

Si le démarrage de l’agent de dépendances échoue, recherchez des informations détaillées sur l’erreur dans les journaux d’activité. Sur les agents Linux, le répertoire des journaux est */var/opt/microsoft/dependency-agent/log*.

Les fichiers de l’agent de dépendances sont placés dans les répertoires suivants :

| Fichiers | Lieu |
|:--|:--|
| Fichiers principaux | /opt/microsoft/dependency-agent |
| Fichiers journaux | /var/opt/microsoft/dependency-agent/log |
| Fichiers de configuration | /etc/opt/microsoft/dependency-agent/config |
| Fichiers exécutables de service | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Fichiers de stockage binaires | /var/opt/microsoft/dependency-agent/storage |

## <a name="enable-performance-counters"></a>Activer les compteurs de performance
Si l’espace de travail Log Analytics référencé par la solution n’est pas déjà configuré pour collecter les compteurs de performance requis par la solution, vous devez activer ces derniers. Il existe deux méthodes pour le faire :
* Manuellement, tel que décrit dans [Sources de données de performance Windows et Linux dans Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* En téléchargeant et en exécutant un script PowerShell disponible dans [Azure PowerShell Gallery](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Déployer Azure Monitor pour machines virtuelles
Cette méthode inclut un modèle JSON spécifiant la configuration requise pour activer les composants de la solution dans votre espace de travail Log Analytics.

Si vous ne savez pas comment déployer des ressources à l’aide d’un modèle, consultez :
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Pour utiliser Azure CLI, vous devez d’abord l’installer et l’utiliser localement. Vous devez exécuter Azure CLI 2.0.27 ou version ultérieure. Pour identifier votre version, exécutez `az --version`. Pour installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Créer et exécuter un modèle

1. Copiez et collez la syntaxe JSON suivante dans votre fichier :

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Enregistrez ce fichier sous *installsolutionsforvminsights.json* dans un dossier local.

1. Capturez les valeurs pour *WorkspaceName*, *ResourceGroupName* et *WorkspaceLocation*. La valeur de *WorkspaceName* est le nom de votre espace de travail Log Analytics. La valeur de *WorkspaceLocation* correspond à la région dans laquelle l’espace de travail est défini.

1. Vous êtes prêt à déployer ce modèle à l’aide de la commande PowerShell suivante :

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire à celui-ci s’affiche avec les résultats :

    ```powershell
    provisioningState       : Succeeded
    ```
   Une fois que vous avez activé la supervision, 10 minutes peuvent s’écouler avant que vous ne puissiez voir les métriques et l’état d’intégrité pour l’ordinateur hybride.

## <a name="next-steps"></a>Étapes suivantes

Une fois la surveillance activée pour vos machines virtuelles, ces informations peuvent être analysées par Azure Monitor pour machines virtuelles.
 
- Pour savoir comment utiliser la fonctionnalité de contrôle de l’intégrité, consultez [Comprendre l’intégrité de vos machines virtuelles Azure grâce à Azure Monitor pour machines virtuelles](vminsights-health.md).
- Pour afficher les dépendances des applications détectées, consultez [Utilisation de la fonctionnalité Map d’Azure Monitor pour machines virtuelles dans le but de comprendre les composants d’application](vminsights-maps.md).
- Pour identifier les goulots d’étranglement et l’utilisation globale avec les performances de votre machine virtuelle, consultez [Afficher les performances des machines virtuelles Azure](vminsights-performance.md).
- Pour afficher les dépendances des applications détectées, consultez [Utilisation de la fonctionnalité Map d’Azure Monitor pour machines virtuelles dans le but de comprendre les composants d’application](vminsights-maps.md).