---
title: 'Démarrage rapide : Détecter la langue du texte, Java - API de traduction de texte Translator Text'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez apprendre à détecter la langue du texte fourni à l’aide de Java et de l’API REST de traduction de texte Translator Text.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 35fde6810a788e3d134388144adcd08dfc1e8919
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705581"
---
# <a name="quickstart-use-the-translator-text-api-to-detect-text-language-using-java"></a>Démarrage rapide : Utiliser l’API de traduction de texte Translator Text et Java pour détecter la langue du texte

Dans ce guide de démarrage rapide, vous allez apprendre à détecter la langue du texte fourni à l’aide de Java et de l’API REST de traduction de texte Translator Text.

Pour suivre ce démarrage rapide, vous devrez disposer d’un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource Traduction de texte Translator Text. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/) pour obtenir une clé d’abonnement.

>[!TIP]
> Si vous souhaitez voir tout le code à la fois, le code source de cet exemple est disponible sur [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Java).

## <a name="prerequisites"></a>Prérequis

* [JDK 7 ou ultérieur](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle](https://gradle.org/install/)
* Une clé d’abonnement Azure pour Translator Text

## <a name="initialize-a-project-with-gradle"></a>Initialiser un projet avec Gradle

Commencez par créer un répertoire de travail pour ce projet. À partir de la ligne de commande (ou d’une session Terminal Server), exécutez cette commande :

```console
mkdir detect-sample
cd detect-sample
```

Ensuite, initialisez un projet Gradle. Cette commande crée les fichiers de build nécessaires pour Gradle, dont le plus important est le fichier `build.gradle.kts`, qui est utilisé au moment de l’exécution pour créer et configurer votre application. Exécutez cette commande à partir de votre répertoire de travail :

```console
gradle init --type basic
```

Quand vous êtes invité à choisir un **DSL**, sélectionnez **Kotlin**.

## <a name="configure-the-build-file"></a>Configurer le fichier de build

Recherchez le fichier `build.gradle.kts` et ouvrez-le dans votre IDE ou éditeur de texte habituel. Copiez-y ensuite cette configuration de build :

```
plugins {
    java
    application
}
application {
    mainClassName = "Detect"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Notez que cet exemple présente des dépendances sur OkHttp pour les requêtes HTTP, et sur Gson pour gérer et analyser JSON. Pour en savoir plus sur les configurations de build, consultez [Creating New Gradle Builds](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Créer un fichier Java

Créez maintenant un dossier pour votre exemple d’application. À partir de votre répertoire de travail, exécutez cette commande :

```console
mkdir -p src/main/java
```

Ensuite, dans ce dossier, créez un fichier nommé `Detect.java`.

## <a name="import-required-libraries"></a>Importer les bibliothèques nécessaires

Ouvrez `Detect.java` et ajoutez-y les instructions import suivantes :

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Définir des variables

Vous devez tout d’abord créer une classe publique pour votre projet :

```java
public class Detect {
  // All project code goes here...
}
```

Ajoutez ces lignes à la classe `Detect` :

```java
String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
String url = "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0";
```

Si vous utilisez un abonnement multiservice Cognitive Services, vous devez également inclure la `Ocp-Apim-Subscription-Region` dans vos paramètres de requête. [En savoir plus sur l’authentification sur l’abonnement multiservice](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Créer un client et générer une requête

Ajoutez cette ligne à la classe `Detect` pour instancier `OkHttpClient` :

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Ensuite, générez la requête POST. Au besoin, changez le texte pour la détection de la langue.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Salve mondo!\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Créer une fonction pour analyser la réponse

Cette fonction simple analyse et agrémente la réponse JSON retournée par le service de traduction de texte Translator Text.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Assemblage

La dernière étape consiste à effectuer une requête et à obtenir une réponse. Ajoutez ces lignes à votre projet :

```java
public static void main(String[] args) {
    try {
        Detect detectRequest = new Detect();
        String response = detectRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Voilà, vous êtes prêt à exécuter votre exemple d’application. À partir de la ligne de commande (ou d’une session Terminal Server), accédez à la racine de votre répertoire de travail, puis exécutez cette commande :

```console
gradle build
```

Une fois la génération terminée, exécutez :

```console
gradle run
```

## <a name="sample-response"></a>Exemple de réponse

Recherchez l’abréviation du pays/de la région dans cette [liste de langues](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

```json
[
  {
    "language": "it",
    "score": 1.0,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "en",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les informations de référence sur l’API pour comprendre tout ce que vous pouvez faire avec l’API de traduction de texte Translator Text.

> [!div class="nextstepaction"]
> [Informations de référence sur l'API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>Voir aussi

* [Traduire le texte](quickstart-java-translate.md)
* [Translittérer du texte](quickstart-java-transliterate.md)
* [Identifier la langue par entrée](quickstart-java-detect.md)
* [Obtenir des traductions alternatives](quickstart-java-dictionary.md)
* [Obtenir une liste des langues prises en charge](quickstart-java-languages.md)
* [Déterminer la longueur des phrases depuis une entrée](quickstart-java-sentences.md)
