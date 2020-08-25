---
title: Regioner – tal tjänst
titleSuffix: Azure Cognitive Services
description: En lista över tillgängliga regioner och slut punkter för tal tjänsten, inklusive tal-till-text, text till tal och tal översättning.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: b0574c41042e172af78365bb273c81729ce204ab
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749306"
---
# <a name="speech-service-supported-regions"></a>Regioner som stöds av tal tjänster

Med tjänsten Speech kan ditt program konvertera ljud till text, utföra tal översättning och konvertera text till tal. Tjänsten är tillgänglig i flera regioner med unika slut punkter för tal-SDK och REST-API: er.

Tal portalen för att utföra anpassade konfigurationer i din tal upplevelse för alla regioner finns här: https://speech.microsoft.com

Tänk på följande när du överväger regioner:

* Om programmet använder ett [tal-SDK](speech-sdk.md)anger du regions-ID: t, till exempel `westus` när du skapar en tal konfiguration.
* Om ditt program använder en av de olika tal tjänstens [REST-API: er](rest-apis.md)är regionen en del av slut punkts-URI: n som du använder när du gör förfrågningar.
* Nycklar som skapats för en region är bara giltiga i den regionen. Om du försöker använda dem med andra regioner resulterar det i autentiseringsfel.

## <a name="speech-sdk"></a>Speech SDK

I [talet SDK](speech-sdk.md)anges regioner som en sträng (till exempel som en parameter till `SpeechConfig.FromSubscription` i tal-SDK för C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Tal till text, text till tal och översättning

Anpassnings portalen för tal finns här: https://speech.microsoft.com

Tal tjänsten är tillgänglig i dessa regioner för **tal igenkänning**, **text till tal**och **översättning**:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Om du använder [tal-SDK](speech-sdk.md)anges regioner av **regions-ID** (till exempel som en parameter till `SpeechConfig.FromSubscription` ). Se till att regionen matchar region för din prenumeration.

### <a name="intent-recognition"></a>Avsiktsigenkänning

Tillgängliga regioner för **avsikts igenkänning** via tal-SDK: n är följande:

| Global region | Region           | Regions-ID |
| ------------- | ---------------- | -------------------- |
| Asien          | Asien, östra        | `eastasia`           |
| Asien          | Sydostasien   | `southeastasia`      |
| Australien     | Australien, östra   | `australiaeast`      |
| Europa        | Norra Europa     | `northeurope`        |
| Europa        | Europa, västra      | `westeurope`         |
| Nordamerika | East US          | `eastus`             |
| Nordamerika | USA, östra 2        | `eastus2`            |
| Nordamerika | USA, södra centrala | `southcentralus`     |
| Nordamerika | USA, västra centrala  | `westcentralus`      |
| Nordamerika | USA, västra          | `westus`             |
| Nordamerika | USA, västra 2        | `westus2`            |
| Sydamerika | Brasilien, södra     | `brazilsouth`        |

Det här är en delmängd av de publicerings regioner som stöds av [language Understandings tjänsten (Luis)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Röstassistenter

[Tal-SDK](speech-sdk.md) har stöd för **röst assistent** funktioner i följande regioner:

| Region         | Regions-ID |
| -------------- | -------------------- |
| USA, västra        | `westus`             |
| USA, västra 2      | `westus2`            |
| East US        | `eastus`             |
| USA, östra 2      | `eastus2`            |
| Europa, västra    | `westeurope`         |
| Norra Europa   | `northeurope`        |
| Sydostasien | `southeastasia`      |

### <a name="speaker-recognition"></a>Talarigenkänning

Talarigenkänning är för närvarande endast tillgängligt i `westus` regionen.

## <a name="rest-apis"></a>REST API:er

Tjänsten Speech visar också REST-slutpunkter för tal-till-text-och text till tal-begäranden.

### <a name="speech-to-text"></a>Tal till text

För referens dokumentation för tal till text, se [tal-till-text-REST API](rest-speech-to-text.md).

Slut punkten för REST API har följande format:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Ersätt `<REGION_IDENTIFIER>` med den identifierare som matchar regionen för din prenumeration från den här tabellen:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Språk parametern måste läggas till i URL: en för att undvika att ett HTTP-4xx-fel tas emot. Till exempel är språket inställt på amerikansk engelska med slut punkten västra USA: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

### <a name="text-to-speech"></a>Text till tal

Dokumentation om text till tal-referenser finns i [text till tal-REST API](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
