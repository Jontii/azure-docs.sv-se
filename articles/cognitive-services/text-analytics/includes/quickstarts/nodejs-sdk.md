---
title: 'Snabb start: Textanalys v3-klient bibliotek för Node.js | Microsoft Docs'
description: Kom igång med v3-Textanalys klient biblioteket för Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/20/2021
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: adfe6282d4a7d2cc35b4f5c71ecb67e6cda641a8
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090773"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Version 3,1 Preview](#tab/version-3-1)

[v3 referens dokumentation](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-preview)  |  [v3-biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  [v3-paket (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [v3-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-30"></a>[Version 3,0](#tab/version-3)

[v3 referens dokumentation](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-latest)  |  [v3-biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics)  |  [v3-paket (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics)  |  [v3-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-21"></a>[Version 2,1](#tab/version-2)

[v2 referens dokumentation](/javascript/api/@azure/cognitiveservices-textanalytics)  |  [käll kod](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics)  |  för v2-bibliotek [v2-paket (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics)  |  [v2-exempel](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [Node.js](https://nodejs.org/).
* När du har en Azure-prenumeration <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" skapar du en textanalys resurs "  target="_blank"> skapa en textanalys resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API för textanalys. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* Om du vill använda funktionen analysera behöver du en Textanalys resurs med pris nivån standard (S).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp 

cd myapp
```

Kör `npm init` kommandot för att skapa ett Node-program med en `package.json` fil. 

```console
npm init
```
### <a name="install-the-client-library"></a>Installera klient biblioteket

# <a name="version-31-preview"></a>[Version 3,1 Preview](#tab/version-3-1)

Installera `@azure/ai-text-analytics` NPM-paketen:

```console
npm install --save @azure/ai-text-analytics@5.1.0-beta.3
```

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den [på GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), som innehåller kod exemplen i den här snabb starten. 


# <a name="version-30"></a>[Version 3,0](#tab/version-3)

Installera `@azure/ai-text-analytics` NPM-paketen:

```console
npm install --save @azure/ai-text-analytics@5.0.0
```

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den [på GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), som innehåller kod exemplen i den här snabb starten. 

# <a name="version-21"></a>[Version 2,1](#tab/version-2)

Installera `@azure/cognitiveservices-textanalytics` NPM-paketen:

```console
npm install --save @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den [på GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js), som innehåller kod exemplen i den här snabb starten. 

---

Appens `package.json` fil kommer att uppdateras med beroenden.
Skapa en fil med namnet `index.js` och Lägg till följande:

# <a name="version-31-preview"></a>[Version 3,1 Preview](#tab/version-3-1)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-30"></a>[Version 3,0](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-21"></a>[Version 2,1](#tab/version-2)

```javascript
"use strict";

const os = require("os");
const CognitiveServicesCredentials = require("@azure/ms-rest-js");
const TextAnalyticsAPIClient = require("@azure/cognitiveservices-textanalytics");
```
---

Skapa variabler för resursens Azure-slutpunkt och nyckel.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>Objekt modell

Den Textanalys klienten är ett `TextAnalyticsClient` objekt som autentiserar till Azure med hjälp av din nyckel. Klienten tillhandahåller flera metoder för att analysera text, som en enskild sträng eller en batch.

Text skickas till API: et som en lista över `documents` , som är `dictionary` objekt som innehåller en kombination av `id` , `text` , och `language` attribut beroende på vilken metod som används. `text`Attributet lagrar texten som ska analyseras i ursprunget `language` och `id` kan vara vilket värde som helst. 

Objektet Response är en lista som innehåller analys informationen för varje dokument. 

## <a name="code-examples"></a>Kodexempel

* [Klientautentisering](#client-authentication)
* [Attitydanalys](#sentiment-analysis) 
* [Åsikts utvinning](#opinion-mining)
* [Språk identifiering](#language-detection)
* [Igenkänning av namngiven entitet](#named-entity-recognition-ner)
* [Länkning av entitet](#entity-linking)
* Personligt identifierbar information
* [Extrahering av nyckel fraser](#key-phrase-extraction)

## <a name="client-authentication"></a>Klientautentisering

# <a name="version-31-preview"></a>[Version 3,1 Preview](#tab/version-3-1)

Skapa ett nytt `TextAnalyticsClient` objekt med din nyckel och slut punkt som parametrar.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-30"></a>[Version 3,0](#tab/version-3)

Skapa ett nytt `TextAnalyticsClient` objekt med din nyckel och slut punkt som parametrar.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-21"></a>[Version 2,1](#tab/version-2)

Skapa ett nytt [TextAnalyticsClient](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) -objekt med `credentials` och `endpoint` som en parameter.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Attitydanalys

# <a name="version-31-preview"></a>[Version 3,1 Preview](#tab/version-3-1)

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `analyzeSentiment()` metod och hämta det returnerade `SentimentBatchResult` objektet. Gå igenom listan över resultat och skriv ut varje dokuments ID, sentiment på dokument nivå med förtroende poäng. För varje dokument innehåller resultatet menings nivå sentiment tillsammans med förskjutningar, längd och förtroende poäng.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Utdata

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

### <a name="opinion-mining"></a>Åsikts utvinning

Skapa en sträng mat ris som innehåller det dokument som du vill analysera för att göra sentiment-analys med uthandlings utvinning. Anropa klientens `analyzeSentiment()` metod med att lägga till alternativ flagga `includeOpinionMining: true` och hämta det returnerade `SentimentBatchResult` objektet. Gå igenom listan över resultat och skriv ut varje dokuments ID, sentiment på dokument nivå med förtroende poäng. För varje dokument innehåller resultatet inte bara menings nivå sentiment som ovan, utan även aspekt-och åsikts nivån sentiment.

```javascript
async function sentimentAnalysisWithOpinionMining(client){

    const sentimentInput = [
        {
            text: "The food and service were unacceptable, but the concierge were nice",
            id: "0",
            language: "en"
        }
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
            console.log("\tMined opinions");
            for (const { aspect, opinions } of sentence.minedOpinions) {
                console.log(`\t\tAspect text: ${aspect.text}`);
                console.log(`\t\tAspect sentiment: ${aspect.sentiment}`);
                console.log(`\t\tAspect Positive: ${aspect.confidenceScores.positive.toFixed(2)} \tNegative: ${aspect.confidenceScores.negative.toFixed(2)}`);
                console.log("\t\tAspect opinions:");
                for (const { text, sentiment, confidenceScores } of opinions) {
                    console.log(`\t\tOpinion text: ${text}`);
                    console.log(`\t\tOpinion sentiment: ${sentiment}`);
                    console.log(`\t\tOpinion Positive: ${confidenceScores.positive.toFixed(2)} \tNegative: ${confidenceScores.negative.toFixed(2)}`);
                }
            }
        });
    });
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Utdata

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 0.84  Negative: 0.16  Neutral: 0.00
        Sentences Sentiment(1):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 0.84  Negative: 0.16  Neutral: 0.00
        Mined opinions
                Aspect text: food
                Aspect sentiment: negative
                Aspect Positive: 0.01   Negative: 0.99
                Aspect opinions:
                Opinion text: unacceptable
                Opinion sentiment: negative
                Opinion Positive: 0.01  Negative: 0.99
                Aspect text: service
                Aspect sentiment: negative
                Aspect Positive: 0.01   Negative: 0.99
                Aspect opinions:
                Opinion text: unacceptable
                Opinion sentiment: negative
                Opinion Positive: 0.01  Negative: 0.99
                Aspect text: concierge
                Aspect sentiment: positive
                Aspect Positive: 1.00   Negative: 0.00
                Aspect opinions:
                Opinion text: nice
                Opinion sentiment: positive
                Opinion Positive: 1.00  Negative: 0.00
```

# <a name="version-30"></a>[Version 3,0](#tab/version-3)

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `analyzeSentiment()` metod och hämta det returnerade `SentimentBatchResult` objektet. Gå igenom listan över resultat och skriv ut varje dokuments ID, sentiment på dokument nivå med förtroende poäng. För varje dokument innehåller resultatet menings nivå sentiment tillsammans med förskjutningar, längd och förtroende poäng.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Utdata

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

# <a name="version-21"></a>[Version 2,1](#tab/version-2)

Skapa en lista över lexikon objekt som innehåller dokumenten som du vill analysera. Anropa klientens [sentiment ()-](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) Metod och hämta den returnerade [SentimentBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Upprepa listan över resultat och skriv ut varje dokuments ID och sentiment poäng. En poäng närmare 0 anger ett negativt sentiment, medan ett resultat närmare 1 anger en positiv sentiment.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Utdata

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Språkidentifiering

# <a name="version-31-preview"></a>[Version 3,1 Preview](#tab/version-3-1)

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `detectLanguage()` metod och hämta returnerade `DetectLanguageResultCollection` . Iterera sedan igenom resultaten och skriv ut varje dokuments ID med respektive primära språk.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Utdata

```console
ID: 0
        Primary Language French
```

# <a name="version-30"></a>[Version 3,0](#tab/version-3)

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `detectLanguage()` metod och hämta returnerade `DetectLanguageResultCollection` . Iterera sedan igenom resultaten och skriv ut varje dokuments ID med respektive primära språk.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Utdata

```console
ID: 0
        Primary Language French
```

# <a name="version-21"></a>[Version 2,1](#tab/version-2)

Skapa en lista över lexikon objekt som innehåller dina dokument. Anropa klientens [detectLanguage ()-](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) Metod och hämta den returnerade [LanguageBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Iterera sedan igenom resultaten och skriv ut dokumentets ID och språk.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Utdata

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Igenkänning av namngivna enheter (NER)

# <a name="version-31-preview"></a>[Version 3,1 Preview](#tab/version-3-1)

> [!NOTE]
> I version `3.1` :
> * Enhets länkning är en separat begäran än NER.

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `recognizeEntities()` metod och hämta `RecognizeEntitiesResult` objektet. Iterera igenom listan över resultat och skriv ut entitetsnamnet, typ, undertyp, förskjutning, längd och poäng.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Utdata

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Entity Linking

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `recognizeLinkedEntities()` metod och hämta `RecognizeLinkedEntitiesResult` objektet. Iterera igenom listan över resultat och skriv ut enhets namn, ID, data källa, URL och matchningar. Varje objekt i `matches` matrisen kommer att innehålla förskjutning, längd och Poäng för matchningen.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Utdata

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

### <a name="personally-identifying-information-pii-recognition"></a>Identifiering av personligt identifierbar information (PII)

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `recognizePiiEntities()` metod och hämta `RecognizePIIEntitiesResult` objektet. Upprepa i listan över resultat och skriv ut entitetsnamnet, typen och poängen.

```javascript
async function piiRecognition(client) {

    const documents = [
        "The employee's phone number is (555) 555-5555."
    ];

    const results = await client.recognizePiiEntities(documents, "en");
    for (const result of results) {
        if (result.error === undefined) {
            console.log("Redacted Text: ", result.redactedText);
            console.log(" -- Recognized PII entities for input", result.id, "--");
            for (const entity of result.entities) {
                console.log(entity.text, ":", entity.category, "(Score:", entity.confidenceScore, ")");
            }
        } else {
            console.error("Encountered an error:", result.error);
        }
    }
}
piiRecognition(textAnalyticsClient)
```

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Utdata

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

# <a name="version-30"></a>[Version 3,0](#tab/version-3)

> [!NOTE]
> I version `3.0` :
> * Enhets länkning är en separat begäran än NER.

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `recognizeEntities()` metod och hämta `RecognizeEntitiesResult` objektet. Iterera igenom listan över resultat och skriv ut entitetsnamnet, typ, undertyp, förskjutning, längd och poäng.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Utdata

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Entity Linking

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `recognizeLinkedEntities()` metod och hämta `RecognizeLinkedEntitiesResult` objektet. Iterera igenom listan över resultat och skriv ut enhets namn, ID, data källa, URL och matchningar. Varje objekt i `matches` matrisen kommer att innehålla förskjutning, längd och Poäng för matchningen.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Utdata

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

# <a name="version-21"></a>[Version 2,1](#tab/version-2)

> [!NOTE]
> I version 2,1 ingår enhets länkning i NER-svaret.

Skapa en lista med objekt som innehåller dina dokument. Anropa klientens [entiteter ()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) -Metod och hämta [EntitiesBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) -objektet. Upprepa i listan över resultat och skriv ut varje dokuments ID. För varje identifierad entitet skriver du ut dess Wikipedia-namn, typ och under typer (om de finns) samt platserna i den ursprungliga texten.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Utdata

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

# <a name="version-31-preview"></a>[Version 3,1 Preview](#tab/version-3-1)

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `extractKeyPhrases()` metod och hämta det returnerade `ExtractKeyPhrasesResult` objektet. Iterera igenom resultaten och skriv ut varje dokuments ID och alla identifierade nyckel fraser.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Utdata

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-30"></a>[Version 3,0](#tab/version-3)

Skapa en sträng mat ris som innehåller det dokument som du vill analysera. Anropa klientens `extractKeyPhrases()` metod och hämta det returnerade `ExtractKeyPhrasesResult` objektet. Iterera igenom resultaten och skriv ut varje dokuments ID och alla identifierade nyckel fraser.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Utdata

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-21"></a>[Version 2,1](#tab/version-2)

Skapa en lista med objekt som innehåller dina dokument. Anropa klientens [()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) -Metod och hämta det returnerade     [KeyPhraseBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) -objektet. Iterera igenom resultaten och skriv ut varje dokuments ID och alla identifierade nyckel fraser.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Kör din kod med `node index.js` i konsol fönstret.

### <a name="output"></a>Utdata

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Använd API asynkront med analys åtgärden

# <a name="version-31-preview"></a>[Version 3,1 Preview](#tab/version-3-1)

> [!CAUTION]
> Om du vill använda analys åtgärder måste du använda en Textanalys resurs med pris nivån standard (S).  

Skapa en ny funktion `analyze_example()` som kallas, som anropar `beginAnalyze()` funktionen. Resultatet blir en tids krävande åtgärd som kommer att avsökas efter resultat.

```javascript
const documents = [
  "Microsoft was founded by Bill Gates and Paul Allen.",
];

async function analyze_example(client) {
  console.log("== Analyze Sample ==");

  const tasks = {
    entityRecognitionTasks: [{ modelVersion: "latest" }]
  };
  const poller = await client.beginAnalyze(documents, tasks);
  const resultPages = await poller.pollUntilDone();

  for await (const page of resultPages) {
    const entitiesResults = page.entitiesRecognitionResults![0];
    for (const doc of entitiesResults) {
      console.log(`- Document ${doc.id}`);
      if (!doc.error) {
        console.log("\tEntities:");
        for (const entity of doc.entities) {
          console.log(`\t- Entity ${entity.text} of type ${entity.category}`);
        }
      } else {
        console.error("  Error:", doc.error);
      }
    }
  }
}

analyze_example(textAnalyticsClient);
```

### <a name="output"></a>Utdata

```console
== Analyze Sample ==
- Document 0
        Entities:
        - Entity Microsoft of type Organization
        - Entity Bill Gates of type Person
        - Entity Paul Allen of type Person
```

Du kan också använda analys åtgärden för att identifiera personligt identifierbar information och extrahering av nyckel fraser. Se analysera exempel för [Java Script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/javascript/beginAnalyze.js) och [typescript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/typescript/src/beginAnalyze.ts) på GitHub.

# <a name="version-30"></a>[Version 3,0](#tab/version-3)

Den här funktionen är inte tillgänglig i version 3,0.

# <a name="version-21"></a>[Version 2,1](#tab/version-2)

Den här funktionen är inte tillgänglig i version 2,1.

---

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `node` kommandot på snabb starts filen.

```console
node index.js
```
