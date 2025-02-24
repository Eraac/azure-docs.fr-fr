---
title: Copier ou déplacer des données vers Stockage Azure avec AzCopy v10 | Microsoft Docs
description: AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des données vers, depuis ou entre des comptes de stockage. Cet article vous aide à télécharger AzCopy, connectez-vous à votre compte de stockage et transférez des fichiers.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 94aca33b2f12c1c39297221a856296dcca052b0f
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565798"
---
# <a name="get-started-with-azcopy"></a>Bien démarrer avec AzCopy

AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des blobs ou des fichiers vers ou depuis un compte de stockage. Cet article vous aide à télécharger AzCopy, connectez-vous à votre compte de stockage et transférez des fichiers.

> [!NOTE]
> La version actuellement prise en charge d’AzCopy est la **version 10**.
>
> Si vous avez besoin d’utiliser AzCopy **v8.1**, consultez la section [Utiliser la version précédente d’AzCopy](#previous-version) de cet article.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Télécharger AzCopy

Commencez par télécharger le fichier exécutable AzCopy V10 dans un répertoire sur votre ordinateur. 

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

> [!NOTE]
> Si vous voulez copier des données depuis et vers le service [Table de stockage Azure](https://docs.microsoft.com/azure/storage/tables/table-storage-overview), installez [AzCopy version 7.3](https://aka.ms/downloadazcopynet).

## <a name="run-azcopy"></a>Exécuter AzCopy

Pour plus de commodité, vous pouvez envisager d’ajouter l’emplacement du répertoire de l’exécutable AzCopy dans votre chemin d’accès du système. De cette façon, vous pouvez saisir `azcopy` à partir de n’importe quel répertoire sur votre système.

Si vous choisissez de ne pas ajouter le répertoire AzCopy à votre chemin d’accès, vous devrez remplacer les répertoires par l’emplacement de votre fichier exécutable AzCopy et saisir `azcopy` ou `.\azcopy` dans les invites de commandes Windows PowerShell.

Pour afficher une liste de commandes, saisissez `azcopy -h` et appuyez sur la touche ENTRÉE.

Pour en savoir plus sur une commande spécifique, incluez simplement le nom de la commande (par exemple : `azcopy list -h`).

![Aide en ligne](media/storage-use-azcopy-v10/azcopy-inline-help.png)

> [!NOTE] 
> En tant que propriétaire de votre compte de stockage Azure, aucune autorisation d’accès aux données ne vous est automatiquement attribuée. Avant d’effectuer une opération pertinente avec AzCopy, vous devez choisir comment fournir les informations d’identification d’autorisation du service de stockage. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Choisissez comment vous allez fournir des informations d’identification d’autorisation.

Vous pouvez fournir des informations d’identification d’autorisation à l’aide d’Azure Active Directory (AD), ou en utilisant un jeton de signature d’accès partagé (SAP).

Utilisez ce tableau pour vous guider :

| Type de stockage | Méthode actuellement prise en charge pour l’autorisation |
|--|--|
|**Stockage Blob** | Azure AD et SAS |
|**Stockage de blobs (espace de noms hiérarchique)** | Azure AD et SAS |
|**Stockage Fichier** | SAS uniquement |

### <a name="option-1-use-azure-ad"></a>Option 1 : Utilisez Azure AD

En utilisant Azure AD, vous pouvez fournir des informations d’identification en une seule fois au lieu d’avoir à ajouter un jeton SAS à chaque commande.  

Le niveau d’autorisation dont vous avez besoin repose sur votre volonté de planifier le chargement des fichiers, ou juste leur téléchargement.

Si vous voulez simplement télécharger les fichiers, vérifiez que le [Lecteur des données blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) a été affecté à votre identité d’utilisateur ou au principal de service. 

> [!NOTE]
> Les identités d’utilisateurs et les principaux de service sont chacun un type de *principal de sécurité*, donc nous utiliserons le terme *principal de sécurité* pour le reste de cet article.

Si vous souhaitez télécharger des fichiers, vérifiez qu’un de ces rôles a été attribué à votre principal de sécurité :

- [Contributeur aux données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Propriétaire des données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Ces rôles peuvent être attribués à votre identité sur l’une de ces étendues :

- Conteneur (système de fichiers)
- Compte de stockage
- Resource group
- Subscription

Pour savoir comment vérifier et attribuer des rôles, voir [Octroyer l’accès aux données blob et de file d’attente Azure avec RBAC dans le Portail Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE] 
> Gardez à l’esprit que les attributions de rôles RBAC peuvent prendre jusqu’à cinq minutes pour se propager.

Vous n’avez pas besoin qu’un de ces rôles soit assigné à votre responsable de la sécurité si votre responsable de la sécurité est ajouté à la liste de contrôle d’accès du conteneur ou du répertoire cible. Dans la liste de contrôle d’accès, votre responsable de la sécurité a besoin de droits d’écriture sur le répertoire cible et d’autorisation d’exécution sur le conteneur et chaque répertoire parent.

Pour en savoir plus, voir [Contrôle d’accès dans Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Authentifier l’identité d’un utilisateur

Une fois que vous avez vérifié que votre identité d’utilisateur est associée au niveau d’autorisation nécessaire, ouvrez une invite de commandes, saisissez la commande suivante, puis appuyez sur la touche ENTRÉE.

```azcopy
azcopy login
```

Si vous appartenez à plusieurs organisations, incluez l’ID de locataire de l’organisation à laquelle appartient le compte de stockage.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Remplacez l’espace réservé `<tenant-id>` par l’ID de locataire de l’organisation à laquelle appartient le compte de stockage. Dans le Portail Microsoft Azure, sélectionnez **Azure Active Directory > Propriétés > ID du répertoire**.

Cette commande retourne un code d’authentification et l’URL d’un site web. Ouvrez le site web, indiquez le code, puis choisissez le bouton **Suivant**.

![Créez un conteneur.](media/storage-use-azcopy-v10/azcopy-login.png)

Une fenêtre de connexion s’affiche. Dans cette fenêtre, connectez-vous à votre compte Azure à l’aide de vos informations d’identification de compte Azure. Une fois connecté, vous pouvez fermer la fenêtre du navigateur et commencer à utiliser AzCopy.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Authentifier un principal du service

C’est une excellente option si vous prévoyez d’utiliser AzCopy à l’intérieur d’un script qui fonctionne sans interaction de l’utilisateur. 

Avant d’exécuter ce script, vous devez vous connecter de manière interactive au moins une fois afin de pouvoir fournir à AzCopy les informations d’identification de votre fournisseur de services.  Ces informations d’identification sont stockées dans un fichier sécurisé et chiffré afin que votre script n’ait pas à fournir ces informations sensibles.

Vous pouvez vous connecter à votre compte en utilisant un secret client ou en utilisant le mot de passe d’un certificat qui est associé à l’enregistrement de l’application de votre directeur de service. 

Pour en savoir plus sur la création du principal du service, consultez la page [Procédure : Utilisez le portail pour créer une application Azure AD et un principal du service pouvant accéder aux ressources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Pour en savoir plus sur les principaux de service, consultez [Objets application et principal de service dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

##### <a name="using-a-client-secret"></a>Utilisation d’une clé secrète client

Commencez par définir la variable d’environnement `AZCOPY_SPA_CLIENT_SECRET` pour la clé secrète client de votre inscription d’application de principal de service. 

> [!NOTE]
> Assurez-vous de définir cette valeur à partir de votre invite de commande, et non dans les paramètres des variables d’environnement de votre système d’exploitation. De cette façon, la valeur n’est disponible que pour la session en cours.

Cet exemple montre comment vous pouvez le faire dans PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Envisagez d’utiliser une invite comme dans cet exemple. De cette façon, la clé secrète client n’apparaîtra pas dans l’historique des commandes de votre console. 

Ensuite, tapez la commande suivante, puis appuyez sur la touche ENTRÉE.

```azcopy
azcopy login --service-principal --application-id <application-id>
```

Remplacez l’espace réservé `<application-id>` par l’ID d’application de votre enregistrement d’application de principal de service.

##### <a name="using-a-certificate"></a>Utilisation d’un certificat

Si vous préférez utiliser vos propres informations d’identification pour l’autorisation, vous pouvez télécharger un certificat à l’enregistrement de votre application, puis utiliser ce certificat pour vous connecter.

En plus de télécharger votre certificat dans votre application, vous devrez également avoir une copie du certificat enregistrée sur la machine ou la VM sur laquelle AzCopy sera exécuté. Cette copie du certificat doit être au format.PFX ou.PEM et doit inclure la clé privée. La clé privée doit être protégée par un mot de passe. Si vous utilisez Windows et que votre certificat n’existe que dans une liste de certificats, assurez-vous d’exporter ce certificat vers un fichier PFX (y compris la clé privée). Pour obtenir des instructions, consultez la page sur [la cmdlet Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Ensuite, définissez la variable d’environnement `AZCOPY_SPA_CERT_PASSWORD` pour le mot de passe du certificat.

> [!NOTE]
> Assurez-vous de définir cette valeur à partir de votre invite de commande, et non dans les paramètres des variables d’environnement de votre système d’exploitation. De cette façon, la valeur n’est disponible que pour la session en cours.

Cet exemple montre comment vous pouvez le faire dans PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Ensuite, tapez la commande suivante, puis appuyez sur la touche ENTRÉE.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file>
```

Remplacez l’espace réservé `<path-to-certificate-file>` par le chemin d’accès complet ou relatif du fichier de certificat. AzCopy enregistre le chemin d’accès à ce certificat, mais il n’enregistre pas une copie du certificat, alors assurez-vous de garder ce certificat en place.

> [!NOTE]
> Envisagez d’utiliser une invite comme dans cet exemple. De cette façon, votre mot de passe n’apparaîtra pas dans l’historique des commandes de votre console. 

### <a name="option-2-use-a-sas-token"></a>Option 2 : Utiliser un jeton SAS

Vous pouvez ajouter un jeton SAP à chaque URL source ou de destination utilisée dans vos commandes AzCopy.

Cet exemple de commande copie de manière récursive les données d’un répertoire local dans un conteneur de blobs. Un jeton SAP fictif est ajouté à la fin de l’URL du conteneur.

```azcopy
azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Pour en savoir plus sur les jetons SAS et savoir comment en obtenir un, voir [Utilisation des signatures d’accès partagé (SAP)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="transfer-files"></a>Transférer des fichiers

Une fois que vous avez authentifié votre identité ou obtenu un jeton SAP, vous pouvez commencer le transfert des fichiers.

Pour obtenir des exemples de commandes, consultez l’un de ces articles.

- [Transférer des données avec AzCopy et le Stockage Blob](storage-use-azcopy-blobs.md)

- [Transférer des données avec AzCopy et le stockage de fichiers](storage-use-azcopy-files.md)

- [Transférer des données avec AzCopy et des compartiments Amazon S3](storage-use-azcopy-s3.md)

- [Transfert de données avec AzCopy et le stockage Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Utiliser AzCopy dans un script

Avant d’exécuter ce script, vous devez vous connecter de manière interactive au moins une fois afin de pouvoir fournir à AzCopy les informations d’identification de votre fournisseur de services.  Ces informations d’identification sont stockées dans un fichier sécurisé et chiffré afin que votre script n’ait pas à fournir ces informations sensibles. Pour obtenir des exemples, consultez la section [Authentifier votre principal du service](#service-principal) de cet article.

Au fil du temps, le [lien de téléchargement](#download-and-install-azcopy) AzCopy pointera vers les nouvelles versions d’AzCopy. Si votre script télécharge AzCopy, il se peut qu’il cesse de fonctionner si une version plus récente d’AzCopy modifie les fonctionnalités dont votre script dépend. 

Pour éviter ces problèmes, obtenez un lien statique (non modifiable) vers la version actuelle d’AzCopy. De cette façon, votre script télécharge la même version exacte d’AzCopy à chaque fois qu’il s’exécute.

Pour obtenir le lien, exécutez cette commande :

| Système d’exploitation  | Commande |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Pour Linux, `--strip-components=1` sur la commande `tar` supprime le dossier de niveau supérieur qui contient le nom de version, et extrait le binaire directement dans le dossier courant. Ceci permet de mettre à jour le script avec une nouvelle version de `azcopy` en mettant à jour uniquement l’URL `wget`.

L’URL apparaît dans la sortie de cette commande. Votre script peut alors télécharger AzCopy en utilisant cette URL.

| Système d’exploitation  | Commande |
|--------|-----------|
| **Linux** | `wget -O azcopyv10.tar https://azcopyvnext.azureedge.net/release20190301/azcopy_linux_amd64_10.0.8.tar.gz tar -xf azcopyv10.tar --strip-components=1 ./azcopy` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

## <a name="use-azcopy-in-storage-explorer"></a>Utiliser AzCopy dans l’Explorateur de stockage

Si vous souhaitez tirer parti des avantages d’AzCopy en termes de performances, mais préférez utiliser l’Explorateur de stockage plutôt que la ligne de commande pour interagir avec vos fichiers, activez AzCopy dans l’Explorateur de stockage. 

Dans l’Explorateur de stockage, choisissez **Préversion**->**Use AzCopy for Improved Blob Upload and Download** (Utiliser AzCopy pour le chargement et le téléchargement améliorés des blobs).

![Activer AzCopy en tant que moteur de transfert dans l’Explorateur Stockage Microsoft Azure](media/storage-use-azcopy-v10/enable-azcopy-storage-explorer.jpg)

> [!NOTE]
> Vous n’êtes pas obligé d’activer ce paramètre si vous avez activé un espace de noms hiérarchique sur votre compte de stockage. En effet, l’Explorateur de stockage utilise automatiquement AzCopy sur les comptes de stockage qui disposent d’un espace de noms hiérarchique.  

L’Explorateur Stockage utilise votre clé de compte pour effectuer des opérations, donc après vous être connecté à Storage Explorer, vous n’aurez pas besoin de fournir des informations d’autorisation supplémentaires.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Utiliser la version précédentes d’AzCopy

Si vous devez utiliser la version précédente d’AzCopy (AzCopy v8.1), consultez l’un des liens suivants :

- [AzCopy sur Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy sur Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurer, optimiser et dépanner AzCopy

Voir [Configurer, optimiser et dépanner AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Étapes suivantes

Si vous avez des questions, des problèmes ou des commentaires d’ordre général, saisissez-les sur la page [GitHub](https://github.com/Azure/azure-storage-azcopy).
