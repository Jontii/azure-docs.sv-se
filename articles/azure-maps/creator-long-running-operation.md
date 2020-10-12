---
title: API för Azure Maps Long-Running-åtgärd
description: Läs mer om tids krävande asynkron bakgrunds bearbetning i Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 360fc4af688e393bb8639ee773f0bf0de603a425
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "83598390"
---
# <a name="creator-long-running-operation-api"></a>API för skapare Long-Running åtgärd

Vissa API i Azure Maps använda ett [asynkront Request-Reply-mönster](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply). Med det här mönstret kan Azure Maps tillhandahålla tjänster med hög tillgänglighet och hög tillgänglighet. I den här artikeln förklaras Azure Map: s speciella implementering av asynkron bakgrunds bearbetning med lång körning.

## <a name="submitting-a-request"></a>Skicka en begäran

Ett klient program startar en tids krävande åtgärd via ett synkront anrop till ett HTTP-API. Detta anrop är vanligt vis i form av en HTTP POST-begäran. När en asynkron arbets belastning har skapats returnerar API: t en HTTP- `202` statuskod som anger att begäran har godkänts. Det här svaret innehåller ett `Location` sidhuvud som pekar på en slut punkt som klienten kan söka efter för att kontrol lera status för den tids krävande åtgärden.

### <a name="example-of-a-success-response"></a>Exempel på ett lyckat svar

```HTTP
Status: 202 Accepted
Location: https://atlas.microsoft.com/service/operations/{operationId}

```

Om anropet inte klarar valideringen returnerar API i stället ett HTTP- `400` svar för en felaktig begäran. Svars texten ger klienten mer information om varför begäran var ogiltig.

### <a name="monitoring-the-operation-status"></a>Övervaka åtgärds status

Plats slut punkten som anges i de godkända svarshuvuden kan avsökas för att kontrol lera status för den tids krävande åtgärden. Svars texten från begäran om drift status innehåller alltid `status` `createdDateTime` egenskaperna och. `status`Egenskapen visar det aktuella läget för den tids krävande åtgärden. Möjliga tillstånd är `"NotStarted"` , `"Running"` , `"Succeeded"` och `"Failed"` . `createdDateTime`Egenskapen visar den tidpunkt då den första begäran gjordes för att starta den långvariga åtgärden. När status är antingen `"NotStarted"` eller `"Running"` , `Retry-After` kommer även en rubrik att tillhandahållas med svaret. `Retry-After`Rubriken, mätt i sekunder, kan användas för att fastställa när nästa avsöknings anrop till åtgärds status-API: et ska göras.

### <a name="example-of-running-a-status-response"></a>Exempel på att köra ett status svar

```HTTP
Status: 200 OK
Retry-After: 30
{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Running"
}
```

## <a name="handling-operation-completion"></a>Slut för ande av hanterings åtgärd

När den långvariga åtgärden slutförs, kommer svarets status antingen vara `"Succeeded"` eller `"Failed"` . När en ny resurs har skapats från en tids krävande åtgärd returnerar svars koden HTTP- `201 Created` status kod. Svaret innehåller även ett `Location` huvud som pekar på metadata om resursen. När ingen ny resurs har skapats returnerar svars `200 OK` koden http status kod. Vid ett haveri är svars status koden också `200 OK` koden. Svaret kommer dock att ha en `error` egenskap i bröd texten. Fel data följer OData-fel specifikationen.

### <a name="example-of-success-response"></a>Exempel på lyckat svar

```HTTP
Status: 201 Created
Location: "https://atlas.microsoft.com/tileset/{tileset-id}"
 {
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/tileset/{tileset-id}"
}
```

### <a name="example-of-failure-response"></a>Exempel på felsvar

```HTTP
Status: 200 OK

{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Failed",
    "error": {
        "code": "InvalidFeature",
        "message": "The provided feature is invalid.",
        "details": {
            "code": "NoGeometry",
            "message": "No geometry was provided with the feature."
        }
    }
}
```
