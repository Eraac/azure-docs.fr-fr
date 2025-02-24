---
title: Créer un modèle de générateur d’images Azure (préversion)
description: Découvrez comment créer un modèle à utiliser avec le générateur d’images Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/10/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 16ad2a93c9ff035166a738edba40c99075a6e7ba
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671458"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Aperçu : Créer un modèle de générateur d’images Azure 

Le générateur d’images Azure utilise un fichier .json pour transmettre des informations au service du générateur d’images. Dans cet article, nous allons vous présenter les sections du fichier json pour que vous puissiez créer le vôtre. Pour voir des exemples de fichiers .json complets, consultez [GitHub sur le générateur d’images Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Voici le format de modèle de base :

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Type et version de l’API

`type` est le type de ressource, qui doit être `"Microsoft.VirtualMachineImages/imageTemplates"`. `apiVersion` change au fil du temps à mesure que l’API change, mais doit être `"2019-05-01-preview"` pour la préversion.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

L’emplacement est la région dans laquelle l’image personnalisée sera créée. Pour le générateur d’images en préversion, les régions suivantes sont prises en charge :

- USA Est
- USA Est 2
- USA Centre-Ouest
- USA Ouest
- USA Ouest 2


```json
    "location": "<region>",
```
    
## <a name="depends-on-optional"></a>Dépend de (facultatif)

Cette section facultative peut être utilisée pour s’assurer que les dépendances sont terminées avant de continuer. 

```json
    "dependsOn": [],
```

Pour plus d’informations, consultez [Définir les dépendances des ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identité
Par défaut, le générateur d’images Azure prend en charge l’utilisation de scripts ou la copie de fichiers à partir de plusieurs emplacements, tels que GitHub et le Stockage Azure. Pour pouvoir les utiliser, ils doivent être accessibles publiquement.

Vous pouvez également utiliser une identité managée affectée par l’utilisateur Azure, définie par vous, pour autoriser le générateur d’images à accéder au Stockage Azure, tant que l’identité dispose au minimum de l’autorisation de « lecteur de données d’objets Blob de stockage » sur le compte de Stockage Azure. Cela signifie que vous ne devez pas nécessairement rendre les objets Blob de stockage accessibles en externe, ni configurer de jetons SAS.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Pour un exemple complet, consultez [Créer une image et utiliser une identité managée affectée par l’utilisateur pour accéder aux fichiers dans le Stockage Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Prise en charge du générateur d’images pour une identité affectée par l’utilisateur : • Prend en charge une identité unique uniquement • Ne prend pas en charge les noms de domaines personnalisés

Pour en savoir plus, consultez [Que sont les identités managées pour les ressources Azure ?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Pour plus d’informations sur le déploiement de cette fonctionnalité, consultez [Configurer des identités managées pour ressources Azure sur une machine virtuelle Azure en utilisant Azure CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Propriétés : source

La section `source` fournit des informations sur l’image source qui sera utilisée par le générateur d’images.

L’API nécessite un « SourceType » qui définit la source pour la génération d’image. Il en existe actuellement trois types :
- ISO : utilisez cette option lorsque la source est un fichier ISO RHEL.
- PlatformImage : indique que l’image source est une image de la Place de marché.
- ManagedImag : utilisez cette option au démarrage à partir d’une image managée classique.
- SharedImageVersion : cette option s’applique lorsque vous utilisez une version d’image dans une galerie d’images partagées comme source.

### <a name="iso-source"></a>Source ISO

Le générateur d’images Azure prend uniquement en charge l’utilisation d’ISO DVD binaires Red Hat Enterprise Linux 7.x publiées pour la préversion. Le générateur d’images prend en charge :
- RHEL 7.3 
- RHEL 7.4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

Pour obtenir les valeurs `sourceURI` et `sha256Checksum`, accédez à `https://access.redhat.com/downloads` puis sélectionnez le produit **Red Hat Enterprise Linux** et une version prise en charge. 

Dans la liste des **programmes d’installation et des images pour Red Hat Enterprise Linux Server**, vous devez copier le lien de DVD binaire Red Hat Enterprise Linux 7.x et la somme de contrôle.

> [!NOTE]
> Les jetons d’accès des liens sont fréquemment actualisés. Ainsi, chaque fois que vous souhaitez envoyer un modèle, vous devez vérifier si l’adresse du lien RH a été modifiée.
 
### <a name="platformimage-source"></a>Source PlatformImage 
Le générateur d’images Azure prend en charge les images de Place de Marché Azure suivantes :
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7.6
* CentOS 7.6
* Windows 2016
* Windows 2019

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


Ces propriétés sont les mêmes que celles utilisées pour créer des machines virtuelles, à l’aide d’AZ CLI. Exécutez la commande ci-dessous pour obtenir les propriétés : 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> La version ne peut pas être « latest », vous devez utiliser la commande ci-dessus pour obtenir un numéro de version. 

### <a name="managedimage-source"></a>Source ManagedImage

Définit l’image source comme une image managée existante d’un disque dur virtuel généralisé ou d’une machine virtuelle. L’image managée source doit provenir d’un système d’exploitation pris en charge et se trouver dans la même région que votre modèle de générateur d’images Azure. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` doit être l’ID de ressource de l’image managée. Utilisez `az image list` pour répertorier les images disponibles.


### <a name="sharedimageversion-source"></a>Source SharedImageVersion
Définit l’image source comme une version d’image existante dans une galerie d’images partagées. La version d’image doit provenir d’un système d’exploitation pris en charge, et l’image doit être répliquée dans la même région que votre modèle de générateur d’images Azure. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId` doit être l’ID de ressource de la version d’image. Utilisez [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list) pour répertorier les versions d’image.

## <a name="properties-customize"></a>Propriétés : personnaliser


Le générateur d’images prend en charge plusieurs « personnalisateurs ». Les personnalisateurs sont des fonctions utilisées pour personnaliser votre image, telles que l’exécution de scripts ou le redémarrage de serveurs. 

Lorsque vous utilisez `customize` : 
- Vous pouvez utiliser plusieurs personnalisateurs, mais ils doivent avoir une valeur `name` unique.
- Les personnalisateurs sont exécutés dans l’ordre spécifié dans le modèle.
- En cas d’échec d’un personnalisateur, l’ensemble du composant de personnalisation échoue et renvoie une erreur.
- Évaluez le temps nécessaire de création de votre image et ajustez la propriété « buildTimeoutInMinutes » pour laisser le temps nécessaire au générateur d’images pour terminer.
- Il est vivement recommandé de tester rigoureusement le script avant de l’utiliser dans un modèle. Le débogage du script sur votre propre machine virtuelle en sera simplifié.
- Ne placez pas de données sensibles dans les scripts. 
- Les emplacements de script doivent être accessibles publiquement, sauf si vous utilisez [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
La section de personnalisation est un tableau. Le générateur d’images Azure s’exécute via les personnalisateurs dans un ordre séquentiel. L’échec d’un personnalisateur entraîne un échec du processus de génération. 
 
 
### <a name="shell-customizer"></a>Personnalisateur de l’interpréteur de commandes

Le personnalisateur de l’interpréteur de commandes prend en charge l’exécution de scripts shell. Ceux-ci doivent donc être accessibles publiquement pour que le générateur d’images puisse y accéder.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>"        
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Système d’exploitation pris en charge : Linux 
 
Propriétés de personnalisation :

- **type** - Shell 
- **name** - Nom pour le suivi de la personnalisation 
- **scriptUri** - URI vers l’emplacement du fichier 
- **inline** - Tableau de commandes d’interpréteur de commandes, séparées par des virgules.
 
> [!NOTE]
> Lors de l’exécution du personnalisateur de l’interpréteur de commandes avec la source ISO RHEL, vous devez vérifier que votre premier interpréteur de commandes de personnalisation gère l’inscription auprès d’un serveur de droits Red Hat avant toute personnalisation. Lorsque la personnalisation est terminée, le script doit annuler l’inscription auprès du serveur de droits.

### <a name="windows-restart-customizer"></a>Personnalisateur de redémarrage Windows 
Le personnalisateur de redémarrage vous permet de redémarrer une machine virtuelle Windows et d’attendre qu’elle revienne en ligne, vous permettant ainsi d’installer un logiciel qui nécessite un redémarrage.  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

Système d’exploitation pris en charge :  Windows
 
Propriétés de personnalisation :
- **Type** : WindowsRestart
- **restartCommand** - Commande pour exécuter le redémarrage (facultatif). Par défaut, il s’agit de `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** - Commande pour vérifier si le redémarrage a réussi (facultatif). 
- **restartTimeout** - Délai d’expiration de redémarrage spécifié sous forme de chaîne de magnitude et d’unité. Par exemple, `5m` (5 minutes) ou `2h` (2 heures). La valeur par défaut est : '5m'


### <a name="powershell-customizer"></a>Personnalisateur PowerShell 
Le personnalisateur PowerShell prend en charge l’exécution de scripts PowerShell et de commande en ligne. Les scripts doivent être accessibles publiquement pour que le générateur d’images puisse y accéder.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>" 
         } 
    ], 
```

Système d’exploitation pris en charge : Windows et Linux

Propriétés de personnalisation :

- **type** - PowerShell.
- **scriptUri** - URI vers l’emplacement du fichier de script PowerShell. 
- **inline** - Commandes en ligne à exécuter, séparées par des virgules.
- **valid_exit_codes** - Facultatif. Des codes valides peuvent être retournés par le script/la commande en ligne, permettant ainsi d’éviter le signalement d’un échec du script/de la commande en ligne.

### <a name="file-customizer"></a>Personnalisateur de fichier

Le personnalisateur de fichier permet aux générateurs d’images de télécharger un fichier d’un GitHub ou Stockage Azure. Si vous disposez d’un pipeline de build d’image qui s’appuie sur des artefacts, vous pouvez définir le personnalisateur de fichier à télécharger du partage de build et déplacer les artefacts dans l’image.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>" 
         }
     ]
```

Système d’exploitation pris en charge : Linux et Windows 

Propriétés du personnalisateur de fichier :

- **sourceUri** - Point de terminaison de stockage accessible, il peut s’agir de GitHub ou de Stockage Azure. Vous pouvez uniquement télécharger un fichier, pas un répertoire complet. Si vous devez télécharger un répertoire, utilisez un fichier compressé, puis décompressez-le à l’aide du personnalisateur de l’interpréteur de commandes ou PowerShell. 
- **destination** - Nom de destination complet et nom du fichier. Le chemin d’accès et les sous-répertoires référencés doivent exister, utilisez les personnalisateurs de l’interpréteur de commandes ou PowerShell pour les définir au préalable. Vous pouvez utiliser les personnalisateurs de script pour créer le chemin d’accès. 

Cela est pris en charge par les répertoires Windows et les chemins d’accès Linux, mais à quelques différences près : 
- Système d’exploitation Linux - Le seul chemin dans lequel le générateur d’images peut écrire est /tmp.
- Windows - Aucune restriction de chemin d’accès, mais le chemin d’accès doit exister.
 
 
Si une erreur se produit lors de la tentative de téléchargement du fichier, ou de son placement dans un répertoire spécifié, l’étape de personnalisation échoue, et sera consignée dans le fichier customization.log.

>> Remarque : le Personnalisateur de fichier est uniquement adapté au téléchargement de fichiers de petite taille (moins de 20 Mo); Pour le téléchargement de fichiers plus volumineux, utilisez un script ou une commande incluse, le code d’utilisation pour télécharger des fichiers, tel que `wget` ou `curl` pour Linux et `Invoke-WebRequest` pour Windows.

Les fichiers dans le personnalisateur de fichier peuvent être téléchargés depuis le Stockage Azure à l’aide de [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="generalize"></a>Généraliser 
Le générateur d’images Azure exécute également du code de « déprovisionnement » à la fin de chaque phase de personnalisation d’image, pour « généraliser » l’image. La généralisation est un processus dans lequel l’image est configurée pour pouvoir être réutilisée afin de créer plusieurs machines virtuelles. Pour les machines virtuelles Windows, le générateur d’images Azure utilise Sysprep. Pour Linux, le générateur d’images Azure exécute « waagent-deprovision ». 

Les commandes du générateur d’images utilisées pour généraliser peuvent ne pas convenir à chaque situation. Le générateur d’images Azure vous permet ainsi de personnaliser cette commande si nécessaire. 

Si vous migrez une personnalisation existante et que vous utilisez différentes commandes Sysprep/waagent, vous pouvez utiliser les commandes génériques du générateur d’images. De plus, si la création de la machine virtuelle échoue, utilisez vos propres commandes Sysprep ou waagent.

Si le générateur d’images Azure crée une image personnalisée Windows avec succès et que vous créez une machine virtuelle à partir de celle-ci, recherchez la machine virtuelle dont la création échoue ou ne se termine pas correctement. Vous devrez consulter la documentation de Windows Server Sysprep ou ouvrir une demande de support auprès de l’équipe du support technique du service client Windows Server Sysprep Services, qui peut résoudre les problèmes et vous conseiller sur l’utilisation correcte de Sysprep.


#### <a name="default-sysprep-command"></a>Commande Sysprep par défaut
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Commande de déprovisionnement Linux par défaut

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Remplacement de commandes
Pour remplacer les commandes, utilisez les fournisseurs de script PowerShell ou de l’interpréteur de commandes pour créer les fichiers de commandes avec le nom de fichier exact, puis placez-les dans les répertoires appropriés :

* Windows : c:\DeprovisioningScript.ps1
* Linux : /tmp/DeprovisioningScript.sh

Le générateur d’images lira ces commandes, celles-ci sont écrites dans les journaux AIB, « customization.log ». Consultez la section de [dépannage](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) pour savoir comment collecter les journaux.
 
## <a name="properties-distribute"></a>Propriétés : distribuer

Le générateur d’images Azure prend en charge trois cibles de distribution : 

- **managedImage** - Image managée.
- **sharedImage** - Galerie d’images partagées.
- **VHD** - Disque dur virtuel dans un compte de stockage.

Vous pouvez distribuer une image sur les deux types de cibles dans la même configuration, consultez les [exemples](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Comme vous pouvez avoir plusieurs cibles sur lesquelles distribuer, le générateur d’images gère un état pour chaque cible de distribution accessible en interrogeant `runOutputName`.  `runOutputName` est un objet que vous pouvez interroger après la distribution pour plus d’informations sur cette distribution. Par exemple, vous pouvez interroger l’emplacement du disque dur virtuel ou des régions dans lesquelles la version d’image a été répliquée. Il s’agit d’une propriété de chaque cible de distribution. `runOutputName` doit être unique pour chaque cible de distribution.
 
### <a name="distribute-managedimage"></a>Distribuer : managedImage

La sortie de l’image correspond à une ressource d’image managée.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Propriétés de distribution :
- **type** - managedImage 
- **imageId** - ID de ressource de l’image de destination, format attendu : /subscriptions/<subscriptionId>/resourceGroups/<destinationResourceGroupName>/providers/Microsoft.Compute/images/<imageName>
- **location** - Emplacement de l’image managée.  
- **runOutputName** - Nom unique d’identification de la distribution.  
- **artifactTags** - Facultatif, balises de paire de valeur de clé spécifiées par l’utilisateur.
 
 
> [!NOTE]
> Le groupe de ressources de destination doit exister.
> Si vous souhaitez distribuer l’image dans une autre région, le temps de déploiement est prolongé. 

### <a name="distribute-sharedimage"></a>Distribuer : sharedImage 
La galerie d’images partagées Azure est un nouveau service de gestion des images qui permet de gérer la réplication de la région d’image, le contrôle de version et le partage d’images personnalisées. Le générateur d’images Azure prend en charge la distribution avec ce service, vous pouvez donc distribuer des images dans des régions prises en charge par les galeries d’images partagées. 
 
Une galerie d’images partagées est constituée des éléments suivants : 
 
- Galerie : conteneur pour plusieurs images partagées. Une galerie est déployée dans une région.
- Définitions d’image : regroupement logique d’images. 
- Versions d’image : il s’agit d’un type d’image utilisé pour le déploiement d’une machine virtuelle ou d’un groupe identique. Des versions d’image peuvent être répliquées vers d’autres régions où des machines virtuelles doivent être déployées.
 
Avant de pouvoir distribuer dans la galerie d’images, vous devez créer une galerie et une définition d’image, consultez [Images partagées](shared-images.md). 

```json
{
     "type": "sharedImage",
     "galleryImageId": “<resource ID>”,
     "runOutputName": "<name>",
     "artifactTags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
     "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]}
``` 

Propriétés de distribution de galeries d’images partagées :

- **type** - sharedImage  
- **galleryImageId** - ID de la galerie d’images partagées. Le format est : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>.
- **runOutputName** - Nom unique d’identification de la distribution.  
- **artifactTags** - Facultatif, balises de paire de valeur de clé spécifiées par l’utilisateur.
- **replicationRegions** - Tableau de régions pour la réplication. Une des régions doit être la région où la galerie est déployée.
 
> [!NOTE]
> Vous pouvez utiliser le générateur d’images Azure dans une région différente de celle de la galerie, mais le service de générateur d’images Azure devra transférer l’image entre les centres de données, ce qui prendra plus de temps. Le générateur d’images contrôle automatiquement la version de l’image, en fonction d’un entier monotone. Vous ne pouvez pas le spécifier actuellement. 

### <a name="distribute-vhd"></a>Distribuer : Disque dur virtuel (VHD)  
Vous pouvez générer sur un disque dur virtuel. Vous pouvez ensuite copier le disque dur virtuel et l’utiliser pour publier sur la Place de marché Azure ou l’utiliser avec Azure Stack.  

```json
 { 
     "type": "VHD",
     "runOutputName": "<VHD name>",
     "tags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
 }
```
 
Système d’exploitation pris en charge : Windows et Linux

Distribuer des paramètres de disque dur virtuel :

- **type** - Disque dur virtuel.
- **runOutputName** - Nom unique d’identification de la distribution.  
- **tags** - Facultatif, balises de paire de valeur de clé spécifiées par l’utilisateur.
 
Le générateur d’images Azure ne permet pas à l’utilisateur de spécifier un emplacement de compte de stockage, mais vous pouvez interroger l’état de `runOutputs` pour obtenir l’emplacement.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Lorsque le disque dur virtuel est créé, copiez-le dès que possible dans un autre emplacement. Le disque dur virtuel est stocké dans un compte de stockage dans le groupe de ressources temporaire créé lors de l’envoi du modèle d’image au service de générateur d’images Azure. Si vous supprimez le modèle d’image, vous perdez le disque dur virtuel. 
 
## <a name="next-steps"></a>Étapes suivantes

Il existe des exemples de fichiers .json pour différents scénarios dans le [GitHub de générateur d’images Azure](https://github.com/danielsollondon/azvmimagebuilder).
 
 
 
 
 
 
 
 
 
 
