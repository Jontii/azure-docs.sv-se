---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: trbye
ms.openlocfilehash: 65340bfcab76bd35901d1b3a9f3b4a8736205706
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376906"
---
En av de viktigaste funktionerna i tal tjänsten är möjligheten att känna igen mänskligt tal (kallas ofta tal till text). I den här snabb starten får du lära dig hur du använder tal-SDK i dina appar och produkter för att utföra högkvalitativt tal-till-text-konvertering.

## <a name="skip-to-samples-on-github"></a>Hoppa till exempel på GitHub

Om du vill hoppa över direkt till exempel kod, se [snabb starts exemplen för C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera talet SDK. Använd följande instruktioner, beroende på plattform:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Aktivitets <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Om du vill anropa tal tjänsten med hjälp av tal-SDK måste du skapa en [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) . Den här klassen innehåller information om din prenumeration, till exempel din nyckel och tillhör ande region, slut punkt, värd eller token för auktorisering.

> [!NOTE]
> Oavsett om du utför tal igenkänning, tal syntes, översättning eller avsikts igenkänning, skapar du alltid en konfiguration.

Det finns några sätt som du kan initiera en [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) :

* Med en prenumeration: skicka i en nyckel och tillhör ande region.
* Med en slut punkt: skicka i en röst tjänst slut punkt. En nyckel eller autentiseringstoken är valfri.
* Med en värd: skicka in en värd adress. En nyckel eller autentiseringstoken är valfri.
* Med en autentiseringstoken: skicka in en autentiseringstoken och den associerade regionen.

Låt oss ta en titt på hur en [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) har skapats med hjälp av en nyckel och region. Se sidan [region support](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) för att hitta din regions-ID.

```cpp
auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Initiera en identifierare

När du har skapat en [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) är nästa steg att initiera en [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) . När du initierar en måste [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) du skicka den `speech_config` . Detta ger de autentiseringsuppgifter som tal tjänsten behöver för att verifiera din begäran.

```cpp
auto recognizer = SpeechRecognizer::FromConfig(config);
```

## <a name="recognize-from-microphone-or-file"></a>Identifiera från mikrofon eller fil

Om du vill ange enheten för ljud inspelning måste du skapa en [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) och skicka den som en parameter när du initierar [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) .

Om du vill känna igen tal med din enhets mikrofon skapar du en `AudioConfig` med `FromDefaultMicrophoneInput()` och skickar sedan ljud konfigurationen när du skapar `SpeechRecognizer` objektet.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
```

> [!TIP]
> [Lär dig hur du hämtar enhets-ID: t för din enhet för ljud inspelning](../../../how-to-select-audio-input-devices.md).

Om du vill känna igen tal från en ljudfil i stället för att använda en mikrofon måste du ändå skapa en `AudioConfig` . Men när du skapar [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) , i stället för att anropa `FromDefaultMicrophoneInput()` , anropar `FromWavFileInput()` och skickar `filename` parametern.

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

## <a name="recognize-speech"></a>Identifiera tal

[Igenkännings klassen](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) för tal SDK för C++ exponerar några metoder som du kan använda för tal igenkänning.

* Identifiering av enstaka bild (async) – utför igenkänning i ett icke-blockerande (asynkront) läge. Detta kommer att identifiera en enda uttryck. Slutet på en enskild uttryck bestäms genom att lyssna efter tystnad i slutet eller tills maximalt 15 sekunders ljud bearbetas.
* Kontinuerlig igenkänning (async) – initierar kontinuerlig igenkännings åtgärd asynkront. Användaren måste ansluta för att hantera händelser för att få igenkännings resultat. Anropa för att stoppa asynkron kontinuerlig igenkänning [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync) .

> [!NOTE]
> Läs mer om hur du [väljer ett tal igenkännings läge](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Igenkänning av enstaka bild

Här är ett exempel på en asynkron igenkänning av enstaka bild med [`RecognizeOnceAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) :

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

Du måste skriva kod för att hantera resultatet. I det här exemplet utvärderas [`result->Reason`](https://docs.microsoft.com/cpp/cognitive-services/speech/recognitionresult#reason) :

* Skriver ut resultatet för igenkänning: `ResultReason::RecognizedSpeech`
* Om det inte finns någon igenkännings matchning, informera användaren: `ResultReason::NoMatch`
* Om ett fel påträffas skriver du ut fel meddelandet: `ResultReason::Canceled`

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>Kontinuerlig igenkänning

Kontinuerlig igenkänning är lite mer engagerande än igenkänning av enstaka steg. Det kräver att du prenumererar på `Recognizing` -, `Recognized` -och- `Canceled` händelserna för att få igenkännings resultatet. För att stoppa igenkänningen måste du anropa [StopContinuousRecognitionAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync). Här är ett exempel på hur kontinuerlig igenkänning utförs på en inspelnings fil.

Vi börjar med att definiera indatamängden och initiera en [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) :

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Nu ska vi skapa en variabel för att hantera läget för tal igenkänning. För att starta, deklarerar vi en `promise<void>` , eftersom vi i början av igenkänningen på ett säkert sätt kan anta att det inte är klart.

```cpp
promise<void> recognitionEnd;
```

Vi kommer att prenumerera på de händelser som skickas från [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) .

* [`Recognizing`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizing): Signal för händelser som innehåller mellanliggande igenkännings resultat.
* [`Recognized`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognized): Signal för händelser som innehåller slutgiltiga igenkännings resultat (indikerar ett lyckat igenkännings försök).
* [`SessionStopped`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): Signal för händelser som indikerar att en avläsnings session avslutas (åtgärd).
* [`Canceled`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#canceled): Signal för händelser som innehåller avbrutna igenkännings resultat (vilket indikerar ett igenkännings försök som avbrutits som ett resultat eller en direkt uppsägnings förfrågan eller, alternativt, ett transport-eller protokoll haveri).

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

Med allt konfigurerat kan vi anropa [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync) .

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Diktamensläge

När du använder kontinuerlig igenkänning kan du aktivera dikterings bearbetning genom att använda motsvarande "Aktivera diktering"-funktion. Det här läget kommer att göra att tal konfigurations instansen tolkar ord beskrivningar av menings strukturer som interpunktion. Till exempel skulle uttryck "är du bor i stadens frågetecken" tolkas som texten "är du bor i staden?".

Om du vill aktivera dikteringsläget använder du [`EnableDictation`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation) metoden på din [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) .

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>Ändra käll språk

En vanlig uppgift för tal igenkänning anger språk för indata (eller källa). Låt oss ta en titt på hur du ändrar indatamängds språk till tyska. Leta upp din kod i koden [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) och Lägg sedan till den här raden direkt under den.

```cpp
config->SetSpeechRecognitionLanguage("de-DE");
```

[`SetSpeechRecognitionLanguage`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage) är en parameter som tar en sträng som ett argument. Du kan ange ett värde i listan över [språk](../../../language-support.md)som stöds.

## <a name="improve-recognition-accuracy"></a>Förbättra igenkännings precisionen

Det finns några sätt att förbättra igenkännings precisionen med talet SDK. Låt oss ta en titt på fras listor. Fras listor används för att identifiera kända fraser i ljuddata, t. ex. en persons namn eller en viss plats. Enstaka ord eller fullständiga fraser kan läggas till i en fras lista. Under igenkänning används en post i en fras lista om en exakt matchning för hela frasen ingår i ljudet. Om det inte går att hitta en exakt matchning till frasen går det inte att hitta igenkänning.

> [!IMPORTANT]
> Funktionen fras lista är bara tillgänglig på engelska.

Om du vill använda en fras lista måste du först skapa ett [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) objekt och sedan lägga till vissa ord och fraser med [`AddPhrase`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar#addphrase) .

Eventuella ändringar [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) börjar gälla nästa igenkänning eller efter en åter anslutning till tal-tjänsten.

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

Om du behöver rensa fras listan: 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andra alternativ för att förbättra igenkännings precisionen

Fras listor är bara ett alternativ för att förbättra igenkännings precisionen. Du kan även: 

* [Förbättra noggrannheten med anpassat tal](../../../how-to-custom-speech.md)
* [Förbättra noggrannheten med klientmodeller](../../../tutorial-tenant-model.md)
