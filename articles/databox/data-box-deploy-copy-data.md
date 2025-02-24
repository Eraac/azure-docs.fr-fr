---
title: Tutoriel pour copier des données via SMB sur Azure Data Box | Microsoft Docs
description: Découvrez comment copier des données sur votre Azure Data Box par le biais de SMB.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: alkohli
ms.openlocfilehash: 6b2a0655173405008e0bccf3e31a8db391da6127
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496286"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Didacticiel : Copier des données sur Azure Data Box Disk par le biais de SMB

Ce tutoriel explique comment vous connecter à votre ordinateur hôte et copier des données à partir de cet ordinateur à l’aide de l’interface utilisateur web locale.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Prérequis
> * Se connecter à Data Box
> * Copier des données sur Data Box


## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que :

1. Vous avez terminé le [Tutoriel : Configurer Azure Data Box](data-box-deploy-set-up.md).
2. Vous avez reçu votre Data Box et que l’état de la commande dans le portail est **Remis**.
3. Vous disposez d’un ordinateur hôte contenant les données que vous souhaitez copier sur Data Box Votre ordinateur hôte doit
    - Exécuter un [système d’exploitation pris en charge](data-box-system-requirements.md)
    - Connectez-vous à un réseau haut débit. Nous vous recommandons vivement d’utiliser au minimum une connexion 10 GbE. Si vous ne disposez pas d’une connexion 10 GbE, utilisez une liaison de données 1 GbE. Cependant, les vitesses de copie seront affectées.

## <a name="connect-to-data-box"></a>Se connecter à Data Box

Selon le compte de stockage sélectionné, Data Box crée jusqu’à :
- Trois partages pour chaque compte de stockage associé pour GPv1 et GPv2.
- Un partage pour le stockage Premium.
- Un partage pour le compte de stockage Blob.

Sous les partages d’objet blob de blocs et d’objet blob de pages, les entités de premier niveau sont des conteneurs et les entités de second niveau sont des objets blob. Sous les partages Azure Files, les entités de premier niveau sont des partages et les entités de second niveau sont des fichiers.

Le tableau suivant montre le chemin UNC aux partages sur votre Data Box et l’URL de chemin Stockage Azure où les données sont chargées. La dernière URL de chemin de Stockage Azure peut être dérivée à partir du chemin de partage UNC.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Objets blob de blocs Azure | <li>Chemin UNC aux partages : `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL de Stockage Azure : `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Objets blob de pages Azure  | <li>Chemin UNC aux partages : `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL de Stockage Azure : `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Chemin UNC aux partages : `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL de Stockage Azure : `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Si vous utilisez un ordinateur hôte Windows Server, effectuez les étapes suivantes pour vous connecter à la Data Box.

