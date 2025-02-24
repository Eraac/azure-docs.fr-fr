---
title: À propos d’Azure Lab Services | Microsoft Docs
description: Découvrez comment Lab Services facilite la création, la gestion et la sécurisation de laboratoires grâce à des machines virtuelles pouvant être utilisées par les développeurs, les testeurs, les enseignants et les étudiants, entre autres.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/13/2018
ms.author: spelluru
ms.openlocfilehash: 3c23cc4fc7647cffa98cff9310498f683fda6328
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275716"
---
# <a name="an-introduction-to-azure-lab-services"></a>Présentation d’Azure Lab Services
Azure Lab Services vous permet de configurer rapidement un environnement pour votre équipe (par exemple : un environnement de développement, un environnement de test, un environnement de laboratoire de salle de classe) dans le cloud. Le propriétaire d’un laboratoire crée un laboratoire, configure Windows ou des machines virtuelles Linux, installe les logiciels et outils nécessaires, puis les rend disponibles aux utilisateurs du laboratoire. Les utilisateurs du laboratoire se connectent à des machines virtuelles (VM) dans le laboratoire puis s’en servent pour leurs tâches quotidiennes, leurs projets à court terme ou les exercices en classe. Lorsque les utilisateurs commencent à exploiter les ressources du laboratoire, un administrateur de laboratoire peut analyser les coûts et l’utilisation entre plusieurs laboratoires et définir des stratégies globales afin d’optimiser les coûts de l’équipe ou de l’organisation.

