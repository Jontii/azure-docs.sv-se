---
title: Anropa API:t för textanalys
titleSuffix: Azure Cognitive Services
description: Den här artikeln förklarar hur du kan anropa Azure-Cognitive Services Textanalys REST API och Postman.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: 2977946b2e1f37aa356ee075d2caac237170df0f
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "95993331"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Så här anropar du Textanalys REST API

I den här artikeln använder vi Textanalys REST API och [Postman](https://www.postman.com/downloads/) för att demonstrera viktiga begrepp. API: et tillhandahåller flera synkrona och asynkrona slut punkter för att använda funktionerna i tjänsten. 

## <a name="using-the-api-asynchronously"></a>Använda API asynkront

Från och med v 3.1 – för hands version. 3 tillhandahåller API för textanalys två asynkrona slut punkter: 

* Med `/analyze` slut punkten för textanalys kan du analysera samma uppsättning text dokument med flera text analys funktioner i ett API-anrop. Om du tidigare vill använda flera funktioner måste du göra separata API-anrop för varje åtgärd. Överväg den här funktionen när du behöver analysera stora uppsättningar med dokument med mer än en Textanalys-funktion.

* `/health`Slut punkten för textanalys för hälsa, som kan extrahera och märka relevant medicinsk information från kliniska dokument.  

Se tabellen nedan för att se vilka funktioner som kan användas asynkront. Observera att endast några få funktioner kan anropas från `/analyze` slut punkten. 

| Funktion | Synkront | Asynkrona |
|--|--|--|
| Språkidentifiering | ✔ |  |
| Attitydanalys | ✔ |  |
| Åsikts utvinning | ✔ |  |
| Extrahering av nyckelfraser | ✔ | ✔* |
| Identifiering av namngiven entitet (inklusive PII och PHI) | ✔ | ✔* |
| Textanalys för hälsa (container) | ✔ |  |
| Textanalys för hälso tillstånd (API) |  | ✔  |

`*` -Anropade asynkront via `/analyze` slut punkten.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

## <a name="prerequisites"></a>Förutsättningar


> [!NOTE]
> Du behöver en Textanalys-resurs som använder en standard [pris nivå](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) (S) om du vill använda- `/analyze` eller- `/health` slut punkterna.

1.  Börja med att gå till [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) och skapa en ny textanalys resurs, om du inte redan har en. Välj pris nivån standard (S) om du vill använda-eller- `/analyze` `/health` slut punkterna.

2.  Välj den region där du vill använda slut punkten.

3.  Skapa Textanalys resursen och gå till bladet "nycklar och slut punkt" till vänster på sidan. Kopiera nyckeln som ska användas senare när du anropar API: erna. Du lägger till detta senare som ett värde för `Ocp-Apim-Subscription-Key` sidhuvudet.


<a name="json-schema"></a>

## <a name="api-request-format"></a>API-format för begäran

#### <a name="synchronous"></a>[Synkront](#tab/synchronous)

Formatet för API-begäranden är detsamma för alla synkrona åtgärder. Dokument skickas i ett JSON-objekt som rå ostrukturerad text. XML stöds inte. JSON-schemat består av de element som beskrivs nedan.

| Element | Giltiga värden | Obligatoriskt? | Användning |
|---------|--------------|-----------|-------|
|`id` |Data typen är sträng, men i dokument-ID: n är det vanligt vis heltal. | Obligatorisk | Systemet använder de ID: n som du anger för att strukturera utdata. Språk koder, nyckel fraser och sentiment resultat genereras för varje ID i begäran.|
|`text` | Ostrukturerad rå text, upp till 5 120 tecken. | Obligatorisk | För språk identifiering kan text uttryckas på valfritt språk. För sentiment analys, extrahering av nyckel fraser och enhets identifiering måste texten vara på ett [språk som stöds](../language-support.md). |
|`language` | 2 teckens [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) -kod för ett [språk som stöds](../language-support.md) | Det varierar | Krävs för sentiment-analys, extrahering av nyckel fraser och länkning av entiteter. valfritt för språk identifiering. Det finns inget fel om du exkluderar det, men analysen har minskats utan den. Språk koden ska motsvara den `text` du anger. |

Följande är ett exempel på en API-begäran för synkrona Textanalys-slutpunkter. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="analyze"></a>[Analysera](#tab/analyze)

> [!NOTE]
> Den senaste för hands versionen av Textanalys klient biblioteket gör att du kan anropa asynkrona analys åtgärder med ett klient objekt. Du kan hitta exempel på GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

Med `/analyze` slut punkten kan du välja vilken av de textanalys funktioner som stöds som du vill använda i ett enda API-anrop. Den här slut punkten stöder för närvarande:

* extrahering av nyckel fraser 
* Identifiering av namngiven entitet (inklusive PII och PHI)

| Element | Giltiga värden | Obligatoriskt? | Användning |
|---------|--------------|-----------|-------|
|`displayName` | Sträng | Valfritt | Används som visnings namn för den unika identifieraren för jobbet.|
|`analysisInput` | Innehåller `documents` fältet nedan | Obligatorisk | Innehåller informationen för de dokument som du vill skicka. |
|`documents` | Innehåller `id` fälten och `text` nedan | Obligatorisk | Innehåller information för varje dokument som skickas och dokumentets rå text. |
|`id` | Sträng | Obligatorisk | De ID: n som du anger används för att strukturera utdata. |
|`text` | Ostrukturerad rå text, upp till 125 000 tecken. | Obligatorisk | Måste vara på det engelska språket, vilket är det enda språk som stöds för närvarande. |
|`tasks` | Innehåller följande Textanalys funktioner: `entityRecognitionTasks` , `keyPhraseExtractionTasks` eller `entityRecognitionPiiTasks` . | Obligatorisk | En eller flera av de Textanalys-funktioner som du vill använda. Observera att `entityRecognitionPiiTasks` har en valfri `domain` parameter som kan anges till `pii` eller `phi` . Om inget anges används standardvärdet i systemet `pii` . |
|`parameters` | Innehåller `model-version` fälten och `stringIndexType` nedan | Obligatorisk | Det här fältet ingår i ovanstående funktions uppgifter som du väljer. De innehåller information om modell versionen som du vill använda och index typen. |
|`model-version` | Sträng | Obligatorisk | Ange vilken version av modellen som du vill använda.  |
|`stringIndexType` | Sträng | Obligatorisk | Ange den text avkodare som matchar din programmerings miljö.  De typer som stöds är `textElement_v8` (standard), `unicodeCodePoint` , `utf16CodeUnit` . Mer information finns i [artikeln text förskjutning](../concepts/text-offsets.md#offsets-in-api-version-31-preview) .  |
|`domain` | Sträng | Valfritt | Gäller endast för en parameter för `entityRecognitionPiiTasks` uppgiften och kan anges till `pii` eller `phi` . Den används som standard `pii` om inget värde anges.  |

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}

```

#### <a name="text-analytics-for-health"></a>[Textanalys för hälsa](#tab/health)

Formatet för API-begäranden till Textanalys för hälso värdbaserade API: er är detsamma som för dess behållare. Dokument skickas i ett JSON-objekt som rå ostrukturerad text. XML stöds inte. JSON-schemat består av de element som beskrivs nedan.  Fyll i och skicka [formuläret Cognitive Services begäran](https://aka.ms/csgate) för att begära åtkomst till textanalys för offentlig för hands version av hälso tillstånd. Du debiteras inte för Textanalys för hälso tillstånds användning. 

| Element | Giltiga värden | Obligatoriskt? | Användning |
|---------|--------------|-----------|-------|
|`id` |Data typen är sträng, men i dokument-ID: n är det vanligt vis heltal. | Obligatorisk | Systemet använder de ID: n som du anger för att strukturera utdata. |
|`text` | Ostrukturerad rå text, upp till 5 120 tecken. | Obligatorisk | Observera att endast engelsk text stöds för närvarande. |
|`language` | 2 teckens [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) -kod för ett [språk som stöds](../language-support.md) | Obligatorisk | `en`Stöds för närvarande inte. |

Följande är ett exempel på en API-begäran för Textanalys för hälso slut punkter. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Se artikeln [data och frekvens begränsningar](../concepts/data-limits.md) för information om priser och storleks gränser för att skicka Data till API för textanalys.


## <a name="set-up-a-request"></a>Konfigurera en begäran 

I Postman (eller något annat verktyg för webb-API-testning) lägger du till slut punkten för den funktion som du vill använda. Använd tabellen nedan för att hitta rätt slut punkts format och Ersätt `<your-text-analytics-resource>` med resurs slut punkten. Exempel:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Synkront](#tab/synchronous)

| Funktion | Typ av begäran | Resurs slut punkter |
|--|--|--|
| Språkidentifiering | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Attitydanalys | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Åsikts utvinning | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Extrahering av nyckelfraser | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Igenkänning av namngiven entitet – allmänt | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Igenkänning av namngivna enheter – PII | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Igenkänning av namngiven entitet – Fi | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="analyze"></a>[Analysera](#tab/analyze)

| Funktion | Typ av begäran | Resurs slut punkter |
|--|--|--|
| Skicka analys jobb | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze` |
| Hämta analys status och resultat | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>` |

#### <a name="text-analytics-for-health"></a>[Textanalys för hälsa](#tab/health)

| Funktion | Typ av begäran | Resurs slut punkter |
|--|--|--|
| Skicka Textanalys för hälso tillstånds jobb  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs` |
| Hämta jobb status och resultat | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |
| Avbryt jobb | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |

--- 

När du har slut punkten, i Postman (eller något annat verktyg för webb-API-testning):

1. Välj typ av begäran för den funktion som du vill använda.
2. Klistra in i slut punkten för den korrekta åtgärd som du vill använda från tabellen ovan.
3. Ange de tre begärandehuvuden:

   + `Ocp-Apim-Subscription-Key`: din åtkomst nyckel, hämtas från Azure Portal
   + `Content-Type`: Application/JSON
   + `Accept`: Application/JSON

    Om du använder Postman bör din begäran se ut ungefär som på följande skärm, förutsatt att en `/keyPhrases` slut punkt används.
    
    ![Skärm bild för begäran med slut punkt och sidhuvud](../media/postman-request-keyphrase-1.png)
    
4. Välj **RAW** som **brödtext**
    
    ![Skärm bild för begäran med inställningar för brödtext](../media/postman-request-body-raw.png)

5. Klistra in vissa JSON-dokument i ett giltigt format. Använd exemplen i avsnittet **API Request format** ovan och mer information och exempel finns i avsnitten nedan:

      + [Språkidentifiering](text-analytics-how-to-language-detection.md)
      + [Extrahering av nyckelfraser](text-analytics-how-to-keyword-extraction.md)
      + [Sentiment-analys](text-analytics-how-to-sentiment-analysis.md)
      + [Igenkänning av enhet](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>Skicka begäran

Skicka API-begäran. Om du har gjort anropet till en synkron slut punkt visas svaret omedelbart, som ett enda JSON-dokument med ett objekt för varje dokument-ID som anges i begäran.

Om du har gjort anropet till asynkrona `/analyze` eller `/health` slut punkter kontrollerar du att du har fått en 202-svarskod. Du måste få svaret att visa resultaten:

1. I API-svaret hittar du `Operation-Location` från-huvudet, som identifierar det jobb som du skickade till API: et. 
2. Skapa en GET-begäran för den slut punkt som du använde. Se [tabellen ovan](#set-up-a-request) för slut punkts formatet och granska [API-referens dokumentationen](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus). Exempel:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>`

3. Lägg till i `Operation-Location` begäran.

4. Svaret är ett enda JSON-dokument med ett objekt för varje dokument-ID som anges i begäran.

## <a name="example-api-responses"></a>Exempel-API-svar
 
# <a name="synchronous"></a>[Synkront](#tab/synchronous)

Svaren på den synkrona slut punkten varierar beroende på vilken slut punkt du använder. Se följande artiklar för exempel svar.

+ [Språkidentifiering](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Extrahering av nyckelfraser](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Sentiment-analys](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Igenkänning av enhet](text-analytics-how-to-entity-linking.md#view-results)

# <a name="analyze"></a>[Analysera](#tab/analyze)

Om det lyckas, kommer GET-begäran till `/analyze` slut punkten returnera ett objekt som innehåller de tilldelade aktiviteterna. Till exempel `keyPhraseExtractionTasks`. Dessa aktiviteter innehåller objektet Response från lämplig Textanalys funktion. Se följande artiklar för mer information.

+ [Extrahering av nyckelfraser](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Igenkänning av enhet](text-analytics-how-to-entity-linking.md#view-results)


```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```

# <a name="text-analytics-for-health"></a>[Textanalys för hälsa](#tab/health)

I följande artikel finns mer information om det asynkrona API-svaret Textanalys för hälso tillstånd:

+ [Textanalys för hälsa](text-analytics-for-health.md#hosted-asynchronous-web-api-response)


--- 

## <a name="see-also"></a>Se även

* [Översikt över Textanalys](../overview.md)
* [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)</br>
* [Produktsida för textanalys](//go.microsoft.com/fwlink/?LinkID=759712)
* [Använda klient biblioteket för Textanalys](../quickstarts/text-analytics-sdk.md)
* [Nyheter](../whats-new.md)
