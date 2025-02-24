---
title: Sécurité d’Azure Blockchain Service
description: Concepts de sécurité et d’accès aux données Azure Blockchain Service
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dd0a33364ed9395a85478798e47352c533bd47dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028199"
---
# <a name="azure-blockchain-service-security"></a>Sécurité d’Azure Blockchain Service

Azure Blockchain Service assure la sécurisation et la disponibilité de vos données à l’aide de différentes fonctionnalités Azure. Les données sont sécurisées par le biais de processus d’isolation, de chiffrement et d’authentification.

## <a name="isolation"></a>Isolation

Les ressources Azure Blockchain Service sont isolées dans un réseau virtuel privé. Chaque nœud de transaction et de validation correspond à une machine virtuelle. Les machines virtuelles d’un réseau virtuel ne peuvent pas communiquer directement avec celles d’un autre réseau virtuel. L’isolation offre l’assurance que la communication reste privée au sein du réseau virtuel. Pour plus d’informations sur l’isolation de réseau virtuel Azure, consultez l’article [Isolation dans le cloud public Azure](../../security/azure-isolation.md#networking-isolation).

![Diagramme de réseau virtuel](./media/data-security/vnet.png)

## <a name="encryption"></a>Chiffrement

Les données utilisateur sont stockées dans le service Stockage Azure. Elles sont chiffrées en transit et au repos à des fins de sécurité et de confidentialité. Pour plus d'informations, consultez les pages suivantes : [Guide de sécurité du Stockage Azure](../../storage/common/storage-security-guide.md).

## <a name="authentication"></a>Authentication

Les transactions peuvent être envoyées aux nœuds de blockchain par le biais d’un point de terminaison RPC. Les clients communiquent avec un nœud de transaction à l’aide d’un serveur proxy inverse qui gère l’authentification utilisateur et chiffre les données par l’intermédiaire du protocole SSL.

![Diagramme d’authentification](./media/data-security/authentication.png)

Il existe trois modes d’authentification pour l’accès RPC.

### <a name="basic-authentication"></a>Authentification de base

L’authentification de base utilise un en-tête d’authentification HTTP contenant le nom d’utilisateur et le mot de passe. Le nom d’utilisateur est le nom du nœud de blockchain. Le mot de passe est défini lors de l’approvisionnement d’un membre ou d’un nœud. Il peut être modifié à l’aide du Portail Azure ou de l’interface de ligne de commande (CLI) Azure.

### <a name="access-keys"></a>Clés d’accès

Les clés d’accès utilisent une chaîne générée de manière aléatoire incluse dans l’URL du point de terminaison. Deux clés d’accès permettent d’activer la rotation des clés. Les clés peuvent être régénérées à partir du Portail Azure et d’Azure CLI.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) utilise un mécanisme d’authentification basé sur les revendications, au cours duquel Azure AD authentifie l’utilisateur à l’aide des informations d’identification d’utilisateur Azure AD. Azure AD assure la gestion des identités basée sur le cloud, et permet aux clients d’utiliser une seule et même identité dans l’ensemble d’une entreprise et d’accéder aux applications dans le cloud. Azure Blockchain Service s’intègre à Azure AD, autorisant ainsi la fédération d’ID, l’authentification unique et l’authentification multifacteur. Vous pouvez attribuer des utilisateurs, des groupes et des rôles d’application de votre organisation pour l’accès aux membres et aux nœuds de blockchain.

Le proxy client Azure AD est disponible sur [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). Le proxy client dirige l’utilisateur vers la page de connexion Azure AD et obtient un jeton du porteur dès que l’authentification réussit. Par la suite, l’utilisateur connecte une application cliente Ethereum, telle que Geth ou Truffle, au point de terminaison du proxy client. Enfin, lorsqu’une transaction est envoyée, le proxy client injecte le jeton du porteur dans l’en-tête http, et le proxy inverse valide le jeton à l’aide du protocole OAuth.

## <a name="keys-and-ethereum-accounts"></a>Clés et comptes Ethereum

Lorsque vous approvisionnez un membre Azure Blockchain Service, un compte Ethereum et une paire de clés publique et privée sont générés. La clé privée est utilisée pour l’envoi de transactions à la blockchain. Le compte Ethereum correspond aux 20 derniers octets du code de hachage de la clé publique. Ce compte est également appelé portefeuille.

La paire de clés privée et publique est stockée sous la forme d’un fichier de clé au format JSON. La clé privée est chiffrée à l’aide du mot de passe entré lors de la création du service de registre blockchain.

Les clés privées sont utilisées pour la signature numérique des transactions. Dans les blockchains privées, un contrat intelligent signé par une clé privée représente l’identité du signataire. Pour vérifier la validité de la signature, le récepteur peut comparer la clé publique du signataire avec l’adresse calculée à partir de la signature.

Les clés de constellation servent à identifier de manière unique un nœud Quorum. Ces clés sont générées lors de l’approvisionnement des nœuds et sont spécifiées dans le paramètre « privateFor » d’une transaction privée dans Quorum.

## <a name="next-steps"></a>Étapes suivantes

[Configurer les nœuds de transaction Azure Blockchain Service](configure-transaction-nodes.md)
