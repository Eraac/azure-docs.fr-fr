---
title: À propos des connexions VPN point à site Azure | Microsoft Docs
description: Cet article vous aide à comprendre les connexions point à site et à choisir le type d’authentification à une passerelle VPN P2S que vous devez utiliser.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: cherylmc
ms.openlocfilehash: f1e014bb14b2b5c1ae924f4371e08aa8bf8698f2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056477"
---
# <a name="about-point-to-site-vpn"></a>À propos du VPN de point à site

Une connexion par passerelle VPN point à site (P2S) vous permet de créer une connexion sécurisée à votre réseau virtuel à partir d’un ordinateur de client individuel. Une connexion P2S est établie en étant démarrée à partir de l’ordinateur client. Cette solution est utile pour les télétravailleurs souhaitant se connecter à un réseau virtuel à partir d’un emplacement distant, comme depuis leur domicile ou pendant une conférence. De même, l’utilisation d’un VPN P2S est une solution utile qui constitue une alternative au VPN Site à Site (S2S) lorsqu’un nombre restreint de clients doit se connecter à un réseau virtuel. Cet article s'applique au modèle de déploiement Resource Manager.

## <a name="protocol"></a>Quel protocole est utilisé par le P2S ?

La connexion VPN point à site peut utiliser un des protocoles suivants :

* **Protocole OpenVPN®**, un protocole VPN basé sur SSL/TLS. Une solution VPN SSL peut pénétrer des pare-feu puisque la plupart des pare-feu ouvrent le port de sortie TCP 443 utilisé par le protocole SSL. Vous pouvez utiliser OpenVPN pour vous connecter à partir d’appareils Android, iOS (versions 11.0 et versions ultérieures), Windows, Linux et Mac (OSX 10.13 et versions ultérieures).

* Le Protocole SSTP (Secure Socket Tunneling Protocol) est un protocole propriétaire VPN basé sur le protocole SSL. Une solution VPN SSL peut pénétrer des pare-feu puisque la plupart des pare-feu ouvrent le port de sortie TCP 443 utilisé par le protocole SSL. SSTP est pris en charge sur les appareils Windows uniquement. Azure prend en charge toutes les versions de Windows disposant de SSTP (Windows 7 et versions ultérieures).

* Un VPN IKEv2 est une solution VPN IPsec basée sur des normes. Un VPN IKEv2 peut être utilisé pour se connecter à partir d’appareils Mac (OSX 10.11 et versions ultérieures).


>[!NOTE]
>IKEv2 et OpenVPN pour P2S sont uniquement disponibles pour le modèle de déploiement Resource Manager. Ils ne sont pas disponibles pour le modèle de déploiement classique.
>

## <a name="authentication"></a>Comment les clients VPN P2S sont-ils authentifiés ?

Avant qu’Azure n’accepte une connexion VPN P2S, l’utilisateur doit d’abord s’authentifier. Azure permet aux utilisateurs qui se connectent de s’authentifier à l’aide de deux mécanismes.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>S’authentifier à l’aide d’une authentification par certificat Azure native

Lorsque vous utilisez l’authentification par certificat Azure native, un certificat client présent sur l’appareil est utilisé pour authentifier l’utilisateur qui se connecte. Les certificats client sont générés à partir d’un certificat racine de confiance et installés sur chaque ordinateur client. Vous pouvez utiliser un certificat racine qui a été généré à l’aide d’une solution d’entreprise, ou vous pouvez générer un certificat auto-signé.

La validation du certificat client est effectuée par la passerelle VPN et se produit pendant l’établissement de la connexion VPN P2S. Le certificat racine est requis pour la validation et doit être chargé sur Azure.

### <a name="authenticate-using-active-directory-ad-domain-server"></a>S’authentifier à l’aide du serveur de domaine Active Directory (AD)

L’authentification de domaine AD permet aux utilisateurs de se connecter à Azure à l’aide des informations d’identification du domaine de l’organisation. Un serveur RADIUS qui s’intègre avec le serveur AD est requis. Les organisations peuvent aussi exploiter un déploiement RADIUS existant.   
  
Le serveur RADIUS peut être déployé localement ou sur votre réseau virtuel Azure. Lors de l’authentification, la passerelle VPN Azure permet le transfert direct et transfère les messages d’authentification entre le serveur RADIUS et l’appareil de connexion. Par conséquent, l’accessibilité de la passerelle au serveur RADIUS est importante. Si le serveur RADIUS est situé en local, une connexion VPN S2S au site local à partir d’Azure est requise pour établir l’accessibilité.  
  
