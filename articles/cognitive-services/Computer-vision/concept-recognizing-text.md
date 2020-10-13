---
title: Optisk tecken läsning (OCR) – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp som rör optisk tecken läsning (OCR) av bilder och dokument med utskriven text och handskriven text med hjälp av API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 32759ed59e280980abdced46c29390e00ee7229c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973282"
---
# <a name="optical-character-recognition-ocr"></a>Optisk teckenläsning (OCR)

Azures API för visuellt innehåll innehåller OCR-funktioner (optisk tecken läsning) som extraherar tryckt eller handskriven text från bilder. Du kan extrahera text från bilder, till exempel foton av licens plattor eller behållare med serie nummer, samt från dokument fakturor, fakturor, ekonomiska rapporter, artiklar med mera.

## <a name="read-api"></a>Läs-API 

Visuellt innehåll [Read API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) är Azures senaste OCR-teknik ([Läs om vad som är nytt](./whats-new.md)) som extraherar utskriven text (på flera språk), handskriven text (endast engelska), siffror och valuta symboler från bilder och PDF-dokument med flera sidor. Det är optimerat för att extrahera text från text – tunga bilder och PDF-dokument med flera sidor med blandade språk. Det stöder identifiering av både skriven och handskriven text i samma bild eller dokument.

![Hur OCR konverterar bilder och dokument till strukturerade utdata med extraherad text](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Krav för indatamängd
**Läs** anropet tar bilder och dokument som inaktuella. De har följande krav:

* Fil format som stöds: JPEG, PNG, BMP, PDF och TIFF
* För PDF-och TIFF-filer, bearbetas upp till 2000 sidor (endast de två första sidorna för den kostnads fria nivån).
* Fil storleken måste vara mindre än 50 MB (4 MB för den kostnads fria nivån) och dimensioner minst 50 x 50 bild punkter och högst 10000 x 10000 bild punkter. 
* PDF-dimensionerna måste bestå av högst 17 × 17 tum, som motsvarar legal eller a3 pappers storlekar och mindre.

### <a name="read-32-preview-allows-selecting-pages"></a>Läs 3,2 för hands version tillåter att du väljer sidor
Med [läsa 3,2 för hands versions-API](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005): t kan du ange ett visst sid nummer eller sid intervall som en indataparameter för att extrahera text från dessa sidor för stora dokument med flera sidor. Detta är en ny indataparameter förutom den valfria språk parametern.

> [!NOTE]
> **Språk information** 
>
> [Read-anropet](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) har en valfri begär ande parameter för språk. Det här är språk koden BCP-47 för texten i dokumentet. Läs stöder automatisk språk identifiering och flerspråkiga dokument, så du behöver bara ange en språkkod om du vill tvinga dokumentet att bearbetas som det specifika språket.

## <a name="the-read-call"></a>Read-anropet

Läsnings-API: s [Läs anrop](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) tar en bild eller ett PDF-dokument som indata och extraherar text asynkront. Anropet returnerar med ett svars huvud fält som kallas `Operation-Location` . `Operation-Location`Värdet är en URL som innehåller det åtgärds-ID som ska användas i nästa steg.

|Svars huvud| Resultat-URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.2/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> Sidan [visuellt innehåll prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) innehåller pris nivån för läsning. Varje analyserad bild eller sida är en transaktion. Om du anropar åtgärden med ett PDF-eller TIFF-dokument som innehåller 100 sidor, räknas den som 100 transaktioner och du faktureras för 100 transaktioner. Om du gjorde 50 anrop till åtgärden och varje anrop skickade ett dokument med 100 sidor debiteras du för 50 X 100 = 5000 transaktioner.

## <a name="the-get-read-results-call"></a>Anropet get Read result

Det andra steget är att anropa åtgärden [Hämta Läs resultat](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d9869604be85dee480c8750) . Den här åtgärden utförs som indatamängden för det åtgärds-ID som skapades av Läs åtgärden. Den returnerar ett JSON-svar som innehåller ett **status** fält med följande möjliga värden. Du anropar den här åtgärden iterativt tills den returnerar värdet **lyckades** . Använd ett intervall på 1 till 2 sekunder för att undvika att överskrida antalet begär Anden per sekund (RPS).

|Fält| Typ | Möjliga värden |
|:-----|:----:|:----|
|status | sträng | notStarted: åtgärden har inte startats. |
| |  | körs: åtgärden bearbetas. |
| |  | misslyckades: åtgärden misslyckades. |
| |  | lyckades: åtgärden har slutförts. |

> [!NOTE]
> Den kostnads fria nivån begränsar begär ande frekvensen till 20 anrop per minut. På den betalda nivån tillåts 10 begär Anden per sekund (RPS) som kan ökas på begäran. Använd support kanalen för Azure eller ditt konto team för att begära en högre RPS-taxa (Request/Second).

När fältet **status** har värdet **lyckades** innehåller JSON-svaret det extraherade text innehållet från din avbildning eller ditt dokument. JSON-svaret underhåller de ursprungliga rad grupperna av identifierade ord. Den innehåller de extraherade text raderna och deras avgränsnings Rute koordinater. Varje textrad innehåller alla extraherade ord med deras koordinater och förtroende poäng.

## <a name="sample-json-output"></a>Exempel på JSON-utdata

Se följande exempel på ett lyckat JSON-svar:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-05-28T05:13:21Z",
  "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0.8551,
        "width": 2661,
        "height": 1901,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              67,
              646,
              2582,
              713,
              2580,
              876,
              67,
              821
            ],
            "text": "The quick brown fox jumps",
            "words": [
              {
                "boundingBox": [
                  143,
                  650,
                  435,
                  661,
                  436,
                  823,
                  144,
                  824
                ],
                "text": "The",
                "confidence": 0.958
              }
            ]
          }
        ]
      }
    ]
  }
}
```
### <a name="read-32-preview-adds-text-line-style-latin-languages-only"></a>Läs 3,2 Preview lägger till text linje format (endast latinska språk)
I [Read 3,2 Preview-API: et](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005) visas ett **utseende** objekt som klassificerar om varje textrad är ett utskrifts-eller handstil, tillsammans med en förtroende poäng. Den här funktionen stöds bara för latinska språk.

```json
  "appearance": {
              "style": "handwriting",
              "styleConfidence": 0.836
            }
