---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: a27fba6e426b72d72160a9a238f68cf8cef5c73b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947113"
---
En av de viktigaste funktionerna i tal tjänsten är möjligheten att känna igen mänskligt tal (kallas ofta tal till text). I den här snabb starten får du lära dig hur du använder tal-SDK i dina appar och produkter för att utföra högkvalitativt tal-till-text-konvertering.

## <a name="skip-to-samples-on-github"></a>Hoppa till exempel på GitHub

Om du vill hoppa över direkt till exempel kod kan du läsa [snabb starts exemplen för Java Script](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK för Java Script <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Använd följande instruktioner, beroende på plattform:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webbläsare <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Beroende på mål miljön använder du dessutom något av följande:

# <a name="script"></a>[skript](#tab/script)

Hämta och extrahera <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">talet SDK för Java Script <span class="docon docon-navigate-external x-hidden-focus"></span></a> *microsoft.cognitiveservices.speech.sdk.bundle.js* -filen och placera den i en mapp som är tillgänglig för HTML-filen.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Om du använder en webbläsare och använder- `<script>` taggen `sdk` behövs inte prefixet vid referens av klasser. `sdk`Prefixet är ett alias som används för att namnge `require` modulen.

# <a name="require"></a>[innebära](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Mer information om `require` finns i <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Vad kräver <span class="docon docon-navigate-external x-hidden-focus"></span> ? </a>.

---

## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Om du vill anropa tal tjänsten med hjälp av tal-SDK måste du skapa en [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) . Den här klassen innehåller information om din prenumeration, till exempel din nyckel och tillhör ande region, slut punkt, värd eller token för auktorisering. Skapa en [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) med hjälp av din nyckel och region. På sidan [Sök efter nycklar och region](../../../overview.md#find-keys-and-region) hittar du ett nyckel områdes par.

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

Det finns några andra sätt som du kan initiera [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) :

* Med en slut punkt: skicka i en röst tjänst slut punkt. En nyckel eller autentiseringstoken är valfri.
* Med en värd: skicka in en värd adress. En nyckel eller autentiseringstoken är valfri.
* Med en autentiseringstoken: skicka in en autentiseringstoken och den associerade regionen.

> [!NOTE]
> Oavsett om du utför tal igenkänning, tal syntes, översättning eller avsikts igenkänning, skapar du alltid en konfiguration.

## <a name="recognize-from-microphone-browser-only"></a>Identifiera från mikrofon (endast webbläsare)

Skapa en med hjälp av om du vill känna igen tal med din enhets mikrofon `AudioConfig` `fromDefaultMicrophoneInput()` . Initiera sedan en [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) , och skicka in `speechConfig` och `audioConfig` .

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromMic() {
    let audioConfig = sdk.AudioConfig.fromDefaultMicrophoneInput();
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    
    console.log('Speak into your microphone.');
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
    });
}
fromMic();
```

Om du vill använda en *speciell* enhet för ljud inspelning måste du ange enhets-ID i `AudioConfig` . Lär dig [hur du hämtar enhets-ID](../../../how-to-select-audio-input-devices.md) : t för din enhet för ljud inspelning.

## <a name="recognize-from-file"></a>Identifiera från fil 

# <a name="browser"></a>[Webbläsare](#tab/browser)

Om du vill känna igen tal från en ljudfil i en webbläsarbaserad JavaScript-miljö använder du `fromWavFileInput()` funktionen för att skapa en [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) . Funktionen `fromWavFileInput()` förväntar sig ett JavaScript- [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File/File) objekt som en parameter.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    // wavByteContent should be a byte array of the raw wav content
    let file = new File([wavByteContent]);
    let audioConfig = sdk.AudioConfig.fromWavFileInput(file);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
    });
}
fromFile();
```

# <a name="nodejs"></a>[Node.js](#tab/node)

Om du vill känna igen tal från en ljudfil i Node.js, måste ett alternativt design mönster som använder en push-dataström användas, eftersom JavaScript- [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File/File) objektet inte kan användas i en Node.js Runtime. Följande kod:

* Skapar en push-ström med `createPushStream()`
* Öppnar `.wav` filen genom att skapa en Läs ström och skriver den till push-strömmen
* Skapar en ljud konfiguration med push-strömmen

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");

function fromFile() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

Om du använder en push-dataström som indata förutsätter vi att ljuddata är en RAW PCM, t. ex. om du hoppar över rubriker.
API fungerar fortfarande i vissa fall om huvudet inte har hoppats över, men för bästa resultat bör du överväga att implementera logiken för att läsa upp rubrikerna så att `fs` börjar i *början av ljuddata*.

---

## <a name="error-handling"></a>Felhantering

