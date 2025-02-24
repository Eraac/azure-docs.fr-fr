---
title: Synthèse vocale avec les services de reconnaissance vocale Azure
titleSuffix: Azure Cognitive Services
description: Le service de synthèse vocale des services de reconnaissance vocale Azure permet à vos applications, outils ou appareils de convertir du texte en parole naturelle synthétisée quasi humaine. Choisissez parmi les voix standard et neuronales, ou créez une voix personnalisée propre à votre produit ou votre marque. Plus de 75 voix standard sont disponibles dans plus de 45 langues et paramètres régionaux, et 5 voix neuronales sont disponibles dans 4 langues et paramètres régionaux.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: da7259585ad66ac9b58131ce834d82e7b3d4bcf2
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466960"
---
# <a name="what-is-text-to-speech"></a>Qu’est-ce que la synthèse vocale ?

Le service de synthèse vocale des services de reconnaissance vocale Azure permet à vos applications, outils ou appareils de convertir du texte en parole naturelle synthétisée quasi humaine. Choisissez parmi les voix standard et neuronales, ou créez une voix personnalisée propre à votre produit ou votre marque. Plus de 75 voix standard sont disponibles dans plus de 45 langues et paramètres régionaux, et 5 voix neuronales sont disponibles dans 4 langues et paramètres régionaux. Pour en obtenir la liste complète, consultez les [langues prises en charge](language-support.md#text-to-speech).

La technologie de synthèse vocale permet aux créateurs de contenu d’interagir avec leurs utilisateurs de différentes façons. La synthèse vocale peut améliorer l’accessibilité en proposant aux utilisateurs une option permettant d’interagir avec le contenu de façon audible. Que l’utilisateur souffre d’une déficience visuelle, de difficultés d’apprentissage, ou qu’il ait besoin d’informations de navigation tout en conduisant, la synthèse vocale peut améliorer une expérience existante. La synthèse vocale constitue également un module complémentaire précieux pour les bots vocaux et assistants virtuels.


Avec le langage de balisage de synthèse vocale (SSML), un langage de balisage basé sur XML, les développeurs utilisant le service de synthèse vocale peuvent spécifier la manière dont un texte en entrée est converti en parole synthétisée. Avec SSML, vous pouvez ajuster la tonalité, la prononciation, la vitesse d’énonciation, le volume et bien plus encore. Pour plus d’informations, consultez la page [SSML](#speech-synthesis-markup-language-ssml).

### <a name="standard-voices"></a>Voix standard

Les voix standard sont créées à l’aide des techniques de synthèse paramétrique statistique et/ou de synthèse concaténative. Ces voix sont hautement intelligibles et naturelles. Vous pouvez facilement configurer vos applications pour qu’elles parlent dans plus de 45 langues, avec un large éventail d’options vocales. Ces voix sont très précises en matière de prononciation. Elles prennent en charge les abréviations, les développements d’acronyme, l’interprétation des dates/heures, les polyphones et bien plus encore. Utilisez la voix standard pour améliorer l’accessibilité de vos applications et services en permettant aux utilisateurs d’interagir avec votre contenu de manière audible.

### <a name="neural-voices"></a>Voix neurales

Les voix neuronales utilisent des réseaux neuronaux profonds pour dépasser les limites des systèmes de synthèse vocale traditionnels, avec mise en correspondance de modèles d’accent et d’intonation dans la langue parlée, et synthétisation des unités de discours en voix d’ordinateur. La synthèse vocale standard décompose la prosodie en plusieurs étapes d’analyse linguistique et de prédiction acoustique régies par des modèles indépendants, ce qui peut entraîner une voix synthétique étouffée. Ici, les capacités neuronales exécutent la prédiction prosodique et la synthèse vocale simultanément, pour une voix plus naturelle et une énonciation plus fluide.

Les voix neuronales peuvent être utilisées pour rendre les interactions avec les chatbots et les assistants virtuels plus naturelles et agréables, convertir des textes numériques comme les livres électroniques en livres audio et améliorer les systèmes de navigation embarqués. Grâce à la prosodie naturelle quasi humaine et à la bonne articulation des mots, les voix neuronales réduisent considérablement la fatigue d’écoute lorsque vous interagissez avec des systèmes d’intelligence artificielle.

Les voix neuronales prennent en charge différents styles. Elles peuvent être neutres ou enthousiastes. Par exemple, la voix Jessa (en-US) peut parler sur un ton enthousiaste, optimisé pour les conversations chaleureuses et joyeuses. Vous pouvez ajuster la sortie de la voix, comme le ton, la tonalité et le débit avec le [langage de balisage de synthèse vocale](speech-synthesis-markup.md). Vous trouverez une liste complète des voix disponibles dans [Langues prises en charge](language-support.md#text-to-speech).

Pour en savoir plus sur les avantages des voix neuronales, consultez l’article [Microsoft’s new neural text-to-speech service helps machines speak like people](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/) (Le nouveau service Microsoft de synthèse vocale aide les machines à parler comme des humains).

### <a name="custom-voices"></a>Voix personnalisées

La personnalisation des voix vous permet de créer une voix unique reconnaissable entre toutes pour votre marque. Pour créer votre police de voix personnalisée, effectuez un enregistrement en studio et chargez les scripts associés en tant que données d’apprentissage. Le service crée ensuite un modèle vocal unique adapté à votre enregistrement. Vous pouvez alors utiliser cette police de voix personnalisée pour synthétiser la parole. Pour plus d’informations, consultez la page sur les [voix personnalisées](how-to-customize-voice-font.md).

## <a name="speech-synthesis-markup-language-ssml"></a>SSML (Speech Synthesis Markup Language)

Le langage de balisage de synthèse vocale (SSML) est un langage de balisage basé sur XML qui permet aux développeurs de spécifier la manière dont un texte en entrée est converti en parole synthétisée via le service de synthèse vocale. Comparé à du texte brut, le SSML permet aux développeurs de régler finement la tonalité, la prononciation, le débit, le volume et d’autres paramètres de la synthèse vocale. La ponctuation normale, telle que la pause après un point ou l’utilisation de l’intonation correcte quand une phrase se termine par un point d’interrogation, est traitée automatiquement.

Toutes les entrées de texte envoyées au service de synthèse vocale doivent être structurées en langage SSML. Pour plus d’informations, consultez [Speech Synthesis Markup Language](speech-synthesis-markup.md).

### <a name="pricing-note"></a>Remarque sur la tarification

Lorsque vous utilisez le service de synthèse vocale, vous êtes facturé pour chaque caractère converti en parole, y compris les signes de ponctuation. Alors que le document SSML lui-même n’est pas facturable, les éléments facultatifs utilisés pour ajuster la conversion du texte en parole, tels que les phonèmes et la tonalité, sont comptabilisés comme caractères facturables. Voici une liste des éléments pouvant être facturés :

* Texte transmis au service de synthèse vocale dans le corps SSML de la demande
* Tout balisage dans le champ de texte du corps de la demande au format SSML, à l’exception des balises `<speak>` et `<voice>`
* Lettres, ponctuation, espaces, tabulations, balisage et tous les caractères d’espace blanc
* Chaque point de code défini au format Unicode

Pour plus d’informations, consultez la section [Tarification](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Chaque caractère chinois, japonais et coréen compte pour deux en matière de facturation.

## <a name="core-features"></a>Fonctionnalités de base

Ce tableau répertorie les principales fonctionnalités de la synthèse vocale :

| Cas d’utilisation | Kit SDK | REST |
|----------|-----|------|
| Convertir du texte en parole. | OUI | OUI |
| Charger des jeux de données pour l’adaptation de la voix. | Non | Oui\* |
| Créer et gérer des modèles de police de voix. | Non | Oui\* |
| Créer et gérer les déploiements de police de voix. | Non | Oui\* |
| Créer et gérer les tests de police de voix. | Non | Oui\* |
| Gérer les abonnements. | Non | Oui\* |

\* *Ces services sont disponibles avec le point de terminaison cris.ai. Voir les [Informations de référence sur Swagger](https://westus.cris.ai/swagger/ui/index). Ces API de gestion et d’apprentissage de voix personnalisée implémentent des restrictions qui limitent les demandes à 25 toutes les 5 secondes, tandis que l’API de synthèse vocale elle-même implémente une limitation qui permet de gérer 200 demandes par seconde au maximum. En cas de limitation, vous serez averti par en-têtes de message.*

## <a name="get-started-with-text-to-speech"></a>Bien démarrer avec la synthèse vocale

Nous proposons des guides de démarrage rapide conçu pour que vous exécutiez le code en moins de 10 minutes. Ce tableau comprend une liste de guides de démarrage rapide pour la synthèse vocale organisés par langage.

### <a name="sdk-quickstarts"></a>Démarrages rapides du kit de développement logiciel (SDK)

| Démarrage rapide (SDK) | Plateforme | Informations de référence sur l'API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-text-to-speech-dotnetcore.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-text-to-speech-csharp-uwp.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, Unity](quickstart-text-to-speech-csharp-unity.md) | Windows, Android | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Browse](https://aka.ms/csspeech/cppref) |

### <a name="rest-quickstarts"></a>Démarrages rapides REST

| Démarrage rapide (REST) | Plateforme | Informations de référence sur l'API |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.JS](quickstart-nodejs-text-to-speech.md) | Window, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Window, macOS, Linux | [Browse](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Exemple de code

Un exemple de code pour la synthèse vocale est disponible sur GitHub. Ces exemples couvrent la conversion du texte en parole dans les langages de programmation les plus populaires.

* [Exemples de synthèse vocale (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Exemples de synthèse vocale (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Documents de référence

* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk-reference.md)
* [SDK Speech Devices](speech-devices-sdk.md)
* [API REST : Reconnaissance vocale](rest-speech-to-text.md)
* [API REST : Synthèse vocale](rest-text-to-speech.md)
* [API REST : Transcription et personnalisation par lot](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir un abonnement gratuit aux services Speech](get-started.md)
* [Créer des polices de la voix personnalisées](how-to-customize-voice-font.md)