```
Kom igång med snabb starter för [visuellt innehåll OCR SDK](./quickstarts-sdk/client-library.md) och [Läs REST API snabb](./QuickStarts/CSharp-hand-text.md) Starter för att börja integrera OCR-funktioner i dina program.

## <a name="supported-languages-for-print-text"></a>Språk som stöds för utskrifts text
[API: et Read 3,2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) stöder extrahering av utskriven text på engelska, spanska, tyska, franska, italienska, portugisiska och nederländska språk.

Se de [språk som stöds](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) för den fullständiga listan över språk som stöds av OCR.

### <a name="read-32-preview-adds-simplified-chinese-and-japanese"></a>Läs 3,2 för hands version Lägg till förenklad kinesiska och japanska
Den [offentliga för hands versionen av Read 3,2 API](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005) lägger till stöd för förenklad kinesiska och japanska. Om ditt scenario kräver stöd för fler språk, se avsnittet [OCR-API](#ocr-api) . 

## <a name="supported-languages-for-handwritten-text"></a>Språk som stöds för handskriven text
Läs åtgärden stöder för närvarande extrahering av handskriven text exklusivt på engelska.

## <a name="use-the-rest-api-and-sdk"></a>Använd REST API och SDK
Filen [Read 3. x REST API](./QuickStarts/CSharp-hand-text.md) är det bästa alternativet för de flesta kunder på grund av enkel integrering och snabb produktivitet. Azure och Visuellt innehåll service hanterar skalning, prestanda, data säkerhet och efterlevnad för att möta kundernas behov.

## <a name="deploy-on-premise-with-docker-containers"></a>Distribuera lokalt med Docker-behållare
Med den [Read 2,0 Docker-behållaren (för hands version)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers) kan du distribuera de nya OCR-funktionerna i din egen lokala miljö. Containrar är bra för specifika säkerhets- och datastyrningskrav.

## <a name="example-outputs"></a>Exempel på utdata

### <a name="text-from-images"></a>Text från bilder

Följande Read API-utdata visar den extraherade texten från en bild med olika text vinklar, färger och teckensnitt.

![En bild av flera ord med olika färger och vinklar, med extraherad text listad](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Text från dokument

Read API kan också ta PDF-dokument som inmatade.

![Ett faktura dokument med extraherad text listad](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>Handskriven text

Läs åtgärden extraherar handskriven text från bilder (för närvarande endast på engelska).

![En bild av en handskriven anteckning med extraherad text listad](./Images/handwritten-example.png)

### <a name="printed-text"></a>Utskriven text

Läs åtgärden kan extrahera utskriven text på flera olika språk.

![En bild av en spansk Textbook, med extraherad text listad](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>Dokument med blandat språk

Read API stöder bilder och dokument som innehåller flera olika språk, vanligt vis kallade blandade språk dokument. Det fungerar genom att klassificera varje text rad i dokumentet på det identifierade språket innan dess text innehåll extraheras.

![En bild av fraser på flera språk, med extraherad text listad](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>OCR-API

[OCR-API: t](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) använder en äldre igenkännings modell, stöder bara bilder och körs synkront, och returneras omedelbart med den identifierade texten. Se de [språk som stöds för OCR](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) och sedan Read API.

## <a name="data-privacy-and-security"></a>Datasekretess och säkerhet

Precis som med alla kognitiva tjänster bör utvecklare som använder Läs-/OCR-tjänsterna vara medvetna om Microsofts principer för kund information. Mer information finns på sidan Cognitive Services på [Microsoft Trust Center](https://www.microsoft.com/trust-center/product-overview) .

> [!NOTE]
> Datorn Vison 2,0 RecognizeText-åtgärder håller på att bli föråldrad med den nya Read-API: n som beskrivs i den här artikeln. Befintliga kunder ska [övergå till att använda Läs åtgärder](upgrade-api-versions.md).

## <a name="next-steps"></a>Nästa steg

- Kom igång med [visuellt innehåll Läs snabb starter för 3,0 SDK](./quickstarts-sdk/client-library.md) i C#, Java, java script eller python.
- Använd [REST API snabb starter för att läsa 3,1](./QuickStarts/CSharp-hand-text.md) i C#, Java, java script eller python för att lära dig hur du använder REST-API: er.
- Lär dig mer om att [läsa 3,1 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Läs mer om den [offentliga för hands versionen av läs 3,2 REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005) med stöd för förenklad kinesiska och japanska.