> [!IMPORTANT]
> **Azure DevTest Labs** intègre de nouveaux types de laboratoires (Azure Lab Services).
>  
> Azure Lab Services vous permet de créer des types de labos managés, comme des labos de salle de classe. Le service lui-même gère toute l’infrastructure d’un type de labo managé, de l’augmentation de la capacité des machines virtuelles à la gestion des erreurs, en passant par la mise à l’échelle de cette infrastructure. Pour l’instant, [DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) et [Azure Lab Services](https://azure.microsoft.com/services/lab-services/) continuent d’être des services distincts dans le portail Azure. 

## <a name="key-capabilities"></a>Fonctionnalités clés

Azure Lab Services prend en charge les fonctionnalités clés suivantes :

- **Configuration rapide et flexible d’un laboratoire**. Grâce à Azure Lab Services, les propriétaires de laboratoire peuvent rapidement configurer un laboratoire selon leurs besoins. Le service offre la possibilité d’effectuer tout le travail de l’infrastructure Azure pour les labos managés, ou permet aux propriétaires de labo de gérer et de personnaliser l’infrastructure dans leur abonnement. Le service intègre une mise à l’échelle et une résilience de l’infrastructure pour les laboratoires que le service gère pour vous.
- **Expérience simplifiée pour les utilisateurs du laboratoire**. Dans un labo managé, comme un labo de salle de classe, les utilisateurs peuvent s’inscrire avec un code d’inscription et accéder ainsi à tout moment aux ressources du labo. Dans un laboratoire créé dans DevTest Labs, le propriétaire d’un laboratoire peut autoriser les utilisateurs à créer et à accéder aux machines virtuelles, à gérer et à réutiliser des disques de données, et à configurer des clés secrètes réutilisables.  
- **Optimisation et analyse des coûts**. Un propriétaire du laboratoire peut définir des planifications de laboratoire pour arrêter et démarrer automatiquement des machines virtuelles. Le propriétaire du laboratoire peut définir une planification pour spécifier les plages horaires où les machines virtuelles du laboratoire sont accessibles aux utilisateurs, définir des stratégies d’utilisation par utilisateur ou par un laboratoire afin d’optimiser les coûts, et pour analyser les tendances d’utilisation et d’activité d’un laboratoire. Pour les types de labos managés comme les labos de salle de classe, un sous-ensemble plus petit d’options d’optimisation et d’analyse des coûts est actuellement disponible.
- **Sécurité intégrée**. Le propriétaire du laboratoire peut configurer des réseaux virtuels privés et un sous-réseau pour un laboratoire, et activer une adresse IP publique partagée. Les utilisateurs du laboratoire peuvent accéder en toute sécurité aux ressources à l’aide du réseau virtuel configuré avec ExpressRoute ou d’un VPN de site à site. (uniquement disponible dans DevTest Labs actuellement)
- **Intégration dans vos flux de travail et outils**. Azure Lab Services vous permet d’intégrer les laboratoires dans le site Web et les systèmes de gestion de votre organisation. Vous pouvez automatiquement mettre en service les environnements à l’aide de vos outils de déploiement/intégration (CI/CD) en continu. (uniquement disponible dans DevTest Labs actuellement)

> [!NOTE]
> Actuellement, Azure Lab Services ne prend en charge que les machines virtuelles créées à partir d’images de la Place de marché Azure. Si vous souhaitez utiliser des images personnalisées ou créer d’autres ressources PaaS dans un environnement lab, utilisez DevTest Labs. Pour plus d’informations, voir [Créer une image personnalisée dans DevTest Labs](devtest-lab-create-custom-image-from-vm-using-portal.md) et [Créer des environnements lab à l’aide de modèles Resource Manager](devtest-lab-create-environment-from-arm.md).

## <a name="scenarios"></a>Scénarios

Voici quelques scénarios pris en charge par Azure Lab Services :

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Configurer un laboratoire redimensionnable d’ordinateurs dans le cloud pour votre classe  

- Créer un laboratoire de salle de classe managé. Vous indiquez au service exactement ce dont vous avez besoin, et celui-ci crée et gère l’infrastructure du laboratoire pour vous. Vous pouvez ainsi vous concentrer sur votre classe, sans vous soucier des aspects techniques d’un laboratoire.
- Offrir aux élèves un laboratoire de machines virtuelles configurées avec exactement ce qui est nécessaire pour une classe. Accorder à chaque élève un nombre limité d’heures afin d’utiliser les machines virtuelles pour le travail en classe.  
- Déplacer vers le cloud le laboratoire d’ordinateurs physiques de votre école. Redimensionner automatiquement le nombre de machines virtuelles uniquement à l’utilisation maximale et au seuil de coûts que vous définissez sur le laboratoire.
- Supprimer le laboratoire d’un simple clic, une fois que vous avez terminé.

### <a name="use-devtest-labs-for-development-environments"></a>Utiliser DevTest Labs pour les environnements de développement

Azure Labs DevTest peut être utilisé pour implémenter de nombreux scénarios clés, dont celui qui implique l’utilisation de DevTest Labs dans l’hébergement d’ordinateurs de développement pour les développeurs. Dans ce scénario, DevTest Labs offre les avantages suivants :

- Permettre aux développeurs de rapidement mettre en service leurs ordinateurs de développement à la demande.
- Mettre en service les environnements Windows et Linux avec des modèles et des artefacts réutilisables.
- Si besoin, les développeurs peuvent facilement personnaliser leurs ordinateurs de développement.
- Les administrateurs peuvent contrôler les coûts en s’assurant que les développeurs n’obtiennent pas plus de machines virtuelles qu’ils n’en ont besoin pour le développement, et les machines virtuelles sont arrêtées lorsqu’elles ne sont pas utilisées.

Pour plus d’informations, consultez [Utiliser DevTest Labs pour le développement](devtest-lab-developer-lab.md).

### <a name="use-devtest-labs-for-test-environments"></a>Utiliser DevTest Labs pour les environnements de test

Vous pouvez utiliser Azure DevTest Labs pour implémenter de nombreux scénarios clés, mais un scénario principal implique l’utilisation de DevTest Labs pour héberger des machines à l’usage des testeurs. Dans ce scénario, DevTest Labs offre les avantages suivants :

- Les testeurs peuvent tester la dernière version de l’application en approvisionnant rapidement des environnements Windows et Linux à l’aide d’artefacts et de modèles réutilisables.
- Ils peuvent faire monter en puissance leur test de charge en approvisionnant plusieurs agents de test.
- Les administrateurs peuvent contrôler les coûts en s’assurant que les testeurs n’obtiennent pas plus de machines virtuelles qu’ils n’en ont besoin pour les tests, et les machines virtuelles sont arrêtées lorsqu’elles ne sont pas utilisées.

Pour plus d’informations, consultez [Utiliser DevTest Labs pour les tests](devtest-lab-test-env.md).

## <a name="types-of-labs"></a>Types de labos
Vous pouvez créer deux types de labos avec Azure Lab Services : les **types de labos managés** et les **labos**. Si vous voulez simplement ajouter ce dont vous avez besoin dans un labo et laisser le service configurer et gérer l’infrastructure requise, choisissez l’un des **types de labos managés**. Actuellement, le **labo de salle de classe** est le seul type de labo managé que vous pouvez créer avec Azure Lab Services. Pour gérer votre propre infrastructure, créez un labo avec **Azure DevTest Labs**.

Les sections suivantes décrivent plus en détail ces laboratoires. 

## <a name="managed-lab-types"></a>Types de laboratoires gérés
Azure Lab Services vous permet de créer des laboratoires dont l’infrastructure est gérée par Azure. Dans cet article, ces labos sont appelés types de labos managés. Les types de labos managés se déclinent en différents types adaptés à différents besoins. Actuellement, le seul type de labo managé pris en charge est le **labo de salle de classe**. 

Les types de labos managés permettent de commencer sans délai, avec une procédure d’installation minimale. Le service gère lui-même toute l’infrastructure du laboratoire, du lancement des machines virtuelles à la gestion des erreurs, en passant par la mise à l’échelle de cette infrastructure. Pour créer un type de labo managé, par exemple un labo de salle de classe, commencez par créer un compte Lab pour votre organisation. Il sert de compte central, dans lequel sont gérés tous les laboratoires de l’entreprise. 

Lorsque vous créez et utilisez des ressources Azure dans ces types de labos managés, le service crée et gère les ressources dans des abonnements Microsoft internes, et non dans votre propre abonnement Azure. Le service effectue le suivi de l’utilisation de ces ressources dans des abonnements Microsoft internes, utilisation qui est refacturée à votre abonnement Azure associé au compte Lab.   

Voici quelques **cas d’usage des types de labos managés** : 

- Proposer aux étudiants un laboratoire de machines virtuelles configurées précisément pour répondre aux besoins de la classe ; Donner à chacun un temps limité d’utilisation des machines virtuelles pour le travail à la maison ou les projets personnels.
- Configurer un pool de machines virtuelles offrant des performances élevées en calcul pour effectuer des recherches nécessitant beaucoup de ressources système ou de graphismes. Exécutez les machines virtuelles suivant vos besoins, et nettoyez-les une fois que vous avez terminé. 
- Déplacer vers le cloud l’ordinateur physique qui servait de laboratoire à votre établissement. Redimensionner automatiquement et exclusivement le nombre de machines virtuelles à la limite maximale d’utilisation et au seuil de coûts définis sur le laboratoire.  
- Configurer rapidement un laboratoire de machines virtuelles pour héberger un hackathon. Supprimez le laboratoire d’un simple clic une fois que vous avez terminé. 


## <a name="devtest-labs"></a>DevTest Labs
Certains cas de figure impliquent de gérer soi-même toute l’infrastructure et toute la configuration dans son propre abonnement. Pour cela, vous pouvez créer un laboratoire avec Azure DevTest Labs sur le Portail Azure. Il n’est pas nécessaire dans ce cas de créer un compte Lab. Ces labos n’apparaissent pas dans le compte Lab (qui sert aux types de labos managés).  

Voici quelques **cas d’usage des laboratoires DevTest** : 

- Configurer rapidement un laboratoire de machines virtuelles pour héberger un hackathon ou une session pratique lors d’une conférence. Supprimez le laboratoire d’un simple clic une fois que vous avez terminé. 
- Créer un pool de machines virtuelles configurées avec une application et offrir à l’équipe un accès à une machine virtuelle pour le dépistage des bogues.  
- Fournir aux développeurs des machines virtuelles configurées avec tous les outils nécessaires. Planifiez le démarrage et l’arrêt automatiques pour réduire les coûts. 
- Créer à plusieurs reprises un laboratoire de machines de test dans le cadre d’un déploiement. Effectuez les derniers tests et nettoyez les machines de test une fois que vous avez terminé. 
- Configurer différemment des machines virtuelles et plusieurs agents de test à des fins de tests de performances et de mise à l’échelle. 
- Proposer des sessions de formation aux clients à l’aide d’un laboratoire configuré avec la dernière version du produit. Donnez à chaque client un temps limité d’utilisation dans le laboratoire. 


## <a name="managed-lab-types-vs-devtest-labs"></a>Types de laboratoires gérés et DevTest Labs
Le tableau suivant compare les deux types de laboratoires pris en charge par Azure Lab Services : 

| Caractéristiques | Types de laboratoires gérés | DevTest Labs |
| -------- | ----------------- | ---------- |
| Gestion de l’infrastructure Azure dans le laboratoire |  Gérée automatiquement par le service. | Gérée par vos soins.  |
| Résilience intégrée en cas de problèmes d’infrastructure | Gérée automatiquement par le service. | Gérée par vos soins.  |
| Gestion des abonnements | Le service gère l’allocation des ressources au sein des abonnements Microsoft sur lesquels il repose. La mise à l’échelle est gérée automatiquement par le service. | Gérée par vos soins dans votre propre abonnement Azure. Aucune mise à l’échelle automatique des abonnements. |
| Déploiement Azure Resource Manager au sein du laboratoire | Non disponible | Disponible |

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants : 

- [À propos des laboratoires de salle de classe](./classroom-labs/classroom-labs-overview.md)
- [À propos de DevTest Labs](devtest-lab-overview.md)
