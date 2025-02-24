---
title: 'Démarrage rapide : Personnaliser l’assistant virtuel « voice-first » (préversion), Java (Android) - Services Speech'
titleSuffix: Azure Cognitive Services
description: Découvrez comment créer une application d’assistant virtuel « voice-first » dans Java sur Android à l’aide du SDK Speech
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: c62402faa1995e1e992c8251ed87160a8f33d3a7
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67602742"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-in-java-on-android-by-using-the-speech-sdk"></a>Démarrage rapide : Créer un assistant virtuel « voice-first » dans Java sur Android à l’aide du SDK Speech

Un démarrage rapide est également disponible pour la [reconnaissance vocale](quickstart-java-android.md).

Dans cet article, vous allez créer un assistant virtuel « voice-first » avec Java pour Android à l’aide du [SDK Speech](speech-sdk.md). Cette application se connecte à un bot que vous avez déjà créé et configuré avec le [canal Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech). Elle envoie ensuite une demande de voix au bot et présente une activité de réponse vocale.

Cette application est basée sur le package Maven 3 du SDK Speech et sur Android Studio 3.3. Le kit SDK Speech est actuellement compatible avec les appareils Android équipés de processeurs ARM 32/64 bits et Intel x86/x64 compatibles.

> [!NOTE]
> Pour le kit SDK Speech et l’appareil Roobo, consultez le [kit SDK Speech Devices](speech-devices-sdk.md).

## <a name="prerequisites"></a>Prérequis

