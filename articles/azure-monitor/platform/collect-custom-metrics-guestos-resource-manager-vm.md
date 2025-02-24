---
title: Envoyer des métriques de système d’exploitation invité vers le magasin de métriques d’Azure Monitor à l’aide d’un modèle Resource Manager pour une machine virtuelle Windows
description: Envoyer des métriques de système d’exploitation invité vers le magasin de métriques d’Azure Monitor à l’aide d’un modèle Resource Manager pour une machine virtuelle Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 85f7395cbfa4ef2ba6ab448c9541b3f107eb0e96
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249820"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Envoyer des métriques de système d’exploitation invité vers le magasin de métriques d’Azure Monitor à l’aide d’un modèle Resource Manager pour une machine virtuelle Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

À l’aide de l’[extension Diagnostics](diagnostics-extension-overview.md) d’Azure Monitor, vous pouvez collecter des métriques et des journaux d’activité à partir du système d’exploitation invité (SE invité) qui est exécuté dans le cadre d’une machine virtuelle, d’un service cloud ou d’un cluster Service Fabric. L’extension peut envoyer des données de télémétrie à de [nombreux emplacements différents](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json).

Cet article décrit le processus pour envoyer les métriques de performance du SE invité d’une machine virtuelle Windows vers le magasin de données d’Azure Monitor. À partir de la version 1.11 de l’extension Diagnostics, vous pouvez écrire des métriques directement dans le magasin de métriques Azure Monitor, où les métriques standard de la plateforme sont déjà collectées.

En les stockant dans cet emplacement, vous avez accès aux mêmes actions que celles disponibles pour les métriques de la plateforme. Ces actions incluent notamment la génération d’alertes en temps quasi réel, la création de graphiques, le routage, l’accès à partir d’une API REST, etc. Avant, l’extension Diagnostics écrivait les données dans le stockage Azure, et non dans le magasin de données d’Azure Monitor.

Si vous découvrez les modèles Resource Manager, obtenez plus d’informations sur les [déploiements de modèle](../../azure-resource-manager/resource-group-overview.md), leur structure et leur syntaxe.

## <a name="prerequisites"></a>Prérequis

- Votre abonnement doit être inscrit auprès de [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- Vous devez avoir installé [Azure PowerShell](/powershell/azure) ou [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurer Azure Monitor en tant que récepteur de données
L’extension Diagnostics Azure utilise une fonctionnalité appelée « récepteurs de données » pour acheminer les métriques et les journaux vers différents emplacements. Les étapes suivantes montrent comment utiliser un modèle Resource Manager et PowerShell pour déployer une machine virtuelle à l’aide du nouveau récepteur de données « Azure Monitor ».

## <a name="author-resource-manager-template"></a>Créer un modèle Resource Manager
Pour cet exemple, vous pouvez utiliser un exemple de modèle rendu public. Les modèles de démarrage se trouvent dans https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows.

- **Azuredeploy.json** est un modèle Resource Manager préconfiguré pour le déploiement d’une machine virtuelle.

- **Azuredeploy.parameters.json** est un fichier de paramètres qui stocke des informations telles que le nom d’utilisateur et le mot de passe que vous souhaitez définir pour votre machine virtuelle. Pendant le déploiement, le modèle Resource Manager utilise les paramètres définis dans ce fichier.

Téléchargez et enregistrez les deux fichiers en local.

### <a name="modify-azuredeployparametersjson"></a>Modifier le fichier azuredeploy.parameters.json
Ouvrez le fichier *azuredeploy.parameters.json*

1. Entrez les valeurs **adminUsername** et **adminPassword** pour la machine virtuelle. Ces paramètres sont utilisés pour l’accès à distance à la machine virtuelle. Pour éviter tout accès non autorisé à votre machine virtuelle, N’UTILISEZ PAS les valeurs de ce modèle. Les bots recherchent sur Internet des noms d’utilisateur et mots de passe dans les référentiels GitHub publics. Avec ces valeurs par défaut, ils sont susceptibles de se transformer en machines virtuelles de test.

1. Créez un dnsname unique pour la machine virtuelle.

### <a name="modify-azuredeployjson"></a>Modifier le fichier azuredeploy.json

Ouvrez le fichier *azuredeploy.json*

Ajoutez un ID de compte de stockage à la section **variables** du modèle après l’entrée **storageAccountName**.

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Ajoutez cette extension MSI (Managed Service Identity) au modèle en haut de la section **ressources**. L’extension garantit qu’Azure Monitor accepte les métriques émises.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'), '/', 'WADExtensionSetup')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    },
```

Ajoutez la configuration de l’**identité** à la ressource de la machine virtuelle pour vous assurer qu’Azure attribue une identité système à l’extension MSI. Cette étape permet de s’assurer que la machine virtuelle peut envoyer des métriques invitées la concernant à Azure Monitor.

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    // add these 3 lines below
    "identity": {
    "type": "SystemAssigned"
    },
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {
    ...
```

Ajoutez la configuration suivante pour activer l’extension Diagnostics sur une machine virtuelle Windows. Pour une simple machine virtuelle basée sur Resource Manager, nous pouvons ajouter la configuration de l’extension au tableau Ressources de la machine virtuelle. La ligne « récepteurs » &mdash; « AzMonSink » et la configuration correspondante « SinksConfig » plus loin dans la section &mdash; permettent à l’extension d’envoyer les métriques directement à Azure Monitor. N’hésitez pas à ajouter ou supprimer des compteurs de performances si nécessaire.


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]"
    }
}
},
//Start of section to add
"resources": [
{
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'), '/', 'Microsoft.Insights.VMDiagnosticsSettings')]",
            "apiVersion": "2017-12-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.12",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "WadCfg": {
                "DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
        },
                    "Directories": {
                    "scheduledTransferPeriod": "PT1M",
    "IISLogs": {
                        "containerName": "wad-iis-logfiles"
                    },
                    "FailedRequestLogs": {
                        "containerName": "wad-failedrequestlogs"
                    }
                    },
                    "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "sinks": "AzMonSink",
                    "PerformanceCounterConfiguration": [
                        {
                        "counterSpecifier": "\\Memory\\Available Bytes",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\Committed Bytes",
                        "sampleRate": "PT15S"
                        }
                    ]
                    },
                    "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                        {
                        "name": "Application!*"
                        }
                    ]
                    },
                    "Logs": {
                    "scheduledTransferPeriod": "PT1M",
                    "scheduledTransferLogLevelFilter": "Error"
                    }
                },
                "SinksConfig": {
                    "Sink": [
                    {
                        "name" : "AzMonSink",
                        "AzureMonitor" : {}
                    }
                    ]
                }
                },
                "StorageAccount": "[variables('storageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[variables('storageAccountName')]",
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]",
                "storageAccountEndPoint": "https://core.windows.net/"
            }
            }
        }
        ]
