---
title: Qu’est-ce qu’Azure Key Vault ? | Microsoft Docs
description: Découvrez comment Azure Key Vault protège les clés de chiffrement et les secrets utilisés par les services et les applications cloud.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 43794b8ef4e0272362c7695eda75f5af36a77d1e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683458"
---
# <a name="what-is-azure-key-vault"></a>Qu’est-ce qu’Azure Key Vault ?

Les applications et les services cloud utilisent des clés de chiffrement et des secrets pour protéger les informations. Azure Key Vault protège ces clés et ces secrets. Lorsque vous utilisez Key Vault, vous pouvez chiffrer les clés d’authentification, les clés des comptes de stockage, les clés de chiffrement de données, les fichiers .pfx et les mots de passe à l’aide de clés protégées par des modules de sécurité matériels (HSM).

Key Vault aide à résoudre les problèmes suivants :

- **Gestion des secrets** : Stocker en toute sécurité et contrôler étroitement l’accès aux jetons, mots de passe, certificats, clés API et autres secrets.
- **Gestion des clés** : Créer et contrôler les clés de chiffrement qui chiffrent vos données. 
- **Gestion des certificats** : Configurer, gérer et déployer facilement des certificats SSL/TLS publiques et privés pour une utilisation avec Azure et vos ressources connectées internes. 
- **Store secrets avec HSM** : Utiliser des logiciels ou des HSM conformes aux normes FIPS 140-2 de niveau 2 pour protéger les secrets et les clés.

## <a name="basic-concepts"></a>Concepts de base

Azure Key Vault est un outil permettant de stocker les secrets et d’y accéder en toute sécurité. Un secret est un élément pour lequel vous voulez contrôler étroitement l’accès. Il peut s’agir de clés d’API, de mots de passe ou de certificats. Un coffre est un groupe logique de secrets.

Voici d’autres termes importants :

- **Tenant** : un locataire est l’organisation qui possède et gère une instance spécifique de services cloud Microsoft. Ce terme est souvent utilisé pour faire référence à l’ensemble des services Azure et Office 365 d’une organisation.

- **Propriétaire de coffre** : un propriétaire de coffre peut créer un coffre de clés, et bénéficier d’un accès et d’un contrôle complets à celui-ci. Le propriétaire du coffre peut également configurer l’audit pour consigner qui accède aux secrets et aux clés. Les administrateurs peuvent contrôler le cycle de vie de la clé. Ils peuvent déployer une nouvelle version de la clé, la sauvegarder, et effectuer les tâches associées.

- **Consommateur de coffre** : un consommateur de coffre peut effectuer des actions sur les ressources à l’intérieur du coffre de clés quand le propriétaire du coffre lui accorde l’accès de consommateur. Les actions disponibles varient selon les autorisations accordées.

- **Ressource** : une ressource est un élément gérable disponible par le biais d’Azure. Il peut s’agir par exemple de machines virtuelles, de comptes de stockage, d’applications web, de bases de données et de réseaux virtuels. Mais il en existe bien d’autres encore.

- **Groupe de ressources** : Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Le groupe de ressources peut inclure toutes les ressources de la solution, ou uniquement celles que vous souhaitez gérer en tant que groupe. Pour déterminer comment allouer des ressources aux groupes de ressources, choisissez l’approche la plus pertinente pour votre organisation.

- **Principal du service** : Un principal du service Azure est une identité de sécurité utilisée par les applications, les services et les outils d’automatisation créés par l’utilisateur pour accéder à des ressources Azure spécifiques. Il équivaut un peu à une « identité d’utilisateur » (nom d’utilisateur et mot de passe ou certificat) avec un rôle spécifique et des autorisations étroitement contrôlées. Un principal du service doit uniquement effectuer des opérations spécifiques, contrairement à une identité d’utilisateur générale. Il améliore la sécurité si vous lui octroyez seulement le niveau d’autorisation minimal nécessaire pour effectuer ses tâches de gestion.

- [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) : Azure AD est le service Active Directory pour un locataire. Chaque répertoire contient au moins un domaine personnalisé. Un répertoire peut avoir plusieurs abonnements associés, mais qu’un seul locataire.

- **ID de locataire Azure** : un ID de locataire est un moyen unique d’identifier une instance Azure AD au sein d’un abonnement Azure.

