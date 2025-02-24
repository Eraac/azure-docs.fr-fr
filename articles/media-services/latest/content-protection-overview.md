---
title: Protéger votre contenu à l’aide du chiffrement dynamique de Media Services - Azure | Microsoft Docs
description: Cet article donne une vue d’ensemble de la protection du contenu via la fonction de chiffrement dynamique. Il aborde également les différents protocoles de diffusion en continu et les types de chiffrement.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 0172879ff2a2c351c4ca721a449e2e2839934baa
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854068"
---
# <a name="content-protection-with-dynamic-encryption"></a>Protection du contenu avec chiffrement dynamique

Vous pouvez utiliser Azure Media Services pour sécuriser votre contenu multimédia du moment où il quitte votre ordinateur jusqu’à la remise, en passant par le stockage et le traitement. Media Services vous permet de transmettre votre contenu dynamique ou à la demande chiffré dynamiquement avec la norme Advanced Encryption Standard (AES-128) ou un des principaux systèmes de gestion des droits numériques (DRM) : Microsoft PlayReady, Google Widevine et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et licences (PlayReady, Widevine et FairPlay) DRM aux clients autorisés. 

L’image suivante illustre le flux de travail de protection du contenu Media Services : 

![Protéger du contenu](./media/content-protection/content-protection.svg)

&#42; *le chiffrement dynamique prend en charge les standards AES-128 « clé en clair », CBCS et CENC. Pour plus d’informations, consultez la matrice de prise en charge [ici](#streaming-protocols-and-encryption-types).*

Cet article explique les concepts et la terminologie pertinents pour comprendre la protection du contenu avec Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Principaux composants du système de protection du contenu

Pour mener à bien votre conception de système/d’application de « protection de contenu », vous devez comprendre pleinement l’étendue de l’effort. La liste suivante donne un aperçu des trois éléments que vous devez implémenter. 