//End of section to add
```


Enregistrez et fermez les deux fichiers.


## <a name="deploy-the-resource-manager-template"></a>Déployer le modèle Resource Manager

> [!NOTE]
> Vous devez exécuter la version 1.5 ou supérieure de l’extension Diagnostics Azure ET définir la propriété **autoUpgradeMinorVersion** sur « true » dans votre modèle Resource Manager. Azure charge alors l’extension appropriée lorsqu’il démarre la machine virtuelle. Si ces paramètres ne figurent pas dans votre modèle, modifiez-les et redéployez le modèle.


Pour déployer le modèle Resource Manager, vous allez utiliser Azure PowerShell.

1. Lancez PowerShell.
1. Connectez-vous à Azure à l’aide de `Login-AzAccount`.
1. Obtenez la liste de vos abonnements à l’aide de `Get-AzSubscription`.
1. Définissez l’abonnement que vous utilisez pour créer/mettre à jour la machine virtuelle dans :

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Pour créer un nouveau groupe de ressources pour la machine virtuelle en cours de déploiement, exécutez la commande suivante :

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > N’oubliez pas d’[utiliser une région Azure dans laquelle les mesures personnalisées sont activées](metrics-custom-overview.md).

1. Exécutez les commandes suivantes pour déployer la machine virtuelle à l’aide du modèle Resource Manager.
   > [!NOTE]
   > Si vous souhaitez mettre à jour une machine virtuelle existante, ajoutez simplement *-Mode Incremental* à la fin de la commande suivante.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Une fois votre déploiement réussi, la machine virtuelle doit se trouver dans le portail Azure et envoie des métriques vers Azure Monitor.

   > [!NOTE]
   > Des erreurs liées à la vmSkuSize sélectionnée sont susceptibles de se produire. Si cela se produit, revenez à votre fichier azuredeploy.json et mettez à jour la valeur par défaut du paramètre vmSkuSize. Dans ce cas, nous vous recommandons d’essayer « Standard_DS1_v2 »).

## <a name="chart-your-metrics"></a>Représenter vos métriques graphiquement

1. Connectez-vous au portail Azure.

2. Dans le menu de gauche, sélectionnez **Surveiller**.

3. Sur la page Surveiller, sélectionnez **Métriques**.

   ![Page Métriques](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Modifiez la période d’agrégation afin d’indiquer **30 dernières minutes**.

5. Dans le menu déroulant de la ressource, sélectionnez la machine virtuelle que vous venez de créer. Si vous n’avez pas modifié le nom dans le modèle, elle doit s’appeler *SimpleWinVM2*.

6. Dans le menu déroulant des espaces de noms, sélectionnez **azure.vm.windows.guest**

7. Dans le menu déroulant des métriques, sélectionnez **Mémoire\%Octets validés utilisés**.


## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [métriques personnalisées](metrics-custom-overview.md).