1. La première étape consiste à vous authentifier et à démarrer une session. Accédez à la page **Connect and copy** (Connexion et copie). Cliquez sur **Get credentials** (Obtenir les informations d’identification) pour obtenir les informations d’identification requises pour accéder aux partages associés à votre compte de stockage. 

    ![Obtenir les informations d’identification du partage 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. Dans la boîte de dialogue Access share and copy data (Accéder au partage et copier les données), copiez les valeurs **Nom d’utilisateur** et **Mot de passe** correspondant au partage. Cliquez sur **OK**.
    
    ![Obtenir les informations d’identification du partage 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Pour accéder aux partages associés à votre compte de stockage (*devicemanagertest1* dans l’exemple suivant) à partir de votre ordinateur hôte, ouvrez une fenêtre de commande. À l’invite de commandes, tapez :

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    En fonction du format de vos données, les chemins de partage sont les suivants :
    - Objet blob de blocs Azure - `\\10.126.76.172\devicemanagertest1_BlockBlob`
    - Objet blob de pages Azure - `\\10.126.76.172\devicemanagertest1_PageBlob`
    - Azure Files - `\\10.126.76.172\devicemanagertest1_AzFile`
    
4. Lorsque vous y êtes invité, entrez le mot de passe du partage. L’exemple suivant illustre la connexion à un partage à l’aide de la commande précédente.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Appuyez sur Windows + R. Dans la fenêtre **Exécuter**, spécifiez `\\<device IP address>`. Cliquez sur **OK** pour ouvrir l’Explorateur de fichiers.
    
    ![Se connecter au partage à l’aide de l’Explorateur de fichiers 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    À présent, vous devriez voir les partages sous forme de dossiers.
    
    ![Se connecter au partage à l’aide de l’Explorateur de fichiers 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)    

    **Toujours créer un dossier pour les fichiers que vous envisagez de copier sous le partage, puis copier les fichiers dans ce dossier**. Le dossier créé sous les partages d’objets blob de pages et d’objets blob de blocs représente un conteneur dans lequel les données sont chargées en tant qu’objets blob. Vous ne pouvez pas copier de fichiers directement dans le dossier *root* du compte de stockage.
    
Si vous utilisez un client Linux, utilisez la commande suivante pour monter le partage SMB. Le paramètre « vers » ci-dessous est la version de SMB que votre hôte Linux prend en charge. Utilisez la version appropriée dans la commande ci-dessous. Pour connaître les versions de SMB que le service the Data Box prend en charge, consultez [Systèmes de fichiers pris en charge pour les clients Linux](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients). 

    `sudo mount -t nfs -o vers=2.1 10.126.76.172:/devicemanagertest1_BlockBlob /home/databoxubuntuhost/databox`
    


## <a name="copy-data-to-data-box"></a>Copier des données sur Data Box

Une fois que vous êtes connecté aux partages Data Box, l’étape suivante consiste à copier les données. Avant de commencer la copie des données, passez en revue les considérations suivantes :

- Vérifiez que les données sont copiées vers des partages compatibles avec le format des données. Par exemple, les données d’objet blob de blocs doivent être copiées dans le partage des objets blob de blocs. Copiez les disques durs virtuels dans un objet blob de pages. Si le format des données ne correspond pas au type de partage, les données ne pourront pas être chargées dans Azure.
-  Quand vous copiez des données, vérifiez que leur taille est conforme aux limites de taille spécifiées dans l’article [Limitations relatives au Stockage Azure et à Data Box](data-box-limits.md).
- Si les données, qui sont en cours de chargement par Data Box, sont chargées simultanément par d’autres applications en dehors de Data Box, cela peut entraîner l’échec du chargement ou des corruptions de données.
- Nous vous recommandons :
    - N’utilisez pas SMB et NFS en même temps.
    - Copiez les mêmes données à la même destination finale sur Azure. 
     
  En effet, le résultat final ne pourrait pas être déterminé.
- Créez toujours un dossier pour les fichiers que vous envisagez de copier sous le partage, puis copiez les fichiers dans ce dossier. Le dossier créé sous les partages d’objets blob de pages et d’objets blob de blocs représente un conteneur dans lequel les données sont chargées en tant qu’objets blob. Vous ne pouvez pas copier de fichiers directement dans le dossier *root* du compte de stockage.

Après vous être connecté au partage SMB, commencez la copie des données. Vous pouvez utiliser n’importe quel outil de copie de fichier SMB, comme Robocopy, pour copier vos données. Plusieurs travaux de copie peuvent être lancés simultanément à l’aide de Robocopy. Utilisez la commande suivante :
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Les attributs sont décrits dans le tableau suivant.
    
|Attribut  |Description  |
|---------|---------|
|/e     |Copie les sous-répertoires, y compris les répertoires vides.         |
|/r:     |Spécifie le nombre de nouvelles tentatives en cas d’échec de la copie.         |
|/w:     |Spécifie le délai d’attente entre les tentatives, en secondes.         |
|/is     |Inclut les mêmes fichiers.         |
|/nfl     |Indique que les noms de fichiers ne sont pas enregistrés dans le journal.         |
|/ndl    |Indique que les noms de répertoires ne sont pas enregistrés dans le journal.        |
|/np     |Spécifie que la progression de l’opération de copie (le nombre de fichiers ou répertoires copiés jusqu’ici) n’est pas affichée. L’affichage de la progression réduit considérablement les performances.         |
|/MT     | Utilise le multithreading (32 ou 64 threads recommandés). Cette option n’est pas utilisée avec les fichiers chiffrés. Vous devrez peut-être séparer les fichiers chiffrés et non chiffrés. Toutefois, une copie monothread réduit considérablement les performances.           |
|/fft     | Utilisé pour réduire la granularité de l’horodatage pour n’importe quel système de fichiers.        |
|/b     | Copie les fichiers en mode de sauvegarde.        |
|/z    | Copie les fichiers en mode de redémarrage. Utilisez cette option si l’environnement est instable. La journalisation supplémentaire effectuée par cette option réduit le débit.      |
| /zb     | Utilise le mode de redémarrage. Si l’accès est refusé, cette option utilise le mode de sauvegarde. La création de points de contrôle par cette option réduit le débit.         |
|/efsraw     | Copie tous les fichiers chiffrés en mode EFS brut. Utilisez cette option avec des fichiers chiffrés uniquement.         |
|log+:\<LogFile>| Ajoute la sortie au fichier journal existant.|    
 
L’exemple suivant affiche la sortie de la commande robocopy pour copier les fichiers sur Data Box.
    
    C:\Users>robocopy
        -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:53 PM
            Simple Usage :: ROBOCOPY source destination /MIR
    
                    source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                    /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                    Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
        Files :        17        17         0         0         0         0
        Bytes :     3.9 k     3.9 k         0         0         0         0          
    C:\Users>
       

Pour optimiser les performances, utilisez les paramètres robocopy suivants lors de la copie des données.

|    Plateforme    |    Principalement des fichiers de petite taille (< 512 Ko)                           |    Principalement des fichiers de taille moyenne (entre 512 Ko et 1 Mo)                      |    Principalement des fichiers volumineux (> 1 Mo)                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    2 sessions Robocopy <br> 16 threads par session    |    3 sessions Robocopy <br> 16 threads par session    |    2 sessions Robocopy <br> 24 threads par session    |


Pour plus d’informations sur la commande Robocopy, consultez [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy et quelques exemples).

Ouvrez le dossier cible pour afficher et vérifier les fichiers copiés. Si vous rencontrez des erreurs au cours du processus de copie, téléchargez les fichiers d’erreurs pour résoudre les problèmes. Pour plus d’informations, consultez [Afficher les journaux d’erreurs pendant la copie de données vers Data Box](data-box-logs.md#view-error-log-during-data-copy). Pour obtenir une liste détaillée des erreurs lors de la copie des données, consultez [Résolution des problèmes liés à Data Box](data-box-troubleshoot.md).

Pour garantir l’intégrité des données, la somme de contrôle est calculée par le biais d’une fonction inline lors de la copie des données. Une fois la copie terminée, vérifiez l’espace utilisé et l’espace libre sur votre appareil.
    
   ![Vérifier l’espace libre et l’espace utilisé sur le tableau de bord](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)



## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a apporté des connaissances concernant Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
> * Prérequis
> * Se connecter à Data Box
> * Copier des données sur Data Box


Passez au tutoriel suivant pour découvrir comment renvoyer votre Data Box à Microsoft.

> [!div class="nextstepaction"]
> [Ship your Azure Data Box to Microsoft](./data-box-deploy-picked-up.md) (Expédier votre Azure Data Box à Microsoft)

