---
title: Charger, télécharger, lister et supprimer des objets blob à l’aide du kit SDK Azure Storage v10 pour JavaScript
description: Créer, charger et supprimer des objets blob et des conteneurs avec Node.js et le stockage Azure
services: storage
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: mhopkins
ms.reviewer: seguler
ms.openlocfilehash: e2deda6bc9a5d13a631e9917f3020cfa68ee1e10
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536159"
---
# <a name="quickstart-upload-download-list-and-delete-blobs-using-azure-storage-v10-sdk-for-javascript"></a>Démarrage rapide : Charger, télécharger, lister et supprimer des objets blob à l’aide du kit SDK Azure Storage v10 pour JavaScript

Dans ce guide de démarrage rapide, vous apprenez à utiliser le [kit SDK Azure Storage v10 pour JavaScript](https://github.com/Azure/azure-sdk-for-js) avec Node.js pour charger, télécharger, lister et supprimer des objets blob, et gérer des conteneurs.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="download-the-sample-application"></a>Téléchargement de l'exemple d'application

[L’exemple d’application](https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git) utilisé dans ce guide de démarrage rapide est une application console de base Node.js. Pour commencer, clonez le référentiel dans votre machine en exécutant la commande suivante :

```bash
git clone https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git
```

Ensuite, remplacez le dossier par celui de l’application :

```bash
cd azure-storage-js-v10-quickstart
```

À présent, ouvrez le dossier dans l’environnement de modification de code de votre choix.

## <a name="configure-your-storage-credentials"></a>Configurer vos informations d’identification de stockage

Avant d’exécuter l’application, vous devez fournir les informations d’identification de votre compte de stockage. Le référentiel d’exemple contient un fichier nommé *.env.example*. Renommez ce fichier en supprimant l’extension *.example*, ce qui donne un fichier nommé *.env*. Dans le fichier *.env*, ajoutez votre nom de compte et les valeurs des clés d’accès après les clés *AZURE_STORAGE_ACCOUNT_NAME* et *AZURE_STORAGE_ACCOUNT_ACCESS_KEY*.

## <a name="install-required-packages"></a>Installer les packages nécessaires

Dans le répertoire de l’application, exécutez la commande *npm install* pour installer les packages nécessaires à l’application.

```bash
npm install
```

## <a name="run-the-sample"></a>Exécution de l'exemple
Maintenant que les dépendances sont installées, vous pouvez exécuter l’exemple en émettant la commande suivante :

```bash
npm start
```

La sortie de l’application doit ressembler à l’exemple suivant :

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme-stream.md
 - readme.md
Blob downloaded blob content: "hello!"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```
Si vous utilisez un nouveau compte de stockage pour ce guide de démarrage rapide, les noms des conteneurs peuvent ne pas être listés sous l’étiquette « *Containers* ».

## <a name="understanding-the-code"></a>Présentation du code
L’exemple commence par l’importation d’un nombre de classes et de fonctions à partir de l’espace de noms du stockage Blob Azure. Chaque élément importé est expliqué en contexte au fur et à mesure qu’il est utilisé dans l’exemple.

```javascript
const {
    Aborter,
    BlobURL,
    BlockBlobURL,
    ContainerURL,
    ServiceURL,
    SharedKeyCredential,
    StorageURL,
    uploadStreamToBlockBlob
} = require('@azure/storage-blob');
```

Les informations d’identification sont lues à partir de variables d’environnement basées sur le contexte approprié.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
```

Le module *dotenv* charge les variables d’environnement au moment de l’exécution locale de l’application à des fins de débogage. Les valeurs sont définies dans un fichier nommé *.env* et chargées dans le contexte d’exécution actuel. En production, la configuration du serveur fournit ces valeurs, c’est pourquoi ce code s’exécute uniquement quand le script n’est *pas* en cours d’exécution dans un environnement de « production ».

Le bloc de modules suivant est importé pour permettre l’interaction avec le système de fichiers.

```javascript
const fs = require('fs');
const path = require('path');
```

L’objectif de ces modules est le suivant : 

- *fs* est le module Node.js natif utilisé pour travailler avec le système de fichiers

- *path* est obligatoire pour déterminer le chemin absolu du fichier ; il est utilisé lors du chargement d’un fichier vers le stockage d’objets blob

Ensuite, les valeurs des variables d’environnement sont lues et mises de côté sous forme de constantes.

```javascript
const STORAGE_ACCOUNT_NAME = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const ACCOUNT_ACCESS_KEY = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
```
L’ensemble de constantes suivant permet de connaître la prévision des calculs de taille des fichiers lors des opérations de chargement.
```javascript
const ONE_MEGABYTE = 1024 * 1024;
const FOUR_MEGABYTES = 4 * ONE_MEGABYTE;
```
Les requêtes effectuées par l’API peuvent être définies pour expirer après un intervalle donné. La classe [Aborter](/javascript/api/%40azure/storage-blob/aborter?view=azure-node-preview) est chargée de gérer la façon dont les requêtes arrivent à expiration, et la constante suivante permet de définir les délais d’attente utilisés dans cet exemple.
```javascript
const ONE_MINUTE = 60 * 1000;
```
### <a name="calling-code"></a>Appel de code

Pour prendre en charge la syntaxe *async/await* de JavaScript, tout le code appelant est wrappé dans une fonction nommée *execute*. Puis, *execute* est appelée et gérée comme une promesse.

```javascript
async function execute() {
    // commands... 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
Tout le code suivant s’exécute dans la fonction execute où le commentaire `// commands...` est placé.

Tout d’abord, les variables appropriées sont déclarées pour attribuer des noms, échantillonner du contenu et pointer vers le fichier local à charger sur Stockage Blob.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello!";
const localFilePath = "./readme.md";
```

Les informations d’identification de compte sont utilisées pour créer un pipeline, qui est chargé de gérer la façon dont les requêtes sont envoyées à l’API REST. Les pipelines sont thread-safe et spécifient une logique pour les stratégies de nouvelle tentative, la journalisation, les règles de la désérialisation de réponse HTTP et bien plus encore.

```javascript
const credentials = new SharedKeyCredential(STORAGE_ACCOUNT_NAME, ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(credentials);
const serviceURL = new ServiceURL(`https://${STORAGE_ACCOUNT_NAME}.blob.core.windows.net`, pipeline);
```
Les classes suivantes sont utilisées dans ce bloc de code :

- La classe [SharedKeyCredential](/javascript/api/%40azure/storage-blob/sharedkeycredential?view=azure-node-preview) est chargée de l’encapsulation des informations d’identification du compte de stockage pour les fournir à un pipeline de requête.

- La classe [StorageURL](/javascript/api/%40azure/storage-blob/storageurl?view=azure-node-preview) est chargée de la création d’un pipeline.

- La classe [ServiceURL](/javascript/api/%40azure/storage-blob/serviceurl?view=azure-node-preview) modélise une URL utilisée dans l’API REST. Les instances de cette classe permettent d’accomplir des actions, comme l’établissement d’une liste de conteneurs et l’apport d’informations de contexte pour générer des URL de conteneur.

L’instance de *ServiceURL* est utilisée avec les instances [ContainerURL](/javascript/api/%40azure/storage-blob/containerurl?view=azure-node-preview) et [BlockBlobURL](/javascript/api/%40azure/storage-blob/blockbloburl?view=azure-node-preview) pour gérer des conteneurs et des objets blob dans votre compte de stockage.

```javascript
const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, blobName);
```
Les variables *containerURL* et *blockBlobURL* sont réutilisées tout au long de l’exemple pour agir sur le compte de stockage. 

À ce stade, le conteneur n’existe pas dans le compte de stockage. L’instance de *ContainerURL* représente une URL sur laquelle vous pouvez agir. Avec cette instance, vous pouvez créer et supprimer le conteneur. L’emplacement de ce conteneur équivaut à un emplacement tel que celui-ci :

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo
```

Le conteneur *blockBlobURL* est utilisé pour gérer les objets blob individuels, ce qui vous permet de charger, télécharger et supprimer le contenu d’un objet blob. L’URL représentée ici est similaire à cet emplacement :

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo/quickstart.txt
```
Tout comme le conteneur, l’objet blob de blocs n’existe pas encore. La variable *blockBlobURL* est utilisée ultérieurement pour créer l’objet blob en chargeant le contenu.

### <a name="using-the-aborter-class"></a>Utilisation de la classe Aborter

Les requêtes faites par l’API peuvent être définies pour expirer après un intervalle donné. La classe *Aborter* est chargée de gérer la façon dont les requêtes arrivent à expiration. Le code suivant crée un contexte dans lequel un jeu de requêtes a 30 minutes pour s’exécuter.

```javascript
const aborter = Aborter.timeout(30 * ONE_MINUTE);
```
Les conditions d’abandon vous permettent de contrôler les requêtes en vous donnant les possibilités suivantes :

- Déterminer la quantité de temps donné à un lot de requêtes
- Déterminer la durée pendant laquelle une requête individuelle doit être exécutée dans le lot
- Pouvoir annuler des requêtes
- Utiliser le membre statique *Aborter.none* pour éviter que vos requêtes arrivent à expiration toutes ensemble

### <a name="show-container-names"></a>Afficher les noms de conteneur
Les comptes peuvent stocker un grand nombre de conteneurs. Le code suivant montre comment répertorier des conteneurs de façon segmentée, ce qui vous permet de parcourir un grand nombre de conteneurs. La fonction *showContainerNames* est transmise à des instances de *ServiceURL* et de *Aborter*.

```javascript
console.log("Containers:");
await showContainerNames(serviceURL, aborter);
```
La fonction *showContainerNames* utilise la méthode *listContainersSegment* pour demander des lots de noms de conteneur à partir du compte de stockage.
```javascript
async function showContainerNames(aborter, serviceURL) {

    let response;
    let marker;

    do {
        response = await serviceURL.listContainersSegment(aborter, marker);
        marker = response.marker;
        for(let container of response.containerItems) {
            console.log(` - ${ container.name }`);
        }
    } while (marker);
}
```
Lorsque la réponse est retournée, les éléments *containerItems* sont itérés pour consigner le nom dans la console. 

### <a name="create-a-container"></a>Créez un conteneur.

Pour créer un conteneur, la méthode *create* de *ContainerURL* est utilisée.

```javascript
await containerURL.create(aborter);
console.log(`Container: "${containerName}" is created`);
```
Comme le nom du conteneur est défini lors de l’appel de *ContainerURL.fromServiceURL (serviceURL, containerName)* , l’appel de la méthode *create* est la seule chose qui soit demandée pour créer le conteneur.

### <a name="upload-text"></a>Charger du texte
Pour charger du texte dans l’objet blob, utilisez la méthode *upload*.
```javascript
await blockBlobURL.upload(aborter, content, content.length);
console.log(`Blob "${blobName}" is uploaded`);
```
Ici, le texte et sa longueur sont passés dans la méthode.
### <a name="upload-a-local-file"></a>Charger un fichier local
Pour charger un fichier local dans le conteneur, vous avez besoin d’une URL de conteneur et du chemin au fichier.
```javascript
await uploadLocalFile(aborter, containerURL, localFilePath);
console.log(`Local file "${localFilePath}" is uploaded`);
```
La fonction *uploadLocalFile* appelle la fonction *uploadFileToBlockBlob*, qui prend le chemin de fichier et une instance de la destination de l’objet blob de bloc en tant qu’arguments.
```javascript
async function uploadLocalFile(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath);
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    return await uploadFileToBlockBlob(aborter, filePath, blockBlobURL);
}
```
### <a name="upload-a-stream"></a>Charger un flux de données
Le chargement de flux de données est également pris en charge. Cet exemple ouvre un fichier local en tant que flux à passer à la méthode de chargement.
```javascript
await uploadStream(containerURL, localFilePath, aborter);
console.log(`Local file "${localFilePath}" is uploaded as a stream`);
```
La fonction *uploadStream* appelle *uploadStreamToBlockBlob* pour charger le flux de données dans le conteneur de stockage.
```javascript
async function uploadStream(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath).replace('.md', '-stream.md');
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    const stream = fs.createReadStream(filePath, {
      highWaterMark: FOUR_MEGABYTES,
    });

    const uploadOptions = {
        bufferSize: FOUR_MEGABYTES,
        maxBuffers: 5,
    };

    return await uploadStreamToBlockBlob(
                    aborter, 
                    stream, 
                    blockBlobURL, 
                    uploadOptions.bufferSize, 
                    uploadOptions.maxBuffers);
}
```
Pendant un chargement, *uploadStreamToBlockBlob* alloue des mémoires tampons pour mettre en cache les données à partir du flux, au cas où une nouvelle tentative serait nécessaire. Chaque tampon créant une requête de chargement distincte, la valeur *maxBuffers* désigne le nombre maximal de mémoires tampons utilisées. Dans l’idéal, utiliser plus de mémoires tampons équivaut à bénéficier de vitesses supérieures, mais c’est au détriment d’une utilisation de la mémoire plus importante. La vitesse de chargement se stabilise lorsque le nombre de mémoires tampons est suffisamment élevé pour que le goulot d’étranglement se passe sur le réseau ou sur le disque, et pas sur le client.

### <a name="show-blob-names"></a>Afficher les noms des objets blob
Tout comme les comptes peuvent contenir de nombreux conteneurs, chaque conteneur peut potentiellement contenir une grande quantité d’objets blob. Les accès à chaque objet blob d’un conteneur sont disponibles via une instance de la classe *ContainerURL*. 
```javascript
console.log(`Blobs in "${containerName}" container:`);
await showBlobNames(aborter, containerURL);
```
La fonction *showBlobNames* appelle *listBlobFlatSegment* pour demander des lots d’objets blob à partir du conteneur.
```javascript
async function showBlobNames(aborter, containerURL) {

    let response;
    let marker;

    do {
        response = await containerURL.listBlobFlatSegment(aborter);
        marker = response.marker;
        for(let blob of response.segment.blobItems) {
            console.log(` - ${ blob.name }`);
        }
    } while (marker);
}
```
### <a name="download-a-blob"></a>Télécharger un objet blob
Une fois qu’un objet blob est créé, vous pouvez télécharger le contenu à l’aide de la méthode *download*.
```javascript
const downloadResponse = await blockBlobURL.download(aborter, 0);
const downloadedContent = downloadResponse.readableStreamBody.read(content.length).toString();
console.log(`Downloaded blob content: "${downloadedContent}"`);
```
La réponse est retournée sous forme de flux de données. Dans cet exemple, le flux est converti en une chaîne à consigner dans la console.
### <a name="delete-a-blob"></a>Supprimer un objet blob
La méthode *delete* depuis une instance *BlockBlobURL* supprime un objet blob à partir du conteneur.
```javascript
await blockBlobURL.delete(aborter)
console.log(`Block blob "${blobName}" is deleted`);
```
### <a name="delete-a-container"></a>Supprimer un conteneur
La méthode *delete* depuis une instance *ContainerURL* supprime un conteneur à partir du compte de stockage.
```javascript
await containerURL.delete(aborter);
console.log(`Container "${containerName}" is deleted`);
```
## <a name="clean-up-resources"></a>Supprimer des ressources
Toutes les données écrites dans le compte de stockage sont automatiquement supprimées à la fin de l’exemple de code. 

## <a name="next-steps"></a>Étapes suivantes

Ce guide de démarrage rapide explique comment gérer des objets blob et des conteneurs dans le stockage Blob Azure à l’aide de Node.js. Pour en savoir plus sur l’utilisation de ce kit SDK, reportez-vous au dépôt GitHub.

> [!div class="nextstepaction"]
> [Référentiel de kit SDK Azure Storage v10 pour JavaScript](https://github.com/Azure/azure-storage-js)
> [Référence API JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/storage/client?view=azure-node-preview)