- **Identités managées** : Azure Key Vault permet de stocker en toute sécurité des informations d’identification et autres clés et secrets, mais votre code doit s’authentifier sur Key Vault pour les récupérer. L’utilisation d’une identité mangée simplifie la résolution de ce problème en donnant aux services Azure une identité automatiquement managée dans Azure AD. Vous pouvez utiliser cette identité pour vous authentifier sur Key Vault ou n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. Pour plus d’informations, consultez l’image ci-dessous et lisez la présentation des [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagramme illustrant le fonctionnement des identités managées pour les ressources Azure](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Authentication
Pour effectuer des opérations avec un coffre de clés, vous devez vous authentifier auprès de celui-ci. Il existe trois façons de s’authentifier auprès de Key Vault :

- [Identités managées pour les ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) : quand vous déployez une application sur une machine virtuelle dans Azure, vous pouvez attribuer une identité à votre machine virtuelle qui a accès à Key Vault. Vous pouvez également assigner une identité aux [autres ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). L’avantage de cette approche est que l’application ou le service ne gère pas la rotation du premier secret. Azure fait alterner automatiquement l’identité. Nous recommandons cette approche en tant que meilleure pratique. 
- **Principal de service et certificat** : vous pouvez utiliser un principal de service et un certificat associé qui a accès à Key Vault. Nous ne recommandons pas cette approche, car le propriétaire de l’application ou son développeur doit régulièrement renouveler le certificat.
- **Principal de service et secret** : bien que vous puissiez utiliser un principal de service et un secret pour vous authentifier auprès de Key Vault, cette option n’est pas recommandée. Il est difficile de renouveler automatiquement le secret de démarrage qui sert à l’authentification auprès de Key Vault.


## <a name="key-vault-roles"></a>Rôles de Key Vault

Utilisez le tableau suivant afin de mieux comprendre comment Key Vault peut permettre de répondre aux besoins des développeurs et des administrateurs de sécurité.

| Rôle | Définition du problème | Résolu par Azure Key Vault |
| --- | --- | --- |
| Développeur d’une application Azure |« Je souhaite écrire une application Azure qui utilise des clés pour la signature et le chiffrement. Mais ces clés doivent être externes à mon application, afin que la solution soit adaptée à une application répartie au niveau géographique. <br/><br/>Je souhaite protéger ces clés et secrets, sans avoir à écrire le code moi-même, et je veux qu’ils soient faciles à utiliser pour moi à partir de mes applications, avec des performances optimales. » |√ Les clés sont stockées dans un coffre et appelées par un URI, si nécessaire.<br/><br/> √ Les clés sont protégées par Azure, à l’aide d’algorithmes standard, de longueurs de clé et de modules de sécurité matériel.<br/><br/> √ Les clés sont traitées dans des modules de sécurité matériels situés dans les mêmes centres de données Azure que les applications. Cette méthode garantit une meilleure fiabilité et une latence plus faible que si les clés résidaient dans un emplacement séparé, par exemple localement. |
| Développeur de logiciels SaaS (Software as a service) |« Je ne veux pas prendre la responsabilité des clés et secrets de mes clients. <br/><br/>Je veux que les clients détiennent et gèrent leurs clés, pour pouvoir me concentrer sur ce que je fais le mieux, c’est-à-dire fournir les principales fonctionnalités du logiciel. » |√ Les clients peuvent importer leurs propres clés dans Azure et les gérer. Lorsqu’une application SaaS doit effectuer des opérations de chiffrement à l’aide des clés des clients, Key Vault s’en charge à sa place. L’application ne voit pas les clés des clients. |
| Responsable de la sécurité |« Je veux être sûr que nos applications sont conformes à la norme FIPS 140-2HSM de niveau 2 pour la gestion sécurisée des clés. <br/><br/>Je souhaite m’assurer que mon organisation contrôle le cycle de vie d’une clé et peut surveiller son utilisation. <br/><br/>Et bien que nous utilisions plusieurs ressources et services Azure, je souhaite gérer les clés à partir d’un emplacement unique dans Azure. » |√ Les modules de sécurité matériels sont certifiés FIPS 140-2 de niveau 2.<br/><br/>√ Key Vault a été conçu de manière à ce que Microsoft ne puisse pas afficher ni extraire vos clés.<br/><br/>√ L’utilisation de la clé est consignée en temps quasi réel.<br/><br/>√ Le coffre fournit une interface unique, indépendamment du nombre de coffres dont vous disposez dans Azure, des régions qui sont prises en charge et des applications qui les utilisent. |

Toute personne disposant d’un abonnement Azure peut créer et utiliser des coffres de clés. Bien que Key Vault procure des avantages aux développeurs et aux administrateurs de sécurité, il peut être implémenté et géré par l’administrateur d’une organisation qui gère les autres services Azure. Par exemple, cet administrateur peut se connecter avec un abonnement Azure, créer un coffre pour l’organisation dans lequel stocker les clés et avoir la responsabilité de tâches opérationnelles comme celles-ci :

- créer ou importer une clé ou un secret ;
- supprimer ou effacer une clé ou un secret ;
- Autoriser des utilisateurs ou des applications à accéder au coffre de clés, afin qu’ils puissent gérer ou utiliser les clés et les clés secrètes
- configurer l’utilisation de la clé (par exemple, signer ou chiffrer) ;
- surveiller l’utilisation de clés.

Cet administrateur donne ensuite aux développeurs les URI à appeler à partir de leurs applications. Cet administrateur donne également les informations de journalisation sur l’utilisation de la clé à l’administrateur de sécurité. 

![Vue d'ensemble du fonctionnement d'Azure Key Vault][1]

Les développeurs peuvent également gérer les clés directement à l’aide d’API. Pour plus d’informations, consultez le [guide du développeur de Key Vault](key-vault-developers-guide.md).

## <a name="next-steps"></a>Étapes suivantes

Apprenez à [sécuriser votre coffre de clés](key-vault-secure-your-key-vault.md).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault est disponible dans la plupart des régions. Pour plus d’informations, consultez la [page de tarification de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
