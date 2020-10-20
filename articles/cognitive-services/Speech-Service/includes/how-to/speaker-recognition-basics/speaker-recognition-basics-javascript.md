---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: bb78a60b911823da96c52a104a3e06ecfc634da6
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210933"
---
I den här snabb starten lär du dig grundläggande design mönster för Talarigenkänning med hjälp av tal-SDK, inklusive:

* Text beroende och text oberoende verifiering
* Högtalar identifiering för att identifiera ett röst exempel bland en grupp röster
* Ta bort röst profiler

En övergripande överblick över tal igenkännings koncept finns i [översikts](../../../speaker-recognition-overview.md) artikeln.

## <a name="skip-to-samples-on-github"></a>Hoppa till exempel på GitHub

Om du vill hoppa över direkt till exempel kod kan du läsa [snabb starts exemplen för Java Script](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/fa6428a0837779cbeae172688e0286625e340942/quickstart/javascript/node/speaker-recognition) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Talarigenkänning stöds för närvarande *bara* i Azure tal-resurser som skapats i `westus` regionen.

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK för Java Script <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Använd följande instruktioner, beroende på plattform:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webbläsare <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Beroende på mål miljön använder du dessutom något av följande:

# <a name="script"></a>[skript](#tab/script)

Hämta och extrahera <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">talet SDK för Java Script <span class="docon docon-navigate-external x-hidden-focus"></span> </a> *microsoft.cognitiveservices.speech.sdk.bundle.js* -filen och placera den i en mapp som är tillgänglig för HTML-filen.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Om du är mål för en webbläsare och använder `<script>` taggen, `sdk` behövs inte prefixet. `sdk`Prefixet är ett alias som används för att namnge `require` modulen.

# <a name="import"></a>[export](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Mer information finns i `import` <a href="https://javascript.info/import-export" target="_blank">Exportera och importera <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

# <a name="require"></a>[innebära](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Mer information om `require` finns i <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Vad kräver <span class="docon docon-navigate-external x-hidden-focus"></span> ? </a>.

---

## <a name="import-dependencies"></a>Importera beroenden

Om du vill köra exemplen i den här artikeln lägger du till följande-instruktioner överst i. js-filen.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="dependencies":::

Dessa påståenden importerar de bibliotek som krävs och hämtar prenumerations nyckeln för din röst tjänst och region från dina miljövariabler. De anger även sökvägar till ljudfiler som du kommer att använda i följande uppgifter.

## <a name="create-helper-function"></a>Skapa hjälp funktion

Lägg till följande hjälp funktion för att läsa ljudfiler i strömmar för användning av tal tjänsten.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="helpers":::

