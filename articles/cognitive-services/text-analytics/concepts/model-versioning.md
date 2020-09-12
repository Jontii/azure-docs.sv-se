---
title: Ange modell versionen i Textanalys v3
titleSuffix: Azure Cognitive Services
description: Lär dig hur du anger den API för textanalys modell som används för dina data.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: e6763633ce27c537f1384e8e51020d44132c0377
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469855"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Modell version i API för textanalys

Med version 3 av API för textanalys kan du välja den modell version som används på dina data. Använd den valfria `model-version` parametern för att välja modell versionen i API-begärandena. Till exempel: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Om den här parametern inte anges kommer API: et att standardvärdet vara den senaste stabila versionen. 

## <a name="available-versions"></a>Tillgängliga versioner

Använd tabellen nedan för att se vilka modell versioner som stöds av varje slut punkt.


| Slutpunkt                        | Versioner som stöds                                     | Senaste version |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,              | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`  | `2020-07-01`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`                             | `2020-07-01`   |


Du hittar information om uppdateringarna för de här modellerna i [Vad är nytt](../whats-new.md).

## <a name="text-analytics-for-health"></a>Textanalys för hälsa

[Textanalys för hälso tillstånds](../how-tos/text-analytics-for-health.md) container använder separata modell versioner än API-slutpunkterna ovan.  Observera att endast en modell version är tillgänglig per behållar avbildning.

| Slutpunkt                        | Etikett för container avbildning                     | Modell version |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`               | `1.1.013150001-amd64-preview` eller senaste | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |


## <a name="next-steps"></a>Nästa steg

* [Översikt över Textanalys](../overview.md)
* [Attitydanalys](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Igenkänning av enhet](../how-tos/text-analytics-how-to-entity-linking.md)
