---
title: Genomför sentiment-analys och utåsikts utvinning med Textanalys REST API
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du kan identifiera sentiment och mina åsikter i text med Azure Cognitive Services API för textanalys.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 12/02/2020
ms.author: aahi
ms.openlocfilehash: 0437704709e863cdbc62321c8a4b79015564e362
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558767"
---
# <a name="how-to-sentiment-analysis-and-opinion-mining"></a>Gör så här: sentiment analys och avyttrande

API för textanalysens Attitydanalys-funktion erbjuder två sätt att identifiera positiva och negativa sentiment. Om du skickar en Attitydanalys-begäran kommer API: et att returnera sentiment-etiketter (till exempel "negativa", "neutral" och "positiv") och säkerhets resultat på meningen och dokument nivå. Du kan också skicka avyttranden om utgångs punkt med Attitydanalys slut punkten, som innehåller detaljerad information om de åsikter som rör aspekter (till exempel attributen för produkter eller tjänster) i text. 

AI-modellerna som används av API: et tillhandahålls av tjänsten. du behöver bara skicka innehåll för analys.

## <a name="sentiment-analysis-versions-and-features"></a>Attitydanalys versioner och funktioner

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Funktion                                   | Attitydanalys v3 | Attitydanalys v 3.1 (förhands granskning) |
|-------------------------------------------|-----------------------|-----------------------------------|
| Metoder för enkel-och batch-begäranden    | X                     | X                                 |
| Attitydanalys resultat och etiketter             | X                     | X                                 |
| Linux-baserad [Docker-behållare](text-analytics-how-to-install-containers.md) | X  |  |
| Åsikts utvinning                            |                       | X                                 |

## <a name="sentiment-analysis"></a>Attitydanalys

Attitydanalys i version 3. x används sentiment-etiketter på text, som returneras på en mening och dokument nivå, med en förtroende poäng för var och en. 

Etiketterna är *positiva*, *negativa* och *neutrala*. På dokument nivå kan du också returnera den *blandade* sentiment-etiketten. Sentiment för dokumentet fastställs nedan:

| Mening sentiment                                                                            | Etikett för returnerat dokument |
|-----------------------------------------------------------------------------------------------|-------------------------|
| Minst en `positive` mening finns i dokumentet. Resten av meningarna är `neutral` . | `positive`              |
| Minst en `negative` mening finns i dokumentet. Resten av meningarna är `neutral` . | `negative`              |
| Minst en `negative` mening och minst en `positive` mening finns i dokumentet.    | `mixed`                 |
| Alla meningar i dokumentet är `neutral` .                                                  | `neutral`               |

Förtroendet sträcker sig från 1 till 0. Resultat närmare 1 anger en högre exakthet i etikettens klassificering, medan lägre poäng visar lägre exakthet. För varje dokument eller varje mening är de förväntade poängen som är kopplade till etiketterna (positiva, negativa och neutrala) upp till 1.

## <a name="opinion-mining"></a>Åsikts utvinning

Åsikts utvinning är en funktion i Attitydanalys, från och med version 3,1 – för hands version. 1. Den här funktionen är även känd som Aspect-baserad Attitydanalys i naturlig språk bearbetning (NLP) och ger mer detaljerad information om de åsikter som rör aspekter (till exempel attributen för produkter eller tjänster) i text. 

Om en kund till exempel lämnar feedback om ett hotell, till exempel "rummet var fantastiskt, men personalen var friendd.", skall yttrandet för att hitta aspekter i texten och deras associerade åsikter och sentiment. Attitydanalys kan bara rapportera ett negativt sentiment.

:::image type="content" source="../media/how-tos/opinion-mining.png" alt-text="Ett diagram över utvinnings exemplet för yttrandet" lightbox="../media/how-tos/opinion-mining.png":::

För att få fram en utgångs utvinning i resultatet måste du inkludera `opinionMining=true` flaggan i en begäran om sentiment-analys. Utsvars resultatet kommer att tas med i sentiment analys svar. Yttrandet är en utökning av Attitydanalys och ingår i den aktuella [pris nivån](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).


## <a name="sending-a-rest-api-request"></a>Skicka en REST API-begäran 

### <a name="preparation"></a>Förberedelse

Sentiment-analys ger bättre resultat när du ger det mindre mängd text att arbeta med. Detta är motsatsen till nyckelfrasextrahering vilket fungerar på bättre på större textblock. För att få bästa resultat från båda åtgärderna kan du överväga omstrukturering av indata på lämpligt sätt.

Du måste ha JSON-dokument i det här formatet: ID, text och språk. Attitydanalys stöder en mängd olika språk, med mer i för hands version. Mer information finns i [språk som stöds](../language-support.md).

Dokument storleken måste vara under 5 120 tecken per dokument. Det maximala antalet dokument som tillåts i en samling finns i artikeln [data begränsningar](../concepts/data-limits.md?tabs=version-3) under begrepp. Samlingen skickas i begäranstexten.

## <a name="structure-the-request"></a>Strukturera begäran

Skicka en POST-begäran. Du kan [använda Postman](text-analytics-how-to-call-api.md) eller **konsolen för API-testning** i följande referens länkar för att snabbt strukturera och skicka en. 

#### <a name="version-31-preview2"></a>[Version 3,1 – för hands version. 2](#tab/version-3-1)