I den här funktionen använder du metoderna [AudioInputStream. createPushStream](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioinputstream?view=azure-node-latest&preserve-view=true#createpushstream-audiostreamformat-) och [AudioConfig. fromStreamInput](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true#fromstreaminput-audioinputstream---pullaudioinputstreamcallback-) för att skapa ett [AudioConfig](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true) -objekt. `AudioConfig`Objektet representerar en ljud ström. Du kommer att använda flera av dessa `AudioConfig` objekt under följande uppgifter.

## <a name="text-dependent-verification"></a>Text beroende verifiering

Talarverifiering är en bekräftelse på att en talare matchar en känd eller **registrerad** röst. Det första steget är att **Registrera** en röst profil, så att tjänsten har något att jämföra framtida röst exempel mot. I det här exemplet registrerar du profilen med en **text beroende** strategi, som kräver att en unik lösen fras används för både registrering och verifiering. En lista över lösen fraser som stöds finns i [referens dokumenten](https://docs.microsoft.com/rest/api/speakerrecognition/) .

### <a name="textdependentverification-function"></a>Funktionen TextDependentVerification

Börja med att skapa `TextDependentVerification` funktionen.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_dependent_verification":::

Den här funktionen skapar ett [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) -objekt med metoden [VoiceProfileClient. createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) . Observera att det finns tre [typer](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofiletype?view=azure-node-latest&preserve-view=true) av `VoiceProfile` :

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

I det här fallet skickar du `VoiceProfileType.TextDependentVerification` till `VoiceProfileClient.createProfileAsync` .

Sedan anropar du två hjälp funktioner som du definierar härnäst `AddEnrollmentsToTextDependentProfile` och `SpeakerVerify` . Anropa slutligen [VoiceProfileClient. deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) för att ta bort profilen.

### <a name="addenrollmentstotextdependentprofile-function"></a>Funktionen AddEnrollmentsToTextDependentProfile

Definiera följande funktion för att registrera en röst profil.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_dependent":::

I den här funktionen anropar du `GetAudioConfigFromFile` funktionen som du definierade tidigare för att skapa `AudioConfig` objekt från ljud exempel. Dessa ljud exempel innehåller en lösen fras som "min röst är mitt Passport, verifiera mig". Du registrerar sedan dessa ljud exempel med hjälp av metoden [VoiceProfileClient. enrollProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-) .

### <a name="speakerverify-function"></a>Funktionen SpeakerVerify

Definiera `SpeakerVerify` enligt följande.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_verify":::

I den här funktionen skapar du ett [SpeakerVerificationModel](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel?view=azure-node-latest&preserve-view=true) -objekt med metoden [SpeakerVerificationModel. FromProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel?view=azure-node-latest&preserve-view=true#fromprofile-voiceprofile-) som skickar i [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) -objektet som du skapade tidigare.

Sedan anropar du metoden [SpeechRecognizer. recognizeOnceAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) för att verifiera ett ljud prov som innehåller samma lösen fras som de ljud exempel du registrerade tidigare. `SpeechRecognizer.recognizeOnceAsync` Returnerar ett [SpeakerRecognitionResult](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult?view=azure-node-latest&preserve-view=true) -objekt, vars `score` egenskap innehåller en likhets Poäng i intervallet 0,0-1.0. `SpeakerRecognitionResult`Objektet innehåller också en `reason` egenskap av typen [ResultReason](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/resultreason?view=azure-node-latest&preserve-view=true). Om verifieringen lyckades måste `reason` egenskapen ha ett värde `RecognizedSpeaker` .

## <a name="text-independent-verification"></a>Text oberoende verifiering

I motsats till **text beroende** verifiering, **text oberoende** verifiering:

* Kräver ingen viss lösen fras för att kunna läsas, något kan ses över
* Kräver inte tre ljud exempel *, men kräver* 20 sekunders total ljud

### <a name="textindependentverification-function"></a>Funktionen TextIndependentVerification

Börja med att skapa `TextIndependentVerification` funktionen.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_verification":::

Precis som `TextDependentVerification` funktionen skapar den här funktionen ett [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) -objekt med metoden [VoiceProfileClient. createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) .

I det här fallet skickar du `VoiceProfileType.TextIndependentVerification` till `createProfileAsync` .

Sedan anropar du två hjälp funktioner: `AddEnrollmentsToTextIndependentProfile` , som du definierar härnäst och `SpeakerVerify` , som du definierade redan. Anropa slutligen [VoiceProfileClient. deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) för att ta bort profilen.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Definiera följande funktion för att registrera en röst profil.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_independent":::

I den här funktionen anropar du `GetAudioConfigFromFile` funktionen som du definierade tidigare för att skapa `AudioConfig` objekt från ljud exempel. Du registrerar sedan dessa ljud exempel med hjälp av metoden [VoiceProfileClient. enrollProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-) .

## <a name="speaker-identification"></a>Talaridentifiering

Talaridentifiering används för att bestämma **vem som** talar från en specifik grupp med registrerade röster. Processen liknar **text oberoende verifiering**, med den största skillnaden att kunna verifiera mot flera röst profiler samtidigt, i stället för att verifiera mot en enskild profil.

### <a name="textindependentidentification-function"></a>Funktionen TextIndependentIdentification

Börja med att skapa `TextIndependentIdentification` funktionen.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_indentification":::

Precis som `TextDependentVerification` `TextIndependentVerification` -och-funktionerna skapar den här funktionen ett [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) -objekt med metoden [VoiceProfileClient. createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) .

I det här fallet skickar du `VoiceProfileType.TextIndependentIdentification` till `VoiceProfileClient.createProfileAsync` .

Sedan anropar du två hjälp funktioner: `AddEnrollmentsToTextIndependentProfile` , som du definierade redan och `SpeakerIdentify` , som du definierar härnäst. Anropa slutligen [VoiceProfileClient. deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) för att ta bort profilen.

### <a name="speakeridentify-function"></a>Funktionen SpeakerIdentify

Definiera `SpeakerIdentify` funktionen enligt följande.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_identify":::

I den här funktionen skapar du ett [SpeakerIdentificationModel](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel?view=azure-node-latest&preserve-view=true) -objekt med metoden [SpeakerIdentificationModel. fromProfiles](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel?view=azure-node-latest&preserve-view=true#fromprofiles-voiceprofile---) som skickar i [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest&preserve-view=true) -objektet som du skapade tidigare.

Sedan anropar du metoden [SpeechRecognizer. recognizeOnceAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) och skickar i ett ljud exempel.
`SpeechRecognizer.recognizeOnceAsync` försöker identifiera rösten för det här ljud exemplet baserat på de `VoiceProfile` objekt som du använde för att skapa `SpeakerIdentificationModel` . Den returnerar ett [SpeakerRecognitionResult](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult?view=azure-node-latest&preserve-view=true) -objekt, vars `profileId` egenskap identifierar matchningen `VoiceProfile` , om det finns några, medan `score` egenskapen innehåller ett likhets resultat mellan 0,0-1.0.

## <a name="main-function"></a>Huvud funktion

Definiera slutligen `main` funktionen enligt följande.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="main":::

Den här funktionen skapar ett [VoiceProfileClient](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest&preserve-view=true) -objekt som används för att skapa, registrera och ta bort röst profiler. Sedan anropas de funktioner som du definierade tidigare.