* Clé d’abonnement Azure pour les services Speech. [Obtenez-en un gratuitement](get-started.md) ou créez-le sur le [portail Azure](https://portal.azure.com).
* Un bot existant configuré avec le [canal Direct Line Speech ](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
* [Android Studio](https://developer.android.com/studio/) v3.3 ou version ultérieure

    > [!NOTE]
    > Direct Line Speech (préversion) est actuellement disponible dans un sous-ensemble de régions pour les services Speech. Reportez-vous à [la liste des régions prises en charge pour les assistants virtuels voice-first](regions.md#Voice-first virtual assistants) et vérifiez que vos ressources sont déployées dans une de ces régions.

## <a name="create-and-configure-a-project"></a>Créer et configurer un projet

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Créer une interface utilisateur

Dans cette section, vous allez créer une interface utilisateur de base pour l’application. Commencez par ouvrir l’activité principale : `activity_main.xml`. Le modèle de base inclut une barre de titre avec le nom de l’application et un `TextView` avec le message « Hello world ! ».

Ensuite, remplacez le contenu du fichier `activity_main.xml` par le code suivant :

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:onClick="onBotButtonClicked"
        android:text="Talk to your bot" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Recognition Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/recoText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="  \n(Recognition goes here)\n" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Activity Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/activityText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        android:text="  \n(Activities go here)\n" />

   </LinearLayout>
   ```

Ce fichier XML définit une interface utilisateur simple pour interagir avec votre bot.

* L’élément `button` initie une interaction et appelle la méthode `onBotButtonClicked` lorsque vous cliquez dessus.
* L’élément `recoText` affiche les résultats de la reconnaissance vocale à mesure que vous parlez à votre bot.
* L’élément `activityText` affiche la charge utile JSON pour l’activité Bot Framework la plus récente de votre bot.

Le texte et la représentation graphique de votre interface utilisateur doivent maintenant ressembler à ce qui suit :

![](media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Ajouter un exemple de code

1. Ouvrez `MainActivity.java` et remplacez le contenu par le code suivant :

   ```java
    package samples.speech.cognitiveservices.microsoft.com;

    import android.media.AudioFormat;
    import android.media.AudioManager;
    import android.media.AudioTrack;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.text.method.ScrollingMovementMethod;
    import android.view.View;
    import android.widget.TextView;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your bot's own Direct Line Speech channel secret
        private static String channelSecret = "YourChannelSecret";
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region (note: only a subset of regions are currently supported)
        private static String serviceRegion = "YourSpeechServiceRegion";

        private DialogServiceConnector connector;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            TextView recoText = (TextView) this.findViewById(R.id.recoText);
            TextView activityText = (TextView) this.findViewById(R.id.activityText);
            recoText.setMovementMethod(new ScrollingMovementMethod());
            activityText.setMovementMethod(new ScrollingMovementMethod());

            // Note: we need to request permissions for audio input and network access
            int requestCode = 5; // unique code for the permission request
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{RECORD_AUDIO, INTERNET}, requestCode);
        }

        public void onBotButtonClicked(View v) {
            // Recreate the DialogServiceConnector on each button press, ensuring that the existing one is closed
            if (connector != null) {
                connector.close();
                connector = null;
            }

            // Create the DialogServiceConnector from the channel and speech subscription information
            DialogServiceConfig config = DialogServiceConfig.fromBotSecret(channelSecret, speechSubscriptionKey, serviceRegion);
            connector = new DialogServiceConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            connector.connectAsync();

            // Register the DialogServiceConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            connector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            connector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            connector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            connector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            connector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            connector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                connector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            connector.activityReceived.addEventListener((o, activityArgs) -> {
                try {
                    // Here we use JSONObject only to "pretty print" the condensed Activity JSON
                    String rawActivity = activityArgs.getActivity().serialize();
                    String formattedActivity = new JSONObject(rawActivity).toString(2);
                    activityText.setText(formattedActivity);
                } catch (JSONException e) {
                    activityText.setText("Couldn't format activity text: " + e.getMessage());
                }

                if (activityArgs.hasAudio()) {
                    // Text-to-speech audio associated with the activity is 16 kHz 16-bit mono PCM data
                    final int sampleRate = 16000;
                    int bufferSize = AudioTrack.getMinBufferSize(sampleRate, AudioFormat.CHANNEL_OUT_MONO, AudioFormat.ENCODING_PCM_16BIT);

                    AudioTrack track = new AudioTrack(
                            AudioManager.STREAM_MUSIC,
                            sampleRate,
                            AudioFormat.CHANNEL_OUT_MONO,
                            AudioFormat.ENCODING_PCM_16BIT,
                            bufferSize,
                            AudioTrack.MODE_STREAM);

                    track.play();

                    PullAudioOutputStream stream = activityArgs.getAudio();

                    // Audio is streamed as it becomes available. Play it as it arrives.
                    byte[] buffer = new byte[bufferSize];
                    long bytesRead = 0;

                    do {
                        bytesRead = stream.read(buffer);
                        track.write(buffer, 0, (int) bytesRead);
                    } while (bytesRead == bufferSize);

                    track.release();
                }
            });
        }
    }
   ```

   * La méthode `onCreate` inclut du code qui demande une autorisation d’accès au microphone et à Internet.

   * La méthode `onBotButtonClicked` est, comme indiqué précédemment, le gestionnaire de clic du bouton. Appuyer sur le bouton déclenche une seule interaction (« tour ») avec votre bot.

   * La méthode `registerEventListeners` illustre les événements utilisés par `DialogServiceConnector` et la gestion de base des activités entrantes.

1. Dans le même fichier, remplacez les chaînes de configuration par vos ressources :

    * Remplacez `YourChannelSecret` par le secret de canal Direct Line Speech pour votre bot.

    * Remplacez `YourSpeechSubscriptionKey` par votre clé d’abonnement.

    * Remplacez `YourServiceRegion` par la [région](regions.md) associée à votre abonnement. Seul un sous-ensemble de régions Speech Services sont actuellement prises en charge avec Direct Line Speech. Pour plus d’informations, consultez la page sur les [régions](regions.md#voice-first-virtual-assistants).

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

1. Connectez votre appareil Android à votre PC de développement. Vérifiez que vous avez activé le [mode de développement et le débogage USB](https://developer.android.com/studio/debug/dev-options) sur l’appareil.

1. Pour générer l’application, appuyez sur les touches Ctrl+F9 ou, dans la barre de menus, choisissez **Build (Générer)**  > **Make Project (Créer le projet)** .

1. Pour lancer l’application, appuyez sur les touches Maj+F10, ou choisissez **Run (Exécuter)**  > **Run 'app' (Exécutez 'app')** .

1. Dans la fenêtre de la cible de déploiement qui s’affiche, choisissez votre appareil Android.

   ![Capture d’écran de la fenêtre de sélection de la cible de déploiement](media/sdk/qs-java-android-12-deploy.png)

Une fois que l’application et son activité ont été lancées, cliquez sur le bouton pour commencer à communiquer avec votre bot. Le texte retranscrit apparaît à mesure que vous parlez et l’activité la plus récente reçue de votre bot s’affiche au moment où elle est reçue. Si votre bot est configuré pour fournir des réponses vocales, la conversion de parole en texte est lue automatiquement.

![Capture d’écran de l’application Android](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer et déployer un bot de base](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Voir aussi
- [À propos des assistants virtuels « voice-first »](voice-first-virtual-assistants.md)
- [Obtenir une clé d’abonnement aux services Speech gratuitement](get-started.md)
- [Mots déclencheurs personnalisés](speech-devices-sdk-create-kws.md)
- [Connecter Direct Line Speech à votre bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Explorer des exemples Java sur GitHub](https://aka.ms/csspeech/samples)