1. Code Azure Media Services
  
   L’exemple de [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) vous montre comment implémenter le système multi-DRM avec Media Services v3, à l’aide de .NET. Il montre également comment utiliser le service de remise de clés ou de licences de Media Services. Vous pouvez chiffrer chaque ressource avec plusieurs types de chiffrement (AES-128, PlayReady, Widevine, FairPlay). Consultez les [protocoles de diffusion en continu et les types de chiffrement](#streaming-protocols-and-encryption-types) pour identifier la meilleure combinaison.
  
   Cet exemple indique comment effectuer les opérations suivantes :

   1. Créer et configurer des [stratégies de clé de contenu](content-key-policy-concept.md). Vous créez une **stratégie de clé de contenu** pour configurer le mode de remise de la clé de contenu (qui fournit un accès sécurisé à vos ressources) aux clients finaux.    

      * Définissez l'autorisation de remise de licence, spécifiant la logique de vérification d’autorisation basée sur les revendications dans JWT.
      * Configurez les licences associées à [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md) et/ou [FairPlay](fairplay-license-overview.md). Les modèles vous permettent de configurer les droits et autorisations pour chacun des DRM utilisés.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. Créer un [localisateur de streaming](streaming-locators-concept.md) configuré pour diffuser en continu une ressource chiffrée. 
  
      Le **localisateur de streaming** doit être associé à une [stratégie de diffusion en continu](streaming-policy-concept.md). Dans cet exemple, nous définissons l’élément StreamingLocator.StreamingPolicyName sur la stratégie « Predefined_MultiDrmCencStreaming ». Les chiffrements PlayReady et de Widevine sont appliqués. La clé est envoyée au client de lecture en fonction des licences DRM configurées. Si vous voulez aussi chiffrer votre flux avec CBCS (FairPlay), utilisez l’élément «Predefined_MultiDrmStreaming».
      
      Le localisateur de streaming est également associé à la **stratégie de clé de contenu** qui a été définie.
    
   3. Créer un jeton de test.

      La méthode **GetTokenAsync** indique comment créer un jeton de test.
   4. Générer l’URL de diffusion en continu.

      La méthode **GetDASHStreamingUrlAsync** indique comment générer l’URL de diffusion en continu. Dans le cas présent, l’URL diffuse en continu le flux **DASH**.

2. Lecteur avec client AES ou DRM. Une application de lecteur vidéo basée sur un kit de développement logiciel du lecteur (natif ou dans le navigateur) doit remplir les conditions suivantes :
   * Le Kit de développement logiciel du lecteur prend en charge les clients DRM nécessaires
   * Le Kit de développement logiciel du lecteur prend en charge les protocoles de diffusion en continu requis : Smooth, DASH et/ou HLS
   * Le Kit de développement logiciel du lecteur doit être en mesure de gérer le passage d’un jeton JWT lors de la requête d’acquisition de licence
  
     Vous pouvez créer un lecteur à l’aide de [l’API Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/). Utilisez [l’API ProtectionInfo d’Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/) pour spécifier la technologie DRM à utiliser sur d’autres plateformes DRM.

     Pour tester du contenu chiffré par AES ou CENC (Widevine et/ou PlayReady), vous pouvez utiliser le [lecteur multimédia Azure](https://aka.ms/azuremediaplayer). Veillez à cliquer sur Options avancées et vérifiez vos options de chiffrement.

     Si vous souhaitez tester le contenu chiffré de FairPlay, utilisez [ce lecteur test](https://aka.ms/amtest). Le lecteur prend en charge les DRM Widevine, PlayReady et FairPlay, ainsi que le chiffrement de clé en clair AES-128. 
    
     Vous devez choisir le navigateur approprié pour tester les différents systèmes DRM : Chrome/Opera/Firefox pour Widevine, Microsoft Edge/IE11 pour PlayReady, Safari sur macOS pour FairPlay.

3. Secure Token Service (STS), qui émet le jeton JSON Web Token (JWT) en tant que jeton d’accès pour accéder aux ressources principales. Vous pouvez utiliser les services de distribution de licence AMS en tant que ressource principale. Un service STS doit définir les éléments suivants :

   * L’émetteur et l’audience (ou l’étendue)
   * Les revendications, qui dépendent des exigences de l’activité concernant la protection du contenu
   * La vérification symétrique ou asymétrique pour vérifier la signature
   * La prise en charge de substitution de clé (si nécessaire)

     Vous pouvez utiliser [cet outil STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) pour tester STS, qui prend en charge les 3 types de clés de vérification : symétriques, asymétriques ou Azure AD avec substitution de clé. 

> [!NOTE]
> Nous recommandons fortement de tester chaque partie (décrite plus haut) entièrement et une par une, avant de passer à la partie suivante. Pour tester votre système de « protection de contenu », utilisez les outils spécifiés dans la liste ci-dessus.  

## <a name="streaming-protocols-and-encryption-types"></a>Types de protocoles de diffusion en continu et de chiffrement

Vous pouvez utiliser Media Services pour transmettre du contenu chiffré de manière dynamique avec le chiffrement à clé en clair AES ou DRM en utilisant PlayReady, Widevine ou Apple FairPlay. Actuellement, vous pouvez chiffrer les formats de diffusion en continu HLS (HTTP Live Streaming), MPEG DASH et Smooth Streaming. Chaque protocole prend en charge les méthodes de chiffrement suivantes :

### <a name="hls"></a>HLS

Le protocole HLS prend en charge les formats de conteneur et schémas de chiffrement ci-après.

|Format de conteneur|Schéma de chiffrement|Exemple d’URL|
|---|---|---|
|Tous|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

Le chiffrement HLS/CMAF + FairPlay (y compris HEVC/H.265) est pris en charge sur les appareils suivants :

* iOS 11 ou version ultérieure 
* iPhone 8 ou version ultérieure
* MacOS High Sierra avec un CPU Intel de 7e génération

### <a name="mpeg-dash"></a>MPEG-DASH

Le protocole MPEG-DASH prend en charge les formats de conteneur et schémas de chiffrement ci-après.

|Format de conteneur|Schéma de chiffrement|Exemples d’URL
|---|---|---|
|Tous|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

Le protocole Smooth Streaming prend en charge les formats de conteneur et schémas de chiffrement ci-après.

|Protocole|Format de conteneur|Schéma de chiffrement|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Navigateurs

Les navigateurs courants prennent en charge les clients DRM suivants :

|Browser|Chiffrement|
|---|---|
|Chrome|Widevine|
|Edge, IE 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="aes-128-clear-key-vs-drm"></a>Clé en clair AES-128 et DRM

Les clients se demandent souvent s’ils devraient utiliser un chiffrement AES ou un système DRM. La principale différence entre les deux systèmes est qu’avec le chiffrement AES, la clé de contenu est transmise au client via TLS ; elle est chiffrée en transit, mais sans autre chiffrement (« en clair »). Par conséquent, la clé utilisée pour déchiffrer le contenu peut être lue par le lecteur client et affichée dans la trace réseau du client, en texte en clair. Le chiffrement avec une clé en clair AES-128 convient à des cas d’utilisation dans lesquels l’observateur est fiable (par exemple, le chiffrement de vidéos d’entreprise distribuées dans une société et destinées aux employés).

Par rapport à la clé en clair AES-128, les systèmes DRM comme PlayReady, Widevine et FairPlay fournissent tous un niveau supplémentaire de chiffrement sur la clé utilisée pour déchiffrer le contenu. La clé de contenu est chiffrée selon une clé protégée par le runtime DRM, en plus de tout chiffrement lors du transit fourni par TLS. De plus, le déchiffrement est pris en charge dans un environnement sécurisé au niveau du système d’exploitation, où il est plus difficile pour un utilisateur malintentionné de commettre une attaque. DRM est recommandé dans des cas d’utilisation où l’observateur peut ne pas être fiable et où donc un niveau de sécurité supérieur est requis.

## <a name="dynamic-encryption-and-key-delivery-service"></a>Chiffrement dynamique et service de remise de clé

Dans Media Services v3, une clé de contenu est associée à un localisateur de streaming (consultez [cet exemple](protect-with-aes128.md)). Si vous utilisez le service de remise de clé Azure Media Services, vous pouvez demander à ce service de générer la clé de contenu pour vous. Vous devez générer la clé de contenu vous-même si vous utilisez votre propre service de remise de clé, ou si vous avez besoin de gérer un scénario de haute disponibilité au cours duquel vous avez besoin d’avoir la même clé de contenu dans deux centres de données.

Lorsqu’un flux est demandé par un lecteur, Media Services utilise la clé spécifiée pour chiffrer dynamiquement votre contenu à l’aide du chiffrement de clé en clair AES ou DRM. Pour déchiffrer le flux, le lecteur demande la clé au service de remise des clés Media Services, ou au service de remise des clés que vous avez spécifié. Pour déterminer si l’utilisateur est autorisé ou non à obtenir la clé, le service évalue la stratégie de clé de contenu que vous avez spécifiée pour la clé.

Media Services fournit un service de remise de licences DRM (PlayReady, Widevine, FairPlay) et de clés AES aux clients autorisés. Vous pouvez utiliser l’API REST ou le la bibliothèque de client Media Services pour configurer des stratégies d’authentification et d’autorisation pour vos licences et vos clés.

### <a name="custom-key-and-license-acquisition-url"></a>URL d’acquisition de licences et de clés personnalisées

Utilisez les modèles suivants si vous souhaitez spécifier un service de remise de licences et de clés autre que Media Services. Les deux champs modifiables présents dans les modèles sont là pour vous permettre de partager votre stratégie de diffusion en continu sur plusieurs ressources, afin d’éviter de créer une stratégie par ressource. 

* EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate : modèle pour l’URL du service personnalisé qui remet les clés aux lecteurs de l’utilisateur final. Il n’est pas nécessaire lorsque vous utilisez Azure Media Services pour émettre vos clés. Ce modèle prend en charge les jetons remplaçables que le service met à jour lors de l’exécution, en utilisant la valeur spécifique à la requête.  Les valeurs de jeton actuellement prises en charge sont {AlternativeMediaId}, qui est remplacée par la valeur de StreamingLocatorId.AlternativeMediaId et {ContentKeyId}, qui est remplacée par la valeur de l’identificateur de la clé demandée.
* StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate : modèle pour l’URL du service personnalisé remettant les licences aux lecteurs de l’utilisateur final. Il n’est pas nécessaire lorsque vous utilisez Azure Media Services pour émettre vos licences. Ce modèle prend en charge les jetons remplaçables que le service met à jour lors de l’exécution, en utilisant la valeur spécifique à la requête. Les valeurs de jeton actuellement prises en charge sont {AlternativeMediaId}, qui est remplacée par la valeur de StreamingLocatorId.AlternativeMediaId et {ContentKeyId}, qui est remplacée par la valeur de l’identificateur de la clé demandée. 
* StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate : même utilisation que ci-dessus, mais pour Widevine. 
* StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate : même utilisation que ci-dessus, mais pour FairPlay.  

Par exemple :

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

Le paramètre `ContentKeyId` a la valeur de la clé demandée, et le paramètre `AlternativeMediaId` peut être utilisé si vous souhaitez mapper la requête à une entité de votre côté. Par exemple, le paramètre `AlternativeMediaId` peut être utilisé pour vous aider à rechercher des autorisations.

Pour accéder à des exemples REST qui utilisent des URL d’acquisition de licences et de clés personnalisées, voir [Streaming Policies - Create](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) (Stratégies de diffusion en continu - Création).

## <a name="control-content-access"></a>Contrôle de l’accès au contenu

Vous pouvez contrôler qui a accès à votre contenu en configurant la stratégie d’autorisation de clé de contenu. Media Services prend en charge plusieurs méthodes d’autorisation des utilisateurs effectuant des demandes de clé. Vous devez configurer la stratégie de clé de contenu. Le client (lecteur) doit répondre à la stratégie avant que la clé de contenu ne puisse lui être remise. La stratégie de clé de contenu peut contenir une restriction **ouverte** ou **de jeton**. 

Avec une stratégie de clé de contenu de jeton, la clé de contenu n’est envoyée qu’à un client qui présente un JSON Web Token (JWT) ou Simple Web Token (SWT) valide dans la requête de clé/licence. Ce jeton doit être émis par un service d’émission de jeton de sécurité (STS). Vous pouvez utiliser Azure Active Directory en tant que STS ou déployer un STS personnalisé. Le STS doit être configuré pour créer un jeton signé avec la clé spécifiée et émettre les revendications spécifiées dans la configuration de restriction de jeton. Le service de remise de clé Media Services retourne la clé/licence demandée au client si le jeton est valide et que les revendications du jeton correspondent à celles configurées pour la clé/licence.

Quand vous configurez la stratégie de restriction par jeton, vous devez définir les paramètres de clé de vérification, émetteur et audience principaux. La clé de vérification principale contient le jeton avec lequel la clé a été signée. L’émetteur est le service STS qui émet le jeton. L’audience, parfois appelé étendue, décrit l’objectif du jeton ou la ressource à laquelle le jeton autorise l’accès. Le service de remise de clé Media Services valide le fait que les valeurs du jeton correspondent aux valeurs du modèle.

Les clients utilisent souvent un service d’émission de jeton de sécurité personnalisé pour inclure des revendications personnalisées dans le jeton, afin de choisir entre différents éléments ContentKeyPolicyOptions avec différents paramètres de licence DRM (une licence d’abonnement par rapport à une licence de location) ou pour inclure une revendication représentant l’identificateur de la clé de contenu auquel le jeton permet d’accéder.
 
## <a name="storage-side-encryption"></a>Chiffrement côté stockage

Pour protéger vos éléments au repos, les ressources doivent être chiffrées par le chiffrement côté stockage. Le tableau suivant montre comment fonctionne le chiffrement côté stockage dans Media Services v3 :

|Option de chiffrement|Description|Media Services v3|
|---|---|---|
|Chiffrement du stockage de Media Services| Chiffrement AES-256, clé gérée par Media Services|Non pris en charge<sup>(1)</sup>|
|[Storage Service Encryption pour les données au repos](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Chiffrement côté serveur proposé par le stockage Azure, clé gérée par Azure ou par un client|Pris en charge|
|[Chiffrement de stockage côté client](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Chiffrement côté client proposé par le stockage Azure, clé gérée par un client dans un coffre de clés|Non pris en charge|

<sup>1</sup> Dans Media Services v3, le chiffrement de stockage (chiffrement AES-256) est uniquement pris en charge pour la compatibilité descendante lorsque vos ressources ont été créées avec Media Services v2. Cela signifie que la version v3 fonctionne avec les ressources chiffrées du stockage existant mais qu’elle n’autorisera pas de nouvelles créations.

## <a name="troubleshoot"></a>Résolution des problèmes

Si vous obtenez l’erreur `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY`, veillez à spécifier la stratégie de diffusion en continu appropriée.

Si vous obtenez des erreurs qui se terminent par `_NOT_SPECIFIED_IN_URL`, vérifiez que vous avez spécifié le format de chiffrement dans l’URL. Par exemple : `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Voir [Types de protocoles de diffusion en continu et de chiffrement](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Poser des questions, envoyer des commentaires, obtenir des mises à jour

Découvrez l’article [Communauté Azure Media Services](media-services-community.md) pour découvrir les différentes façons dont vous pouvez poser des questions, faire des commentaires et obtenir des mises à jour sur Media Services.

## <a name="next-steps"></a>Étapes suivantes

* [Protection avec le chiffrement AES](protect-with-aes128.md)
* [Protéger avec DRM](protect-with-drm.md)
* [Concevoir un système de protection de contenu multi-DRM avec contrôle d’accès](design-multi-drm-system-with-access-control.md)
* [Forum Aux Questions](frequently-asked-questions.md)

