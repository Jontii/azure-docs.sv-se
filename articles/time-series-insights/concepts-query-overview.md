---
title: Fråga data-Azure Time Series Insights Gen2 | Microsoft Docs
description: Data som frågar koncept och REST API översikt i Azure Time Series Insights Gen2.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: e9491757852b42faef40c107540e0ce3da3c7f99
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2020
ms.locfileid: "91650909"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>Fråga efter data från Azure Time Series Insights Gen2

Azure Time Series Insights Gen2 möjliggör data frågor om händelser och metadata som lagras i miljön via API: er på offentlig yta. Dessa API: er används också av [Azure Time Series Insights TSD-Utforskare](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).

Tre primära API-kategorier är tillgängliga i Azure Time Series Insights Gen2:

* **Miljö-API**: er: dessa API: er aktiverar frågor i själva Azure Time Series Insights Gen2-miljön. Dessa kan användas för att samla in listan över miljöer som anroparen har åtkomst till och miljömetadata.
* **Tids serie modell – fråga (TSM-Q) API: er**: aktiverar åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) i metadata som lagras i tids serie modellen i miljön. Dessa kan användas för att komma åt och redigera instanser, typer och hierarkier.
* **TSQ-API: er (Time Series Query)**: aktiverar hämtning av telemetri-eller händelse data när de registreras från käll leverantören och möjliggör utföra beräkningar och agg regeringar för data med avancerade skalärfunktioner och mängd funktioner.

Azure Time Series Insights Gen2 använder ett omfattande strängbaserade uttrycks språk, ett [Time Series-uttryck (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)för att uttrycka beräkningar i [Time Series-variabler](./concepts-variables.md).

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Översikt över Azure Time Series Insights Gen2 API: er

Följande kärn-API: er stöds.

[![Översikt över Time Series-frågor](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Miljö-API: er

* [Hämta miljöer API](https://docs.microsoft.com/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment): returnerar listan över miljöer som anroparen har behörighet att komma åt.
* [Hämta tillgänglighets-API för miljöer](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/getavailability): returnerar fördelningen av antal händelser över händelsens tidsstämpel `$ts` . Det här API: et hjälper till att avgöra om det finns några händelser i miljön genom att returnera antalet händelser som är brutna i tidsintervall, om sådana finns.
* [Hämta API för händelse schema](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/geteventschema): Returnerar metadata för händelse schema för ett angivet Sök omfång. Med detta API kan du hämta alla metadata och egenskaper som är tillgängliga i schemat för angivet Sök omfång.

## <a name="time-series-model-query-tsm-q-apis"></a>Time Series-modell – fråga (TSM-Q) API: er

De flesta av dessa API: er har stöd för batch-körning för att aktivera CRUD-åtgärder i flera tids serie modell enheter:

* [API för modell inställningar](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis): aktiverar *Get* och *patch* för standard typen och modell namnet för miljön.
* [Typ-API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#types-api): aktiverar CRUD för tids serie typer och deras associerade variabler.
* [Hierarkier API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#hierarchies-api): aktiverar CRUD i Time Series-hierarkier och deras associerade fält Sök vägar.
* [Instans-API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#instances-api): aktiverar CRUD på Time Series-instanser och deras associerade instans fält. Instans-API: et stöder dessutom följande åtgärder:
  * [Sök](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search): hämtar en partiell lista med träffar vid sökning efter Time Series-instanser baserat på instans-attribut.
  * [Föreslå](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest): söker efter och föreslår en partiell lista med träffar vid sökning efter Time Series-instanser baserat på instans-attribut.

## <a name="time-series-query-tsq-apis"></a>API: er för Time Series-frågor (TSQ)

Dessa API: er är tillgängliga i båda butikerna (varm och kall) i vår lagrings lösning för flera lager. Fråge-URL-parametrar används för att ange den [lagrings typ](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters) som frågan ska köras på:

* [Hämta händelse-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents): aktiverar frågor och hämtning av rå händelser och associerade händelse-tidsstämplar när de registreras i Azure Time Series Insights Gen2 från käll leverantören. Med det här API: et kan du hämta rå händelser för ett angivet Time Series-ID och Sök omfång. Detta API stöder sid brytning för att hämta fullständig svars data uppsättning för de valda indata.

  > [!IMPORTANT]

  > * Som en del av de [kommande ändringarna av JSON-förenkling och undantags regler](https://docs.microsoft.com/azure/time-series-insights/ingestion-rules-update)kommer matriser att lagras som **dynamisk** typ. Nytto Last egenskaper som lagras som den här typen är **bara tillgängliga via API: t get Events**.

* [Hämta serie-API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries): aktiverar frågor och hämtning av beräknade värden och associerade händelse-tidsstämplar genom att tillämpa beräkningar som definierats av variabler i rå händelser. Dessa variabler kan definieras i tids serie modellen eller som anges i frågan. Detta API stöder sid brytning för att hämta fullständig svars data uppsättning för de valda indata.

* [API för sammanställd serie](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries): aktiverar frågor och hämtning av sammanställda värden och de associerade intervallens tidsstämplar genom att tillämpa beräkningar som definierats av variabler i rå händelser. Dessa variabler kan definieras i tids serie modellen eller som anges i frågan. Detta API stöder sid brytning för att hämta fullständig svars data uppsättning för de valda indata.
  
  För ett angivet Sök omfång och intervall returnerar detta API ett sammanställt svar per intervall per variabel för ett Time Series-ID. Antalet intervall i data uppsättningen för svar beräknas genom att räkna upp Epoka Tick (antalet millisekunder som har förflutit sedan UNIX epok-1 Jan, 1970) och dividerar skalstrecken med intervall intervall storleken som anges i frågan.

  De tidsstämplar som returneras i svars uppsättningen är av gränserna för det vänstra intervallet, inte av exempel händelser från intervallet.

## <a name="next-steps"></a>Nästa steg

* Läs mer om olika variabler som kan definieras i [tids serie modellen](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm).
* Läs mer om hur du frågar efter data från [Azure Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).