[Attitydanalys v 3.1-referens](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="version-30"></a>[Version 3,0](#tab/version-3)

[Attitydanalys v3-referens](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>Begär slut punkter

Ange HTTPS-slutpunkten för sentiment-analys genom att antingen använda en Textanalys-resurs på Azure eller en instansierad [textanalys-behållare](text-analytics-how-to-install-containers.md). Du måste inkludera rätt URL för den version som du vill använda. Exempel:

> [!NOTE]
> Du kan hitta din nyckel och slut punkt för din Textanalys resurs på Azure Portal. De kommer att finnas på resursens **snabb start** sida under **resurs hantering**. 

#### <a name="version-31-preview2"></a>[Version 3,1 – för hands version. 2](#tab/version-3-1)

**Attitydanalys**

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/sentiment`

**Åsikts utvinning**

Du måste inkludera parametern för att få ett utgångs resultat för utåsikts utvinning `opinionMining=true` . Exempel:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/sentiment?opinionMining=true`

Den här parametern är inställd på `false` som standard. 

#### <a name="version-30"></a>[Version 3,0](#tab/version-3)

**Attitydanalys**

I v 3.0 är den enda tillgängliga slut punkten för Attitydanalys.
 
`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

Ange ett rubrik för begäran som ska innehålla din API för textanalys-nyckel. Ange den JSON-dokumentsamling som du har förberett för den här analysen i begärandetexten.

### <a name="example-request-for-sentiment-analysis-and-opinion-mining"></a>Exempel på begäran om utvinning av Attitydanalys och yttrande  

Följande är ett exempel på innehåll som du kan skicka in för attitydanalys. Formatet för begäran är detsamma för både `v3.0` och `v3.1-preview` .
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>Publicera begäran

Analysen utförs när begäran har tagits emot. Information om storlek och antal begär Anden som du kan skicka per minut och sekund finns i avsnittet [data begränsningar](../overview.md#data-limits) i översikten.

API för textanalys är tillstånds lös. Inga data lagras i ditt konto och resultaten returneras omedelbart i svaret.


### <a name="view-the-results"></a>Visa resultatet

Utdata returneras direkt. Du kan strömma resultaten till ett program som accepterar JSON eller spara utdata till en fil på det lokala systemet. Importera sedan utdata till ett program som du kan använda för att sortera, söka och ändra data. På grund av stöd för flerspråkig och emoji kan svaret innehålla text förskjutningar. Mer information finns i [så här bearbetar du förskjutningar](../concepts/text-offsets.md) .

#### <a name="version-31-preview2"></a>[Version 3,1 – för hands version. 2](#tab/version-3-1)

### <a name="sentiment-analysis-and-opinion-mining-example-response"></a>Svar på Attitydanalys och avyttrande av utvinnings exempel

> [!IMPORTANT]
> Följande är ett JSON-exempel för att använda yttrandet från Attitydanalys, som erbjuds i v 3.1 av API: et. Om du inte begär utåsikts utvinning är API-svaret detsamma som fliken **Version 3,0** .  

Attitydanalys v 3.1 kan returnera svars objekt för både Attitydanalys och avyttrande av utvinning.
  
Sentiment-analys returnerar en sentiment-etikett och förtroende poäng för hela dokumentet och varje mening i det. Resultat närmare 1 anger en högre exakthet i etikettens klassificering, medan lägre poäng visar lägre exakthet. Ett dokument kan ha flera meningar och konfidens intervallet i varje dokument eller mening lägger upp till 1.

Yttrandet kommer att söka efter aspekter i texten och deras associerade åsikter och sentiment. Under svaret nedan hade den mening *som restaurangen haft fantastisk mat och vår vänte* tid hade två aspekter: *mat* och *Waiter*. Varje aspekts `relations` egenskap innehåller ett `ref` värde med URI-referensen till associerade `documents` , `sentences` -och- `opinions` objekt.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly.",
                    "aspects": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 25,
                            "length": 4,
                            "text": "food",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/0"
                                }
                            ]
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 38,
                            "length": 6,
                            "text": "waiter",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/1"
                                }
                            ]
                        }
                    ],
                    "opinions": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 19,
                            "length": 5,
                            "text": "great",
                            "isNegated": false
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 49,
                            "length": 8,
                            "text": "friendly",
                            "isNegated": false
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

#### <a name="version-30"></a>[Version 3,0](#tab/version-3)

### <a name="sentiment-analysis-example-response"></a>Svar på Attitydanalys exempel

Sentiment-analys returnerar en sentiment-etikett och förtroende poäng för hela dokumentet och varje mening i det. Resultat närmare 1 anger en högre exakthet i etikettens klassificering, medan lägre poäng visar lägre exakthet. Ett dokument kan ha flera meningar och konfidens intervallet i varje dokument eller mening lägger upp till 1.

Svar från Attitydanalys v3 innehåller sentiment etiketter och Poäng för varje analyserad mening och dokument.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

---

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för sentiment-analys med hjälp av API för textanalys. Sammanfattningsvis:

+ Utvinning av Attitydanalys och avyttrande är tillgängligt för utvalda språk.
+ JSON-dokument i begär ande texten innehåller ID, text och språkkod.
+ POST-begäran är till en `/sentiment` slut punkt genom att använda en anpassad [åtkomst nyckel och en slut punkt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som är giltig för din prenumeration.
+ Använd `opinionMining=true` i Sentient analys begär Anden för att få utgångs resultat.
+ Svars utdata som består av en sentiment Poäng för varje dokument-ID kan strömmas till alla appar som accepterar JSON. Till exempel Excel och Power BI.

## <a name="see-also"></a>Se även

* [Översikt över Textanalys](../overview.md)
* [Använda klient biblioteket för Textanalys](../quickstarts/text-analytics-sdk.md)
* [Nyheter](../whats-new.md)
