---
title: Migration des zones privées Azure DNS héritées vers le nouveau modèle de ressource
description: Ce guide fournit des instructions pas à pas pour migrer des zones DNS privées héritées vers le dernier modèle de ressource.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: e7ebbf35cd572601f02a69930b58811686a92c86
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276317"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Migration des zones privées Azure DNS héritées vers le nouveau modèle de ressource

Nous avons fourni un nouveau modèle de ressource/d’API pour les zones privées Azure DNS dans le cadre de l’actualisation de la préversion. L’actualisation de la préversion propose de nouvelles fonctionnalités et supprime plusieurs limitations et restrictions de la préversion publique initiale. Toutefois, ces avantages ne sont pas disponibles sur les zones DNS privées créées à l’aide de l’API héritée. Pour obtenir les avantages de la nouvelle version, vous devez migrer vos ressources de zones DNS privées héritées vers le nouveau modèle de ressource. Le processus de migration est simple et nous vous fournissons un script PowerShell pour automatiser ce processus. Ce guide fournit des instructions pas à pas pour migrer vos zones privées Azure DNS vers le nouveau modèle de ressource.

## <a name="prerequisites"></a>Prérequis

Vérifiez que vous avez installé la version la plus récente d’Azure PowerShell. Pour plus d’informations sur Azure PowerShell (Az) et la manière de l’installer, visitez https://docs.microsoft.com/powershell/azure/new-azureps-module-az

Vérifiez que le module Az.PrivateDns pour Azure PowerShell est installé. Pour installer ce module, ouvrez une fenêtre PowerShell avec élévation de privilèges (mode administratif) et entrez la commande suivante :

```powershell
Install-Module -Name Az.PrivateDns -AllowPrerelease
```

>[!IMPORTANT]
>Le processus de migration est entièrement automatisé et ne devrait pas entraîner de temps d’arrêt. Toutefois, si vous utilisez des zones privées Azure DNS (préversion) dans un environnement de production critique, vous devez exécuter le processus de migration ci-dessous pendant une fenêtre de maintenance planifiée. Vérifiez que vous ne modifiez pas la configuration ou les jeux d’enregistrements d’une zone DNS privée quand vous exécutez le script de migration.

## <a name="installing-the-script"></a>Installation du script

Ouvrez une fenêtre PowerShell avec élévation de privilèges (mode administratif) et exécutez la commande suivante :

```powershell
install-script PrivateDnsMigrationScript
```

Entrez « A » lorsque vous êtes invité à installer le script.

![Installation du script](./media/private-dns-migration-guide/install-migration-script.png)

Vous pouvez également obtenir la dernière version du script PowerShell à l’adresse https://www.powershellgallery.com/packages/PrivateDnsMigrationScript

## <a name="running-the-script"></a>Exécution du script

Exécutez la commande suivante pour exécuter le script :

```powershell
PrivateDnsMigrationScript.ps1
```