I föregående exempel får du bara den tolkade texten från `result.text` , men för att hantera fel och andra svar måste du skriva kod för att hantera resultatet. Följande kod utvärderar [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult#reason) egenskapen och:

* Skriver ut resultatet för igenkänning: `ResultReason.RecognizedSpeech`
* Om det inte finns någon igenkännings matchning, informera användaren: `ResultReason.NoMatch`
* Om ett fel påträffas skriver du ut fel meddelandet: `ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>Kontinuerlig igenkänning

I de föregående exemplen används ett enda bilds igenkännings läge, som identifierar en enda uttryck. Slutet på en enskild uttryck bestäms genom att lyssna efter tystnad i slutet eller tills maximalt 15 sekunders ljud bearbetas.

Kontinuerlig igenkänning används däremot när du vill **styra** när du vill sluta identifiera. Det kräver att du prenumererar på `Recognizing` -, `Recognized` -och- `Canceled` händelserna för att få igenkännings resultatet. Om du vill stoppa igenkänningen måste du anropa [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#stopcontinuousrecognitionasync) . Här är ett exempel på hur kontinuerlig igenkänning utförs på en inspelnings fil.

Börja med att definiera indatamängden och initiera en [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) :

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig);
```

Prenumerera sedan på de händelser som skickas från [`SpeechRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer) .

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizing): Signal för händelser som innehåller mellanliggande igenkännings resultat.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognized): Signal för händelser som innehåller slutgiltiga igenkännings resultat (indikerar ett lyckat igenkännings försök).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#sessionstopped): Signal för händelser som indikerar att en avläsnings session avslutas (åtgärd).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#canceled): Signal för händelser som innehåller avbrutna igenkännings resultat (vilket indikerar ett igenkännings försök som avbrutits som ett resultat eller en direkt uppsägnings förfrågan eller, alternativt, ett transport-eller protokoll haveri).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

När allt är konfigurerat kan [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) du ringa till Start igenkänning.

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Diktamensläge

När du använder kontinuerlig igenkänning kan du aktivera dikterings bearbetning genom att använda motsvarande "Aktivera diktering"-funktion. Det här läget kommer att göra att tal konfigurations instansen tolkar ord beskrivningar av menings strukturer som interpunktion. Till exempel skulle uttryck "är du bor i stadens frågetecken" tolkas som texten "är du bor i staden?".

Om du vill aktivera dikteringsläget använder du [`enableDictation`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#enabledictation--) metoden på din [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) .

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Ändra käll språk

En vanlig uppgift för tal igenkänning anger språk för indata (eller källa). Låt oss ta en titt på hur du ändrar indatamängds språk till italienska. Leta upp din kod i koden [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig) och Lägg sedan till den här raden direkt under den.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#speechrecognitionlanguage)Egenskapen förväntar sig en språk språks format sträng. Du kan ange valfritt värde i kolumnen **språk** i listan [över språk som stöds.](../../../language-support.md)

## <a name="improve-recognition-accuracy"></a>Förbättra igenkännings precisionen

Fras listor används för att identifiera kända fraser i ljuddata, t. ex. en persons namn eller en viss plats. Genom att tillhandahålla en lista över fraser förbättrar du precisionen för tal igenkänning.

Om du till exempel har ett kommando "flytta till" och ett möjligt mål för "inåt" som kan bli talade kan du lägga till en post för "flytta till inåt". Om du lägger till en fras ökar sannolikheten att när ljudet identifieras som "flytta till inåt" identifieras i stället för att "gå till"

Enstaka ord eller fullständiga fraser kan läggas till i en fras lista. Under igenkänning används en post i en fras lista för att förbättra igenkänningen av ord och fraser i listan även om posterna visas i mitten av uttryck. 

> [!IMPORTANT]
> Fras List funktionen är tillgänglig på följande språk: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, ES-MX, fr-CA, fr-FR, IT-IT, ja-JP, ko-KR, pt-BR, zh-CN

Om du vill använda en fras lista måste du först skapa ett [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) objekt och sedan lägga till vissa ord och fraser med [`addPhrase`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar#addphrase-string-) .

Eventuella ändringar [`PhraseListGrammar`](/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar) börjar gälla nästa igenkänning eller efter en åter anslutning till tal-tjänsten.

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Om du behöver rensa fras listan:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andra alternativ för att förbättra igenkännings precisionen

Fras listor är bara ett alternativ för att förbättra igenkännings precisionen. Du kan även: 

* [Förbättra noggrannheten med anpassat tal](../../../custom-speech-overview.md)
* [Förbättra noggrannheten med klientmodeller](../../../tutorial-tenant-model.md)
