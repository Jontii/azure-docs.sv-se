---
title: Migrations guide för v2 i API för textanalys
titleSuffix: Azure Cognitive Services
description: Lär dig hur du flyttar dina program till att använda version 3 av API för textanalys.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 06/25/2020
ms.author: aahi
ms.openlocfilehash: 12c09ad8e1db3914263fcc864c9c2d09069d63a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85412591"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Migrera till version 3. x av API för textanalys

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

Om du använder version 2,1 av API för textanalys kommer den här artikeln att hjälpa dig att uppgradera ditt program till att använda version 3. x. Version 3,0 är allmänt tillgänglig och introducerar nya funktioner, till exempel utökad [namngiven enhets igenkänning (ner)](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) och [modell version](concepts/model-versioning.md). En för hands version av v 3.1 (v 3.1 – för hands version. x) är också tillgänglig, som lägger till funktioner som till exempel utsvars [utvinning](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features). De modeller som används i v2 kommer inte att ta emot framtida uppdateringar. 

#### <a name="sentiment-analysis"></a>[Sentiment-analys](#tab/sentiment-analysis)

## <a name="feature-changes"></a>Funktions ändringar 

Attitydanalys i version 2,1 returnerar sentiment resultat mellan 0 och 1 för varje dokument som skickas till API: et, med resultat närmare 1 som anger mer positiv sentiment. Version 3 returnerar i stället sentiment-etiketter (till exempel "positiv" eller "negativ") för båda meningarna och dokumentet som helhet och deras associerade konfidens resultat. 

## <a name="steps-to-migrate"></a>Steg för att migrera

### <a name="rest-api"></a>REST-API

Om programmet använder REST API uppdaterar du slut punkten för begäran till v3-slutpunkten för sentiment-analys. Till exempel: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment` . Du måste också uppdatera programmet så att det använder sentiment-etiketter som returneras i [JSON-svaret](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results). 

### <a name="client-libraries"></a>Klientbibliotek

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

#### <a name="ner-and-entity-linking"></a>[NER och enhets länkning](#tab/named-entity-recognition)

## <a name="feature-changes"></a>Funktions ändringar

> [!NOTE] 
> För närvarande returneras [v3-enhets kategorier](named-entity-types.md) endast på engelska och spanska text. API: n returnerar version 2,1-resultat för begär Anden på andra språk, förutsatt att de stöds i version 2,1.

I version 2,1 använder API för textanalys en slut punkt för namngiven entitets igenkänning (NER) och entitet länkning. Version 3 ger utökad identifiering av namngivna entiteter och använder separata slut punkter för NER och begär Anden om enhets länkning. Från och med v 3.1 – för hands version. 1 kan NER också identifiera personlig `pii` och hälso `phi` information. 

## <a name="steps-to-migrate"></a>Steg för att migrera

### <a name="rest-api"></a>REST-API

Om programmet använder REST API uppdaterar du slut punkten för begäran till v3-slutpunkterna för NER och/eller enhets länkning.

Entity Linking
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

Du måste också uppdatera ditt program för att använda [enhets kategorierna](named-entity-types.md) som returneras i [JSON-svaret](how-tos/text-analytics-how-to-entity-linking.md#view-results).

### <a name="client-libraries"></a>Klientbibliotek

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="language-detection"></a>[Språk identifiering](#tab/language-detection)

## <a name="feature-changes"></a>Funktions ändringar 

Funktionen för språk identifiering har inte ändrats i v3 utanför slut punkts versionen, men JSON-svaret kommer att innehålla `ConfidenceScore` i stället för `score` . V3 returnerar även ett enda språk i utdata. 

## <a name="steps-to-migrate"></a>Steg för att migrera

### <a name="rest-api"></a>REST-API

Om programmet använder REST API uppdaterar du slut punkten för begäran till v3-slutpunkten för språk identifiering. Till exempel: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages` . Du måste också uppdatera det program som ska användas i `ConfidenceScore` stället för `score` i [JSON-svaret](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results). 

### <a name="client-libraries"></a>Klientbibliotek

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]


#### <a name="key-phrase-extraction"></a>[Extrahering av nyckel fraser](#tab/key-phrase-extraction)

## <a name="feature-changes"></a>Funktions ändringar 

Extraherings funktionen för nyckel fraser har inte ändrats i v3 utanför slut punkts versionen.

## <a name="steps-to-migrate"></a>Steg för att migrera

### <a name="rest-api"></a>REST-API

Om programmet använder REST API uppdaterar du slut punkten för begäran till v3-slutpunkten för extrahering av nyckel fraser. Exempelvis: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

### <a name="client-libraries"></a>Klientbibliotek

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---


## <a name="see-also"></a>Se även

* [Referens för API för textanalys v2](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/)
* [Vad är API för textanalys](overview.md)
* [Stöd för språk](language-support.md)
* [Versionshantering för modell](concepts/model-versioning.md)