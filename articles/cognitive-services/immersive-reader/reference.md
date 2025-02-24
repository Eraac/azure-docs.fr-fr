---
title: Référence du kit de développement logiciel (SDK) Lecteur immersif
titleSuffix: Azure Cognitive Services
description: Référence correspondant au kit de développement logiciel (SDK) du lecteur immersif
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 485e8626af4266492e02d4f9fbe4af486e10c082
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718392"
---
# <a name="immersive-reader-sdk-reference"></a>Référence du kit de développement logiciel (SDK) du lecteur immersif

Le kit de développement logiciel (SDK) du lecteur immersif est une bibliothèque JavaScript qui vous permet d’intégrer le lecteur immersif à votre application.

## <a name="functions"></a>Fonctions

Le SDK expose une seule fonction, `ImmersiveReader.launchAsync(token, resourceName, content, options)`.

### <a name="launchasync"></a>launchAsync

Lance le lecteur immersif au sein d'un `iframe` de votre application web.

```typescript
launchAsync(token: string, resourceName: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>parameters

| Nom | type | Description |
| ---- | ---- |------------ |
| `token` | string | Jeton d’accès obtenu à partir de l’appel du point de terminaison `issueToken`. |
| `resourceName` | string | Réservé. Cette propriété doit être définie sur `null`. |
| `content` | [Contenu](#content) | Objet dans lequel figure le contenu à afficher dans le lecteur immersif. |
| `options` | [Options](#options) | Options de configuration de certains comportements du lecteur immersif. facultatif. |

#### <a name="returns"></a>Retours

Retourne un élément `Promise<HTMLDivElement>` qui se résout lorsque le lecteur immersif est chargé. `Promise` se résout en élément `div` dont le seul enfant correspond à un élément `iframe` contenant la page du lecteur immersif.

#### <a name="exceptions"></a>Exceptions

L'élément `Promise` retourné est rejeté avec un objet [`Error`](#error) si le lecteur immersif ne se charge pas. Pour plus d’informations, consultez les [codes d’erreur](#error-codes).

## <a name="types"></a>Types

### <a name="content"></a>Contenu

Contient le contenu à afficher dans le lecteur immersif.

```typescript
{
    title?: string;      // Title text shown at the top of the Immersive Reader (optional)
    chunks: [ {          // Array of chunks
        content: string; // Plain text string
        lang?: string;   // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
        mimeType?: string; // MIME type of the content (optional). Defaults to 'text/plain' if not specified.
    } ];
}
```

#### <a name="supported-mime-types"></a>Types MIME pris en charge

| Type MIME | Description |
| --------- | ----------- |
| texte/brut | Texte brut. |
| application/mathml+xml | Langage de balisage mathématique (MathML). [Plus d’informations](https://developer.mozilla.org/en-US/docs/Web/MathML)

### <a name="options"></a>Options

Contient les propriétés qui configurent certains comportements du lecteur immersif.

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
}
```

### <a name="error"></a>Error

Contient des informations sur l'erreur.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Codes d’erreur

| Code | Description |
| ---- | ----------- |
| BadArgument | L'argument fourni n’est pas valide. Pour plus d'informations, voir `message`. |
| Délai d'expiration | Le lecteur immersif n'a pas été chargé dans le délai spécifié. |
| TokenExpired| Le jeton fourni a expiré. |

## <a name="launching-the-immersive-reader"></a>Lancement du lecteur immersif

Le kit de développement logiciel (SDK) fournit le style par défaut du bouton de lancement du lecteur immersif. Utilisez l'attribut de classe `immersive-reader-button` pour activer ce style.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Attributs facultatifs

Utilisez les attributs suivants pour configurer l’apparence du bouton.

| Attribut | Description |
| --------- | ----------- |
| `data-button-style` | Définit le style du bouton. Peut être `icon`, `text` ou `iconAndText`. La valeur par défaut est `icon`. |
| `data-locale` | Définit les paramètres régionaux, par exemple, `en-US`, `fr-FR`. La valeur par défaut est l'anglais. |
| `data-icon-px-size` | Définit la taille de l’icône en pixels. La valeur par défaut est 20px. |

## <a name="browser-support"></a>Prise en charge des navigateurs

Pour une meilleure expérience avec le lecteur immersif, utilisez les versions les plus récentes des navigateurs suivants.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [kit de développement logiciel (SDK) du lecteur immersif sur GitHub](https://github.com/Microsoft/immersive-reader-sdk)
* [Démarrage rapide : Créer une application web qui lance le lecteur immersif (C#)](./quickstart.md)