![Exécution du script](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Entrer l’ID d’abonnement et se connecter à Azure

Vous allez être invité à entrer l’ID d’abonnement qui contient les zones DNS privées que vous voulez migrer. Vous devrez vous connecter à votre compte Azure. Procédez à la connexion afin que le script puisse accéder aux ressources des zones DNS privées dans l’abonnement.

![Connexion à Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Sélectionner les zones DNS à migrer

Le script obtient la liste de toutes les zones DNS privées de l’abonnement et vous invite à confirmer celles que vous voulez migrer. Entrez « A » pour migrer toutes les zones DNS privées. Une fois que vous exécutez cette étape, le script va créer des zones DNS privées à l’aide du nouveau modèle de ressource, puis copier les données dans la nouvelle zone DNS. Cette étape ne modifie aucunement vos zones DNS privées existantes.

![Sélectionner les zones DNS](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Basculement de la résolution DNS vers les nouvelles zones DNS

Une fois que les zones et enregistrements ont été copiés dans le nouveau modèle de ressource, le script vous invite à basculer la résolution DNS vers les nouvelles zones DNS. Cette étape supprime l’association entre les zones DNS privées héritées et vos réseaux virtuels. Quand la zone héritée n’est plus liée aux réseaux virtuels, les nouvelles zones DNS créées à l’étape ci-dessus prennent automatiquement le relais de la résolution DNS pour ces réseaux virtuels.

Sélectionnez « A » pour basculer la résolution DNS pour tous les réseaux virtuels.

![Basculement de la résolution de noms](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Vérifier la résolution DNS

Avant de continuer, vérifiez que la résolution DNS sur vos zones DNS fonctionne comme prévu. Vous pouvez vous connecter à vos machines virtuelles Azure et émettre une requête nslookup sur les zones migrées pour vérifier que la résolution DNS fonctionne.

![Vérifier la résolution de noms](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Si vous remarquez que les requêtes DNS ont du mal à se résoudre, patientez quelques minutes et faites une nouvelle tentative. Si les requêtes DNS fonctionnent comme prévu, entrez « Y » quand le script vous invite à supprimer le réseau virtuel de la zone DNS privée.

![Confirmer la résolution de noms](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Si, pour une raison quelconque, la résolution DNS sur les zones migrées ne fonctionne pas comme prévu, entrez « N » à l’étape ci-dessus pour que le script rebascule la résolution DNS vers les zones héritées. Créez un ticket de support pour que nous puissions vous aider dans la migration de vos zones DNS.

## <a name="cleanup"></a>Nettoyage

Cette étape va supprimer les zones DNS héritées, vous devez donc l’exécuter uniquement après avoir vérifié que la résolution DNS fonctionne comme prévu. Vous allez être invité à supprimer chaque zone DNS privée. Entrez « Y » à chaque invite après avoir vérifié que la résolution DNS pour les zones fonctionne correctement.

![Nettoyer](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Mettre à jour votre automatisation

Si vous avez recours à une automatisation avec des modèles, des scripts PowerShell ou du code personnalisé développé à l’aide d’un SDK, vous devez mettre à jour votre automatisation afin d’utiliser le nouveau modèle de ressource pour les zones DNS privées. Voici les liens vers la nouvelle documentation CLI/PS/SDK des zones DNS privées.
* [API REST pour les zones privées Azure DNS](https://docs.microsoft.com/rest/api/dns/privatedns/privatezones)
* [CLI pour les zones privées Azure DNS](https://docs.microsoft.com/cli/azure/ext/privatedns/network/private-dns?view=azure-cli-latest)
* [PowerShell pour les zones privées Azure DNS](https://docs.microsoft.com/powershell/module/az.privatedns/?view=azps-2.3.2)
* [SDK pour les zones privées Azure DNS](https://docs.microsoft.com/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Encore besoin d’aide ?

Créez un ticket de support si vous avez besoin d’aide avec le processus de migration ou si, pour une raison quelconque, les étapes listées ci-dessus ne fonctionnent pas pour vous. Incluez le fichier de transcription généré par le script PowerShell dans votre ticket de support.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment créer une zone privée dans Azure DNS à l’aide d’[Azure PowerShell](./private-dns-getstarted-powershell.md) ou d’[Azure CLI](./private-dns-getstarted-cli.md).

* Passez en revue certains [scénarios de zones privées](./private-dns-scenarios.md) courants qui peuvent être réalisés avec des zones privées dans Azure DNS.

* Consultez la [FAQ sur les zones DNS privées](./dns-faq-private.md) pour étudier certaines des questions et réponses courantes relatives aux zones privées dans Azure DNS, notamment le comportement spécifique auquel vous pouvez vous attendre pour certains types d’opérations.

* Pour plus d’informations sur les zones et enregistrements DNS, consultez [Vue d’ensemble des enregistrements et zones DNS](dns-zones-records.md).

* Découvrez certaines des autres [fonctionnalités de réseau](../networking/networking-overview.md) clés d’Azure.
