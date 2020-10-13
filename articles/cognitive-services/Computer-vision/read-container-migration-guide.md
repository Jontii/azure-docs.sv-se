---
title: Migrera till Read v3. x OCR-behållare
titleSuffix: Azure Cognitive Services
description: Lär dig hur du migrerar till v3-läsa OCR-behållare
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 09/21/2020
ms.author: aahi
ms.openlocfilehash: 856e73181ee02fe2bb21c4317ec8c733e2536d53
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973130"
---
# <a name="migrate-to-the-read-v3x-ocr-containers"></a>Migrera till Read v3. x OCR-behållare

Om du använder version 2 av Visuellt innehåll Read OCR-behållare använder du den här artikeln för att lära dig hur du uppgraderar ditt program till att använda version 3. x av behållaren. 


## <a name="configuration-changes"></a>Konfigurations ändringar

* `ReadEngineConfig:ResultExpirationPeriod` stöds inte längre. Läs containern har ett inbyggt cron-jobb som tar bort de resultat och metadata som är kopplade till en begäran efter 48 timmar.
* `Cache:Redis:Configuration` stöds inte längre. Cachen används inte i v3. x-behållare, så du behöver inte ange den.

## <a name="api-changes"></a>API-ändringar

Read v3. x-behållare använder version 3 av API för visuellt innehåll och har följande slut punkter:

#### <a name="version-31-preview"></a>[Version 3,1 – för hands version](#tab/version-3-1)

* `/vision/v3.1-preview.2/read/analyzeResults/{operationId}`
* `/vision/v3.1-preview.2/read/analyze`
* `/vision/v3.1-preview.2/read/syncAnalyze`

#### <a name="version-30-preview"></a>[Version 3,0 – för hands version](#tab/version-3)

* `/vision/v3.0/read/analyzeResults/{operationId}`
* `/vision/v3.0/read/analyze`
* `/vision/v3.0/read/syncAnalyze`

---

Mer information om hur du uppdaterar dina program för att använda version 3 av molnbaserad Read API finns i [migreringsguiden för visuellt innehåll v3-REST API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/upgrade-api-versions) . Den här informationen gäller även för behållaren. Observera att Sync-åtgärder endast stöds i behållare.

## <a name="memory-requirements"></a>Minneskrav

Kraven och rekommendationerna baseras på benchmarks med en enda begäran per sekund, med en 8 MB-avbildning av en skannad affärs skrivelse som innehåller 29 rader och totalt 803 tecken. I följande tabell beskrivs den lägsta och rekommenderade fördelningen av resurser för varje read-behållare.

|Container  |Minimum | Rekommenderas  |
|---------|---------|------|
|Läs 3,0 – för hands version     | 8 kärnor, 16 GB minne         | 8 kärnor, 24 GB minne
|Läs 3,1 – för hands version | 8 kärnor, 16 GB minne         | 8 kärnor, 24 GB minne

Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.

Core och minne motsvarar `--cpus` `--memory` inställningarna och som används som en del av kommandot Docker Run.

## <a name="storage-implementations"></a>Lagrings implementeringar

>[!NOTE]
> MongoDB stöds inte längre i 3. x-versioner av behållaren. I stället stöder behållarna Azure Storage och fil system offline.

| Implementering |    Obligatoriska körnings argument |
|---------|---------|
|Filnivå (standard)    | Inga körnings argument krävs. `/share` katalogen kommer att användas. |
|Azure-blobb    | `Storage:ObjectStore:AzureBlob:ConnectionString={AzureStorageConnectionString}` |

## <a name="queue-implementations"></a>Köa implementeringar

I v3. x i behållaren stöds inte RabbitMQ för närvarande. De implementeringar som stöds är:

| Implementering | Körnings argument | Avsedd användning |
|---------|---------|-------|
| I minnet (standard) | Inga körnings argument krävs. | Utveckling och testning |
| Azure Queues | `Queue:Azure:ConnectionString={AzureStorageConnectionString}` | Produktion |
| RabbitMQ    | Inte tillgänglig | Produktion |

För tillagd redundans använder Read v3. x-behållaren en Synlighets-timer för att se till att begär Anden kan bearbetas i händelse av en krasch när de körs i en konfiguration med flera behållare. 

Ställ in timern med `Queue:Azure:QueueVisibilityTimeoutInMilliseconds` , vilket anger hur lång tid ett meddelande ska vara osynligt när en annan arbets process bearbetar det. För att undvika att sidor bearbetas redundanta rekommenderar vi att du ställer in timeout-perioden på 120 sekunder. Standardvärdet är 30 sekunder.

| Standardvärde | Rekommenderat värde |
|---------|---------|
| 30000 |    120000 |


## <a name="next-steps"></a>Nästa steg

* Granska [Konfigurera behållare](computer-vision-resource-container-config.md) för konfigurations inställningar
* Läs [visuellt innehåll översikt](overview.md) och lär dig mer om att känna igen utskrift och handskriven text
* Mer information om de metoder som stöds av behållaren finns i [API för visuellt innehåll](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .
* Läs vanliga [frågor och svar (FAQ)](FAQ.md) för att lösa problem som rör visuellt innehåll-funktioner.
* Använd fler [Cognitive Services behållare](../cognitive-services-container-support.md)