Le serveur RADIUS peut aussi être intégré aux services de certificat AD. Cela vous permet d’utiliser le serveur RADIUS et le déploiement de certificat d’entreprise pour votre authentification par certificat P2S comme alternative à l’authentification par certificat Azure. L’avantage est que vous n’avez pas besoin de charger les certificats racine et les certificats révoqués sur Azure.

Un serveur RADIUS permet également l’intégration avec d’autres systèmes d’identité externe. Cette opération ouvre de nombreuses options d’authentification pour les VPN P2S, notamment les options de multifacteur.

>[!NOTE]
>**Le protocole OpenVPN®** n’est pas pris en charge avec l’authentification RADIUS.
>

![point à site](./media/point-to-site-about/p2s.png "Point à site")

## <a name="what-are-the-client-configuration-requirements"></a>Quelle est la configuration requise pour les clients ?

>[!NOTE]
>Pour les clients Windows, vous devez disposer des droits d’administrateur sur l’appareil client afin d’initialiser la connexion VPN à partir de l’appareil client vers Azure.
>

Les utilisateurs utilisent les clients VPN natifs sur les appareils Windows et Mac pour P2S. Azure fournit un fichier zip de configuration de client VPN qui contient les paramètres requis par ces clients natifs afin de se connecter à Azure.

* Pour les appareils Windows, la configuration du client VPN comprend un package d’installation que les utilisateurs installent sur leurs appareils.
* Pour les appareils Mac, elle contient un fichier mobileconfig que les utilisateurs installent sur leurs appareils.

Le fichier zip fournit également les valeurs de certains paramètres importants pour Azure que vous pouvez utiliser pour créer votre propre profil pour ces appareils. Ces valeurs incluent notamment l’adresse de passerelle VPN, les types de tunnel configurés, les itinéraires et le certificat racine pour la validation de la passerelle.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="gwsku"></a>Quelles références SKU de passerelle prennent en charge les VPN P2S ?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Pour obtenir des recommandations sur les références SKU de passerelle, consultez [À propos des paramètres de la passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>La référence SKU de base ne prend pas en charge IKEv2 ou l’authentification RADIUS.
>

## <a name="IKE/IPsec policies"></a>Quelles stratégies IKE/IPsec sont configurées sur les passerelles VPN pour P2S ?


**IKEv2**

|**Cipher** | **Intégrité** | **PRF** | **Groupe DH** |
|---        | ---           | ---       | ---   |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP256 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384      | SHA384    | GROUP_24 |
|AES256     |   SHA384      | SHA384    | GROUP_14 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_24 |
|AES256     |   SHA256      | SHA256    | GROUP_14 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_2 |

**IPsec**

|**Cipher** | **Intégrité** | **Groupe PFS** |
|---        | ---           | ---       |
|GCM_AES256 | GCM_AES256 | GROUP_NONE |
|GCM_AES256 | GCM_AES256 | GROUP_24 |
|GCM_AES256 | GCM_AES256 | GROUP_14 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

## <a name="TLS policies"></a>Quelles stratégies TLS sont configurées sur les passerelles VPN pour P2S ?
**TLS**

|**Stratégies** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |




## <a name="configure"></a>Comment configurer une connexion P2S ?

Une configuration P2S requiert quelques étapes spécifiques. Les articles suivants détaillent les étapes de la configuration P2S et incluent des liens pour configurer les appareils clients VPN :

* [Configurer une connexion P2S - Authentification RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurer une connexion P2S - Authentification par certificat natif Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Configurer OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="how-do-i-remove-the-configuration-of-a-p2s-connection"></a>Comment supprimer la configuration d’une connexion P2S ?

Vous pouvez supprimer une configuration P2S avec Azure CLI et la commande suivante : 

`az network vnet-gateway update --name <gateway-name> --resource-group <resource-group name> --remove "vpnClientConfiguration"`
 
## <a name="faqcert"></a>Forum Aux Questions (FAQ) sur l’authentification par certificat Azure native

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>Forum Aux Questions (FAQ) sur l’authentification RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Configurer une connexion P2S - Authentification RADIUS](point-to-site-how-to-radius-ps.md)

* [Configurer une connexion P2S - Authentification par certificat natif Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**« OpenVPN » est une marque d’OpenVPN Inc.**
